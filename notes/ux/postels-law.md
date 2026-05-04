---
title: Postel's Law (포스텔의 법칙 / Robustness Principle)
date: 2026-05-04
category: ux
tags: [ux-laws, robustness, input-validation, accessibility]
summary: 받을 때는 관대하게, 보낼 때는 엄격하게. 사용자 입력의 다양성을 받아들이고 시스템 요구에 맞게 변환하라. 회복탄력적 디자인의 기초.
---

# Postel's Law

> "Be liberal in what you accept, and conservative in what you send."

Robustness Principle이라고도 함.

## 한 줄 정리

사용자가 입력하는 다양한 형태를 받아들이되, 시스템이 내보내는 건 정확하고 일관되게. 이게 회복탄력적 시스템의 기초.

## 핵심 원리

1. 사용자의 다양한 행동·입력에 공감과 유연성·관용을 보인다.
2. 다양한 입력·접근 패턴·사용자 능력을 예상하면서도 신뢰성과 접근성을 유지한다.
3. 폭넓은 예상과 계획이 전체적 회복탄력성을 만든다.
4. 사용자 입력을 받아 시스템 요구에 맞게 변환하고, 명확한 입력 경계와 투명한 피드백을 제공한다.

## 출처

Jon Postel — 인터넷 파이어니어. 원래는 TCP 디자인 가이드라인: "TCP 구현은 robustness 원칙을 따라야 한다 — 보낼 때는 보수적으로, 받을 때는 관대하게." 의미가 명확하다면 비표준 입력도 받아들이라.

## 적용

- **전화번호 폼**: `010-1234-5678`, `01012345678`, `+82 10 1234 5678` 모두 받고 내부에서 정규화.
- **이메일 trim**: 앞뒤 공백 자동 제거.
- **검색**: 오타·대소문자·띄어쓰기 차이를 흡수 (fuzzy match).
- **날짜 입력**: `2026-05-04`, `2026/5/4`, `5/4/2026` 모두 인식.
- **주소**: 한 줄 / 여러 줄 / 우편번호 누락도 가능한 한 처리.
- **파일 업로드**: 다양한 포맷 받아서 표준 포맷으로 변환.
- **API 응답**: 하지만 API가 *반환*하는 건 엄격한 스키마 — 클라이언트 호환성을 위해.

## 주의사항

받기에 너무 관대하면 보안 취약점이 될 수 있다. 의미를 파악할 수 있는 범위까지만 관대하고, 그 외는 명확한 에러 메시지로.

## 관련 법칙

- [`tesler's-law.md`](./teslers-law.md)
- [`occams-razor.md`](./occams-razor.md)

## 참고

- 원문: https://lawsofux.com/postels-law/
- Mark Boulton, "Design Systems and Postel's Law"
- Wikipedia: Robustness Principle
