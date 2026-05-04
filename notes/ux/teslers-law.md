---
title: Tesler's Law (테슬러의 법칙 / Law of Conservation of Complexity)
date: 2026-05-04
category: ux
tags: [ux-laws, complexity, design-philosophy, system-design]
summary: 모든 시스템에는 줄일 수 없는 본질적 복잡도가 있다. 사라지지 않는 그 복잡도를 누가 감당할지(시스템/사용자) 선택하는 게 디자인.
---

# Tesler's Law

> Tesler's Law, also known as The Law of Conservation of Complexity, states that for any system there is a certain amount of complexity which cannot be reduced.

## 한 줄 정리

복잡도는 사라지지 않고 어딘가로 옮겨질 뿐이다. UI에서 빼면 사용자 머릿속으로, 사용자에서 빼면 시스템 안으로. 그 선택이 곧 디자인이다.

## 핵심 원리

1. 모든 프로세스에는 시스템이나 사용자가 감당해야 하는 **줄일 수 없는 복잡도**가 있다.
2. 디자이너는 본질적 복잡도를 **사용자가 아니라 시스템으로 옮기는** 걸 우선시해야 한다.
3. 합리적이지 않은 사용자 — 실제 사람들 — 을 가정하지 말 것. 사람은 예측 불가능하게 행동한다.
4. 사용자가 선택한 경로에서 컨텍스트별 가이드(tooltip 등)를 제공한다.

## 출처

Larry Tesler, Xerox PARC 1980년대 중반. Dan Saffer의 *Designing for Interaction*에 그의 인터뷰가 실려 있음. "엔지니어가 일주일을 더 써서 앱을 단순하게 만드는 게, 수백만 사용자에게 1분의 부담을 지우는 것보다 낫다." Bruce Tognazzini의 반론: "사람들은 자기 삶에서 복잡함이 줄어드는 것에 저항한다 — 단순해지면 더 복잡한 작업을 시도하기 시작한다."

## 적용

- **결제 폼**: 카드 번호에서 카드사 자동 인식, 만료일 형식 자동 변환. 사용자는 그냥 적기만.
- **검색**: 오타·대소문자·동의어 처리는 시스템이.
- **단위 변환**: 사용자가 입력한 단위를 자동 변환·표시.
- **에러 복구**: 입력이 형식에 안 맞으면 자동 보정 시도, 안 되면 명확한 가이드.
- **시간대 처리**: UTC 변환·표시는 시스템이.
- **상태 동기화**: 멀티 디바이스 동기화는 백엔드가 처리.

## 주의사항

복잡도를 시스템으로 다 옮기면 시스템 유지보수 비용이 폭증. 자주 일어나는 케이스는 시스템이 흡수, 드문 케이스는 명시적으로 사용자에게 노출 — Pareto와 결합.

## 관련 법칙

- [`occams-razor.md`](./occams-razor.md)
- [`hicks-law.md`](./hicks-law.md)
- [`pareto-principle.md`](./pareto-principle.md)
- [`postels-law.md`](./postels-law.md)

## 참고

- 원문: https://lawsofux.com/teslers-law/
- NNg, "Tesler's Law: Shift Complexity to Simplify UX"
- Wikipedia: Law of conservation of complexity
