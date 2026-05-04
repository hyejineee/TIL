---
title: Miller's Law (밀러의 법칙)
date: 2026-05-04
category: ux
tags: [ux-laws, working-memory, chunking, cognitive-load]
summary: 작업 기억 한계는 7±2개. 이 숫자를 디자인 제약으로 오용하지 말 것 — 핵심은 "청킹으로 처리 가능한 단위로 묶어라"다.
---

# Miller's Law

> "The average person can only keep 7 (plus or minus 2) items in their working memory."

## 한 줄 정리

사람은 한 번에 5~9개 정도만 머릿속에 들고 있을 수 있다. 단, 이 숫자를 "메뉴는 7개까지" 같은 규칙으로 곡해하면 안 된다.

## 핵심 원리

1. **"매직 넘버 7"을 불필요한 디자인 제약의 정당화**로 쓰지 말 것.
2. 콘텐츠를 작은 청크로 묶어 사용자가 쉽게 처리·이해·기억할 수 있도록 한다.
3. 단기 기억 용량은 사전 지식·맥락에 따라 개인마다 달라진다.

## 출처

George Miller (1956), "The Magical Number Seven, Plus or Minus Two." 정보 이론(bit, channel capacity)을 인지 심리에 도입한 기념비적 논문.

## 적용

- **청킹**: 16자리 카드 번호를 4-4-4-4로, 전화번호 010-1234-5678로.
- **그룹 메뉴**: 항목이 7개를 넘으면 카테고리·sub-menu로 분리.
- **멀티스텝 폼**: 한 화면 정보량 < 작업 기억 한계.
- **비교 테이블**: 옵션이 7±2를 넘으면 필터·접기로 동시에 보이는 수를 줄인다.
- **단축키 안내**: 한 번에 5~7개씩 그룹화해서 cheatsheet 구성.

## 주의사항

"7±2"는 마법의 숫자가 아니다. 진짜 한계는 **사용자가 들고 있어야 하는 정보 단위의 인지 부하**. 친숙한 청크(자주 쓰는 회사명·로고)는 한 단위로 처리되므로 더 많이 담을 수 있다.

## 관련 법칙

- [`chunking.md`](./chunking.md)
- [`working-memory.md`](./working-memory.md)
- [`cognitive-load.md`](./cognitive-load.md)
- [`choice-overload.md`](./choice-overload.md)

## 참고

- 원문: https://lawsofux.com/millers-law/
- Miller (1956), 원본 논문
- Khan Academy, "Miller's Law, Chunking, and the Capacity of Working Memory"
