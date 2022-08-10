---
title: Component를 훌륭하게 만드는 법
date: 2022-08-03 12:08:33
categories:
- Jun Conponent System
- Tech Stack
tags:
- clean code
- react
- component
---

### 계기

Tech Stack 프로젝트를 진행하던 중 meun 부분을 반복적으로 구현해야 하는 상황이 되었다. 반복적인 작업을 하면 크게 어려운 일은 아니지만, 코드가 너무 길고 난잡해져서 쉽게 재사용할 수 있게 깔끔한 컴포넌트를 구성할 필요를 느끼게 되었다.


### 좋은 컴포넌트

우선 좋은 컴포넌트를 짜기 위한 규칙과 개념을 학습해야 했는데 [토스ㅣSLASH 22 - Effective Component 지속 가능한 성장과 컴포넌트](https://www.youtube.com/watch?v=fR8tsJ2r7Eg&t=2s&ab_channel=%ED%86%A0%EC%8A%A4)가 큰 도움이 되었다. 이 영상를 내가 다시 정리해봤다.


##### 습관

판단을 할 때 한번 더 생각하기 : 컴포넌트로 분리하면 재사용 가능한지, 복잡도가 줄어드는지.

##### 규칙

1. 컴포넌트의 역할과 구성을 파악하게 쉽게 이름을 정한다.
2. 컴포넌트가 한 가지 역할을 하게 한다.
3. UI 단계에서 인터페이스를 먼저 추가하여, 역순으로 코드 작성하기. (컴포넌트가 이미 있다는 생각으로 코드 작성)

##### 구조

변동 가능성이 있는 부분을 최대한 나누어 데이터를 중심으로 봤을 때 유사한 구조에서 재사용할 수 있게 한다.

데이터 -> UI <-> 사용자

Headless 기반의 추상화

- UI와 데이터를 분리하여 한 가지 문제에만 집중 : (hooks로 데이터 수집을 담당하는 컴포넌트 / hooks을 통한 데이터를 UI로 제작하는 컴포넌트)


### 실천


```tsx

import React, { useState } from 'react'
import styled from '@emotion/styled'
import { useDispatch } from 'react-redux';

interface IMeun{
    title: string;
    meun: string[];
}

const Meun = (props: IMeun) => {

    const [reactIsOn, setReactIsOn] = useState(false);
    const [angularIsOn, setAngularIsOn] = useState(false);
    const [vueIsOn, setVueIsOn] = useState(false);
    
    const [Option, setOption] = useState({
        JSFramework: "none",
        JSSuperset: "none",
        JSLibrary: "none",
        CSSinJS: "none",
    });

    const objToStr = (a: object) => {
        var options = Object.values(a);
        const b: string = `${options[0]}-${options[1]}-${options[2]}-${options[3]}`;
        return b;
    }
    
    const nowOtption = objToStr(Option)
    
    const dispatch = useDispatch();
    dispatch({type: 'UPDATE_OPTION', text: nowOtption});
    

    const onClick = (option: string) => {

        if(option === 'react') {
            if(reactIsOn) {
                setReactIsOn(false);
                option = 'none';
            }else
            {
                setReactIsOn(true);
            }
        }else{
            setReactIsOn(false);
        }

        if(option === 'angular') {
            if(angularIsOn) {
                setAngularIsOn(false);
                option = 'none';
            }else
            {
                setAngularIsOn(true);
            }
        }else{
            setAngularIsOn(false);
        }

        if(option === 'vuejs') {
            if(vueIsOn) {
                setVueIsOn(false);
                option = 'none';
            }else
            {
                setVueIsOn(true);
            }
        }else{
            setVueIsOn(false);
        }

        setOption({ ...Option, JSFramework: option });
    }

    //useState의 업데이트는 한 컴포넌트가 끝난 후에 작동한다

    

    return (
        <>
            <MeunWrap>
                <MeunTitle>JavaScript Framework</MeunTitle>
                <MeunBtn onClick={() => { onClick("react") }} isOn={reactIsOn}>React</MeunBtn>
                <MeunBtn onClick={() => { onClick("angular") }} isOn={angularIsOn}>Angular</MeunBtn>
                <MeunBtn onClick={() => { onClick('vuejs') }} isOn={vueIsOn}>Vue.js</MeunBtn>
            </MeunWrap>
            <MeunWrap>
                <MeunTitle>JavaScript Superset</MeunTitle>
                <MeunBtn>TypeScript</MeunBtn>
                <MeunBtn>Flow</MeunBtn>
            </MeunWrap>
            <MeunWrap>
                <MeunTitle>JavaScript Library</MeunTitle>
                <MeunBtn>jQuery</MeunBtn>
                <MeunBtn>core-js</MeunBtn>
            </MeunWrap>
            <MeunWrap>
                <MeunTitle>CSS in JS</MeunTitle>
                <MeunBtn>styled-components</MeunBtn>
                <MeunBtn>Emotion</MeunBtn>
            </MeunWrap>
        </>
    )
}

type MeunBtnProps = {
    isOn?: boolean
}

const MeunBtn = styled.button`
background: ${(props: MeunBtnProps) => props.isOn ? '#3FDCE5' : 'white'};
border: 1px solid #000000; 
border-radius: 14px;
padding: 7px 8px;
margin-right: 15px;

:hover{
    background-color: ${(props: MeunBtnProps) => props.isOn ? '#3FDCE5' : '#EEEEEE'};
}
`

const MeunWrap = styled.div`
    width: 100%;
    height: 63px;
    margin-bottom: 37px;
`

const MeunTitle = styled.h3`
    margin: 0;
    padding-bottom: 10px;
    font-size: 14px;
`
export default Meun

```


<img src='https://github.com/jun3047/jun3047.github.io/blob/master/assets/images/meun_old.gif?raw=true' alt='meun_old'>

위에 코드를 실행한 것이다. Tech Stack 아래에 메뉴 이름과 메뉴 목록의 UI, 기능을 모두 담당하는 컴포넌트이다. 위에서 정리한 좋은 컴포넌트의 기준에 부합하지 않은 포인트를 살펴보자. 

위에 코드를 자세히 나누어 코드 속 문제를 살펴보자

```tsx
const [reactIsOn, setReactIsOn] = useState(false);
const [angularIsOn, setAngularIsOn] = useState(false);
const [vueIsOn, setVueIsOn] = useState(false);

const [Option, setOption] = useState({
    JSFramework: "none",
    JSSuperset: "none",
    JSLibrary: "none",
    CSSinJS: "none",
});
```

이 부분은 meun를 구현하기 위한 데이터 관리 hooks이고,

```tsx
const onClick = (option: string) => {

    if(option === 'react') {
        if(reactIsOn) {
            setReactIsOn(false);
            option = 'none';
        }else
        {
            setReactIsOn(true);
        }
    }else{
        setReactIsOn(false);
    }

    if(option === 'angular') {
        if(angularIsOn) {
            setAngularIsOn(false);
            option = 'none';
        }else
        {
            setAngularIsOn(true);
        }
    }else{
        setAngularIsOn(false);
    }

    if(option === 'vuejs') {
        if(vueIsOn) {
            setVueIsOn(false);
            option = 'none';
        }else
        {
            setVueIsOn(true);
        }
    }else{
        setVueIsOn(false);
    }
}
```

이 부분은 onClick에 할당될 hook 실행 조건을 명시하였다.

```tsx
<MeunBtn onClick={() => { onClick("react") }} isOn={reactIsOn}>React</MeunBtn>
<MeunBtn onClick={() => { onClick("angular") }} isOn={angularIsOn}>Angular</MeunBtn>
<MeunBtn onClick={() => { onClick('vuejs') }} isOn={vueIsOn}>Vue.js</MeunBtn>
```

이 부분은 데이터 hooks를 MeunBtn에 할당해 준 것이다.

불필요한 반복이 많고 같은 역할을 하는 코드끼리 뭉쳐있지 않다는 문제를 알 수 있다.

우선 **반복적인 부분을 추상화**하여 재사용성을 높이고, **하나의 컴포넌트가 하나의 역할**을 하도록 리펙토링할 것이다.

