---
title: Paradox of the Active User (활동적 사용자의 역설)
date: 2026-05-04
category: ux
tags: [ux-laws, onboarding, documentation, contextual-help]
summary: 사용자는 매뉴얼을 읽지 않고 바로 쓰기 시작한다. 도움말은 별도 페이지가 아니라 사용 흐름 안에 끼워넣어야 한다.
---

# Paradox of the Active User

> Users never read manuals but start using the software immediately.

## 한 줄 정리

사용자는 문서를 읽지 않는다. 도움말이 필요하면 그들이 있는 자리(작업 흐름 안)로 가져가야 한다.

## 핵심 원리

1. **작업 우선**: 사용자는 문서 학습보다 즉시 작업 완료를 우선시한다.
2. **장기 학습 기회**: 학습 시간이 결국 절약하는데도, 사람들은 그러지 않는다.
3. **컨텍스트 가이드**: 도움말을 제품 경험 안에 통합한다 — tooltip, 인라인 힌트, contextual onboarding.

## 출처

Mary Beth Rosson & John Carroll (1987), *Interfacing thought: cognitive aspects of human-computer interaction*. "신규 사용자는 매뉴얼을 읽지 않고 바로 쓰기 시작한다 — 에러가 나고 막혀도."

## 적용

- **빈 상태(empty state)**: 처음 들어왔을 때 "뭘 해야 하지?"가 보여야 한다 — 설명 + sample data + CTA.
- **인라인 hint**: 입력 필드 placeholder, 아이콘 옆 tooltip.
- **점진적 disclosure**: 처음에 모든 기능을 보여주지 말고, 사용 흐름에 따라 노출.
- **Contextual onboarding**: 사용자가 특정 액션을 처음 만났을 때 그 자리에서 가이드.
- **에러 복구**: 에러 메시지가 곧 도움말 — "이렇게 고치세요"까지 포함.
- **별도 docs는 보조**: 외부 문서는 검색 가능해야 하고, 제품 안에서 deep link로 연결.

## 관련 법칙

- [`jakobs-law.md`](./jakobs-law.md)
- [`mental-model.md`](./mental-model.md)
- [`occams-razor.md`](./occams-razor.md)

## 참고

- 원문: https://lawsofux.com/paradox-of-the-active-user/
- NNg, "Paradox of the Active User"
- Rosson & Carroll (1987)
