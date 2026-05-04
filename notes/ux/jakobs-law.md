---
title: Jakob's Law (야콥의 법칙)
date: 2026-05-04
category: ux
tags: [ux-laws, mental-model, conventions, familiarity]
summary: 사용자는 다른 사이트에서 시간을 더 많이 보낸다. 그래서 당신 사이트도 그들이 이미 아는 사이트처럼 작동하길 기대한다. 컨벤션을 깰 때는 비용을 정당화해야 한다.
---

# Jakob's Law

> "Users spend most of their time on other sites. This means that users prefer your site to work the same way as all the other sites they already know."

## 한 줄 정리

당신의 UI를 처음 보는 사람도, 이미 머릿속에 "수많은 다른 사이트가 어떻게 동작하는지"의 평균값을 가지고 있다. 그 평균에 맞추면 학습 비용이 0에 가까워진다.

## 핵심 원리

1. 사용자는 익숙한 제품에서 만든 기대치를, 비슷해 보이는 인터페이스에 그대로 적용한다.
2. 기존 멘탈 모델에 맞추면 사용자는 새 패러다임을 학습하는 대신 작업 자체에 집중할 수 있다.
3. 인터페이스를 변경할 때는 **친숙한 버전을 일시적으로 유지**해 충격을 줄인다.

## 출처

Jakob Nielsen (Nielsen Norman Group 공동 창립자). "Discount usability engineering" 운동을 시작했고, heuristic evaluation을 만든 사람.

## 적용

- **폼 컨트롤**: 토글·라디오·버튼 등은 물리적 원형의 디자인을 따른다 — 이미 알고 있는 멘탈 모델 활용.
- **장바구니 아이콘** = 우상단, **검색 아이콘** = 돋보기, **햄버거** = 모바일 메뉴 — 지키는 게 디폴트.
- **YouTube 2017 Material Design 리뉴얼**: 새 UI를 미리 보고 피드백 + 이전 버전 복귀 옵션 제공 → 전환 충격 완화.
- 디자인 시스템 컴포넌트는 인더스트리 표준(Material/HIG)을 기준선으로 삼고, 그 위에서만 차별화.

## 주의사항

컨벤션을 깰 때는 명확한 가치 제공이 있어야 한다. "신선해 보이려는" 차별화는 학습 비용만 만든다. 깰 거라면 onboarding·tooltip으로 보완.

## 관련 법칙

- [`mental-model.md`](./mental-model.md)
- [`aesthetic-usability-effect.md`](./aesthetic-usability-effect.md)
- [`paradox-of-the-active-user.md`](./paradox-of-the-active-user.md)

## 참고

- 원문: https://lawsofux.com/jakobs-law/
- NNg, "Jakob's Law of Internet User Experience"
- NNg, "Top 10 Mistakes in Web Design"
