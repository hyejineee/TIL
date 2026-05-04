---
title: Fitts's Law (피츠의 법칙)
date: 2026-05-04
category: ux
tags: [ux-laws, motor-control, click-target, mobile, accessibility]
summary: 타겟 도달 시간은 거리와 크기의 함수. 버튼은 충분히 크게, 손가락 동선에 가깝게, 사이 간격을 두고 배치한다.
---

# Fitts's Law

> "The time to acquire a target is a function of the distance to and size of the target."

## 한 줄 정리

타겟이 멀고 작을수록 누르기 어렵다. 클수록 가까울수록 빠르고 정확하다.

## 핵심 원리

1. 터치 타겟은 사용자가 정확히 선택할 수 있을 만큼 **충분히 커야** 한다.
2. 타겟 사이에 **충분한 여백**을 둔다 (오터치 방지).
3. 타겟은 사용자가 **쉽게 닿을 수 있는 영역**에 배치한다.

## 출처

Paul Fitts (1954), 인간 운동 시스템 연구. 타겟까지 이동 시간이 거리와 크기의 함수라는 것, 그리고 빠른 동작은 작은 타겟에서 정확도가 떨어진다는 속도-정확도 트레이드오프를 입증.

## 적용

- 모바일 터치 타겟: 최소 44×44pt (Apple HIG) / 48×48dp (Material). 손가락 평균 너비 기준.
- 데스크톱: 작은 아이콘 버튼이라도 클릭 가능 영역(hitbox)은 패딩으로 키운다.
- 자주 쓰는 버튼은 화면 가장자리·코너에 배치 (커서가 갈 수 있는 무한 거리). macOS 메뉴바가 화면 최상단인 이유.
- 모바일: 엄지 닿는 영역(thumb zone) 하단 1/3에 주요 액션. iOS의 탭바가 하단인 이유.
- 폼 submit 버튼: 입력 필드 바로 아래·근처. 멀리 두면 거리 비용 발생.
- 인접 버튼 사이 최소 8px gap (오터치 방지).

## 관련 법칙

- [`doherty-threshold.md`](./doherty-threshold.md)
- [`hicks-law.md`](./hicks-law.md)

## 참고

- 원문: https://lawsofux.com/fittss-law/
- Steven Hoober, "Design for Fingers, Touch, and People" — UX Matters
- NNg, "Fitts's Law and Its Applications in UX"
