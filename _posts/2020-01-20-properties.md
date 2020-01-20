---
layout: post
title:  "Properties"
subtitle: "Properties에 대해 알아봅시다."
date:   2020-01-20 14:06:00 +0900
categories: ['technique']
tags: React
comments: true
---

### Properties

컴포넌트간 어떤 데이터를 보내기 위해서 Properties 를 사용합니다. 타 언어를 공부하셨던 분이라는 `매개변수` 혹은 `파라미터` 라고 생각하면 쉽게 이해할 수 있습니다.

```JS
[App.js]

import React, { Fragment } from 'react';
import FirstComponent from "./FirstComponent";
import "./App.css";

function App() { // 함수형 컴포넌트
  return (
    
    <Fragment>
      <FirstComponent name="관리자"></FirstComponent>
    </Fragment>
    
  );
}

export default App;
```

```JS
import React from "react";


const FirstComponent = (props) => {
    return <div>{props.name}님 안녕하세요.</div>
};

export default FirstComponent;
```

먼저, App.js의 `<FirstComponent name="관리자"></FirstComponent>` 코드를 보면, 해당 컴포넌트 태그에 name 이라는 `props` 를 사용한 것을 확인할 수 있습니다. 그러면, FirstComponent에 name이라는 이름과 "관리자"라는 내용이 FirstComponent로 전달되게 됩니다.


다음으로, FirstComponent.js 에서는 전달 받은 내용을 사용하기 위해 `() => {}`에서 소괄호에 props 라는 것으로 받게 됩니다.  

props로 받는 내용에는 name이라는 값이 존재하기 때문에 `{props.name}`으로 값을 받아 사용할 수 있습니다.


### defaultProps

props를 설정하지 않고, 기본적으로 설정한 값으로 표현하고 싶은 경우에는 defaultProps를 사용합니다.

위에서 설명한 name="관리자"를 제거한 후, 컴포넌트 태그만 남겨두고 FirstComponent.js 에서 아래와 같은 코드를 추가합니다.


```JS
import React from "react";


const FirstComponent = (props) => {
    return <div>{props.name}님 안녕하세요.</div>
};

FirstComponent.defaultProps = {
    name: "일반 사용자"
};

export default FirstComponent;
```

app.js에서는 `<FirstComponent></FirstComponent>`만 사용되어, props 값이 존재하지 않지만 FirstComponent.js에서 해당 값이 없기 때문에 defaultProps를 찾아 기본 값으로 설정해 줍니다.


### children

children은 react에서 컴포넌트 태그 사이에 있는 값을 출력하기 위한 props 입니다.

```JS
[App.js]

import React, { Fragment } from 'react';
import FirstComponent from "./FirstComponent";
import "./App.css";

function App() { // 함수형 컴포넌트
  return (
    
    <Fragment>
      <FirstComponent>내가 보이나?</FirstComponent>
    </Fragment>
    
  );
}

export default App;

```

```JS
[FirstComponent.js]

import React from "react";


const FirstComponent = (props) => {
    return <div>{props.name}님 안녕하세요.</div>
};

FirstComponent.defaultProps = {
    name: "일반 사용자"
};

export default FirstComponent;
```

위 코드를 작성하고 확인하면 `내가 보이나?` 라는 문자열을 브라우저에 나타나지 않습니다.

이 처럼, 컴포넌트 태그 사이의 값을 출력하기 위해서는 FirstComponent.js 에서 props.children을 사용합니다.

```JS
[FirstComponent.js]

import React, { Fragment } from "react";


const FirstComponent = (props) => {
    return (
        <Fragment>
    	    <div>{props.name}님 안녕하세요.</div>
    	    <div> children 값: {props.children} </div>
        </Fragment>
    );
};

FirstComponent.defaultProps = {
    name: "일반 사용자"
};

export default FirstComponent;
```

props.children을 이용해서 컴포넌트 태그 사이의 값을 웹 브라우저에 나타낼 수 있습니다.


### 비구조화 props

비구조화 props는 props의 내용을 조금 더 쉽게 사용하기 위한 방식입니다.

`props를 한 번 걸러준다.` 는 느낌으로 사용하면 됩니다.


```JS
[FirstComponent.js]

import React, { Fragment } from "react";


const FirstComponent = (props) => {
    const {name, children} = props;

    return (
        <Fragment>
    	    <div>{name}님 안녕하세요.</div>
    	    <div> children 값: {children} </div>
        </Fragment>
    );
};

FirstComponent.defaultProps = {
    name: "일반 사용자"
};

export default FirstComponent;
```

`const {name, children} = props`를 통해서 props의 내용을 name과 children으로 걸러줍니다. 이미 한번 걸러진 name과 children은 `props.name` 처럼 사용하지 않고 `name` 이름 그 자체로 사용할 수 있습니다.

다른 방법으로 `() => {}`에서 소괄호의 props 대신 바로 사용되는 변수 이름으로 받아서 사용할 수 있습니다.


```JS
[FirstComponent.js]

import React, { Fragment } from "react";


const FirstComponent = ({name, children}) => {
    return (
        <Fragment>
    	    <div>{name}님 안녕하세요.</div>
    	    <div> children 값: {children} </div>
        </Fragment>
    );
};

FirstComponent.defaultProps = {
    name: "일반 사용자"
};

export default FirstComponent;
```


### propTypes

propTypes는 props의 타입을 검사하는 역할입니다. propTypes를 사용하기 위해서는 import 후에 사용할 수 있습니다.


```JS
[App.js]

import React, { Fragment } from 'react';
import FirstComponent from "./FirstComponent";
import "./App.css";

function App() { // 함수형 컴포넌트
  return (
    
    <Fragment>
      <FirstComponent name={10}>내가 보이나?</FirstComponent>
    </Fragment>
    
  );
}

export default App;
```

```JS
[FirstComponent.js]

import React, { Fragment } from "react";
import PropTypes from "prop-types";

const FirstComponent = ({name, children}) => {
    return (
        <Fragment>
    	    <div>{name}님 안녕하세요.</div>
    	    <div> children 값: {children} </div>
        </Fragment>
    );
};

FirstComponent.defaultProps = {
    name: "일반 사용자"
};

FirstComponent.propTypes = {
    name: PropTypes.string
};

export default FirstComponent;
```

실제 위 코드를 실행하면 웹 브라우저에서는 정상적으로 나타납니다. 하지만, `개발자 도구`로 살펴보면 아래와 같은 에러가 발생합니다.

```shell
Warning: Failed prop type: Invalid prop `name` of type `number` supplied to `FirstComponent`, expected `string`.
    in FirstComponent (at App.js:9)
    in App (at src/index.js:7)
```

name의 타입이 number로 들어갔으며, 실제 타입은 string을 원하고 있다는 내용입니다. 


해당 에러와 같은 경우에는 실제 웹 브라우저에 나타나지 않아야 개발하는 입장에서 편하겠지만 반드시 `개발자 도구`로 살펴보는 것이 좋습니다.


### Class 형 컴포넌트에서 props 사용

```JS
[FirstComponent.js]


import React, {Component} from "react";
import PropTypes from "prop-types";

class FirstComponent extends Component {
    render() {
        const {name, children} = this.props;

        return (
            <div>
    	        <div>{name}님 안녕하세요.</div>
    	        <div> children 값: {children} </div>
            </div>
        );
    };
};

FirstComponent.defaultProps = {
    name: "일반 사용자"
};

FirstComponent.propTypes = {
    name: PropTypes.string
};

export default FirstComponent;
```

class 형태에서 props를 사용하는 경우 render() 함수 안에서 `this.props`를 사용합니다.
C++, Java에서 클래스 내의 멤버 변수에 접근하기 위해 this를 사용하는 것과 같습니다. (python에서는 self)



위 코드처럼 작성하는 경우에 defaultProps나 propTypes가 하단에 존재하게 되어, 실제 코드가 길어지는 경우에 쉽게 파악할 수 없습니다. 이때 클래스 안에 넣어서 사용할 수 있습니다.


```JS
[FirstComponent.js]

import React, {Component} from "react";
import PropTypes from "prop-types";

class FirstComponent extends Component {
    static defaultProps = {
        name: "일반 사용자"
    };

    static propTypes = {
        name: PropTypes.string
    };

    render() {
        const {name, children} = this.props;

        return (
            <div>
    	        <div>{name}님 안녕하세요.</div>
    	        <div> children 값: {children} </div>
            </div>
        );
    };
};

export default FirstComponent;
```

