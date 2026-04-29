---
title: 모노레포 테스트 환경 구축 — 자산 8개 카테고리와 배치 분리 원칙
date: 2026-04-29
category: testing
tags: [testing, monorepo, vitest, msw, package-design, test-foundation, llm-workflow, decomposition]
summary: "테스트 환경 구축"이라는 추상적 작업을 8개 자산 카테고리로 분해하고, 각 자산을 공유 패키지 vs 앱 co-location 중 어디에 둘지 결정하는 기준. 새 패키지 신설보다 기존 lib 패키지에 subpath export로 얹는 게 가벼움.
---

# 모노레포 테스트 환경 구축 — 자산 8개 카테고리와 배치 분리 원칙

mkt24(빈 신규 앱)에 테스트 환경을 깔려고 했는데, "어떻게 잡지?"라고 막연히 시작했더니 패키지 구조·Phase 로드맵·exports 설정이 한꺼번에 쏟아져서 정리가 안 됐다. 진짜 사용자(나) 머릿속에 들어가야 했던 건 **"테스트 환경에 들어갈 자산이 뭐고 각각 어디에 두는가"**라는 카테고리 분해.

## 배운 것

### 1. "테스트 환경"은 8개 자산 카테고리로 분해된다

| # | 카테고리 | 구체 예시 |
|---|---|---|
| 1 | 러너 설정 | `vite.config.mjs` test 블록, `setupTests.ts` (jest-dom 매처·MSW listen) |
| 2 | 모듈 mock | `__mocks__/zustand.ts` (스토어 자동 reset), `__mocks__/next-navigation.ts` |
| 3 | 헬퍼 함수 | `renderWithProviders` (QueryClient·Theme 감싸기), `createTestQueryClient`(retry off), `renderHookWithQuery` |
| 4 | MSW 인프라 | `server.ts` (node), 공통 인증/token 베이스 핸들러 |
| 5 | 공통 fixtures | `ApiResponse<T>` 래퍼, 페이지네이션 베이스, 공통 user/token mock |
| 6 | 도메인 fixtures | `<도메인>.fixtures.ts` (DTO 계약 따르는 도메인별 mock 데이터, 도메인 MSW 핸들러) |
| 7 | CI/리포터 | `package.json` scripts, JUnit·HTML reporter, coverage threshold |
| 8 | 컨벤션 문서 | `.claude/rules/test-workflow.md`, 도메인별 `CLAUDE.md` |

이 분해가 머리에 박히면 "헬퍼함수, 목 데이터 등등 뭐가 있지?" 같은 막연한 질문이 #3·#5·#6 위치 결정 문제로 좁혀진다.

### 2. 배치 원칙: 앱·도메인 무관하면 공유, 도메인별 데이터는 co-location

- **공유 패키지 (#2~#5)**: 앱이 바뀌어도 동일하게 쓰이는 인프라성 코드. zustand mock, QueryClient wrapper, MSW server setup, ApiResponse 래퍼 등.
- **앱 co-location (#1, #6, #7)**: 그 앱·그 도메인의 데이터·설정. 도메인 fixture는 *그 도메인 폴더 옆*에 둔다.

시니어가 viewup에 박은 패턴도 동일 — `apps/viewup/app/viewing/click/model/logic/__test-helpers__/fixtures.ts`처럼 viewing 도메인 안에 fixture가 같이 있다. 추상적 베이스만 위로 올리고, 도메인 의미 들어가는 건 그 옆에 둔다.

### 3. 새 패키지 신설보다 기존 lib에 subpath export로 얹는 게 가볍다

처음엔 `packages/test-config`, `packages/test-utils` 둘을 신설하려 했는데, 모노레포에 새 패키지 추가는 turbo 설정·pnpm-workspace·tsconfig·exports 등 부수 비용이 따라온다.

기존 `modoogram-libs`가 이미 빌드 단계 없이 src 직접 export(`./src/index.ts`)에 subpath 패턴(`./client`)을 쓰고 있어서, **subpath만 하나 더 추가하면 됨**:

```jsonc
"exports": {
  ".":                "./src/index.ts",
  "./client":         "./src/client/index.ts",
  "./testing":        "./src/testing/index.ts",        // 신규
  "./testing/preset": "./src/testing/preset.ts"        // 신규 (vitest mergeConfig용)
}
```

**핵심 안전장치**: 런타임 export(`.`, `./client`)와 testing export(`./testing`, `./testing/preset`)를 **완전 분리**. 앱이 `import from 'modoogram-libs'` 해도 testing 코드는 안 따라와서 번들에 안 들어간다. vitest·msw·@testing-library는 lib 패키지의 **devDependencies로만** 추가.

### 4. "페이지별 대분류 시나리오" 욕구는 자연스럽게 도메인 CLAUDE.md 패턴으로 수렴

"테스트를 페이지별로 대분류하고 그 안에서 수정하는 식으로 하면 되나?"라는 직관이 — 시니어 viewup 패턴(`app/<도메인>/CLAUDE.md` + 그 안의 store/mapper/hook/component 단위 테스트)과 그대로 매핑된다. **페이지(=도메인)가 인지 스코프 단위**로서 자연스러운 격리 경계라는 점은 둘이 독립적으로 도달한 결론.

modoogram처럼 FSD 슬라이스(`_entities`, `_features`, `_modules`, `_pages`)로 분해된 구조에서는 도메인의 "주소"를 `_pages/<도메인>/CLAUDE.md`로 두고, 거기서 entities·features·modules가 어디 흩어져 있는지 매핑하는 FSD Map 섹션을 추가하는 게 viewup 패턴 대비 보강 포인트다.

### 5. 메타 — 큰 그림 펼치기 전에 자산 분해부터

브레인스토밍 중에 Phase 0~4 로드맵·exports 설정·subpath 분리 원칙을 한꺼번에 던졌더니 사용자(나)가 "정리가 안된다 ㅠㅠ"로 무너졌다. 그 다음 8개 카테고리 표로 펼쳤더니 한 번에 잡혔다.

**교훈**: 추상적 task("테스트 환경 구축")를 받았을 때 단계(Phase)·구조(패키지)부터 그리지 말고, **"여기 들어가는 자산이 뭐가 있나"** 카테고리부터 분해. 카테고리가 잡히면 그 다음에 배치 결정·로드맵이 자연스럽게 따라온다. 단계는 카테고리의 함수, 카테고리는 단계의 입력이 아님.

## 어디 써먹나

- **신규 모노레포 테스트 환경 셋업**: 8개 카테고리 표를 체크리스트로 쓴다. 각각이 채워지는지 확인.
- **기존 모노레포에 회귀 테스트 박을 때**: #2~#5만 먼저 공유 lib에 박고, 도메인 진입할 때 #6·#8(도메인 CLAUDE.md)을 그 도메인 옆에 추가.
- **공유 패키지 신설 욕구가 들 때**: 일단 기존 lib 패키지에 subpath export로 얹을 수 있는지 먼저 본다. 운영 중에 분리 필요성이 명확해지면 그때 갈라낸다 (YAGNI).
- **Claude/에이전트한테 추상적 task 던질 때**: "단계·구조" 대신 "들어가는 자산 카테고리" 먼저 뽑아달라고 주문 — 정리 비용이 훨씬 낮음.

## 관련 TIL

- [`notes/testing/senior-test-code-patterns.md`](./senior-test-code-patterns.md) — viewup 시니어가 만든 테스트 코드의 9가지 패턴 (이 TIL의 *앞 단계*: 시니어가 무엇을 만들었나)
- [`notes/testing/regression-risk-template.md`](./regression-risk-template.md) — 도메인별 CLAUDE.md "회귀 위험 포인트" 섹션 템플릿 (이 TIL의 #8에 들어갈 구체 양식)
- [`notes/tooling/mock-service-gui.md`](../tooling/mock-service-gui.md) — MSW 운영 도구 (이 TIL의 #4 영역)
