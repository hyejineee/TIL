---
title: 글로벌 CLAUDE.md에 에이전트·스킬 라우팅 가이드 분리해 두기
date: 2026-04-24
category: ai-workflow
tags: [claude-code, claude-md, agents, skills, routing, llm-workflow]
summary: 자동 로드되는 에이전트·스킬도 트리거가 흐릿할 수 있어, ~/.claude/CLAUDE.md에 라우팅 힌트(우선순위·안티패턴)를 별도 파일로 분리해 import한다.
---

## 배운 것

### 1. 자동 로드된다고 트리거가 명확한 건 아니다

플러그인 에이전트(`jineee-plugin:*`, `smiledragon-fe-plugin:*` 등)와 스킬은 세션 시작 시 시스템 프롬프트에 description과 함께 자동 주입된다. 그래서 "Claude가 알아서 잘 골라쓰겠지"라고 생각했는데, 실제로는 두 가지 문제가 있다.

- **description이 일반적이거나 영어로만 쓰인 경우** → 한국어 발화에 트리거 매칭이 약하다.
- **동일 기능이 여러 플러그인에 중복 존재** → 어느 걸 골라야 할지 판단이 모호하다. (예: `fe-architect`가 jineee/smiledragon 양쪽에 존재)

→ "라우팅 규칙"을 명시적으로 적어주면 이 모호함이 사라진다.

### 2. CLAUDE.md는 "라우팅 힌트"로 쓰는 게 가장 효과적이다

단순 카탈로그 복붙은 노이즈만 늘리지만, **언제/왜 그 에이전트·스킬을 써야 하는지** 적으면 가치가 분명해진다. 프로젝트 CLAUDE.md에 이미 이런 패턴이 있었다:

```
- /sds-system-v2 — mkt24 퍼블리싱 작업 시 반드시 먼저 참조. SDS 디자인 시스템 가이드
```

이 한 줄이 "퍼블리싱이 들어오면 무조건 이 스킬부터" 라는 강제 라우팅을 만든다.

### 3. import 분리로 도메인별 관리

`~/.claude/CLAUDE.md`에 모두 적기보다는, RTK/TIL처럼 **도메인별 파일로 쪼개고 `@filename.md` 로 import**하는 게 깔끔하다.

```text
~/.claude/
├── CLAUDE.md          # @RTK.md @TIL.md @AGENTS.md @SKILLS.md
├── AGENTS.md          # 에이전트 라우팅
└── SKILLS.md          # 스킬 라우팅
```

장점:
- 수정/추가할 때 한 파일만 건드리면 된다
- 컨텍스트 비용 분리가 가능 (필요한 도메인만 import)
- diff가 깔끔해 무엇이 바뀌었는지 한눈에 보인다

### 4. "안티 패턴" 섹션이 우선순위보다 강력하다

우선순위만 적으면 "그 다음엔 뭐 써?"가 애매해진다. **명시적으로 "이건 쓰지 마"** 를 적으면 중복 기능 회피가 확실해진다.

```markdown
## 안티 패턴
- smiledragon-fe-plugin:* 에이전트는 사용하지 않는다 (jineee에 동등 기능 존재).
- superpowers:code-reviewer 사용하지 않는다 → jineee-plugin:senior-frontend-reviewer 사용.
```

### 5. 자동 주입 vs 수동 호출 구분

`jineee-plugin:using-jineee-skills` 같은 메타-스킬은 세션 시작 시 자동 주입된다. 이걸 또 수동으로 `Skill` 호출하면 중복 컨텍스트 낭비. SKILLS.md에는:

> 자동 주입되는 `using-*` 스킬을 수동 `Skill` 호출로 다시 띄우지 않는다.

같이 명시해 두는 게 좋다.

### 6. 우선순위 충돌 케이스 미리 적어두기

글로벌 SKILLS.md에 "jineee 우선"이라고 적어도, 프로젝트 CLAUDE.md에 "`/sds-system-v2`를 반드시 먼저 참조"라고 명시한 경우는 어떻게 해야 할까? 충돌 케이스는 한 줄로 못박아 두면 끝난다:

> 프로젝트 CLAUDE.md에 "반드시 먼저 참조" 명시된 스킬은 jineee 우선 규칙보다 **앞선다**.

## 왜 중요한가

- **회사 환경에서 일관된 에이전트 선택**: 팀이 여러 플러그인을 깔아둔 상태라도, 글로벌 라우팅 규칙이 있으면 "어떤 에이전트가 호출됐는지"가 예측 가능해진다.
- **새 플러그인 도입 시 의사결정 비용 절감**: AGENTS.md 하나만 업데이트하면 모든 프로젝트에 즉시 반영된다.
- **컨텍스트 비용 통제**: 자주 안 쓰는 도메인은 import에서 빼두면 시스템 프롬프트가 가벼워진다.

## 다음에 써먹을 곳

- 새 회사·프로젝트 합류 시 → 첫날 `~/.claude/AGENTS.md`에 그 회사 컨벤션의 에이전트 우선순위 적기
- 팀에 표준 라우팅을 배포하고 싶을 때 → AGENTS.md/SKILLS.md를 사내 가이드로 공유 (개인 글로벌은 그대로 두고)
- 에이전트 호출이 빗나갈 때 → description 수정 대신 라우팅 파일에 트리거 보강 (한국어 키워드 명시)

## 적용 결과 구조

```text
~/.claude/CLAUDE.md
@RTK.md
@TIL.md
@AGENTS.md     # 신규
@SKILLS.md     # 신규
```

AGENTS.md / SKILLS.md 둘 다 공통 구조:
1. **우선순위 규칙** (jineee-plugin 우선)
2. **주력 도구 트리거 테이블** (상황 → 에이전트/스킬)
3. **보조 도구 라우팅** (jineee에 없을 때)
4. **안티 패턴** (쓰지 말 것 + 대체)
