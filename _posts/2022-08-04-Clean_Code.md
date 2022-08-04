---
title: title
date: 2022-08-03 12:08:33
categories:
- Jun Conponent System
tags:
- clean code
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


`import React, { useState } from 'react'
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

export default Meun`