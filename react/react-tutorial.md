# Start React!! 

----
## 튜토리얼 순서
1. square 컴포넌트 만들기 -> props로 값 받아서 설정
2. board 컴포넌트 만들기 -> square로 props 보내기, 9개의 square컴포넌트 만들기 
3. square에 onclick 설정하기
4. square 클릭시 square의 state를 변경하기 
5. parents component인 board에서 square의 값을 관리하기 위해 board에 square의 값을 관리하는 배열을 state 정의 해준다.
6. board에 square컴포넌트가 클릭되었을 때 board의 square관리 배열의 state의 값을 업데이트 해주는 함수를 만든다. 
7. 위에서 만든 클릭 함수를 props를 통해 각 square컴포넌트로 전달 해준다.
8. board 컴포넌트에서 x,o를 결정할 수 있는 state를 만든다.
9. 위에서 만든 state의 값에 따라 클릭시 board 컴포넌트의 squares의 state를 x또는 o로 값을 설정할 수 있게한다. 



----

* 리액트는 
* React is a declarative, efficient, and flexible JavaScript library for building user interfaces. It lets you compose complex UIs from small and isolated pieces of code called “components”.

* When our data changes, React will efficiently update and re-render our components.

* Passing Data Through Props
* 함수형 컴포넌트에서 props사용하기 
```javascript
    function Square(props) {
        return(
            <button className ="square">
                {props.value}
            </button>
        );
    }
```

* [자바스크립트 배열 만들기](https://www.freecodecamp.org/news/https-medium-com-gladchinda-hacks-for-creating-javascript-arrays-a1b80cb372b/)

* We may think that Board should just ask each Square for the Square’s state. Although this approach is possible in React, we discourage it because the code becomes difficult to understand, susceptible to bugs, and hard to refactor. Instead, the best approach is to store the game’s state in the parent Board component instead of in each Square. The Board component can tell each Square what to display by passing a prop, just like we did when we passed a number to each Square.

* Why Immutability Is Important 
    * There are generally two approaches to changing data. The first approach is to mutate the data by directly changing the data’s values. The second approach is to replace the data with a new copy which has the desired changes.

    * immutable하게 코드를 작성하는 것의 장점 
        1. Complex Features Become Simple : Avoiding direct data mutation lets us keep previous versions of the game’s history intact, and reuse them later.
        쉽게 Undo/Redo와 시간 여행하기 : 이러한 복잡한 기능들을 쉽게 구현 할 수 있게 해준다. -> 데이터 변경을 피하면 이전 버전의 데이터를 계속 참조할 수 있게 해주고 원할때 변경할 수 있게 해준다.


        2. Detecting Changes : Detecting changes in mutable objects is difficult because they are modified directly. (변화에 대한 감지는 이전 버전의 객체(?)와 변경된 객체를 비교할 때 필요하다.)
        변경사항 트래킹 -> 참조되고 있는 객체가 이전과 다르다면 이 객체는 변경된 것이다.

        3. Determining When to Re-Render in React : The main benefit of immutability is that it helps you build pure components in React.
        언제 다시 렌더링할지 결정하기 : 변하지 않는 데이터는 더 쉽게 변경 사항이 있는지 알 수 있기 때문에 컴포넌트가 언제 다시 랜더링을 할지 결정하기 쉽게 만들어줍니다
        [pure component](https://en.reactjs.org/docs/optimizing-performance.html#examples)

