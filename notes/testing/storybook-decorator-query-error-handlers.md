---
title: Storybook 데코레이터로 앱 QueryProvider onError를 재현할 때의 함정 4가지
date: 2026-04-29
category: testing
tags: [storybook, react-query, decorator, react, mutation-cache, msw, useMemo, frontend]
summary: 페이지 prototype용 Storybook에서 모달이 mutationCache.onError에 dispatch되는 앱이라면 데코레이터에 라이트 onError를 이식해야 한다. 이때 QueryProvider 통째 import 금지, useMemo deps는 빈 배열, portal target div 필요, play 함수로 에러 트리거 — 4가지 함정.
---

## 배경

뷰업 앱은 React Query의 `mutationCache.onError`에 글로벌 에러 핸들러가 박혀 있다. mutation의 `meta.errorMessage[code]`에 매칭된 메시지를 `useGlobalModalStore.setAlertModal`로 dispatch → `<GlobalModal>` portal에 렌더되는 구조.

```ts
// 앱 QueryProvider
new MutationCache({
  onError(error, _v, _c, mutation) {
    const code = (error as AxiosError).response?.data?.code ?? '';
    const message = mutation?.meta?.errorMessage?.[code];
    if (message) setAlertModal({ content: message });
  },
});
```

페이지 단위 Storybook 양산 중 "로그인 실패" variant — MSW로 401 + `{code: 'unauthorized'}`를 돌려주고 play 함수로 폼 제출까지 자동화했는데, **모달이 안 뜨고 Storybook이 unhandled error로 표시.** 데코레이터의 `QueryClient`가 평범한 디폴트라 `onError`가 매칭조차 못 한 게 원인.

여기서 데코레이터에 onError를 이식하는 과정에서 4가지 함정을 만남.

---

## 함정 1 — 앱 `QueryProvider`를 통째 import하면 안 된다

직관: "그냥 앱 QueryProvider를 그대로 쓰면 되는 거 아냐?"

문제: 앱 QueryProvider는 `MSWProvider`를 자식으로 품고 있다. Storybook은 `msw-storybook-addon`이 이미 worker를 띄우는데, 둘이 동시에 등록되면 같은 service worker 스코프에서 **이중 등록 → handler 매칭이 의도와 다르게 일어남.**

해법: `mutationCache.onError`만 라이트 이식. `MSWProvider`, `Devtools`, RUM 같은 prod-only 요소는 빼고 핵심 로직만 데코레이터로 가져온다.

```tsx
function QueryWithErrorHandlers({ children }: { children: ReactNode }) {
  const router = useRouter();
  const { getValue } = useGlobalModalStore();
  const { setAlertModal } = getValue('actions');

  const queryClient = useMemo(() =>
    new QueryClient({
      mutationCache: new MutationCache({ onError: /* 앱과 동일한 분기 */ }),
      queryCache: new QueryCache({ onError: /* ... */ }),
    }),
  []);

  return <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>;
}
```

**원칙**: 데코레이터는 *앱 Provider 트리의 라이트 합성*이지, *재사용*이 아니다. 핵심 동작만 옮겨오고 환경 의존(MSW, Devtools, FeatureFlag)은 stub/제외.

---

## 함정 2 — `useMemo` deps에 `router`/`setAlertModal`을 넣으면 무한 hang

직관: "deps에 외부 값 다 넣어야 lint 통과하고 안전하지."

문제: AuthProvider가 매 렌더마다 새 actions 객체를 만들고, `useRouter()`도 storybook 환경에서 ref가 유지된다고 보장 못 한다. deps에 넣으면 **매 렌더마다 새 `QueryClient`가 생성** → mutation/query 누수 + Storybook이 `sb-preparing-story` 상태로 무한 hang. (Storybook iframe이 story를 마운트하는 동안 client가 계속 갈리면 ready 시그널이 끝나지 않음.)

해법: `deps = []`로 두고 **클로저 capture**. zustand selector + Storybook의 router stub은 mount 후 stable해서 첫 capture만으로 충분하다. biome-ignore 주석에 사유를 박제.

```ts
const queryClient = useMemo(
  () => new QueryClient({ mutationCache: ... }),
  // biome-ignore lint/correctness/useExhaustiveDependencies: setAlertModal/router are
  // captured via closure; recreating client per-render leaks mutations and triggers
  // infinite re-mount in storybook
  [],
);
```

**원칙**: lint 룰을 회피할 때는 *왜* 회피하는지가 컴파일 에러보다 더 중요하다. ignore 주석은 "회귀 시 어떤 증상이 재발하는지" 한 줄이라도 남긴다.

---

## 함정 3 — GlobalModal은 portal target DOM이 미리 있어야 한다

직관: "GlobalModal 컴포넌트만 마운트하면 알아서 뜨겠지."

문제: 앱의 `<GlobalModal>`은 `createPortal(content, document.querySelector('#wrapper'))`로 특정 div를 찾는다. Storybook 데코레이터가 `<div id="wrapper">`를 안 두면 portal target이 `null` → 렌더는 시도되지만 DOM에 안 박힘 → 보이지 않음 + 에러 없음.

해법: 데코레이터의 wrapper div에 `id="wrapper"`를 명시. 앱이 `max-width: 440 / 100svh`로 모바일 webview를 가정하는 점도 함께 재현.

```tsx
<div id='wrapper' style={{ maxWidth: 440, minHeight: '100svh', margin: '0 auto', position: 'relative' }}>
  <GlobalModal />
  <QueryWithErrorHandlers>{children}</QueryWithErrorHandlers>
</div>
```

**원칙**: portal-기반 컴포넌트는 *target DOM의 존재*가 계약의 일부. 데코레이터에서 동일 id의 div를 두지 않으면 silent fail로 디버깅 비용이 크다.

---

## 함정 4 — 에러 모달 variant는 정적 마운트로 안 뜬다

직관: "MSW 핸들러로 401 박아두면 마운트 직후 모달이 떠야지."

문제: 로그인 페이지는 마운트 시점에 mutation을 부르지 않는다. 사용자가 폼 제출해야 trigger. 정적 variant로 두면 *상태가 발생하지 않음* → 핸들러는 등록됐지만 호출 안 됨.

해법: Storybook v8 `play` 함수로 사용자 행동까지 자동화.

```tsx
export const 로그인_실패: Story = {
  parameters: {
    auth: { token: '' },
    msw: { handlers: [loginUnauthorizedHandler()] },
  },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    await userEvent.type(canvas.getByPlaceholderText('아이디를 입력해 주세요.'), 'wronguser');
    await userEvent.type(canvas.getByPlaceholderText('비밀번호를 입력해 주세요.'), 'wrongpass');
    await userEvent.click(canvas.getByRole('button', { name: '로그인하기' }));
  },
};
```

**원칙**: variant가 "어떤 데이터가 와서 보이는 화면"인지 *어떤 사용자 행동 후의 화면*인지 구분하라. 후자는 play 함수로 행동까지 박아야 prototype 가치를 만든다 — 기획·QA가 "이 동작 후 이 모달이 뜬다"를 코드 없이 확인 가능.

---

## 회수 가능한 교훈

1. **앱 트리를 통째 재사용하려는 충동을 버려라.** 데코레이터는 *재구성*이지 재사용이 아니다.
2. **무한 hang은 거의 항상 unstable reference의 누적이다.** Storybook 무한 로딩이 보이면 `useMemo`/`useEffect` deps의 객체 reference부터 의심.
3. **portal/wrapper 같은 DOM 계약은 데코레이터에 박제.** `<div id="...">` 같은 구조 의존은 한 번 빼먹으면 시간을 잡아먹는다.
4. **variant는 "보이는 데이터"가 아니라 "보이는 화면 만드는 행동 시퀀스"로 정의.** play 함수가 선택지에 있어야 모달·에러 흐름을 prototype 가치로 잡는다.

---

## 다음에 써먹을 곳

- 다른 페이지 양산 시 같은 4함정 발생 가능성 100%. 데코레이터 자체는 글로벌이라 한 번 정비해두면 재발 안 함.
- 다른 앱(에 동일한 onError-기반 모달 패턴이 있다면) Storybook 도입 시 1번부터 그대로 적용.
- `mutationCache.onError`처럼 Provider 레벨에서 cross-cutting하게 동작하는 것들 일반: 토스트/로깅/RUM/feature flag stale 처리 — 데코레이터 라이트 이식 패턴의 후보들.
