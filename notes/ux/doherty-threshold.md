---
title: Doherty Threshold (도허티 임계점)
date: 2026-05-04
category: ux
tags: [ux-laws, performance, response-time, perceived-performance]
summary: 시스템 응답이 400ms 이내일 때 사용자 생산성·몰입이 급격히 올라간다. 실제 속도가 어렵다면 progress·skeleton·animation으로 perceived performance를 만든다.
---

# Doherty Threshold

> "Productivity soars when a computer and its users interact at a pace (<400ms) that ensures that neither has to wait on the other."

## 한 줄 정리

400ms. 이 안에 응답하면 "기계가 나를 기다리게 하지 않는다"고 느끼고, 사용자는 몰입한다.

## 핵심 원리

1. **400ms 안에 피드백을 줘라** — 주의를 유지하고 생산성을 끌어올린다.
2. **Perceived performance**를 활용해 응답 시간을 단축된 것처럼 느끼게 한다.
3. **애니메이션**은 백그라운드 처리 중 사용자를 시각적으로 잡아둔다.
4. **프로그레스 바**는 정확하지 않아도 대기를 견딜 만하게 만든다.
5. 어떤 경우엔 **의도적으로 지연을 추가**해 신뢰감을 만든다 (예: 보안 검사·매칭).

## 출처

Walter J. Doherty & Ahrvind J. Thadani, IBM Systems Journal (1982). 기존 표준이던 2000ms를 400ms로 끌어내림. 이 임계 안에서 응답하는 앱은 "중독적(addicting)"으로 분류되었다.

## 적용

- 버튼 클릭 → 100ms 안에 visual feedback (`:active`, ripple).
- 1초 이상 걸리는 작업 → 즉시 skeleton/spinner 표시.
- 데이터 로드: optimistic UI로 "이미 처리된 것처럼" 보이게.
- 정말 느린 작업 (3초+): progress bar + 단계별 메시지 ("결제 확인 중...", "주문서 전송 중...").
- 의도적 지연: 인증·매칭 같은 결과에 신뢰감이 필요한 경우 1-2초 의도 지연이 효과적.

## 관련 법칙

- [`flow.md`](./flow.md)
- [`fitts-law.md`](./fitts-law.md)
- [`goal-gradient-effect.md`](./goal-gradient-effect.md)

## 참고

- 원문: https://lawsofux.com/doherty-threshold/
- Doherty & Thadani (1982), IBM Systems Journal
- Brad A. Myers, "The importance of percent-done progress indicators"
