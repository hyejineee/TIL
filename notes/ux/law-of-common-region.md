---
title: Law of Common Region (공통 영역의 법칙)
date: 2026-05-04
category: ux
tags: [ux-laws, gestalt, grouping, visual-hierarchy]
summary: 명확한 경계 안에 있는 요소들은 한 그룹으로 인식된다. 카드·박스·배경색이 가장 흔한 구현. 관계 표현의 가장 강한 신호.
---

# Law of Common Region

> Elements tend to be perceived into groups if they are sharing an area with a clearly defined boundary.

게슈탈트 그룹핑 원리 5종(Proximity, Similarity, Continuity, Closure, Connectedness) 중 하나의 변형.

## 한 줄 정리

같은 박스·같은 배경 안에 있으면, 사람은 자동으로 "한 그룹"이라고 본다.

## 핵심 원리

1. 공통 영역(common region)은 명확한 구조를 만들고, 사용자가 요소·섹션 간 관계를 빠르게 이해하게 한다.
2. **테두리(border)**를 추가하는 것이 공통 영역을 만드는 가장 쉬운 방법.
3. **배경색·배경 패턴**으로도 공통 영역을 만들 수 있다.

## 출처

게슈탈트 심리학자들이 제안한 그룹핑 원리(Prägnanz)에서 파생. 마음은 자극에서 패턴을 발견하려는 본능적 경향이 있다는 가설.

## 적용

- **카드 UI**: 카드 한 장 = 한 단위. 그 안의 모든 정보는 같은 객체에 속함을 자동으로 전달.
- **알림·토스트**: 배경색 + 라운드 박스로 본문과 분리.
- **폼 그룹**: 관련 필드들을 fieldset이나 배경 박스로 묶기.
- **다크모드 분리**: 카드 배경을 살짝 밝게/어둡게 해서 surface 위계 표현.
- **테이블 줄**: zebra striping (alternating row backgrounds)으로 행을 구분.

## 주의사항

너무 많은 공통 영역(중첩 박스)은 시각 노이즈가 된다. 위계를 정리하고 한 레벨에서만 사용.

## 관련 법칙

- [`law-of-proximity.md`](./law-of-proximity.md)
- [`law-of-similarity.md`](./law-of-similarity.md)
- [`law-of-uniform-connectedness.md`](./law-of-uniform-connectedness.md)
- [`law-of-pragnanz.md`](./law-of-pragnanz.md)

## 참고

- 원문: https://lawsofux.com/law-of-common-region/
- Aurora Harley, "The Principle of Common Region" — NNg
- Steven Bradley, "Visual Perception And The Principles Of Gestalt" — Smashing
