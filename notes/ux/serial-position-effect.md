---
title: Serial Position Effect (계열 위치 효과)
date: 2026-05-04
category: ux
tags: [ux-laws, memory, navigation, list-design]
summary: 시퀀스의 처음(primacy)과 끝(recency) 항목이 가장 잘 기억된다. 핵심 액션은 양 끝에, 덜 중요한 건 가운데에 배치하라.
---

# Serial Position Effect

> Users have a propensity to best remember the first and last items in a series.

## 한 줄 정리

리스트의 양 끝이 잘 기억된다. **Primacy effect**(처음)와 **Recency effect**(끝). 가운데는 잊혀진다.

## 핵심 원리

1. **덜 중요한 항목은 가운데**에 배치한다. 가운데는 장기 기억·작업 기억에 덜 저장된다.
2. 내비게이션 같은 요소에서 **핵심 액션은 양 끝(왼쪽/오른쪽)에 배치**해 기억을 강화한다.

## 출처

Hermann Ebbinghaus가 명명. Apple, Electronic Arts, Nike 같은 회사가 디자인 전략에 활용.

## 적용

- **상단 내비게이션**: 가장 중요한 항목(브랜드/홈, 회원가입/로그인)을 양 끝에. 가운데는 보조 메뉴.
- **모바일 탭바**: 가장 자주 쓰는 탭을 양 끝에 — 손가락 도달성과 기억 효과 결합.
- **CTA 정렬**: 한 줄에 여러 버튼이 있으면 primary는 오른쪽 끝(영문 LTR 기준).
- **검색 결과**: 첫 결과가 압도적으로 클릭됨 (primacy + 시각 위계 결합).
- **튜토리얼·온보딩**: 첫 단계와 마지막 단계의 인상을 가장 정성스럽게.
- **스토리텔링**: 강조하고 싶은 메시지는 처음·마지막에.

## 주의사항

primacy/recency는 단기 기억 측면. 사용 빈도(Fitts's Law의 thumb zone과 결합), 사용자 흐름과 함께 고려.

## 관련 법칙

- [`peak-end-rule.md`](./peak-end-rule.md)
- [`cognitive-bias.md`](./cognitive-bias.md)
- [`chunking.md`](./chunking.md)

## 참고

- 원문: https://lawsofux.com/serial-position-effect/
- IxDF, "Serial Position Effect"
- Wikipedia: Serial-position effect
