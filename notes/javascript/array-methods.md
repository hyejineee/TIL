---
title: JavaScript 배열 메서드 — sort와 reduce
date: 2019-12-11
category: javascript
tags: [javascript, array]
summary: sort는 기본적으로 유니코드 정렬이라 숫자엔 compareFunction 필수. reduce는 4개 인자(acc, cur, idx, src)를 받고 중간 탈출은 별도 패턴 필요.
---

## Array.prototype.sort

정렬 순서를 정의하는 함수. 생략하면 배열은 각 요소의 문자열 변환에 따라 각 문자의 **유니코드 코드 포인트 값**에 따라 정렬된다.

→ 숫자 정렬을 하고 싶으면 뒤에 `compareFunction`을 추가해야 한다.

```js
[10, 2, 30].sort();                    // [10, 2, 30]  — 유니코드 정렬
[10, 2, 30].sort((a, b) => a - b);     // [2, 10, 30]  — 숫자 정렬
```

- [MDN — Array.sort](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)

## Array.prototype.reduce

리듀서 함수는 네 개의 인자를 받는다:

1. 누산기 (`acc`) — accumulator
2. 현재 값 (`cur`)
3. 현재 인덱스 (`idx`)
4. 원본 배열 (`src`)

리듀서의 반환 값은 누산기에 할당되고, 누산기는 순회 중 유지되므로 최종 결과는 하나의 값이 된다.

- [MDN — Array.reduce](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

### reduce 중간에 멈추기

`reduce` 자체엔 break가 없다. 대안으로 `some`/`every`를 쓰거나 플래그를 `acc`에 담아 조기 탈출을 시뮬레이션한다.

- [Stack Overflow — how to break on reduce method](https://stackoverflow.com/questions/36144406/how-to-break-on-reduce-method)
