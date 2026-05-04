---
title: Working Memory (작업 기억)
date: 2026-05-04
category: ux
tags: [ux-laws, working-memory, cognitive-load, recognition]
summary: 4-7개 청크를 20-30초만 들고 있을 수 있는 임시 메모리. 사용자가 외우게 하지 말고 화면에 들고 다녀라. 회상보다 재인을 활용하라.
---

# Working Memory

> A cognitive system that temporarily holds and manipulates information needed to complete tasks.

Cognitive Load와 밀접히 연관.

## 한 줄 정리

작업 기억은 4-7개 청크를 20-30초만 들고 있는 휘발성 메모리. 디자인은 사용자 머릿속이 아니라 화면이 정보를 들고 있게 해야 한다.

## 핵심 원리

1. **용량 한계**: 4-7개 청크, 각 청크는 20-30초 후 사라진다. 화면에 표시되는 정보가 정말 필요하고 관련된 것인지 확인.
2. **회상보다 재인 (Recognition over recall)**: 뇌는 본 적 있는 정보를 알아보는 데 강하지만, 새 정보를 외우는 데는 약하다. 방문한 링크 색 차별화, 빵부스러기(breadcrumb)로 재인을 지원.
3. **시스템에 위임**: 사용자가 외워야 할 부담을 인터페이스로 옮긴다 — 이전 화면 정보를 다음 화면에 캐리, 비교 테이블 등.

## 출처

용어는 Miller, Galanter, Pribram (1960년대). Atkinson & Shiffrin (1968) 이 "short-term store"로 형식화. 1세기 전 Hitzig·Ferrier의 전전두피질 절제 연구가 신경 기반 출발점.

## 적용

- **방문한 링크 차별화**: 색 변화로 "이미 본 곳"을 재인하게 한다.
- **빵부스러기(breadcrumb)**: 현재 위치를 외우지 않게.
- **검색어 보존**: 결과 페이지에서도 검색어가 input에 남아 있어야.
- **컨텍스트 캐리**: 멀티스텝 폼에서 이전 단계 입력값을 요약 표시.
- **Auto-suggest**: 사용자가 외워서 입력하지 않게 후보 제공.
- **선택 가능 옵션 노출**: 콤보박스에서 사용자가 옵션을 외워서 입력하게 하지 말 것.
- **비교 테이블**: 사용자가 머릿속으로 비교하지 않도록 모든 옵션을 한 화면에.

## 관련 법칙

- [`millers-law.md`](./millers-law.md)
- [`cognitive-load.md`](./cognitive-load.md)
- [`chunking.md`](./chunking.md)

## 참고

- 원문: https://lawsofux.com/working-memory/
- Raluca Budiu, "Working Memory and External Memory" — NNg
- Atkinson & Shiffrin (1968)
