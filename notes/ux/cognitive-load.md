---
title: Cognitive Load (인지 부하)
date: 2026-05-04
category: ux
tags: [ux-laws, cognitive-load, working-memory, information-architecture]
summary: 인터페이스를 이해·조작하는 데 필요한 정신 자원의 양. intrinsic(목적 정보)과 extraneous(불필요한 노이즈)로 나뉨. 후자를 줄이는 게 디자인의 일.
---

# Cognitive Load

> "The amount of mental resources needed to understand and interact with an interface."

Miller's Law와 밀접한 심리학 개념.

## 한 줄 정리

화면을 이해하는 데 드는 "머리 용량". 한도를 넘으면 사용자는 디테일을 놓치고 압도당한다.

## 핵심 원리

1. 입력되는 정보량이 처리 용량을 넘으면 → 작업이 어려워지고, 디테일을 놓치고, 압도감을 느낀다.
2. **Intrinsic load** (본질적 부하): 사용자가 목적 달성에 필요한 정보를 들고 다니고, 새 정보를 흡수하고, 목표를 추적하는 데 드는 노력. 줄이기 어려움.
3. **Extraneous load** (외재적 부하): UI의 산만한·불필요한 요소가 추가로 잡아먹는 자원. 디자인이 줄여야 할 부분.

## 출처

John Sweller, "Cognitive Load Theory, Learning Difficulty, and Instructional Design" (1988). George Miller의 정보 처리 이론을 확장한 학습 디자인 연구에서 시작.

## 적용

- 한 화면에 노출되는 콘텐츠·액션 수를 줄인다.
- 폼: 자동완성·기본값·인라인 검증으로 사용자가 들고 다닐 정보를 줄인다.
- 시각 노이즈(과한 컬러·애니메이션·장식 요소) 제거.
- "사용자가 직전 화면에서 본 것을 외워야 다음 단계가 가능"하면 위험 신호 → 컨텍스트를 화면에 끌고 와라 (e.g. 스텝퍼 + 직전 입력 요약).

## 관련 법칙

- [`millers-law.md`](./millers-law.md)
- [`working-memory.md`](./working-memory.md)
- [`chunking.md`](./chunking.md)
- [`hicks-law.md`](./hicks-law.md)

## 참고

- 원문: https://lawsofux.com/cognitive-load/
- NNg, "Minimize Cognitive Load to Maximize Usability"
- Sweller (1988), Cognitive Load Theory
