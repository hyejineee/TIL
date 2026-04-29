---
title: React Hook 기본 — useState와 useEffect
date: 2019-12-13
category: react
tags: [react, hooks, useState, useEffect, frontend]
summary: Hook은 함수형 컴포넌트에서 state/생명주기 기능을 쓸 수 있게 하는 함수. useState는 상태 추가, useEffect는 componentDidMount/DidUpdate/WillUnmount 대체 + cleanup 반환 가능.
---

# React Hook!🌂

0. [React Hook?](#React-Hook)
1. [useState(), useEffect()](#useState-useEffect)

## React Hook?

* Read hook은 리액트에서 클래스를 사용하지 않고 함수형 컴포넌트를 만들어 사용할 때 클래스에서의 state와 생명주기와 같은 기능을 할 수 있도록 하는 함수이다.
* react hook을 사용해 클래스 없이 리액트 컴포넌트를 만들 수 있다.
* hook은 class에서 동작하지 않는다.

## useState(), useEffect()

### useState()

useState는 가장 기본적인 Hook으로서, 함수형 컴포넌트에 state를 추가할 수 있게 해준다. (~~이전에 함수형 컴포넌트에서 state를 추가하려면 함수형 컴포넌트를 class로 바꿔야 했다.~~)

* useState()의 사용 :

  * useState()로 state 선언하기 

  * useState 여러 번 사용 : 하나의 useState 함수는 하나의 상태 값만 관리를 할 수 있기 때문에 만약에 컴포넌트에서 관리해야 할 상태가 여러 개라면 useState를 여러 번 사용하면 된다.
    ```javascript
    import React, { useState } from 'react'; 
    /*
    * 배열의 첫번째 요소 : 상태값 
    * 배열의 두번째 요소 : 상태를 설정하는 함수 
    */
    const [value, setValue] = useState(초기값); 
    const [value2, setValue2] = useState(초기값); 
    ```
  
  * 선언한 state사용하기 
  
    ```jsx
    /*
    * <p>You clicked {this.state.count} times</p> : 컴포넌트를 클래스로 만들었을 때 state 사용방법
    */
    <p>You clicked {count} times</p>
    ```
  
  * state값 변경하기 
  
    ```jsx
    <button onClick={() => setCount(count + 1)}>
        Click me
    </button>
    ```

### useEffect

함수형 컴포넌트에서 컴포넌트가 렌더링 될 때마다 특정 작업을 할 수 있도록 설정하는 hook이다. 클래스로 만들어진 컴포넌트의 생명주기함수의 기능과 같은 역할을 한다. 

> 참고
>
> the [Motivation](https://ko.reactjs.org/docs/hooks-intro.html#complex-components-become-hard-to-understand) for Hooks is that class lifecycle methods often contain unrelated logic, but related logic gets broken up into several methods.

* useEffect() : componentDidMount 와 componentDidUpdate와 같은 역할을 수행, render이후에 해야 하는 작업을 한다.(예: 데이터 패치 작업)

  ```javascript
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  ```

* 정리함수(cleanUp)가 있는 useEffect() :componentDidUpdate 와 componentWillUnmount와 같은 역할을 수행, useEffect에서 정리함수(cleanUp)을 반환한다. 컴포넌트가 unmount 될 때 필요한 작업을 수행한다.

* 정리함수(cleanUp) : 메모리 누수 방지를 위해 UI에서 컴포넌트를 제거하기 전에 동작한다.

  ```javascript
  //구독과 구독을 해제하는 작업
  useEffect(() => {
      ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
      return function cleanup() {
        // Clean up the subscription
        ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
      };
    });
  ```

* 조건부 effect : 특정 state가 변경되었을 때만 effect를 수행할 수 있다.
