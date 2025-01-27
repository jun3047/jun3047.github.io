---
title: react에서 복잡한 상태관리 하는 법
date: 2022-08-09 07:24:19
categories:
- state management
tags:
- state
- react
- redux
- hooks
- life cycle
---

### 계기

현재 한창 개발 중에 있는 tech stac에서 메뉴를 누르면 화면의 내용이 달라는 구현을 하던 중, state관리에서 어려움을 느껴 **state와 관련된 명령어와 라이프 사이클을 정확히 파악하고자 한다.** 현재 Tech Stac에서 사용 중인 state management는 redux이면 함수형 컴포넌트를 기준으로 작성된 글임을 밝힌다.

### state management 명령어

react는 useState, useEffect, useMemo, useReducer, useContext, useCallback, useLayoutEffect와 같은 여러 명령어가 있다. react 16.8부터 hook이 도입되면서 함수형 컴포넌트에서 state와 같은 기능을 사용할 수 있게 되었다. 정확히 어떤 순서와 구조로 state를 관리하는지 알아보자.

<img src='https://github.com/jun3047/jun3047.github.io/blob/master/assets/images/hookLifecyecle.png?raw=true' alt='hookLifecyecle'>

|Mounting|Updating|Unmounting
|-|-|-|
|처음 시작할 때|props나 state가 변경될 때|끝날 때|

한 컴포넌터의 입장에서 시작할 때, 변경될 때, 끝날 때이다.
<!-- react는 UI를 구축하기 위한 선언적이고 효율적이며 유연한 JavaScript 라이브러리이다. 컴포넌트라고 불리는 작고 고립된 코드의 파편을 이용하여 복잡한 UI를 구성하도록 돕는다. 그러한 과정에서 props를 통해 좀 더 유연한 컴포넌트를 설계하고 같은 작업을 반복하지 않을 수 있게 해준다. https://ko.reactjs.org/tutorial/tutorial.html -->

#### useMemo

Memo는 memoization을 의미한다. 동적 계획법도 memoization을 한다. memoization는 이미 한 계산을 기억하는 것이다. 주로 시간이 오래 걸리는 계산을 반복하지 않기 위해 사용한다. 마운트에서 많은 계산이 필요하고 무거운 컴포넌트에서 많이 사용될 것 같지만 자주 사용되지 않는 명령어이다. (나도 명령어를 정리하면서 처음 알게 되었다).

우선 useMemo로 저장된 데이터는 garbage collection에서 제외되기 때문에 메모리 낭비가 발생한다. 또한 컴포넌트가 복잡해져서 가독성이 떨어지고, 유지보수가 어려워진다. 무엇보다 이러한 문제가 없이 useMemo를 대체할 수 있는 명령어가 있기 때문이다. 바로 나중에 알아볼 useEffect이다.

#### useCallback

useMemo는 value를 저장한다면, useCallback은 함수를 저장한다. js에서 컴포넌트가 update 될 때 함수와 변수 모두 초기화된 후에 다시 실행된다. 즉 이전과는 다른 주소값을 가진 새로운 데이터가 할당된다는 것을 알 수 있다. 이러한 상황에서 무거운 함수 코드가 계속 실행되지 않게 하기 위해서 함수를 정의할 때 useCallback과 함께 한다면 더욱 효율적인 컴포넌트를 구성할 수 있다. 

#### [여담] 최적화에 대해

useCallback과 useMemo에 대한 여러 포스팅을 보던 중 최적화에 관한 흥미로운 글을 보게 되었다. 코드를 대하는 태도에 관한 글인데 이 글을 읽고 든 생각은 [이곳](https://jun3047.github.io/plan/date)에 작성하였다.

https://www.rinae.dev/posts/review-when-to-usememo-and-usecallback
#### useState

**useState**는 리액트의 핵심이고, hook의 시작이라 생각한다. state를 선언할 때 사용된다. 
```js
const [variableName, functionName] = useState(initValue);
```
variableName은 state 변수의 이름, `functionName(변경값)`을 통해 state값을 수정하고 컴포넌트를 Update한다. `useState(initValue)`를 통해 variableName을 초기화 initValue로 초기화한다. 이때 initValue의 변수형에 따라 varuableName의 변수형이 결정된다. 이것은 완전 처음 변수가 선언될 때 초기화 되고, 다시 Update될 때는 반영되지 않는다. 또한 같은 컴포넌트도 state는 독립적으로 관리된다. 

```js
//Meun.tsx
    const [meunActive, setMeunActive] = useState(()=>{
        var map = new Map()
        for (const meun in meuns) {
            map.set(meun, false)
        }
        return map
    })
```

```js
//Dialog.tsx
<Meun title= "JavaScript Framework" meuns= {["react", "angular", "vuejs"]}/>
<Meun title= "JaveScript Superset" meuns= {["TypeScript", "Flow"]}/>
<Meun title= "JaveScript Library" meuns= {["jQuery", "core-js"]}/>
<Meun title= "CSS in Js" meuns= {["styled-components", "Emotion"]}/>
```
Dialog에서 Meun 컴포넌트를 4번 사용하면, 4개의 Meun 컴포넌트는 각각의 meunActive를 갖는다.

이러한 방식으로 state 관리를 하게 될 때 문제점은 state 부분이 너무 길어져서 코드 가독성이 떨어지는 경우가 발생하기도 한다. 그럴 때 useReducer를 활용할 수 있다.

#### useReducer

컴포넌트와 상태 업데이트 로직을 분리해준다.

다음은 useState을 활용해 counter를 구현한 것이다.

```js
import React, { useState } from "react";

function Counter() {
  const [number, setNumber] = useState(0);

  const onIncrease = () => {
    setNumber((prevNumber) => prevNumber + 1);
  };

  const onDecrease = () => {
    setNumber((prevNumber) => prevNumber - 1);
  };

  return (
    <div>
      <h1>{number}</h1>
      <button onClick={onIncrease}>+1</button>
      <button onClick={onDecrease}>-1</button>
    </div>
  );
}

export default Counter;
```

다음은 위의 코드를 useReducer를 활용하여 바꾼 것이다.
```js
import React, { useReducer } from "react";

function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1;
    case "DECREMENT":
      return state - 1;
    default:
      return state;
  }
}

function Counter() {
  const [number, dispatch] = useReducer(reducer, 0);

  const onIncrease = () => {
    dispatch({ type: "INCREMENT" });
  };
  const onDecrease = () => {
    dispatch({ type: "DECREMENT" });
  };

  return (
    <div>
      <h1>{number}</h1>
      <button onClick={onIncrease}>+1</button>
      <button onClick={onDecrease}>-1</button>
    </div>
  );
}

export default Counter;

```

딱히 개선된 게 없어 보일 수도 있지만, reducer를 별도의 파일로 관리할 수 있다는 점이 큰 이점이다. 관리할 state가 많아지고 코드가 길어지면 state 관련 수정을 해야할 때 state와 state가 어떻게 변하는 지 (action)을 reducer 안에서 모두 관리하는 멋진 경험을 할 수 있다. 이러한 구조는 후에 상태관리 라이브러리를 이해하는 데 도움이 된다. 


#### useEffect

side effect를 다룰 수 있게 해준다. **side effect**는 react 컴포넌트가 화면에 렌더링된 이후에 비동기로 처리되어야 하는 부수적인 효과들을 의미한다. 주로 API를 통해 데이터를 불러오는 작업 같은 무거운 코드를 useEffect로 감싼다. 그렇게 되면 간단한 작업들 먼저 화면에 보여준 후 useEffect에 있는 코드를 처리하여, API가 응답이 없는 경우에도 미리 렌더링한 UI가 있어 피해를 최소화하고 사용자 경험 측면에서 유리하다. 또한 의존성 배열를 설정하여 특정 객체의 값이 변경될 때마다 useEffect 안의 코드가 실행되게 할 수 있다. 의존성 배열이 빈 경우에는 최초 마운트된 후에 한 번 실행된다.

그런데 useEffect는 계속해서 실행될 경우에 비효율을 불러올 수 있다. 그렇기에 cleanup function을 사용한다. **cleanup function**은 언마운트 되기 직전에 실행되고, 의존성 배열이 있을 경우 Update 되기 전에도 실행된다.

https://talkwithcode.tistory.com/90?category=1019863
https://react.vlpt.us/basic/16-useEffect.html
https://velog.io/@kwonh/ReactHook-useState-%EC%99%80-useEffect-%EB%A1%9C-%EC%83%81%ED%83%AF%EA%B0%92%EA%B3%BC-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0
https://velog.io/@yes3427/React-Side-Effect
https://cocoon1787.tistory.com/800
