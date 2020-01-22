---
layout: post
title:  "Component LifeCycle"
subtitle: "컴포넌트 라이프 사이클에 대해 알아봅시다."
date:   2020-01-22 14:05:00 +0900
categories: ['technique']
tags: React
comments: true
---



### 라이프 사이클

라이프 사이클은 `생명 주기`라고 합니다. React, Vue와 같은 프론트 엔드 프레임워크로 개발을 진행하면, `라이프 사이클`이 반드시 필요하게 됩니다.

보통, 외부 API로 어떤 값을 받아와서 화면에 출력해주거나, 해당 내용을 가공하여 변경하는 등 다양한 이유로 생명 주기에 따라 진행되어야 하는 부분이 있습니다.


React에서 라이프 사이클로, `마운트`, `업데이트`, `언마운트`. 총 3가지가 있습니다.



#### 마운트

DOM이 생성되어 웹 브라우저에 나타나는 것을 마운트라 합니다.

이때, `constructor`, `getDerivedStateFromProps`, `render`, `componentDidMount` 함수가 호출됩니다.

1. constructor
컴포넌트를 새로 만들때마다 호출됩니다.

2. getDerivedStateFromProps
props 값을 state에 넣을때 사용하는 함수입니다.

3. render
UI를 렌더링하는 함수입니다.

4. componentDidMount
컴포넌트가 웹 브라우저에 나타난 후, 호출되는 함수 입니다.


#### 업데이트
업데이트는 `props 변경`, `state 변경`, `부모 컴포넌트 re-render` `this.forceUpdate로 강제` 할 때 됩니다.

1. getDerivedStateFromProps
마운트 과정에서도 호출되지만, 업데이트 시작 전에 호출됩니다. 

2. shouldComponentUpdate
컴포넌트가 리렌더링을 해야 할지 말아야할지 결정하는 함수입니다. `ture`, `false`로 반환되며, true인 경우, 라이프 사이클에 따라 함수를 계속 호출하고, false인 경우, 리렌더링 되지 않습니다. 특정 함수에서 this.forceupdate 함수를 호출하면 shouldcomponentUpdate 과정을 생략하고 render 함수를 호출합니다.

3. render
컴포넌트를 리렌더링 합니다.

4. getSnapshotBeforeUpdate
컴포넌트 변화를 DOM에 반영하기 직전 호출되는 함수입니다.

5. componentDidUpdate
컴포넌트 업데이트 작업 직후, 호출되는 함수입니다.


#### 언마운트
마운트의 반대로, 컴포넌트를 DOM에서 제거합니다.

1. componentWillUnmount
컴포넌트가 웹 브라우저에서 사라지기 전에 호출되는 함수입니다.


```javascript
[LifeCycleTest.js]

import React, { Component } from "react";

class LifeCycleTest extends Component {
    state = {
        number: 0,
        color: null,
    };

    myRef = null;

    constructor(props) {
        super(props);
        console.log("constructor");
    }

    static getDerivedStateFromProps(nextProps, prevState) {
        console.log("getDerivedStateFromProps");

        if(nextProps.color !== prevState.color) {
            return {color: nextProps.color};
        }

        return null;
    }

    componentDidMount() {
        console.log("componentDidMount")
    }

    shouldComponentUpdate(nextProps, nextState) {
        console.log("shouldComponentUpdate", nextProps, nextState);

        return nextState.nuber % 10 !== 4;
    }

    componentWillUnmount() {
        console.log("componentWillUnmount");
    }

    handleClick = () => {
        this.setState({
            numbeR: this.state.number + 1
        });
    };

    getSnapshotBeforeUpdate(prevProps, prevState) {
        console.log("getSnapshotBeforeUpdate");

        if(prevProps.color !== this.props.color) {
            return this.myRef.sytle.color;
        }

        return null;
    }

    componentDidUpdate(prevProps, prevState, snapshot) {
        console.log("componentDidUpdate", prevProps, prevState);

        if(snapshot) {
            console.log("업뎃 전 색상: ", snapshot);
        }
    }

    render() {
        console.log("render");

        const style = {
            color: this.props.color
        };

        return (
            <div>
                <h1 style={style} ref={ref => this.myRef = ref}>{this.state.number}</h1>
                <p>color: {this.state.color}</p>
                <button onClick={this.handleClick}>++</button>
            </div>
        );
    };
};

export default LifeCycleTest;
```

해당 코드를 작성하고 웹 브라우저를 확인하면 `constructor`, `getDerivedStateFromProps`, `render`, `componentDidMount` 가 호출됩니다.

컴포넌트를 새로 만들고(constructor), props에 값을 넣고(getDerivedStateFromProps), 화면에 나타내고(render), 호출 됐으니(componentDidMount)가 호출된 것입니다.


여기서, `++` 버튼을 클릭하게 되면, 

`getDerivedStateFromProps`, `shouldComponentUpdate`, `render`, `getSnapshotBeforUpdate`, `componentDidUpdate`가 호출됩니다.

위에서 설명한 내용을 기반으로 코드를 하나씩 살펴보세요. :)

