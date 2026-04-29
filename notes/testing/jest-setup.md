---
title: Jest 프로젝트 세팅 기본
date: 2019-12-11
category: testing
tags: [jest, javascript, testing, node]
summary: npm init 후 package.json scripts에 test/watch 두 줄 추가. 테스트 구문은 test("이름", () => expect(동작).toBe(기대값)).
---

## 프로젝트 세팅

```bash
mkdir my-project && cd my-project
npm init -y
npm i -D jest
```

`package.json`의 `scripts`:

```json
"scripts": {
  "test": "jest",
  "watch": "jest --watchAll"
}
```

- `npm run test` — 테스트 1회 실행
- `npm run watch` — 파일 변경 시 자동 재실행

## 기본 테스트 구문

```js
test("테스트 이름", () => {
  expect(테스트할_동작()).toBe(원하는_결과값);
});
```

`expect(...)`의 matcher에 따라 검증 방식이 달라진다 (`toBe`, `toEqual`, `toHaveBeenCalled` 등).

## 다음에 써먹을 곳

새 JS 프로젝트 초기에 테스트 러너부터 붙여두면 TDD/리팩터 안전망이 초기부터 확보됨.
