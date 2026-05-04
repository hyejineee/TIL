---
title: Occam's Razor (오컴의 면도날)
date: 2026-05-04
category: ux
tags: [ux-laws, simplicity, design-principles, minimalism]
summary: 같은 결과를 내는 가설 중 가정이 가장 적은 걸 선택. 디자인에서는 "더 뺄 게 없을 때 완성"이라는 미니멀리즘 원칙.
---

# Occam's Razor

> "Among competing hypotheses that predict equally well, the one with the fewest assumptions should be selected."

## 한 줄 정리

가장 단순한 설명·디자인이 일반적으로 옳다. 복잡함은 정당화되어야 한다 — 디폴트는 빼는 것.

## 핵심 원리

1. 복잡함을 줄이는 가장 좋은 방법은 처음부터 도입하지 않는 것.
2. 각 요소를 분석하고, 전체 기능을 해치지 않는 한 최대한 제거한다.
3. **더 이상 뺄 게 없을 때**가 완성이다.

## 출처

William of Ockham (c. 1287–1347), 영국 프란치스코회 수사·철학자. 라틴어 *lex parsimoniae* (검약의 법칙).

## 적용

- **디자인 리뷰 체크리스트**: 화면의 각 요소가 사용자 목표에 직접 기여하는지? 안 그러면 제거 후보.
- **CTA**: 한 화면에 primary action 1개. 추가하기 전에 정말 필요한지 검토.
- **폼**: 모든 필드가 필수인지? 옵션은 진짜 옵션인지? 빼도 되는 건 빼라.
- **컴포넌트 prop**: API 디자인에서도 동일. 디폴트로 처리되는 prop은 노출하지 말 것.
- **에러 메시지**: 사용자가 다음 행동을 결정하는 데 필요한 정보만. "Stack trace는 콘솔에" 패턴.

## 주의사항

단순함이 본질을 깎는 수준이 되면 안 된다 — Tesler's Law 참조. 복잡함은 어딘가로 옮길 수 있을 뿐, 0이 되지는 않는다.

## 관련 법칙

- [`teslers-law.md`](./teslers-law.md)
- [`hicks-law.md`](./hicks-law.md)
- [`law-of-pragnanz.md`](./law-of-pragnanz.md)
- [`pareto-principle.md`](./pareto-principle.md)

## 참고

- 원문: https://lawsofux.com/occams-razor/
- Farnam Street, "How to Use Occam's Razor Without Getting Cut"
- IxDF, "Occam's Razor: The Simplest Solution is Always the Best"
