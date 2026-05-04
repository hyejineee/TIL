---
title: Von Restorff Effect (폰 레스토르프 효과 / Isolation Effect)
date: 2026-05-04
category: ux
tags: [ux-laws, memory, visual-hierarchy, accessibility, emphasis]
summary: 비슷한 항목들 사이에서 다른 하나가 가장 잘 기억된다. 강조는 절제해서 — 색에만 의존하지 말고 모션도 신중히.
---

# Von Restorff Effect

> The Von Restorff effect, also known as The Isolation Effect, predicts that when multiple similar objects are present, the one that differs from the rest is most likely to be remembered.

## 한 줄 정리

비슷한 것들 사이에서 다른 하나가 튀어 보이고 더 오래 기억된다. 강조의 본질.

## 핵심 원리

1. 중요한 정보·핵심 액션은 시각적으로 차별화한다.
2. **강조는 절제**한다 — 강조 요소들이 서로 경쟁하지 않게, 광고로 오인되지 않게.
3. **색에만 의존하지 말 것** — 색맹·저시력 사용자를 배제할 수 있음.
4. **모션 사용에도 주의** — 모션 민감 사용자(전정 장애)에게 부담.

## 출처

독일 정신과 의사 Hedwig von Restorff (1906-1962). 1933년 연구에서 카테고리적으로 비슷한 항목 리스트에 한 개의 다른 항목을 끼워 넣으면 그 항목의 기억이 향상되는 것을 발견.

## 적용

- **Primary CTA**: 주변과 다른 색·크기·위치로 즉시 식별 가능하게.
- **선택 상태**: 선택된 카드/탭은 다른 것들과 명확히 차별화 (테두리·배경·체크).
- **Featured 콘텐츠**: 추천 항목은 배지·강조 박스로 구분 (Choice Overload 완화).
- **에러·경고**: 색 + 아이콘 + 텍스트 다중 신호 (접근성).
- **상태 표시**: 새 항목 dot, 변경된 항목 표시.
- **다중 신호 원칙**: 색 + 모양/아이콘 + 텍스트 + 위치 → 색맹 사용자도 인지 가능.

## 주의사항

- 강조 요소가 너무 많으면 모두 평준화돼 강조 효과가 사라진다.
- 광고와 비슷한 디자인은 banner blindness 유발 (Selective Attention 참조).
- 깜빡임·강한 모션은 발작·전정 장애 유발 가능 — `prefers-reduced-motion` 미디어 쿼리 존중.

## 관련 법칙

- [`selective-attention.md`](./selective-attention.md)
- [`law-of-similarity.md`](./law-of-similarity.md)
- [`cognitive-bias.md`](./cognitive-bias.md)

## 참고

- 원문: https://lawsofux.com/von-restorff-effect/
- Wikipedia: Von Restorff effect
- von Restorff (1933) 원본 연구
