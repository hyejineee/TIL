---
title: 시니어의 테스트 코드에서 배운 것 — Vitest로 인프라 레이어 단단하게 테스트하기
date: 2026-04-24
category: testing
tags: [testing, vitest, tdd, code-review, frontend, subagent, workflow, mocking, characterization-test]
summary: 시니어의 SD-2877-tdd 브랜치에서 배운 9가지 패턴 — 한글 describe/it, unit+integration 쌍 운영, 경계 mock, factory/fixture, private 메서드 타입 안전 접근, stateful fake vs spy, colocation, CLAUDE.md로 테스트 맥락 전달, Opus/Sonnet 분업.
---

# 시니어의 테스트 코드에서 배운 것

SD-2877-tdd 브랜치에서 시니어분이 `shared/network`, `_entities/click/model`, `hooks/api` 등 인프라/도메인 레이어에 테스트 코드를 대량 추가하셨다. 커밋 히스토리(초안 → 리뷰 반영 → CI fix → 리포트 세팅)까지 남아있어서 **결과뿐 아니라 과정도** 볼 수 있었다. 배운 점을 정리.

## 1. 한글 `describe` / `it` 문장이 "스펙"이 된다

```ts
describe('handleRefreshExpireError', () => {
  it('refresh endpoint가 unauthorized를 반환하면 STOP을 반환한다', ...);
  it('다른 에러에 대해서는 ONGOING을 반환한다 — retry를 허용한다', ...);
});
```

테스트 이름만 읽어도 **동작 명세**가 된다. 한 커밋은 일부러 영어 description을 전부 한국어로 바꾸기도 했다. "테스트 코드는 코드가 아니라 문서"라는 관점을 못 박은 셈.

**써먹을 곳**: 새 테스트 쓸 때부터 한글 문장으로 시작. "무엇을 검증하는가"가 아니라 "어떤 조건에서 어떤 결과인가"로 쓰기.

## 2. 같은 모듈을 Unit + Integration 두 파일로 쌍 운영

`authInterceptor.test.ts`와 `authInterceptor.integration.test.ts`가 나란히 있다.

| | Unit | Integration |
| --- | --- | --- |
| mock 수준 | `httpClient`, `refreshTokenWithRetry` 전부 spy | `axios` default export까지 mock하되 **재시도 호출을 실제로 spy** |
| token repo | `vi.fn().mockResolvedValue(token)` 고정 | `let current = initial; getToken = () => current` — 상태 추적 |
| `navigator.locks` | 껍데기만 shim | `(_, cb) => cb({})` — 콜백을 즉시 실행해서 락 플로우 통과 |
| 검증 대상 | 메서드 단위 로직 (throw 여부, 반환값) | 401 → refresh → 재시도 전체 플로우 |

**왜 중요**: "unit이냐 integration이냐" 이분법이 아니라 **같은 대상을 두 층위로 본다**. Unit은 조건 분기, Integration은 플로우.

**써먹을 곳**: 복잡한 인프라(인증, 결제, 동기화)는 두 파일로 나눠서 쓰기.

## 3. 경계만 `vi.mock`, 내부는 실제 코드로

```ts
vi.mock('./httpClient', () => ({
  tokenRepository: { getToken: vi.fn(), saveToken: vi.fn(), removeToken: vi.fn() },
}));

vi.mock('Shared/utils', () => ({
  getUserUuidFromToken: vi.fn().mockReturnValue({ userUuid: 'user-1' }),
}));
```

테스트 대상이 의존하는 **모듈 경계만** mock한다. `AuthInterceptor` 클래스 내부 메서드는 진짜로 실행됨. `__mocks__/zustand.ts` 자동 mock도 "zustand라는 경계"에서 끊는 같은 발상.

**반례**: 클래스 내부 메서드를 전부 stub하면 테스트는 초록색이어도 리팩터링에 아무 가치가 없다. **"깨져야 할 때 깨지는가"** 가 좋은 테스트의 기준.

**써먹을 곳**: 새 테스트 쓸 때 "mock할 경계를 하나 정하고 나머지는 실제 코드"를 기본 룰로.

## 4. Factory 함수 + fixture 디렉토리

```ts
const makeAxiosError = ({ status = 401, url = '/v2/some/endpoint', code, authHeader } = {}) => { ... };
const makeTokenRepo = (token = 'current-token') => ({ ... });
```

그리고 `_entities/click/model/logic/__test-helpers__/fixtures.ts` 처럼 **공용 fixture 디렉토리**도 있다.

- 테스트마다 필요한 필드만 override → 케이스 의도가 명확.
- 여러 파일에서 같은 fixture가 필요해지면 `__test-helpers__/`로 승격.

**써먹을 곳**: `new ErrorXyz('msg', ...)`를 반복해서 쓰고 있으면 factory로 뽑을 신호.

## 5. Private 메서드 테스트 — 타입 안전하게

```ts
type InterceptorInternal = {
  isAvailableRefreshRequest(): boolean;
  handleNonAuthError(e: unknown, r: unknown): void;
  failedQueue: Array<{ resolve: (t: string) => void; reject: (e: unknown) => void }>;
};

(interceptor as never as InterceptorInternal).handleNonAuthError(err, repo);
```

`@ts-ignore` 대신 **테스트 파일 안에서만 쓰는 타입 alias**를 정의해서 private에 접근. 프로덕션 코드는 안 건드리면서 내부를 검증할 수 있다.

**써먹을 곳**: "이 private 메서드 테스트하고 싶은데 public으로 바꿀까?" 고민될 때 이 패턴.

## 6. Stateful fake vs Spy mock — 의도에 따라 골라쓰기

- **Spy mock** (`vi.fn().mockResolvedValue(token)`): "호출됐는지, 몇 번 불렀는지"가 궁금할 때.
- **Stateful fake** (`let current = initial; getToken = () => current; saveToken = (t) => { current = t }`): "저장 → 조회의 순서가 맞는지"가 궁금할 때.

같은 `tokenRepository`여도 unit 테스트는 spy, integration 테스트는 fake로 썼다. 테스트가 뭘 검증하려는지에 따라 다름.

## 7. CLAUDE.md가 테스트의 맥락을 설명한다

각 폴더(`shared/`, `viewing/`, `myviewup/`, `hooks/`, `notice/`, `ranking/`, `stamp/`)에 CLAUDE.md가 추가됐다. 내용:

- 레이어 경계 (infrastructure / domain / feature)
- Import 방향 (`_modules → _features → _entities → shared`, 반대 금지)
- Pitfalls (server util을 client에서 import하면 안 되는 이유, interceptor 순서 의미, Proxy 디버깅 혼란 등)

**왜 중요**: AI agent를 위한 문서지만 **신입에게도 최고의 온보딩 자료**. 테스트 코드에서 "왜 이걸 mock했는지" 궁금할 때 CLAUDE.md를 보면 답이 있다. 테스트와 문서가 한 세트.

### 추가: CLAUDE.md는 **context**, rules는 **convention**

시니어분이 설명해주신 역할 분리:

| | CLAUDE.md | rules (`.cursor/rules`, `.claude/rules` 등) |
| --- | --- | --- |
| 담는 것 | **맥락(context)** — 이 레이어가 왜 존재하는가, import 방향이 왜 이 방향인가, 어떤 pitfall이 있는가 | **코드 컨벤션** — 네이밍, 파일 구조 규칙, 스타일, 패턴 |
| 수명 | 도메인/아키텍처 의도 — 잘 안 바뀜 | 팀 합의에 따라 진화 |
| 독자 | LLM + 사람 둘 다 "왜"를 이해해야 할 때 | 코드 작성 시 "어떻게"를 체크할 때 |

즉 "이 모듈은 이런 역할이고 이런 이유로 이렇게 생겼다"는 CLAUDE.md, "함수 이름은 이렇게 짓는다 / 이런 패턴으로 테스트를 쓴다"는 rules.

**또 하나**: **CLAUDE.md에 폴더 구조를 넣을 필요는 없다.** 폴더 구조는 `ls` / 파일 트리로 언제든 관찰 가능한 정보라 문서로 박아두면 금방 stale 된다. CLAUDE.md에는 **구조에서 드러나지 않는 의도**만 쓰는 게 맞다 (예: "왜 `_entities`가 `_features`보다 아래 레이어인가").

**또 하나 더 — 에이전트 목록도 CLAUDE.md에 넣는다**: Claude가 자유롭게 알맞은 subagent를 고를 수 있도록 **사용 가능한 에이전트 목록과 각 에이전트의 역할/트리거 조건**을 CLAUDE.md에 명시한다. 에이전트 이름만 나열하는 게 아니라 "이 상황이면 이 에이전트" 수준까지 적어둬야 Claude가 판단을 위임받을 수 있다. 시니어분이 하시는 "Opus는 맥락, Sonnet은 실행" 분업(섹션 9 참고)도 결국 이 목록이 정확히 써 있어야 동작한다. 내가 subagent 한 번 실패한 이유 중 하나가 이 목록이 없어서 Claude가 어디에 뭘 위임할지 모르는 상태였던 듯.

**써먹을 곳**: 내가 CLAUDE.md 쓸 때 컨벤션 규칙이나 폴더 트리를 복붙하고 있으면 그건 rules/README로 옮겨야 하는 신호. 반대로 "이 레포에서 subagent로 뭘 시킬 수 있는지"가 빠져 있으면 그건 CLAUDE.md에 채워 넣어야 할 신호.

## 8. 테스트 코드도 리뷰받는다

커밋 히스토리:

```
6303bc75d chore: TDD 방식의 agent based 개발을 위한 test code 작성
1592d8531 test: 리뷰 피드백 반영 — 회귀 방지 효과 강화
193e0673d test: CI 빌드 에러 수정 + 테스트 description 한국어화
2aaec1164 test: vitest 리포트(HTML/JUnit) 및 coverage 출력 설정 추가
```

초안 → 리뷰 반영(회귀 방지 강화) → CI fix + 가독성 → 리포트/커버리지 순. **"테스트 코드는 쓰고 끝"이 아니라 "테스트 코드도 리뷰 대상"**이라는 것.

## 9. 큰 그림 — 시니어의 의도와 다음 단계

패턴 이상으로 흥미로웠던 건 **왜 지금 이걸 하고 계신가**. 시니어분 설명을 정리.

### 왜 테스트를? — 커버리지 80%로 "리버스 엔지니어링"

뷰업은 **기획 문서가 없다**. 코드가 사실상 유일한 사양. 이 상태에서 리팩터링/기능 추가를 하면 의도치 않은 동작 변경이 터지기 쉽다.

접근:
1. 테스트 커버리지 80%+를 먼저 확보 → **현재 코드가 뭘 하는지**를 테스트가 문서화
2. 그 과정에서 발견되는 실제 동작을 기반으로 **기획 문서를 역산**
3. 기획이 정리되고 나서야 리팩터/신규 기능에 안전하게 착수

즉 테스트 코드 자체가 목표가 아니라 **"현 시스템의 행동을 서술하는 실행 가능한 문서"**를 만드는 단계. 이게 TDD라기보단 **characterization test / reverse-engineering test**에 가까움.

**왜 중요**: 레거시 코드베이스에 들어갔을 때 "기획서 없는데 어디부터 손대지?"의 답을 테스트로 푸는 사고. 코드만 읽는 게 아니라 **코드의 동작을 외부에서 관찰 가능한 형태로 뽑아내는** 작업부터.

### 테스트 코드 위치 — 테스트 대상과 최대한 가깝게 (colocation)

```
shared/network/
├── authInterceptor.ts
├── authInterceptor.test.ts              ← 바로 옆
├── authInterceptor.integration.test.ts  ← 여기도 옆
├── errorInterceptor.ts
└── errorInterceptor.test.ts
```

별도 `__tests__/` 최상위 디렉토리가 아니라 **구현 파일 바로 옆**에 둠.

장점:
- 파일 열었을 때 "이 함수는 테스트가 있구나 / 없구나"가 한 번에 보임
- 리팩터링/이동 시 같이 움직이기 쉬움 (경로 변경 한 번에)
- 모듈 단위로 생각하게 됨 — 구현과 계약이 한 폴더 안에 공존

### 모델 분리 — Opus는 맥락, Sonnet은 실행

시니어가 생각하시는 분업:

| | 역할 | 예시 작업 |
| --- | --- | --- |
| **Opus** | 고수준 맥락 파악, 아키텍처 판단, 리뷰 | CLAUDE.md 작성/업데이트, 테스트 전략 수립, 경계 설정 |
| **Sonnet** | 맥락 받아서 반복 실행 | 테스트 케이스 작성, 코드 수정, 기능 구현 |

Opus에 맥락을 누적시키고, 실제 작성은 Sonnet subagent로 위임. 토큰/속도/비용 모두 합리적.

### Subagent / 하네스 엔지니어링 — 아직 내 숙제

시니어는 subagent 기반 워크플로우를 쓰신다고 하셨다. 나도 한 번 시도해봤다가 **완전히 실패**했다. 아마도:

- 맥락 전달이 부족해서 subagent가 엉뚱한 방향으로 감
- 검증 루프가 없어서 잘못된 코드가 그대로 올라옴
- 분업 경계가 애매해서 opus도 sonnet도 뭘 하는지 모름

궁금한 점:
- 하나의 태스크를 subagent에게 어떻게 **자급자족 가능한 프롬프트**로 만드는가
- CLAUDE.md가 이 역할을 하는 건지, 아니면 별도의 하네스(harness) 설정이 더 있는지
- subagent 결과를 어떻게 검증 → 머지하는지 (테스트 통과 강제? 리뷰?)
- 병렬화 포인트는 어디인지 (파일 단위? 기능 단위? 레이어 단위?)

**다음 액션**: 시니어 워크플로우 한 번 참관/페어링 요청 드려보기. 내가 혼자 추측하기보다 실제 루프를 한 번 보는 게 빠를 듯.

## 다음에 써먹을 곳

내 작업(마24 등) 적용 우선순위:

1. **한글 describe/it** — 지금 당장 바꿀 수 있고 효과 큼.
2. **CLAUDE.md 먼저, 테스트는 그 뒤** — 레이어 경계와 pitfall을 문서화 안 하면 테스트도 방향을 못 잡는다.
3. **Mock 경계 룰** — "외부 모듈 경계만 mock, 내부는 실제 코드"를 팀 컨벤션으로.
4. **복잡한 인프라는 unit + integration 쌍** — 인증, 결제 같은 건 두 파일로.

## 시니어에게 아직 물어보고 싶은 것

**테스트 코드 자체**
- Unit / integration 경계를 어떤 기준으로 나눴는지 (navigator.locks 같은 브라우저 API는 무조건 integration?)
- 어떤 파일부터 테스트를 썼는지 (중요도? 버그났던 곳? 커버리지 %?)
- TDD라고 하셨는데 실제로 실패 테스트 먼저 쓰고 구현하는 루프였는지, 기존 코드에 회귀 방지 테스트를 덧붙인 쪽인지 — characterization test에 가까운 것 같은데 실제 의도가 궁금
- "회귀 방지 효과 강화"(1592d8531) 리뷰 피드백은 구체적으로 뭐가 약했던 건지
- HTML/JUnit 리포트를 실제로 어디서 읽는지 (PR 코멘트? CI artifact?)
- 커버리지 게이팅 계획이 있는지 (80% 목표는 합의된 숫자인지, 팀 결정 대기인지)

**워크플로우 / subagent**
- 테스트 80% → 기획 문서 역산 순서에서, 기획 문서는 사람이 쓰는지 아니면 테스트 description을 LLM으로 합성하는지
- Subagent에게 위임할 때 프롬프트를 어떻게 구성하는지 (CLAUDE.md 주입 + 태스크 설명 수준인지, 더 구조화되어 있는지)
- 병렬화 기준 (폴더별? 기능별? 레이어별?) — worktree를 함께 쓰시는지
- Subagent 결과 검증 루프 (테스트 통과 강제? 사람 리뷰? 다른 subagent로 리뷰?)
- 실패하는 subagent 세션을 어떻게 회수/재시작하는지 — 내가 한 번 망한 경험이 있는데 그 루프를 어떻게 짧게 만드는지가 핵심일 것 같음
