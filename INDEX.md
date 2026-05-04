# TIL Knowledge Index

> LLM·RAG가 빠르게 검색·참조하기 위한 주제별 인덱스.
> 새 TIL을 추가할 때 이 파일도 함께 갱신한다.

---

## 저장소 구조

```
TIL/
├── README.md            # 진입점
├── INDEX.md             # 이 파일 — 주제/태그/날짜별 인덱스
├── notes/               # 실제 TIL 본문 (주제별 디렉토리)
│   ├── ai-workflow/
│   ├── android/
│   ├── collaboration/
│   ├── devops/
│   ├── git/
│   ├── javascript/
│   ├── react/
│   ├── testing/
│   ├── tooling/
│   ├── ux/
│   └── weekly-report/
└── _archive/            # RAG 제외 — 개인 일상 로그 등
```

**설계 원칙**
- 1 파일 = 1 주제 (RAG 청크 분할이 깔끔해지도록)
- 모든 파일은 frontmatter 필수 (`title`, `date`, `category`, `tags`, `summary`)
- 파일명은 slug만 사용 (날짜는 frontmatter에)
- `_archive/`는 검색·RAG 대상에서 제외

### Frontmatter 스키마

```yaml
---
title: <한 문장 제목>
date: YYYY-MM-DD       # 최초 작성일
category: <notes/ 하위 디렉토리 이름>
tags: [tag1, tag2, ...]
summary: <1~2문장 요약 — 이 인덱스와 RAG 결과 미리보기에 쓰임>
---
```

---

## 검색 팁 (LLM용)

1. 질문 키워드 → 아래 "By Topic" 또는 "By Tag"에서 매칭되는 항목을 고른다.
2. 매칭된 항목의 경로를 `Read`로 연다.
3. 없으면 `grep -r "<키워드>" /Users/hyejin/Desktop/dev/TIL/notes --include="*.md"` 로 본문 검색.
4. 참고한 파일 경로를 답변에 명시한다 (`notes/<category>/<file>.md` 형식).

---

## By Topic

### 🧠 ai-workflow

- [`notes/ai-workflow/claude-md-routing.md`](./notes/ai-workflow/claude-md-routing.md) — 자동 로드되는 에이전트·스킬도 트리거가 흐릿할 수 있어, `~/.claude/CLAUDE.md`에 라우팅 힌트(우선순위·안티패턴)를 별도 파일로 분리해 import한다. `[claude-code, claude-md, agents, skills, routing, llm-workflow]`
- [`notes/ai-workflow/harness-engineering.md`](./notes/ai-workflow/harness-engineering.md) — 우아한형제들 하네스 엔지니어링. Cursor Rules(glob 기반) + Skills(Node 전처리 스크립트)로 토큰 평균 96.5% 절감. `[ai, harness, cursor, claude-code, workflow, token-optimization]`

### 🤖 android

- [`notes/android/android-dev-summit-2019.md`](./notes/android/android-dev-summit-2019.md) — Android Dev Summit 2019 Extended Seoul — Security 라이브러리(Tink 매핑, minSdk 23), New Android Studio, Theme/Style, LiveData + Coroutines + Flow. `[android, kotlin, coroutines, android-studio, security, theme, livedata]`

### 🤝 collaboration

- [`notes/collaboration/cross-team-deployment-coordination.md`](./notes/collaboration/cross-team-deployment-coordination.md) — 백엔드가 배포 일정을 몰라 cross-team 사고가 날 뻔한 회귀. 진짜 원인은 일정이 머릿속에만 있는 것. Jira 필드 → 머지 PR 템플릿 → 정기 sync 3단계로 "공유 잘하자" 다짐 대신 구조가 강제하게. `[pm-collaboration, process, deployment, cross-team, communication, jira, pr-template]`
- [`notes/collaboration/low-roi-request-pushback.md`](./notes/collaboration/low-roi-request-pushback.md) — "있어도 그만 없어도 그만"인 요구사항을 주관적 거절 대신 "데이터로 정당화해주세요"로 공넘기는 푸시백 패턴. 모바일 플랫폼 탭 overflow 측정 요청 사례. `[communication, pm-collaboration, decision-making, frontend, overflow-detection, react]`
- [`notes/collaboration/prd-adr-management.md`](./notes/collaboration/prd-adr-management.md) — 배포 후 휘발되는 *why* 히스토리를 잡는 4가지 장치 — 단일 인덱스, 코드→문서 역방향 링크, ADR을 코드 가까이, PR 템플릿 강제. `[prd, adr, decision-record, documentation, figma, pm-collaboration, communication, code-connect]`
- [`notes/collaboration/prd-adr-rollout.md`](./notes/collaboration/prd-adr-rollout.md) — 4가지 장치를 한 번에 다 깔면 정착 실패. PR 템플릿→메타 ADR→역방향 링크→인덱스 순으로. 1주차에 단 3개 파일만 만들어 모멘텀 잡고, 인덱스는 ADR 5+개 쌓인 뒤. `[adr, prd, rollout, decision-record, pr-template, pm-collaboration, communication]`
- [`notes/collaboration/qa-cycle-process-roadmap.md`](./notes/collaboration/qa-cycle-process-roadmap.md) — 폴리싱 푸시백·회귀 방어를 매번 코멘트로 협상하지 말고 PR 템플릿·회귀 로그·변경 요청 템플릿·QA 동결 룰로 영구화하는 5단계 로드맵. 합의 없이 혼자 시작 가능한 3개부터 → 데이터 쌓인 뒤 팀 합의 2개로 확장. `[pm-collaboration, process, qa, pushback, decision-making, rollout, regression, pr-template]`
- [`notes/collaboration/qa-phase-design-change-pushback.md`](./notes/collaboration/qa-phase-design-change-pushback.md) — QA 페이즈에 들어온 UI 변경 요청을 3단계로 분류 — 이슈 타입 정합성, 변경 사유 분리(누락 정정 vs 위치 이동·시각 강조), 폴리싱이면 배포 후 데이터 보고 판단. 푸시백 한 번도 안 하면 "요청=자동 통과"가 학습돼 매 사이클 반복됨. `[pm-collaboration, qa, decision-making, prioritization, ship-first, pushback, process]`
- [`notes/collaboration/ship-before-polish.md`](./notes/collaboration/ship-before-polish.md) — 검색 페이지 디자인 폴리싱에 2일 쓰다 멈춘 회고. 근거 없는 UX 변경은 "개선인지조차 모르는 주관 변경"이라 배포 마일스톤이 남았을 땐 후순위. "배포 → 데이터 → 근거 기반 개선" 순서. `[decision-making, prioritization, ux, ship-first, self-management, time-management]`

### 🔧 devops

- [`notes/devops/docker-intro.md`](./notes/devops/docker-intro.md) — Docker는 환경 표준화 플랫폼. "Debug your app, not your environment." `[docker, container, devops]`
- [`notes/devops/elasticsearch-intro.md`](./notes/devops/elasticsearch-intro.md) — JSON 기반 검색 엔진. Docker로 띄운 뒤 `_doc`/`_search` endpoint에 curl로 쏘는 기본 흐름. `[elasticsearch, docker, search]`
- [`notes/devops/serverless-lambda.md`](./notes/devops/serverless-lambda.md) — 서버리스 프레임워크와 Lambda — 이벤트 트리거로 함수가 실행되는 구조. `[serverless, aws, lambda]`

### 📦 git

- [`notes/git/git-basics.md`](./notes/git/git-basics.md) — `commit --amend`, `push -f`로 PR 덮어쓰기, `fetch + rebase master`. `[git, vcs]`

### 🟨 javascript

- [`notes/javascript/array-methods.md`](./notes/javascript/array-methods.md) — `sort`는 기본 유니코드 정렬(숫자엔 compareFunction 필수), `reduce`는 4개 인자·중간 탈출은 별도 패턴. `[javascript, array]`

### ⚛️ react

- [`notes/react/hooks-useState-useEffect.md`](./notes/react/hooks-useState-useEffect.md) — Hook은 함수형 컴포넌트에 state/생명주기를 붙이는 함수. `useState`/`useEffect`(cleanup·deps 포함) 기본. `[react, hooks, useState, useEffect, frontend]`
- [`notes/react/tutorial-tictactoe.md`](./notes/react/tutorial-tictactoe.md) — 공식 튜토리얼 — Square/Board props 연결, 상태를 상위 컴포넌트에 모으는 lifting state up, Immutability의 3가지 이점. `[react, tutorial, props, state, immutability, frontend]`

### 🧪 testing

- [`notes/testing/intentional-removal-regression.md`](./notes/testing/intentional-removal-regression.md) — "이 동작은 일부러 뺀다"는 결정을 PR 디스크립션에만 적으면 다음 작업자에게 안 보여서 부활한다. 트리거가 "접근성·성능 개선" 같은 좋은 일일수록 위험. 처방은 코드 주석 + characterization test 둘 다. 회귀 사례 로그 누적해 푸시백 근거화. `[regression, characterization-test, testing, frontend, decision-record, code-comments, accessibility, why-preservation]`
- [`notes/testing/jest-setup.md`](./notes/testing/jest-setup.md) — `npm init` 후 `package.json` scripts에 `test`/`watch` 두 줄. 기본 구문은 `test("이름", () => expect(동작).toBe(기대값))`. `[jest, javascript, testing, node]`
- [`notes/testing/monorepo-test-foundation-layout.md`](./notes/testing/monorepo-test-foundation-layout.md) — 테스트 환경을 8개 자산 카테고리(러너/모듈mock/헬퍼/MSW/공통fixture/도메인fixture/CI/컨벤션)로 분해하고 공유 패키지 vs 앱 co-location 배치를 결정. 새 패키지 신설보다 기존 lib에 subpath export로 얹기. `[testing, monorepo, vitest, msw, package-design, test-foundation, llm-workflow, decomposition]`
- [`notes/testing/regression-risk-template.md`](./notes/testing/regression-risk-template.md) — mkt24 회귀 방지용 CLAUDE.md "회귀 위험 포인트" 템플릿을 4번 갈아엎은 기록. 6 카테고리(외부 계약/내부 파급/상태 잔존/타이밍/가드 유지/운영·배포) + 메타필드(신뢰도/Evidence/검증일) + 구조화 리스트. `[testing, claude-md, regression, template-design, llm-workflow, characterization-test, fsd]`
- [`notes/testing/senior-test-code-patterns.md`](./notes/testing/senior-test-code-patterns.md) — 시니어의 SD-2877-tdd 브랜치에서 배운 9가지 패턴 — 한글 describe/it, unit+integration 쌍, 경계 mock, factory/fixture, private 타입 안전 접근, stateful fake vs spy, colocation, CLAUDE.md로 맥락 전달, Opus/Sonnet 분업. `[testing, vitest, tdd, code-review, frontend, subagent, workflow, mocking, characterization-test]`
- [`notes/testing/storybook-decorator-query-error-handlers.md`](./notes/testing/storybook-decorator-query-error-handlers.md) — Storybook 데코레이터로 앱 `mutationCache.onError`를 라이트 이식할 때의 4가지 함정 — Provider 통째 import 금지, `useMemo` deps `[]`로 무한 hang 회피, portal target div, play 함수로 행동까지 박제. `[storybook, react-query, decorator, react, mutation-cache, msw, useMemo, frontend]`
- [`notes/testing/storybook-test-decay.md`](./notes/testing/storybook-test-decay.md) — 디자인 갈아엎힘에 휘발되는 테스트와 살아남는 테스트가 다르다. 인터랙션·상태 동작은 남기고 비주얼/props 가시화는 버려라. *why*는 테스트가 못 담는다. `[storybook, testing, design-decay, what-vs-why, frontend, decision-making]`
- [`notes/testing/storybook-ui-testing-workflow.md`](./notes/testing/storybook-ui-testing-workflow.md) — Storybook 핸드북의 UI 테스팅 워크플로우 정리. `story.args`를 mock·E2E·visual의 공통 fixture로 재사용해 fixture 동기화 비용을 0으로. E2E는 핵심 흐름만, Cypress→Playwright 매핑까지. `[storybook, testing, playwright, cypress, e2e, visual-testing, a11y, frontend]`

### 🧩 tooling

- [`notes/tooling/mock-service-gui.md`](./notes/tooling/mock-service-gui.md) — MSW를 브라우저 UI에서 제어하는 devtools. preset/profile/override 3단 합성, 체이닝 제네릭으로 label 타입 안전, dev-only 3중 안전장치, 런타임 핸들러 교체(`resetHandlers + use`). `[msw, devtools, testing, architecture, typescript]`

### 🎨 ux

UX/디자인 심리학 법칙 30종 (lawsofux.com 기반). UI·컴포넌트 리뷰 시 reference로 활용.

- [`notes/ux/aesthetic-usability-effect.md`](./notes/ux/aesthetic-usability-effect.md) — 사용자는 미적으로 보기 좋은 디자인을 실제로도 더 사용하기 쉽다고 인식한다. 작은 사용성 결함을 가려버려서 테스트에서 문제가 안 잡힐 수 있다. `[ux-laws, perception, aesthetics, usability-testing]`
- [`notes/ux/choice-overload.md`](./notes/ux/choice-overload.md) — 옵션이 너무 많으면 의사결정 자체가 마비된다. Hick's Law의 사촌. 비교 가능한 형태로 제시하고, 추천·필터로 좁혀줘야 한다. `[ux-laws, decision-making, cognitive-load, paradox-of-choice]`
- [`notes/ux/chunking.md`](./notes/ux/chunking.md) — 정보 덩어리를 의미 있는 그룹으로 묶어서, 스캔 가능한 시각 위계로 만든다. 작업 기억 한계(7±2)를 우회하는 가장 기본적인 기법. `[ux-laws, cognitive-load, working-memory, information-architecture]`
- [`notes/ux/cognitive-bias.md`](./notes/ux/cognitive-bias.md) — 사람의 판단은 휴리스틱(인지 지름길)에 의해 체계적으로 왜곡된다. 디자인은 이 편향을 인지하고 방어하거나 활용한다. `[ux-laws, psychology, decision-making, heuristics]`
- [`notes/ux/cognitive-load.md`](./notes/ux/cognitive-load.md) — 인터페이스를 이해·조작하는 데 필요한 정신 자원의 양. intrinsic(목적 정보)과 extraneous(불필요한 노이즈)로 나뉨. 후자를 줄이는 게 디자인의 일. `[ux-laws, cognitive-load, working-memory, information-architecture]`
- [`notes/ux/doherty-threshold.md`](./notes/ux/doherty-threshold.md) — 시스템 응답이 400ms 이내일 때 사용자 생산성·몰입이 급격히 올라간다. 실제 속도가 어렵다면 progress·skeleton·animation으로 perceived performance를 만든다. `[ux-laws, performance, response-time, perceived-performance]`
- [`notes/ux/fitts-law.md`](./notes/ux/fitts-law.md) — 타겟 도달 시간은 거리와 크기의 함수. 버튼은 충분히 크게, 손가락 동선에 가깝게, 사이 간격을 두고 배치한다. `[ux-laws, motor-control, click-target, mobile, accessibility]`
- [`notes/ux/flow.md`](./notes/ux/flow.md) — 난이도와 사용자 능력이 균형을 이룰 때 발생하는 완전 몰입 상태. 너무 어려우면 좌절, 너무 쉬우면 지루함. 피드백·반응성·마찰 제거가 핵심. `[ux-laws, engagement, motivation, friction]`
- [`notes/ux/goal-gradient-effect.md`](./notes/ux/goal-gradient-effect.md) — 목표에 가까워질수록 동기와 속도가 올라간다. 진행률을 보여주고, 인위적 진척이라도 부여하면 완료율이 오른다. `[ux-laws, motivation, progress-indicator, gamification]`
- [`notes/ux/hicks-law.md`](./notes/ux/hicks-law.md) — 의사결정 시간은 선택지의 수·복잡도에 비례해 늘어난다. 옵션 줄이고, 추천 강조하고, 점진적 노출하라. 단, 추상화 수준까지 단순화하진 말 것. `[ux-laws, decision-making, cognitive-load, navigation]`
- [`notes/ux/jakobs-law.md`](./notes/ux/jakobs-law.md) — 사용자는 다른 사이트에서 시간을 더 많이 보낸다. 그래서 당신 사이트도 그들이 이미 아는 사이트처럼 작동하길 기대한다. 컨벤션을 깰 때는 비용을 정당화해야 한다. `[ux-laws, mental-model, conventions, familiarity]`
- [`notes/ux/law-of-common-region.md`](./notes/ux/law-of-common-region.md) — 명확한 경계 안에 있는 요소들은 한 그룹으로 인식된다. 카드·박스·배경색이 가장 흔한 구현. 관계 표현의 가장 강한 신호. `[ux-laws, gestalt, grouping, visual-hierarchy]`
- [`notes/ux/law-of-pragnanz.md`](./notes/ux/law-of-pragnanz.md) — 사람은 복잡한 이미지를 가장 단순한 형태로 해석한다 — 그게 인지 비용이 가장 적으니까. 게슈탈트 원리의 상위 원리. `[ux-laws, gestalt, simplicity, perception]`
- [`notes/ux/law-of-proximity.md`](./notes/ux/law-of-proximity.md) — 가까이 있는 요소는 한 그룹으로 인식된다. 여백(space)이 가장 강력한 그룹핑 도구. 라벨과 입력의 거리가 의미를 결정한다. `[ux-laws, gestalt, grouping, spacing, visual-hierarchy]`
- [`notes/ux/law-of-similarity.md`](./notes/ux/law-of-similarity.md) — 비슷하게 생긴 요소는 한 그룹으로 인식된다. 색·모양·크기·방향·움직임이 모두 그룹 신호. 링크는 일반 텍스트와 시각적으로 달라야 한다. `[ux-laws, gestalt, grouping, visual-design, color]`
- [`notes/ux/law-of-uniform-connectedness.md`](./notes/ux/law-of-uniform-connectedness.md) — 시각적으로 연결된(선·테두리·색) 요소들은 더 강하게 관련되어 보인다. 그룹핑 원리 중 가장 강력 — proximity·similarity보다 우선. `[ux-laws, gestalt, grouping, visual-hierarchy]`
- [`notes/ux/mental-model.md`](./notes/ux/mental-model.md) — 사용자 머릿속의 "이 시스템이 어떻게 동작할까"의 압축 모델. 디자이너의 멘탈 모델과 사용자의 멘탈 모델 간 격차를 좁히는 게 UX의 핵심 과제. `[ux-laws, mental-model, user-research, expectations]`
- [`notes/ux/millers-law.md`](./notes/ux/millers-law.md) — 작업 기억 한계는 7±2개. 이 숫자를 디자인 제약으로 오용하지 말 것 — 핵심은 "청킹으로 처리 가능한 단위로 묶어라"다. `[ux-laws, working-memory, chunking, cognitive-load]`
- [`notes/ux/occams-razor.md`](./notes/ux/occams-razor.md) — 같은 결과를 내는 가설 중 가정이 가장 적은 걸 선택. 디자인에서는 "더 뺄 게 없을 때 완성"이라는 미니멀리즘 원칙. `[ux-laws, simplicity, design-principles, minimalism]`
- [`notes/ux/paradox-of-the-active-user.md`](./notes/ux/paradox-of-the-active-user.md) — 사용자는 매뉴얼을 읽지 않고 바로 쓰기 시작한다. 도움말은 별도 페이지가 아니라 사용 흐름 안에 끼워넣어야 한다. `[ux-laws, onboarding, documentation, contextual-help]`
- [`notes/ux/pareto-principle.md`](./notes/ux/pareto-principle.md) — 결과의 80%는 원인의 20%에서 나온다. 모든 기능에 같은 노력을 분산하지 말고, 임팩트 큰 20%에 집중하라. `[ux-laws, prioritization, product-strategy]`
- [`notes/ux/parkinsons-law.md`](./notes/ux/parkinsons-law.md) — 작업은 주어진 시간만큼 늘어난다. 사용자 작업 시간을 단축하면 경험이 좋아진다 — autofill·smart default·간소화로 시간을 줄여라. `[ux-laws, time-perception, forms, friction]`
- [`notes/ux/peak-end-rule.md`](./notes/ux/peak-end-rule.md) — 사용자는 경험 전체가 아니라 가장 강한 순간(peak)과 마지막 순간(end)을 기억한다. 이 두 지점에 디자인 노력을 집중하라. 부정 경험이 더 강하게 남는다. `[ux-laws, memory, emotion, user-journey]`
- [`notes/ux/postels-law.md`](./notes/ux/postels-law.md) — 받을 때는 관대하게, 보낼 때는 엄격하게. 사용자 입력의 다양성을 받아들이고 시스템 요구에 맞게 변환하라. 회복탄력적 디자인의 기초. `[ux-laws, robustness, input-validation, accessibility]`
- [`notes/ux/selective-attention.md`](./notes/ux/selective-attention.md) — 사람은 환경의 일부 자극에만 주의를 집중한다. Banner blindness·change blindness 같은 함정을 인지하고 디자인해야 한다. `[ux-laws, attention, banner-blindness, change-blindness]`
- [`notes/ux/serial-position-effect.md`](./notes/ux/serial-position-effect.md) — 시퀀스의 처음(primacy)과 끝(recency) 항목이 가장 잘 기억된다. 핵심 액션은 양 끝에, 덜 중요한 건 가운데에 배치하라. `[ux-laws, memory, navigation, list-design]`
- [`notes/ux/teslers-law.md`](./notes/ux/teslers-law.md) — 모든 시스템에는 줄일 수 없는 본질적 복잡도가 있다. 사라지지 않는 그 복잡도를 누가 감당할지(시스템/사용자) 선택하는 게 디자인. `[ux-laws, complexity, design-philosophy, system-design]`
- [`notes/ux/von-restorff-effect.md`](./notes/ux/von-restorff-effect.md) — 비슷한 항목들 사이에서 다른 하나가 가장 잘 기억된다. 강조는 절제해서 — 색에만 의존하지 말고 모션도 신중히. `[ux-laws, memory, visual-hierarchy, accessibility, emphasis]`
- [`notes/ux/working-memory.md`](./notes/ux/working-memory.md) — 4-7개 청크를 20-30초만 들고 있을 수 있는 임시 메모리. 사용자가 외우게 하지 말고 화면에 들고 다녀라. 회상보다 재인을 활용하라. `[ux-laws, working-memory, cognitive-load, recognition]`
- [`notes/ux/zeigarnik-effect.md`](./notes/ux/zeigarnik-effect.md) — 사람은 완료된 일보다 미완료·중단된 일을 더 잘 기억한다. 진행률 표시가 동기를 만들고, "더 있다"는 신호가 발견을 유도한다. `[ux-laws, motivation, completion, progress-indicator]`

### 📈 weekly-report

- [`notes/weekly-report/2026-w10-w18.md`](./notes/weekly-report/2026-w10-w18.md) — 2026 3~4월 (W10~W18) smiledragon 활동 회고 (정정판). 925 commits / 105 PR. modoogram(811c/80PR) mkt24 풀스택 + pc-qa-fix 사이클, design-system-v2(109c/22PR) SDS 안정화 병행. **1차 집계가 50건이었던 건 본인 author가 한글("현혜진")·소문자("hyejin")·noreply email 등 5가지 변종이라 영문 매칭에서 누락된 함정.** `[weekly-report, retrospective, smiledragon, modoogram, design-system-v2, mkt24, sds, git-author-pitfall]`

---

## By Tag (Flat)

- `a11y` → testing/storybook-ui-testing-workflow
- `accessibility` → testing/intentional-removal-regression, ux/fitts-law, ux/postels-law, ux/von-restorff-effect
- `adr` → collaboration/prd-adr-management, collaboration/prd-adr-rollout
- `aesthetics` → ux/aesthetic-usability-effect
- `agents` → ai-workflow/claude-md-routing
- `ai` → ai-workflow/harness-engineering
- `android` → android/android-dev-summit-2019
- `android-studio` → android/android-dev-summit-2019
- `architecture` → tooling/mock-service-gui
- `array` → javascript/array-methods
- `attention` → ux/selective-attention
- `aws` → devops/serverless-lambda
- `banner-blindness` → ux/selective-attention
- `change-blindness` → ux/selective-attention
- `characterization-test` → testing/intentional-removal-regression, testing/regression-risk-template, testing/senior-test-code-patterns
- `chunking` → ux/chunking, ux/millers-law
- `claude-code` → ai-workflow/claude-md-routing, ai-workflow/harness-engineering
- `claude-md` → ai-workflow/claude-md-routing, testing/regression-risk-template
- `click-target` → ux/fitts-law
- `code-comments` → testing/intentional-removal-regression
- `code-connect` → collaboration/prd-adr-management
- `code-review` → testing/senior-test-code-patterns
- `cognitive-load` → ux/choice-overload, ux/chunking, ux/cognitive-load, ux/hicks-law, ux/millers-law, ux/working-memory
- `color` → ux/law-of-similarity
- `communication` → collaboration/cross-team-deployment-coordination, collaboration/low-roi-request-pushback, collaboration/prd-adr-management, collaboration/prd-adr-rollout
- `cross-team` → collaboration/cross-team-deployment-coordination
- `completion` → ux/zeigarnik-effect
- `complexity` → ux/teslers-law
- `container` → devops/docker-intro
- `contextual-help` → ux/paradox-of-the-active-user
- `conventions` → ux/jakobs-law
- `coroutines` → android/android-dev-summit-2019
- `cursor` → ai-workflow/harness-engineering
- `cypress` → testing/storybook-ui-testing-workflow
- `decision-making` → collaboration/low-roi-request-pushback, collaboration/qa-cycle-process-roadmap, collaboration/qa-phase-design-change-pushback, collaboration/ship-before-polish, testing/storybook-test-decay, ux/choice-overload, ux/cognitive-bias, ux/hicks-law
- `decision-record` → collaboration/prd-adr-management, collaboration/prd-adr-rollout, testing/intentional-removal-regression
- `decomposition` → testing/monorepo-test-foundation-layout
- `decorator` → testing/storybook-decorator-query-error-handlers
- `deployment` → collaboration/cross-team-deployment-coordination
- `design-decay` → testing/storybook-test-decay
- `design-philosophy` → ux/teslers-law
- `design-principles` → ux/occams-razor
- `design-system-v2` → weekly-report/2026-w10-w18
- `devops` → devops/docker-intro
- `devtools` → tooling/mock-service-gui
- `docker` → devops/docker-intro, devops/elasticsearch-intro
- `documentation` → collaboration/prd-adr-management, ux/paradox-of-the-active-user
- `e2e` → testing/storybook-ui-testing-workflow
- `elasticsearch` → devops/elasticsearch-intro
- `emotion` → ux/peak-end-rule
- `emphasis` → ux/von-restorff-effect
- `engagement` → ux/flow
- `expectations` → ux/mental-model
- `familiarity` → ux/jakobs-law
- `figma` → collaboration/prd-adr-management
- `forms` → ux/parkinsons-law
- `friction` → ux/flow, ux/parkinsons-law
- `frontend` → collaboration/low-roi-request-pushback, react/hooks-useState-useEffect, react/tutorial-tictactoe, testing/intentional-removal-regression, testing/senior-test-code-patterns, testing/storybook-decorator-query-error-handlers, testing/storybook-test-decay, testing/storybook-ui-testing-workflow
- `fsd` → testing/regression-risk-template
- `gamification` → ux/goal-gradient-effect
- `gestalt` → ux/law-of-common-region, ux/law-of-pragnanz, ux/law-of-proximity, ux/law-of-similarity, ux/law-of-uniform-connectedness
- `git` → git/git-basics
- `git-author-pitfall` → weekly-report/2026-w10-w18
- `grouping` → ux/law-of-common-region, ux/law-of-proximity, ux/law-of-similarity, ux/law-of-uniform-connectedness
- `harness` → ai-workflow/harness-engineering
- `heuristics` → ux/cognitive-bias
- `hooks` → react/hooks-useState-useEffect
- `immutability` → react/tutorial-tictactoe
- `information-architecture` → ux/chunking, ux/cognitive-load
- `input-validation` → ux/postels-law
- `javascript` → javascript/array-methods, testing/jest-setup
- `jest` → testing/jest-setup
- `jira` → collaboration/cross-team-deployment-coordination
- `kotlin` → android/android-dev-summit-2019
- `lambda` → devops/serverless-lambda
- `list-design` → ux/serial-position-effect
- `livedata` → android/android-dev-summit-2019
- `llm-workflow` → ai-workflow/claude-md-routing, testing/monorepo-test-foundation-layout, testing/regression-risk-template
- `memory` → ux/peak-end-rule, ux/serial-position-effect, ux/von-restorff-effect
- `mental-model` → ux/jakobs-law, ux/mental-model
- `minimalism` → ux/occams-razor
- `mkt24` → weekly-report/2026-w10-w18
- `mobile` → ux/fitts-law
- `mocking` → testing/senior-test-code-patterns
- `modoogram` → weekly-report/2026-w10-w18
- `monorepo` → testing/monorepo-test-foundation-layout
- `motivation` → ux/flow, ux/goal-gradient-effect, ux/zeigarnik-effect
- `motor-control` → ux/fitts-law
- `msw` → testing/monorepo-test-foundation-layout, testing/storybook-decorator-query-error-handlers, tooling/mock-service-gui
- `mutation-cache` → testing/storybook-decorator-query-error-handlers
- `navigation` → ux/hicks-law, ux/serial-position-effect
- `node` → testing/jest-setup
- `onboarding` → ux/paradox-of-the-active-user
- `overflow-detection` → collaboration/low-roi-request-pushback
- `package-design` → testing/monorepo-test-foundation-layout
- `paradox-of-choice` → ux/choice-overload
- `perceived-performance` → ux/doherty-threshold
- `perception` → ux/aesthetic-usability-effect, ux/law-of-pragnanz
- `performance` → ux/doherty-threshold
- `playwright` → testing/storybook-ui-testing-workflow
- `pm-collaboration` → collaboration/cross-team-deployment-coordination, collaboration/low-roi-request-pushback, collaboration/prd-adr-management, collaboration/prd-adr-rollout, collaboration/qa-cycle-process-roadmap, collaboration/qa-phase-design-change-pushback
- `pr-template` → collaboration/cross-team-deployment-coordination, collaboration/prd-adr-rollout, collaboration/qa-cycle-process-roadmap
- `prd` → collaboration/prd-adr-management, collaboration/prd-adr-rollout
- `prioritization` → collaboration/qa-phase-design-change-pushback, collaboration/ship-before-polish, ux/pareto-principle
- `process` → collaboration/cross-team-deployment-coordination, collaboration/qa-cycle-process-roadmap, collaboration/qa-phase-design-change-pushback
- `pushback` → collaboration/qa-cycle-process-roadmap, collaboration/qa-phase-design-change-pushback
- `qa` → collaboration/qa-cycle-process-roadmap, collaboration/qa-phase-design-change-pushback
- `product-strategy` → ux/pareto-principle
- `progress-indicator` → ux/goal-gradient-effect, ux/zeigarnik-effect
- `props` → react/tutorial-tictactoe
- `psychology` → ux/cognitive-bias
- `react` → collaboration/low-roi-request-pushback, react/hooks-useState-useEffect, react/tutorial-tictactoe, testing/storybook-decorator-query-error-handlers
- `react-query` → testing/storybook-decorator-query-error-handlers
- `recognition` → ux/working-memory
- `regression` → collaboration/qa-cycle-process-roadmap, testing/intentional-removal-regression, testing/regression-risk-template
- `response-time` → ux/doherty-threshold
- `retrospective` → weekly-report/2026-w10-w18
- `robustness` → ux/postels-law
- `rollout` → collaboration/prd-adr-rollout, collaboration/qa-cycle-process-roadmap
- `routing` → ai-workflow/claude-md-routing
- `sds` → weekly-report/2026-w10-w18
- `search` → devops/elasticsearch-intro
- `security` → android/android-dev-summit-2019
- `self-management` → collaboration/ship-before-polish
- `serverless` → devops/serverless-lambda
- `ship-first` → collaboration/qa-phase-design-change-pushback, collaboration/ship-before-polish
- `simplicity` → ux/law-of-pragnanz, ux/occams-razor
- `skills` → ai-workflow/claude-md-routing
- `smiledragon` → weekly-report/2026-w10-w18
- `spacing` → ux/law-of-proximity
- `state` → react/tutorial-tictactoe
- `storybook` → testing/storybook-decorator-query-error-handlers, testing/storybook-test-decay, testing/storybook-ui-testing-workflow
- `subagent` → testing/senior-test-code-patterns
- `system-design` → ux/postels-law, ux/teslers-law
- `tdd` → testing/senior-test-code-patterns
- `template-design` → testing/regression-risk-template
- `test-foundation` → testing/monorepo-test-foundation-layout
- `testing` → testing/intentional-removal-regression, testing/jest-setup, testing/monorepo-test-foundation-layout, testing/regression-risk-template, testing/senior-test-code-patterns, testing/storybook-test-decay, testing/storybook-ui-testing-workflow, tooling/mock-service-gui
- `theme` → android/android-dev-summit-2019
- `time-management` → collaboration/ship-before-polish
- `time-perception` → ux/parkinsons-law
- `token-optimization` → ai-workflow/harness-engineering
- `tutorial` → react/tutorial-tictactoe
- `typescript` → tooling/mock-service-gui
- `usability-testing` → ux/aesthetic-usability-effect
- `useEffect` → react/hooks-useState-useEffect
- `useMemo` → testing/storybook-decorator-query-error-handlers
- `user-journey` → ux/peak-end-rule
- `user-research` → ux/mental-model
- `useState` → react/hooks-useState-useEffect
- `ux` → collaboration/ship-before-polish
- `ux-laws` → ux/aesthetic-usability-effect, ux/choice-overload, ux/chunking, ux/cognitive-bias, ux/cognitive-load, ux/doherty-threshold, ux/fitts-law, ux/flow, ux/goal-gradient-effect, ux/hicks-law, ux/jakobs-law, ux/law-of-common-region, ux/law-of-pragnanz, ux/law-of-proximity, ux/law-of-similarity, ux/law-of-uniform-connectedness, ux/mental-model, ux/millers-law, ux/occams-razor, ux/paradox-of-the-active-user, ux/pareto-principle, ux/parkinsons-law, ux/peak-end-rule, ux/postels-law, ux/selective-attention, ux/serial-position-effect, ux/teslers-law, ux/von-restorff-effect, ux/working-memory, ux/zeigarnik-effect
- `vcs` → git/git-basics
- `visual-design` → ux/law-of-similarity
- `visual-hierarchy` → ux/law-of-common-region, ux/law-of-proximity, ux/law-of-uniform-connectedness, ux/von-restorff-effect
- `visual-testing` → testing/storybook-ui-testing-workflow
- `vitest` → testing/monorepo-test-foundation-layout, testing/senior-test-code-patterns
- `weekly-report` → weekly-report/2026-w10-w18
- `what-vs-why` → testing/storybook-test-decay
- `why-preservation` → testing/intentional-removal-regression
- `workflow` → ai-workflow/harness-engineering, testing/senior-test-code-patterns
- `working-memory` → ux/chunking, ux/cognitive-load, ux/millers-law, ux/working-memory

---

## By Date (Chronological)

### 2026
- **2026-05-04** — [`notes/collaboration/cross-team-deployment-coordination.md`](./notes/collaboration/cross-team-deployment-coordination.md)
- **2026-05-04** — [`notes/collaboration/qa-cycle-process-roadmap.md`](./notes/collaboration/qa-cycle-process-roadmap.md)
- **2026-05-04** — [`notes/testing/intentional-removal-regression.md`](./notes/testing/intentional-removal-regression.md)
- **2026-05-04** — [`notes/collaboration/qa-phase-design-change-pushback.md`](./notes/collaboration/qa-phase-design-change-pushback.md)
- **2026-05-04** — [`notes/collaboration/ship-before-polish.md`](./notes/collaboration/ship-before-polish.md)
- **2026-05-04** — UX 법칙 30종 일괄 추가: `notes/ux/` (lawsofux.com 정리)
- **2026-04-30** — [`notes/weekly-report/2026-w10-w18.md`](./notes/weekly-report/2026-w10-w18.md)
- **2026-04-30** — [`notes/collaboration/prd-adr-rollout.md`](./notes/collaboration/prd-adr-rollout.md)
- **2026-04-30** — [`notes/collaboration/prd-adr-management.md`](./notes/collaboration/prd-adr-management.md)
- **2026-04-30** — [`notes/testing/storybook-test-decay.md`](./notes/testing/storybook-test-decay.md)
- **2026-04-29** — [`notes/collaboration/low-roi-request-pushback.md`](./notes/collaboration/low-roi-request-pushback.md)
- **2026-04-29** — [`notes/testing/storybook-ui-testing-workflow.md`](./notes/testing/storybook-ui-testing-workflow.md)
- **2026-04-29** — [`notes/testing/storybook-decorator-query-error-handlers.md`](./notes/testing/storybook-decorator-query-error-handlers.md)
- **2026-04-29** — [`notes/testing/monorepo-test-foundation-layout.md`](./notes/testing/monorepo-test-foundation-layout.md)
- **2026-04-24** — [`notes/ai-workflow/claude-md-routing.md`](./notes/ai-workflow/claude-md-routing.md)
- **2026-04-24** — [`notes/ai-workflow/harness-engineering.md`](./notes/ai-workflow/harness-engineering.md)
- **2026-04-24** — [`notes/testing/regression-risk-template.md`](./notes/testing/regression-risk-template.md)
- **2026-04-24** — [`notes/testing/senior-test-code-patterns.md`](./notes/testing/senior-test-code-patterns.md)
- **2026-04-24** — [`notes/tooling/mock-service-gui.md`](./notes/tooling/mock-service-gui.md)

### 2019
- **2019-12-21** — [`notes/android/android-dev-summit-2019.md`](./notes/android/android-dev-summit-2019.md)
- **2019-12-20** — [`notes/devops/serverless-lambda.md`](./notes/devops/serverless-lambda.md)
- **2019-12-19** — [`notes/react/tutorial-tictactoe.md`](./notes/react/tutorial-tictactoe.md)
- **2019-12-16** — [`notes/devops/docker-intro.md`](./notes/devops/docker-intro.md)
- **2019-12-16** — [`notes/devops/elasticsearch-intro.md`](./notes/devops/elasticsearch-intro.md)
- **2019-12-13** — [`notes/react/hooks-useState-useEffect.md`](./notes/react/hooks-useState-useEffect.md)
- **2019-12-11** — [`notes/git/git-basics.md`](./notes/git/git-basics.md)
- **2019-12-11** — [`notes/javascript/array-methods.md`](./notes/javascript/array-methods.md)
- **2019-12-11** — [`notes/testing/jest-setup.md`](./notes/testing/jest-setup.md)

---

## 제외 대상 (`_archive/`)

RAG/검색 대상이 아님. 과거 일상 로그나 본문이 없는 TODO성 항목들.

- `_archive/personal-2019-12/` — 2019-12-12, 19, 23, 24, 26, 27, 28 (개인 일상 로그 및 미작성 스텁)

---

## Index Maintenance

새 TIL을 추가할 때:

1. 파일 생성: `notes/<category>/<slug>.md`
   - 없는 카테고리면 새 디렉토리 생성
   - 모든 파일에 frontmatter 필수 (`title`, `date`, `category`, `tags`, `summary`)
2. **이 `INDEX.md`에 3곳 갱신**:
   - `By Topic` — 해당 카테고리 섹션에 한 줄 추가 (경로 · 요약 · 태그)
   - `By Tag` — 태그별 매핑 추가 (알파벳 순)
   - `By Date` — 해당 연도 섹션 최상단에 추가
3. 1 파일 = 1 주제 원칙 유지. 여러 주제가 섞이면 파일을 쪼개라.
