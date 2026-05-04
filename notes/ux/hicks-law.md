---
title: Hick's Law (힉의 법칙)
date: 2026-05-04
category: ux
tags: [ux-laws, decision-making, cognitive-load, navigation]
summary: 의사결정 시간은 선택지의 수·복잡도에 비례해 늘어난다. 옵션 줄이고, 추천 강조하고, 점진적 노출하라. 단, 추상화 수준까지 단순화하진 말 것.
---

# Hick's Law

> The time it takes to make a decision increases with the number and complexity of choices.

## 한 줄 정리

선택지가 많을수록 결정 시간이 길어진다. 응답 속도가 중요한 곳일수록 선택지를 좁혀라.

## 핵심 원리

1. **응답 시간이 중요한 곳**에서는 선택지를 최소화한다.
2. 복잡한 작업은 **작은 단계로 분리**해 인지 부하를 낮춘다.
3. **추천 옵션을 시각적으로 강조**해 사용자를 압도하지 않는다.
4. **점진적 온보딩**으로 신규 사용자의 인지 부하를 낮춘다.
5. 추상화 수준까지 단순화하지는 말 것.

## 출처

William Edmund Hick & Ray Hyman (1952). Hick-Hyman Law. 자극의 수와 반응 시간의 관계 연구. 사용자가 선택지를 해석·평가하는 데 시간이 든다는 것을 보임.

## 적용

- **Google 홈페이지**: 검색이라는 핵심 작업 외 모든 결정 요소를 제거.
- **Apple TV 리모컨**: 인터페이스 복잡도를 TV 본체로 옮기고 리모컨은 최소 버튼.
- **Slack 점진적 온보딩**: 봇으로 메시징을 가르치고, 고급 기능은 사용자가 기본을 익힐 때까지 숨김.
- **CTA**: primary 1개 + secondary 1개. 3개부터 의사결정 비용 급증.
- **메뉴**: 1차 메뉴 항목은 5±2개. 그 이상이면 카테고리화·계층화.

## 주의사항

지나친 단순화는 기능 자체를 빼앗는다. "선택지를 보이지 않게 하는 것"과 "선택지 자체를 없애는 것"은 다르다. 고급 사용자에게는 점진적 disclosure로 풀어준다.

## 관련 법칙

- [`choice-overload.md`](./choice-overload.md)
- [`fitts-law.md`](./fitts-law.md)
- [`millers-law.md`](./millers-law.md)
- [`teslers-law.md`](./teslers-law.md)

## 참고

- 원문: https://lawsofux.com/hicks-law/
- Mads Soegaard, IxDF
- Wikipedia: Hick's Law
