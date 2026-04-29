---
title: Mock Service GUI — MSW를 UI로 제어하는 도구 설계
date: 2026-04-24
category: tooling
tags: [msw, devtools, testing, architecture, typescript]
summary: MSW를 브라우저 UI에서 제어하는 devtools 패키지 설계. preset/profile/override 3단 합성 구조, 체이닝 제네릭으로 preset label 타입 안전, dev-only 3중 안전장치, 동적 응답 함수, resetHandlers+use 런타임 교체 패턴.
---

## 배경

우아한형제들 코어웹프론트개발팀의 [Mock Service GUI 블로그](https://techblog.woowahan.com/20154/)를 참고해서 `packages/msw-gui` 패키지를 설계하는 중. 읽고 직접 구현해보며 정리한 내용.

## 배운 것

### 1. "코드 수정 없이 mock을 바꾼다"는 목표가 설계를 다 결정한다

MSW만 쓰면 mock 데이터 바꾸려고 핸들러 파일을 계속 고쳐야 한다. 이게 쌓이면:
- 팀원끼리 `handlers/*.ts` 파일에서 머지 충돌
- 새로 온 사람은 "어떤 시나리오를 어떻게 켜야 하는지" 모름
- 테스트 코드와 브라우저 mock이 따로 놀아서 관리 포인트가 2개로 쪼개짐

"브라우저 UI에서 mock을 토글한다"를 요구사항으로 못 박으니까 **핸들러를 런타임에 교체할 수 있어야 한다**, **상태를 로컬스토리지에 저장해야 한다**, **preset/profile 개념이 필요하다** 같은 설계가 자연스럽게 따라나온다.

### 2. Preset / Profile / Override — 3단 추상화

블로그가 말하는 핵심 구조. 내 구현에서도 그대로 차용했다.

- **Preset**: 핸들러 1개당 응답 케이스를 N개 등록 (`label`로 구분). 성공/빈 결과/에러/페이지네이션 등.
- **Profile**: "장바구니 빈 상태 + 결제 500 에러" 처럼 여러 핸들러의 preset을 묶은 시나리오.
- **Override**: preset 응답의 일부만 즉석에서 수정 (예: `amount만 3990으로`). mock을 새로 만들지 않아도 엣지 케이스 확인 가능.

이 3개를 레이어로 겹치면 최종 응답이 나온다. `resolveState.ts`에서 실제로 이 순서대로 합성한다:

```
initial preset → profile presets → handler override → json override
```

각 단계가 `resolution.trace`에 `{ kind, preset, profileKey? }` 로 기록돼서, 위젯에서 "왜 이 응답이 나왔는지" 역추적할 수 있다. 이게 디버깅할 때 중요할 듯.

### 3. 타입으로 개발자 경험을 잡는다

블로그가 강조한 부분 — preset label을 문자열로 쓰면 오타가 나도 런타임에서야 터진다. 해결책은 체이닝한 label을 제네릭에 누적:

```typescript
interface PresetBuilder<TLabels extends string = never> extends HttpHandler {
  preset<L extends string>(config: PresetConfig & { label: L }): PresetBuilder<TLabels | L>;
  readonly __mswGuiMeta: PresetMeta & { labels: TLabels };
}

// 사용처에서
definePresets(http.get('/api/x', ...))
  .preset({ label: '성공', status: 200, ... })     // PresetBuilder<'성공'>
  .preset({ label: '빈 결과', status: 200, ... })  // PresetBuilder<'성공' | '빈 결과'>

// ProfileConfig는 이걸 꺼내 쓴다
type PresetLabelOf<H> = H extends { __mswGuiMeta: { labels: infer L extends string } } ? L : never;

interface ProfileConfig<H extends HandlerMap> {
  presets: Partial<{ [K in keyof H]: PresetLabelOf<H[K]> | typeof BYPASS_LABEL }>;
}
```

profile 정의할 때 `presets: { 'GET /api/x': '오타난라벨' }` 치면 IDE가 바로 빨간줄. 블로그에 나온 `Extract` 유틸리티 타입 쓰는 아이디어와 같은 접근.

### 4. Dev-only 패키지의 안전장치

실수로 프로덕션에서 mock이 켜지면 재앙이니까 여러 겹으로 막는다:

- `NEXT_PUBLIC_MSW_GUI_ENABLED === 'true'` (정확 문자열 비교)
- `window.location.hostname`을 `allowedHosts` 화이트리스트에 매칭 (`localhost`, `127.0.0.1`, `*.dev.smiledragon.co.kr`)
- 둘 중 하나 실패하면 `createUnboundWidget()` 반환해서 worker 시작 자체를 안 한다
- 활성화되면 콘솔에 `[msw-gui] MSW GUI ACTIVE` 경고 찍기

결론: "기본은 꺼짐, 켜려면 의도적으로 두 번 통과해야 한다."

### 5. 동적 응답 preset (정적 JSON의 한계 넘기)

정적 JSON mock은 페이지네이션/필터에서 막힌다. `PresetConfig.response`를 `T | PresetResponseFn<T>` 유니온으로 두고, 함수면 요청마다 실행:

```typescript
.preset({
  label: '페이지네이션 가능',
  status: 200,
  response: ({ request, params, cookies }) => {
    const url = new URL(request.url);
    const page = Number(url.searchParams.get('page') ?? '1');
    return sliceAndFilter(data, page);
  },
})
```

**트레이드오프**: JSON 편집 override를 적용하면 함수가 정적 값으로 치환된다. `types.ts` 주석에 명시해뒀다 — "필터링 동작을 유지하려면 override를 해제해야 한다." 이런 암묵적 조건은 문서화하지 않으면 사용자가 모른다.

### 6. 핸들러 교체는 `resetHandlers + use` 패턴

MSW는 `worker.resetHandlers()` → `worker.use(...newHandlers)` 로 런타임에 핸들러를 교체할 수 있다. 내부에서는 매번 전체 resolution을 다시 계산해서 통째로 교체하는 방식:

```typescript
export const applyResolutionsToSink = (sink, handlers, resolutions) => {
  const dynamicHandlers = resolutions.map(buildPresetHandler).filter(Boolean);
  sink.resetHandlers();
  if (dynamicHandlers.length > 0) sink.use(...dynamicHandlers);
};
```

`MswHandlerSink` 인터페이스로 `{ resetHandlers, use }` 만 뽑아내면 테스트에서 `setupServer()` 든 `setupWorker()` 든 동일한 로직 재사용 가능. 이게 "브라우저-테스트 일관성"의 기반.

## 왜 중요한가

mock은 개발/테스트 "주변 도구"로 취급되기 쉬운데, 실제로는 팀이 매일 만지는 인터페이스다. 여기에 UX를 잘 설계해두면:

- **온보딩이 빨라짐** — "이 시나리오 보려면 `?__msw=empty-state` 붙이세요" 한 줄로 끝
- **QA 재현성↑** — 프로필을 공유해서 "이 상태로 재현" 말이 통함
- **테스트 코드와 브라우저 상태가 같은 preset 시스템을 공유** → 관리 포인트 1개

"도구를 UI로 감싼다"가 아니라 "**기존 패러다임을 데이터로 만들어서 여러 인터페이스가 공유하게 한다**"가 본질.

## 다음에 써먹을 곳

- 타 devtools/내부 도구 만들 때 **preset → profile → override** 3단 구조 템플릿으로 쓸 만함. 피처 플래그 GUI, 디자인 시스템 테마 스위처 등에도 동일하게 적용 가능.
- 체이닝 API에서 누적 제네릭 패턴 (`PresetBuilder<TLabels | L>`) — builder 패턴에 타입 안전성 붙이고 싶을 때 쓰는 기본기.
- dev-only 패키지 안전장치: 환경변수 정확 비교 + hostname 화이트리스트 + 콘솔 경고. 3중 방어.
- resolution trace — 왜 이 상태가 됐는지 역추적 가능한 로그를 데이터로 남기는 패턴. 복잡한 상태 합성 로직에 유용.
