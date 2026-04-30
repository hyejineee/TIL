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
│   └── tooling/
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

- [`notes/collaboration/low-roi-request-pushback.md`](./notes/collaboration/low-roi-request-pushback.md) — "있어도 그만 없어도 그만"인 요구사항을 주관적 거절 대신 "데이터로 정당화해주세요"로 공넘기는 푸시백 패턴. 모바일 플랫폼 탭 overflow 측정 요청 사례. `[communication, pm-collaboration, decision-making, frontend, overflow-detection, react]`
- [`notes/collaboration/prd-adr-management.md`](./notes/collaboration/prd-adr-management.md) — 배포 후 휘발되는 *why* 히스토리를 잡는 4가지 장치 — 단일 인덱스, 코드→문서 역방향 링크, ADR을 코드 가까이, PR 템플릿 강제. `[prd, adr, decision-record, documentation, figma, pm-collaboration, communication, code-connect]`
- [`notes/collaboration/prd-adr-rollout.md`](./notes/collaboration/prd-adr-rollout.md) — 4가지 장치를 한 번에 다 깔면 정착 실패. PR 템플릿→메타 ADR→역방향 링크→인덱스 순으로. 1주차에 단 3개 파일만 만들어 모멘텀 잡고, 인덱스는 ADR 5+개 쌓인 뒤. `[adr, prd, rollout, decision-record, pr-template, pm-collaboration, communication]`

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

- [`notes/testing/jest-setup.md`](./notes/testing/jest-setup.md) — `npm init` 후 `package.json` scripts에 `test`/`watch` 두 줄. 기본 구문은 `test("이름", () => expect(동작).toBe(기대값))`. `[jest, javascript, testing, node]`
- [`notes/testing/monorepo-test-foundation-layout.md`](./notes/testing/monorepo-test-foundation-layout.md) — 테스트 환경을 8개 자산 카테고리(러너/모듈mock/헬퍼/MSW/공통fixture/도메인fixture/CI/컨벤션)로 분해하고 공유 패키지 vs 앱 co-location 배치를 결정. 새 패키지 신설보다 기존 lib에 subpath export로 얹기. `[testing, monorepo, vitest, msw, package-design, test-foundation, llm-workflow, decomposition]`
- [`notes/testing/regression-risk-template.md`](./notes/testing/regression-risk-template.md) — mkt24 회귀 방지용 CLAUDE.md "회귀 위험 포인트" 템플릿을 4번 갈아엎은 기록. 6 카테고리(외부 계약/내부 파급/상태 잔존/타이밍/가드 유지/운영·배포) + 메타필드(신뢰도/Evidence/검증일) + 구조화 리스트. `[testing, claude-md, regression, template-design, llm-workflow, characterization-test, fsd]`
- [`notes/testing/senior-test-code-patterns.md`](./notes/testing/senior-test-code-patterns.md) — 시니어의 SD-2877-tdd 브랜치에서 배운 9가지 패턴 — 한글 describe/it, unit+integration 쌍, 경계 mock, factory/fixture, private 타입 안전 접근, stateful fake vs spy, colocation, CLAUDE.md로 맥락 전달, Opus/Sonnet 분업. `[testing, vitest, tdd, code-review, frontend, subagent, workflow, mocking, characterization-test]`
- [`notes/testing/storybook-decorator-query-error-handlers.md`](./notes/testing/storybook-decorator-query-error-handlers.md) — Storybook 데코레이터로 앱 `mutationCache.onError`를 라이트 이식할 때의 4가지 함정 — Provider 통째 import 금지, `useMemo` deps `[]`로 무한 hang 회피, portal target div, play 함수로 행동까지 박제. `[storybook, react-query, decorator, react, mutation-cache, msw, useMemo, frontend]`
- [`notes/testing/storybook-ui-testing-workflow.md`](./notes/testing/storybook-ui-testing-workflow.md) — Storybook 핸드북의 UI 테스팅 워크플로우 정리. `story.args`를 mock·E2E·visual의 공통 fixture로 재사용해 fixture 동기화 비용을 0으로. E2E는 핵심 흐름만, Cypress→Playwright 매핑까지. `[storybook, testing, playwright, cypress, e2e, visual-testing, a11y, frontend]`

### 🧩 tooling

- [`notes/tooling/mock-service-gui.md`](./notes/tooling/mock-service-gui.md) — MSW를 브라우저 UI에서 제어하는 devtools. preset/profile/override 3단 합성, 체이닝 제네릭으로 label 타입 안전, dev-only 3중 안전장치, 런타임 핸들러 교체(`resetHandlers + use`). `[msw, devtools, testing, architecture, typescript]`

---

## By Tag (Flat)

- `a11y` → testing/storybook-ui-testing-workflow
- `adr` → collaboration/prd-adr-management, collaboration/prd-adr-rollout
- `agents` → ai-workflow/claude-md-routing
- `ai` → ai-workflow/harness-engineering
- `android` → android/android-dev-summit-2019
- `android-studio` → android/android-dev-summit-2019
- `architecture` → tooling/mock-service-gui
- `array` → javascript/array-methods
- `aws` → devops/serverless-lambda
- `characterization-test` → testing/regression-risk-template, testing/senior-test-code-patterns
- `claude-code` → ai-workflow/claude-md-routing, ai-workflow/harness-engineering
- `claude-md` → ai-workflow/claude-md-routing, testing/regression-risk-template
- `code-connect` → collaboration/prd-adr-management
- `code-review` → testing/senior-test-code-patterns
- `communication` → collaboration/low-roi-request-pushback, collaboration/prd-adr-management, collaboration/prd-adr-rollout
- `container` → devops/docker-intro
- `coroutines` → android/android-dev-summit-2019
- `cursor` → ai-workflow/harness-engineering
- `cypress` → testing/storybook-ui-testing-workflow
- `decision-making` → collaboration/low-roi-request-pushback
- `decision-record` → collaboration/prd-adr-management, collaboration/prd-adr-rollout
- `decomposition` → testing/monorepo-test-foundation-layout
- `decorator` → testing/storybook-decorator-query-error-handlers
- `devops` → devops/docker-intro
- `devtools` → tooling/mock-service-gui
- `docker` → devops/docker-intro, devops/elasticsearch-intro
- `documentation` → collaboration/prd-adr-management
- `e2e` → testing/storybook-ui-testing-workflow
- `elasticsearch` → devops/elasticsearch-intro
- `figma` → collaboration/prd-adr-management
- `frontend` → collaboration/low-roi-request-pushback, react/hooks-useState-useEffect, react/tutorial-tictactoe, testing/senior-test-code-patterns, testing/storybook-decorator-query-error-handlers, testing/storybook-ui-testing-workflow
- `fsd` → testing/regression-risk-template
- `git` → git/git-basics
- `harness` → ai-workflow/harness-engineering
- `hooks` → react/hooks-useState-useEffect
- `immutability` → react/tutorial-tictactoe
- `javascript` → javascript/array-methods, testing/jest-setup
- `jest` → testing/jest-setup
- `kotlin` → android/android-dev-summit-2019
- `lambda` → devops/serverless-lambda
- `livedata` → android/android-dev-summit-2019
- `llm-workflow` → ai-workflow/claude-md-routing, testing/monorepo-test-foundation-layout, testing/regression-risk-template
- `mocking` → testing/senior-test-code-patterns
- `monorepo` → testing/monorepo-test-foundation-layout
- `msw` → testing/monorepo-test-foundation-layout, testing/storybook-decorator-query-error-handlers, tooling/mock-service-gui
- `mutation-cache` → testing/storybook-decorator-query-error-handlers
- `node` → testing/jest-setup
- `overflow-detection` → collaboration/low-roi-request-pushback
- `package-design` → testing/monorepo-test-foundation-layout
- `playwright` → testing/storybook-ui-testing-workflow
- `pm-collaboration` → collaboration/low-roi-request-pushback, collaboration/prd-adr-management, collaboration/prd-adr-rollout
- `pr-template` → collaboration/prd-adr-rollout
- `prd` → collaboration/prd-adr-management, collaboration/prd-adr-rollout
- `props` → react/tutorial-tictactoe
- `react` → collaboration/low-roi-request-pushback, react/hooks-useState-useEffect, react/tutorial-tictactoe, testing/storybook-decorator-query-error-handlers
- `react-query` → testing/storybook-decorator-query-error-handlers
- `regression` → testing/regression-risk-template
- `rollout` → collaboration/prd-adr-rollout
- `routing` → ai-workflow/claude-md-routing
- `search` → devops/elasticsearch-intro
- `security` → android/android-dev-summit-2019
- `serverless` → devops/serverless-lambda
- `skills` → ai-workflow/claude-md-routing
- `state` → react/tutorial-tictactoe
- `storybook` → testing/storybook-decorator-query-error-handlers, testing/storybook-ui-testing-workflow
- `subagent` → testing/senior-test-code-patterns
- `tdd` → testing/senior-test-code-patterns
- `template-design` → testing/regression-risk-template
- `test-foundation` → testing/monorepo-test-foundation-layout
- `testing` → testing/jest-setup, testing/monorepo-test-foundation-layout, testing/regression-risk-template, testing/senior-test-code-patterns, testing/storybook-ui-testing-workflow, tooling/mock-service-gui
- `theme` → android/android-dev-summit-2019
- `token-optimization` → ai-workflow/harness-engineering
- `tutorial` → react/tutorial-tictactoe
- `typescript` → tooling/mock-service-gui
- `useEffect` → react/hooks-useState-useEffect
- `useMemo` → testing/storybook-decorator-query-error-handlers
- `useState` → react/hooks-useState-useEffect
- `vcs` → git/git-basics
- `visual-testing` → testing/storybook-ui-testing-workflow
- `vitest` → testing/monorepo-test-foundation-layout, testing/senior-test-code-patterns
- `workflow` → ai-workflow/harness-engineering, testing/senior-test-code-patterns

---

## By Date (Chronological)

### 2026
- **2026-04-30** — [`notes/collaboration/prd-adr-rollout.md`](./notes/collaboration/prd-adr-rollout.md)
- **2026-04-30** — [`notes/collaboration/prd-adr-management.md`](./notes/collaboration/prd-adr-management.md)
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
