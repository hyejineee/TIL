---
title: Storybook UI 테스팅 워크플로우 — Story를 단일 source of truth로 두는 테스트 피라미드
date: 2026-04-29
category: testing
tags: [storybook, testing, playwright, cypress, e2e, visual-testing, a11y, frontend]
summary: Storybook 핸드북의 UI 테스팅 워크플로우 정리. story.args를 mock·E2E·visual 테스트의 공통 fixture로 재사용해 도구별 중복 셋업을 없애고, E2E는 핵심 흐름에만 제한한다. Cypress 예시를 Playwright로 옮기는 매핑까지.
---

# 배운 것

Storybook UI 테스팅 핸드북([workflow](https://storybook.js.org/tutorials/ui-testing-handbook/react/ko/workflow/), [user-flow-testing](https://storybook.js.org/tutorials/ui-testing-handbook/react/ko/user-flow-testing/))을 읽고 정리.

핵심은 **"story를 단일 source of truth로 두고, 시각 → 상호작용 → a11y → E2E 순으로 같은 자산을 재활용한다"**는 것. 도구별로 fixture를 따로 만들어 두면 데이터 불일치로 테스트 신뢰도가 깎이는데, story의 `args`를 import해서 mock body로 그대로 쓰면 이 문제가 사라진다.

## 테스트 종류별 역할

| 테스트 | 무엇을 잡나 | 도구 | 비중 |
|---|---|---|---|
| Visual | 의도치 않은 스타일 변경 | Chromatic / Playwright `toHaveScreenshot()` | 모든 스토리 |
| Composition | 컴포넌트 조합·연결 | Storybook | 시각 테스트와 함께 |
| Interaction | 클릭·입력 등 사용자 동작 | `@storybook/test` `play` 함수 | 컴포넌트 단위 (대부분 여기서 끝남) |
| Accessibility | 키보드/스크린리더/색대비 | A11y addon (개발 중) + jest-axe / test-runner (CI) | 모든 컴포넌트 |
| User Flow (E2E) | 페이지 간 통합 흐름 | Cypress / Playwright | **결정적인 핵심 흐름만** |

원칙: **컴포넌트 단위에서 80% 끝내고, E2E는 "깨지면 사용자가 즉시 이탈"하는 흐름만**. 모든 걸 E2E로 짜는 건 안티패턴 (느림 + 유지보수 지옥).

## 단계별 실행 시점

- **개발 중**: Storybook에서 stories 작성 → 시각·a11y·interaction 즉시 확인
- **커밋 전**: `yarn test-storybook` watch 모드
- **PR 시**: GitHub Actions가 Chromatic + jest-axe + E2E 병렬 실행
- **머지 후**: 메인에서 회귀 감시

# 왜 중요한가

기존에는 도구별로 fixture를 따로 만들었다 — Storybook용 mock, MSW handler, Cypress fixture, 수동 테스트 데이터… 결국 **같은 도메인 데이터가 4벌**이 되고 한쪽만 업데이트하면 테스트가 거짓 양성/거짓 음성이 된다.

핸드북의 핵심 트릭은 단순하다:

```js
// TaskList.stories.tsx에 정의한 args를…
export const Default = { args: { tasks: [...6개] } };

// E2E에서 그대로 import해서 mock body로
import { Default } from '../src/components/TaskList.stories';
cy.intercept('GET', '/tasks', { body: Default.args });
```

→ Story가 바뀌면 E2E 모킹도 자동으로 따라온다. **fixture 동기화 비용이 0**.

이 패턴이 성립하는 이유는 stories가 이미 "이 컴포넌트가 받는 정상 입력"의 카탈로그이기 때문이다. 별도 fixture를 만드는 건 같은 정보를 두 번 쓰는 것.

# 다음에 써먹을 곳

## 적용 순서 (기존 React 프로젝트)

1. **stories에 mock 데이터 우선 정의** — `args`에 fixture를 박아둔다. `argTypes`에 `{ action: 'onSubmit' }`로 핸들러 추적.
2. **`@storybook/test`로 interaction test** — `play` 함수에서 `userEvent` + `expect`. CI에선 `yarn test-storybook`.
3. **A11y addon 설치** — 개발 중 위반 사항 실시간 표시. test-runner가 axe 체크까지 자동.
4. **Visual regression** — Chromatic 또는 Playwright `toHaveScreenshot()`.
5. **E2E는 마지막에, 소수만** — 로그인/결제/핵심 CRUD 정도. 나머진 interaction test로 충분.

## Cypress vs Playwright

핸드북은 Cypress로 쓰여있지만 **Playwright가 Storybook 생태계와 더 자연스럽다** — `@storybook/test-runner` 자체가 Playwright 기반이라 브라우저 바이너리·CI 캐시가 통일됨.

같은 인증 플로우 매핑:

| 목적 | Cypress | Playwright |
|---|---|---|
| 페이지 방문 | `cy.visit('/')` | `await page.goto('/')` |
| 요소 찾기 | `cy.get('input[name=email]')` | `page.getByLabel('Email')` |
| 입력 | `.type('text')` | `.fill('text')` |
| 클릭 | `.click()` | `await locator.click()` |
| API 모킹 | `cy.intercept()` | `page.route()` |
| 검증 | `.should('have.length', 6)` | `await expect(locator).toHaveCount(6)` |

```ts
// Playwright 버전 — story.args 재사용 패턴은 동일
import { test, expect } from '@playwright/test';
import { Default as TaskListDefault } from '../src/components/TaskList.stories';

test.beforeEach(async ({ page }) => {
  await page.route('**/tasks', (route) =>
    route.fulfill({ status: 201, json: TaskListDefault.args }),
  );
});

test('user can authenticate', async ({ page }) => {
  await page.goto('/');
  await page.getByLabel('Email').fill('alice@test.com');
  await page.getByLabel('Password').fill('secret');
  await page.getByRole('button', { name: /log in/i }).click();
  await expect(page.getByLabel('tasks').getByRole('listitem')).toHaveCount(6);
});
```

Playwright를 고를 만한 이유:
- Storybook test-runner와 같은 엔진
- 멀티 브라우저 기본 (Chromium / Firefox / WebKit)
- 트레이스 뷰어 — 실패 시 타임라인 + DOM 스냅샷 + 네트워크 한눈에
- 병렬 실행 기본값
- `getByRole` / `getByLabel` 같은 a11y-first locator (Testing Library 철학)
- `playwright.config.ts`의 `webServer` 옵션으로 dev 서버 자동 기동

Cypress가 여전히 나은 경우: 팀이 이미 Cypress GUI 디버깅에 익숙하거나, Cypress 전용 플러그인 생태계에 의존 중일 때.

## 함정 / 주의

- **stories가 곧 fixture가 된다는 건 stories 품질이 곧 테스트 품질**이란 뜻. `Default.args`를 대충 쓰면 모든 테스트가 같이 약해진다.
- E2E는 **결정적**이어야 한다 — `page.route()` / `cy.intercept()`로 네트워크를 잠그지 않으면 flaky.
- visual test는 폰트 로딩·애니메이션 때문에 처음엔 무조건 깜빡인다. `disable animations` 옵션과 폰트 프리로드를 셋업 단계에서 같이 박아둘 것.
