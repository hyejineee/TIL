---
title: 하네스 엔지니어링 — 팀 맞춤형 AI 환경 구축
date: 2026-04-24
category: ai-workflow
tags: [ai, harness, cursor, claude-code, workflow, token-optimization]
summary: 우아한형제들 테크블로그 요약. Rules + Skills로 AI한테 프로젝트 맥락을 주입하고, 전처리 스크립트로 토큰을 평균 96.5% 절감한 사례.
---

## 배운 것

AI 코딩 도구(Cursor, Claude Code 등)를 팀 단위로 길들이는 **"하네스(harness) 엔지니어링"** 개념.
개인이 프롬프트를 잘 쓰는 것보다 **환경을 설계해서 AI를 가두는** 쪽이 훨씬 효율적이다.

출처: [우아한형제들 테크블로그 — 하네스 엔지니어링](https://techblog.woowahan.com/26177/)

---

## 왜 중요한가

AI한테 매번 같은 소리를 반복하고 있다면 그건 환경 설계 실패다.
- "React Query 훅 만들 땐 캐시 키 이렇게 관리해줘"
- "API 호출은 이 패턴으로 해줘"
- "이 디렉토리 구조 따라줘"

→ 이걸 대화마다 프롬프트로 넣는 대신, **코드베이스 옆에 설정으로 박아두자**는 발상.

---

## 핵심 메커니즘 2가지

### 1. Rules — 선언형 규칙 (토큰 적음)

`.cursor/rules/` 아래에 YAML 파일로 선언.

```yaml
---
description: React Query 훅 작성 규칙
globs: "src/queries/**/*.ts"
---
[규칙 본문]
```

**핵심은 `globs`**. 해당 경로 파일을 건드릴 때만 규칙이 로드됨 → 전체 세션에 떠 있는 게 아니라 **필요할 때만 컨텍스트에 주입**.
- Claude Code의 `CLAUDE.md`와 비슷하지만, CLAUDE.md는 전역/디렉토리 스코프밖에 없음. Cursor Rules는 **glob 단위**라서 더 정밀.

### 2. Skills — 스크립트로 맥락 전처리 (진짜 핵심)

AI한테 파일을 통째로 읽히지 말고, **Node.js/Bash 스크립트가 미리 메타데이터만 뽑아서** AI에 넘긴다.

```js
function collectContext(targetDir) {
  const files = readdirSync(targetDir, { recursive: true })
    .filter(f => f.endsWith(".ts"));

  // 정규식으로 필요한 것만 추출
  const className = content.match(/class (\w+)/)?.[1];
  const methods = [...content.matchAll(/async (\w+)\(/g)].map(m => m[1]);
  // → import, 에러 처리, 구현 디테일은 전부 버림
}
```

결과: AI는 JSON 요약본만 받는다. "이 도메인엔 이런 클래스, 이런 메서드가 있음" 수준의 골격만.

---

## 실제 측정 결과 (토큰 절감)

| 도메인 규모 | 원본 | 전처리 후 | 절감률 |
|---|---|---|---|
| 대규모 | 41,944 B | 1,763 B | **95.8%** |
| 중규모 | 29,386 B | 668 B | **97.7%** |
| 소규모 | 9,749 B | 539 B | **94.5%** |

**평균 96.5% 절감**, 작업 1회당 약 6,800 토큰 절약.
속도도 덤으로 빨라짐 (컨텍스트가 작으니까).

---

## 다음에 써먹을 곳

### 지금 당장 적용 가능한 패턴

1. **반복 프롬프트 → Rules로 고정**
   - 프로젝트에서 AI한테 "이거 이렇게 해줘"를 2번 이상 말했다면, 그건 Rule 후보.
   - `.cursor/rules/` 또는 `CLAUDE.md`에 glob 기준으로 박아둔다.

2. **"파일 뭉치 읽기" → Skill로 전처리**
   - AI가 `readdirSync`로 디렉토리 훑고 파일 하나하나 `Read`하는 패턴이 반복된다면, 그걸 Node 스크립트로 대체.
   - 스크립트가 요약 JSON을 만들고, AI는 그 JSON만 본다.

3. **openapi-typescript 같은 코드젠**
   - OpenAPI 스펙 → 타입 정의. AI가 스펙 전문 읽을 필요 없음.
   - 유사하게: `tsc --listFiles`, `madge` (의존성 그래프), `ast-grep` 등으로 메타데이터만 뽑기.

### 핵심 원칙

> "AI한테 정보를 주지 말고, **AI가 필요한 정보만 주는 시스템**을 만들어라."

프롬프트 엔지니어링이 아니라 **컨텍스트 엔지니어링**.

---

## 내 프로젝트(마24/뷰업) 적용 아이디어

> ⚠️ 아직 확정된 계획 아님 — 브레인스토밍 단계.

**뷰업**: 기획 문서가 없고 코드가 유일한 사양.
- → Skill 후보: "도메인 요약 추출기". 주요 디렉토리의 클래스/함수/타입 시그니처만 JSON으로 뽑아서 AI 컨텍스트로 주입.
- → characterization test 생성할 때 현재 코드 전체를 읽히는 대신, **public API 시그니처 + 사용 지점만** 뽑아서 AI에게 주는 식.

**마24/뷰업 공통**:
- API 연동 패턴을 Rule로 박아두기 (`src/api/**`, `src/queries/**` glob)
- 컴포넌트 디렉토리 규칙 Rule화 (FSD 레이어 경계, import 방향)
- 테스트 컨벤션 Rule화 (한글 describe/it, unit/integration 쌍)

---

## 관련 TIL
- [`2026-04-24-senior-test-code-patterns.md`](./2026-04-24-senior-test-code-patterns.md) — Opus/Sonnet 분업과 subagent 하네스 (이 글과 결이 같음)
