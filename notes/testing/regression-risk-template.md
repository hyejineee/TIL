---
title: 회귀 방지 테스트 환경 구축 1단계 — CLAUDE.md "회귀 위험 포인트" 템플릿 설계
date: 2026-04-24
category: testing
tags: [testing, claude-md, regression, template-design, llm-workflow, characterization-test, fsd]
summary: mkt24 회귀 방지 테스트의 사전 작업으로 기능 단위 CLAUDE.md를 쓰면서 "회귀 위험 포인트" 섹션 포맷을 불릿 → 카테고리 → 표 → 구조화 리스트로 4번 갈아엎었다. LLM이 실제로 뭘 할 수 있는지를 물어보니 표보다 구조화 리스트가 이긴다.
---

# 회귀 방지 테스트 환경 구축 1단계 — "회귀 위험 포인트" 템플릿 설계

## 배경

mkt24(리뉴얼 앱) 회귀 방지 테스트 환경을 구축하려다 막막해서 멘탈 모델을 정리. 전날 TIL `2026-04-24-senior-test-code-patterns.md`에서 배운 선배의 접근:

> 기획서 없는 레거시 → **characterization test**로 현 동작 박제 → 기획 역산 → 안전한 리팩터

이걸 따르려면 **"어디부터 뭘 테스트할지"** 먼저 정리되어야 한다. 코드만 읽어서는 "왜 이렇게 돼 있는지"가 안 보여서 LLM도 엉뚱한 테스트를 쓴다. 그래서 **기능 단위 CLAUDE.md를 먼저** 쓰기로 했다 (선배 패턴 §7 "CLAUDE.md가 테스트의 맥락을 설명한다"의 선행 작업).

## 결정: 스코프와 섹션 구조

### 스코프

FSD 레이어 중:
- `_entities/<domain>/CLAUDE.md` — 도메인 개념
- `_features/<domain>/<sub-feature>/CLAUDE.md` — 사용자 대면 기능 (2단 구조)
- `_modules/<page>/CLAUDE.md` — 통합 테스트가 자연스럽게 사는 곳

**파일럿: `charge` 도메인** (결제/충전 — 결제수단 9종, 계좌이체 3시간 윈도우, 폴링, VIP, 영수증까지 있어서 복잡성 적당히 있는데 범위 제한됨).

제외: 폴더 구조, import 경로, 네이밍 컨벤션, 코드 스타일 — **코드 읽으면 알 수 있는 것**은 문서화 안 함.

### 섹션 구조

1. 한 줄로 뭐 하는 기능인가
2. 누가 언제 쓰나
3. 핵심 비즈니스 룰
4. 외부 의존
5. **회귀 위험 포인트** ← 이게 이 세션의 진짜 수확

엔티티에는 추가로 "두 개의 축" (카드계열 vs 계좌이체 비동기) 같은 구조 단면을 넣음. 도메인 전체 분기를 결정하는 근본 구조가 보여야 하위 레이어가 이해됨.

## 회귀 위험 포인트 섹션의 4번 갈아엎기

### v1: 느슨한 불릿 리스트

"리팩터 시 조심할 것" 정도의 느슨한 기준으로 써서 문자열 계약, 상수 파급, 상태 잔존, 외부 스크립트 의존, 운영 리스크가 한 섹션에 뒤섞임. LLM이 읽어도 **"뭐 하려는 건지"** 감이 없음.

### v2: 6 카테고리 + 구조화된 설명

기준을 3줄로 명문화:
> ① 코드 미수정 시에도 깨질 수 있음
> ② 수정 시 숨은 결합으로 함께 깨짐
> ③ 코드는 정상이나 운영/배포 상 병목

카테고리 6종:

| 라벨 | 뜻 | 예시 |
| --- | --- | --- |
| `외부 계약` | 백엔드·SDK·ENV 기대 값 이탈로 깨짐 | `chargeStatus === 'unknown'` 문자열 계약 |
| `내부 파급` | 한 곳 변경이 숨은 여러 곳을 깨트림 | `CHARGE_THRESHOLD` 3곳 동시 참조 |
| `상태 잔존` | 언마운트 후에도 남아 다음 사용에 영향 | zustand 메모리 스토어, localStorage 플래그 |
| `타이밍/순서` | 이벤트 순서·effect deps·cleanup 의존 | `watch` subscription, unmount cleanup |
| `가드 유지` | 지금 방어되지만 가드가 풀리면 취약 | capture 핸들러, stable ref 패턴 |
| `운영/배포` | 코드 정상이나 운영/배포 대응이 어려움 | 하드코딩 계좌, ENV 오설정 |

이 카테고리 체계는 이후 내내 유지됨 — **레거시 코드베이스 전반에 재사용 가능한 프레임**.

### v3: 9열 표 + 메타데이터

"스캔하기 좋게" 요청으로 표 전환. 컬럼: `# / 카테고리 / 포인트 / 원인 → 증상 / 탐지/대응 / 소스 / Evidence / 신뢰도 / 검증일`.

여기서 추가된 메타 3종이 진짜 값어치 있었다:

- **신뢰도**: `추측`(코드 유추) / `관찰`(본 적 있음) / `재현`(테스트로 확인)
- **Evidence**: 관련 테스트 파일 path 또는 인시던트 PR. 미작성 `—`
- **검증일**: YYYY-MM-DD — "이 CLAUDE.md는 언제 기준인가" 답 가능해짐 (stale 방지)

### v4 (최종): 구조화 리스트

"LLM이 읽을 때 표 vs 텍스트 뭐가 좋아?" 질문에서 시작. 비교:

| | 표 | 불릿 | 구조화 리스트 |
| --- | --- | --- | --- |
| 필터/스캔 | 🟢 | 🔴 | 🟡 |
| 누락 필드 가시성 | 🟢 | 🔴 | 🟢 |
| 한 항목 깊이 읽기 | 🔴 (셀 분리) | 🟡 | 🟢 |
| 인과 관계 추론 | 🔴 | 🟢 | 🟢 |
| 긴 path/다수 소비처 | 🔴 (셀 넘침) | 🟢 | 🟢 |

**결정 기준**: 주 사용처가 "LLM이 한 항목 읽고 test case 하나 생성"이면 **구조화 리스트 > 표**. 표는 사람이 훑어볼 때 좋지 LLM이 한 항목 깊이 읽는 덴 셀 분리가 방해.

최종 포맷:

```markdown
### 1. `[외부 계약]` `isPendingCharge`의 'unknown' 문자열 계약

- **원인 → 증상**: 백엔드가 'unknown' 외 값을 내려보내면 predicate false → 대기 UI 소멸 → 사용자 중복 이체.
- **탐지/대응**: predicate 레벨 경계값 테스트 (`'pending'`, `null` 등). 서버 enum 변경 시 리뷰 경로 포함.
- **소스**: `lib/charge.utils.ts:4-7`
- **Evidence**: —
- **신뢰도**: 추측 · **검증일**: 2026-04-24
```

## 배운 것

### 1. 템플릿은 사용처를 정의하고 나서야 결정된다

처음엔 "깔끔한 템플릿"을 추상적으로 만들려 했다. 실제로는 **"LLM이 이 섹션을 읽고 뭘 할 건가"** 를 먼저 정의해야 포맷이 자연스럽게 결정된다.

5가지 사용처 후보를 나열해보고:
1. 테스트 시나리오 생성 — 한 항목 → 한 test case
2. 변경 영향 범위 추정 — 소스 파일 수정 PR에서 체크
3. 버그 진단 가설 목록 — 증상 매칭
4. PR 리뷰 포커스 — 카테고리별 체크리스트
5. 신규 기능 추가 시 불변식 상기

(1)이 1순위면 한 항목이 self-contained 블록이어야 함 → 구조화 리스트 승.
(2)(3)이 1순위면 표 승.

**용도를 먼저 정하지 않은 "예쁜 포맷"은 허구다.**

### 2. 메타데이터 필드가 **문서의 생명주기**를 만든다

`신뢰도` + `검증일` + `Evidence` 3필드가 단순 장식이 아니라 **승격 레일**이다:

- `추측` → 내가 코드 읽고 상상한 것
- `관찰` → 실제 코드/히스토리에서 본 것
- `재현` → 테스트로 커버됨 (`Evidence`에 test path 있음)

시간이 지나며 `추측`이 `관찰`로, 테스트 추가되면 `재현`으로 승격. `Evidence`에 test path 채워지는 게 졸업 조건. 이런 승격 룰이 있어야 CLAUDE.md가 **살아있는 문서**가 된다. 없으면 그냥 stale된 vibe-doc.

### 3. 테스트 가능성은 층별로 달라서 "리팩터 후 시작" 함정

charge 도메인 47개 회귀 포인트의 테스트 가능성 분석:

| 층 | 특성 | 커버 | 리팩터 |
| --- | --- | --- | --- |
| 1. 순수 함수 | `isPendingCharge`, `calculateChargeBenefit`, `buildChargeHistoryRequest`, zod 스키마, `getBootPayErrorMessage` 등 | ~10 | 불필요 |
| 2. 얕은 통합 | zustand store, RHF hook, TanStack mutation (wrapper 필요) | ~20 | 불필요 (경계 mock로 충분) |
| 3. 오케스트레이션 | `useChargeProcess`, `useBootpay`, `ChargeContent` — 의존성 폭발 | ~17 | 필요 |

**테스트가 리팩터의 전제**지 그 반대가 아님. 층 1-2로 60% 커버가 안전망이 된 뒤 층 3 리팩터가 안전. "테스트 하기 힘드니까 리팩터부터"는 선배 TIL §9가 경고한 루프(리팩터 중 동작 변경되어도 원인 못 찾음).

### 4. "LLM이 뭘 할 수 있어?" 한 질문이 최대 개선을 만들었다

**"이 섹션 보고 LLM이 어떤 판단 할 수 있어?"** 한 문장이 템플릿을 가장 크게 개선시킨 질문. 추상적 "깔끔함" 대신 구체 action을 나열하게 만든다. 이 질문 자체를 다른 문서 설계에도 습관화할 것.

### 5. 선배 패턴을 이 컨텍스트에 적용하면

| 선배 패턴 | 이 세션에서 적용 |
| --- | --- |
| §1 한글 describe/it | 각 항목 `원인 → 증상` 문장이 그대로 it 문장으로 매핑됨 |
| §2 unit + integration 쌍 | 층 1(unit) + 층 2·3(integration) 구분 |
| §3 경계만 mock | 층 2 setup 전략 = 경계 식별 전략 |
| §4 factory/fixture | 영수증 타입별 factory (`createReceiptByType`) 이미 존재 — fixture 승격만 |
| §7 CLAUDE.md로 맥락 | 이번 작업이 그것 자체 |
| §9 characterization → 역산 | 층 1-2 테스트로 현 동작 박제 → 기획 역산 흐름 |

## 산출물

브랜치 `mkt24-pc` worktree (`/Users/hyejin/Desktop/smiledragon/.worktrees/mkt24-pc-hotfix`)에 작성된 7개 CLAUDE.md:

- `apps/mkt24/src/_entities/charge/CLAUDE.md` — 10 risk points
- `apps/mkt24/src/_features/charge/charge-form/CLAUDE.md` — 7
- `apps/mkt24/src/_features/charge/charge-cancel/CLAUDE.md` — 5
- `apps/mkt24/src/_features/charge/charge-history/CLAUDE.md` — 7
- `apps/mkt24/src/_features/charge/payment-flow/CLAUDE.md` — 7
- `apps/mkt24/src/_modules/charge/CLAUDE.md` — 8
- `apps/mkt24/src/_modules/charge-history/CLAUDE.md` — 3

**주의**: 현재 `mkt24-pc-hotfix` 브랜치라 이 파일들은 아직 커밋 안 됨. 테스트 환경 구축은 별도 브랜치에서 진행 예정.

## 다음에 써먹을 곳 / 남은 작업

### 다음 세션 (별도 브랜치)에서 이어갈 순서

1. **Vitest 러너 세팅 확인** — mkt24에 있는지 check. 없으면 선배의 `2aaec1164` 커밋 패턴으로 세팅.
2. **Phase 1: 층 1 순수 함수 테스트** — `isPendingCharge` 5분짜리로 시작. 템플릿 + 러너 동시 검증.
3. **수확**: 테스트 path를 해당 회귀 포인트의 `Evidence`에 채우고 `신뢰도: 추측` → `재현`으로 승격.
4. **Phase 2**: zustand store, RHF hook, TanStack mutation의 얕은 통합 테스트.
5. **Phase 3 (나중)**: 오케스트레이션 레이어 — 여기서 리팩터 논의 진입.

### 다른 도메인 확장

`orders`, `product`, `auth`, `my`, `proposal` 등 남은 30+ 파일을 같은 템플릿으로 작성. charge에서 검증된 구조라 복사-템플릿 유효. 단, 도메인마다 "두 개의 축" 같은 구조 단면은 새로 파악 필요 (차지는 동기/비동기 이원화, 주문은 일반/자동·정기가 축일 것).

### 재사용 가능성

카테고리 6종 + 메타필드 3종은 **domain-agnostic**. 다른 프로젝트의 레거시 문서화에도 그대로 적용 가능. 특히 "기획서 없는 상태에서 회귀 방지"가 필요한 어떤 상황에든.

## 남은 질문

- `신뢰도` 승격 프로세스를 **누가 언제** 관리? PR 리뷰어 책임? 테스트 작성자 책임? 팀 컨벤션 필요.
- CLAUDE.md + 회귀 위험 포인트가 한 도메인에 30-50개씩 쌓이면 LLM context window 부담. 레이어별 분리 또는 요약 index 필요해질 수 있음.
- `Evidence` 컬럼이 **실제로 채워지는가**는 Phase 1 테스트 작성 시점에 검증 — 귀찮아서 안 채우면 죽은 필드.

## 참고

- 이전 TIL: `2026-04-24-senior-test-code-patterns.md` — 선배의 Vitest + characterization test 패턴 (이 세션의 출발점)
- 브랜치: `mkt24-pc` (hotfix worktree에서 작업) — 실제 테스트 작성은 다른 브랜치로 분리 예정
- 다음 세션 재개 시 이 TIL + 브랜치의 7개 CLAUDE.md 로드하면 즉시 Phase 1 착수 가능
