---
title: Selective Attention (선택적 주의)
date: 2026-05-04
category: ux
tags: [ux-laws, attention, banner-blindness, change-blindness]
summary: 사람은 환경의 일부 자극에만 주의를 집중한다. Banner blindness·change blindness 같은 함정을 인지하고 디자인해야 한다.
---

# Selective Attention

> The process of focusing our attention only to a subset of stimuli in an environment — usually those related to our goals.

## 한 줄 정리

사람은 모든 것에 주의를 기울이지 않고, 자기 목적과 관련된 자극만 본다. 디자이너는 그 주의의 흐름을 안내해야 한다.

## 핵심 원리

1. **정보 필터링**: 사람은 무관한 정보를 걸러내고 중요한 것에 집중한다. 디자인은 사용자 주의를 안내하고, 압도와 산만함을 막고, 관련 정보·액션의 발견을 돕는다.
2. **Banner Blindness**: 사용자는 배너처럼 생긴 콘텐츠를 의식적·무의식적으로 무시한다. 일반 콘텐츠를 광고처럼 디자인하지 말고, 광고와 비슷한 위치에 두지 말 것.
3. **Change Blindness**: 인터페이스에 의미 있는 변화가 일어나도, 주의 용량 한계와 시각 신호 부족으로 사용자가 인지 못 할 수 있다. 동시에 일어나는 변화들이 서로 주의를 빼앗는지 검토.

## 출처

20세기 중반 형성:
- Donald Broadbent, Filter Theory (1958) — 주의의 "병목" 가설
- Cherry, Cocktail Party Effect (1953)
- Anne Treisman, Attenuation Model (1960)
- Kahneman (1973) — 주의를 분할 가능한 제한된 자원으로 봄

## 적용

- **광고와 콘텐츠 분리**: 진짜 콘텐츠를 광고처럼 디자인하지 말 것 (배너 모양·자동재생·강한 색상 + 닫기 버튼 → 사용자가 광고로 분류해 무시).
- **중요 변화 강조**: 폼 검증 에러는 단순 색 변화가 아니라 명시적 텍스트 + 위치 이동·shake 애니메이션 등 다중 신호.
- **시선 흐름 디자인**: F-pattern, Z-pattern을 의식해 핵심 정보 배치.
- **광고 위치**: 주요 콘텐츠와 명확히 분리된 영역에.
- **알림**: 사용자가 인지하지 못하는 변화를 만들지 말 것 — 토스트·badge로 알림.

## 주의사항

지나치게 강조 요소를 많이 쓰면 → 모든 것이 강조 → 아무것도 강조 안 됨. Von Restorff와 결합해 신중하게.

## 관련 법칙

- [`von-restorff-effect.md`](./von-restorff-effect.md)
- [`cognitive-load.md`](./cognitive-load.md)
- [`chunking.md`](./chunking.md)

## 참고

- 원문: https://lawsofux.com/selective-attention/
- Raluca Budiu, "Change Blindness in UX" — NNg
- Kara Pernice, "Banner Blindness Revisited" — NNg
