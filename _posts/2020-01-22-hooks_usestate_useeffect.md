---
layout: post
title:  "Hooks - useState, useEffect"
subtitle: "Hooks의 useState와 useEffect에 대해 알아봅시다."
date:   2020-01-22 15:17:00 +0900
categories: ['technique']
tags: React
comments: true
---


### Hooks

Hooks는 `함수형 컴포넌트`에서 상태 관리를 할 수 있도록 합니다. 이전 포스트에서 클래스형 컴포넌트에서 라이프 사이클을 관리했다면 이번 포스트에서는 함수형에서 라이프 사이클을 관리하는 방법에 대해 알아보겠습니다.



#### useState

useState는 하나의 상태 값만 관리할 수 있습니다. 여러개의 상태를 관리하는 경우에는 useState를 여러번 사용하면 됩니다.

앞서, input 태그의 값을 가져오고 화면에 출력하는 코드를 실행해 봤습니다. 해당 코드와 다를 것이 없습니다.

```javascript
[Hooks_1.js]

import React , { useState } from "react";

const HookTest = () => {
    const [name, setName] = useState("이름");
    const [message, setMessage] = useState("내용");

    const onChangeName = (e) => {
        setName(e.target.value);
    }

    const onChangeMessage = (e) => {
        setMessage(e.target.value);
    }

    return(
        <div>
            <div>
                <input value={name} onChange={onChangeName}></input>
                <input value={message} onChange={onChangeMessage}></input>
            </div>

            <div>
                <span>{name}님 </span>
                <span>{message}.</span>
            </div>
        </div>
    )

}

export default HookTest;
```

2개의 input 태그를 관리하기 위해서 2개의 useState를 사용했습니다.


#### useEffect

useEffect는 컴포넌트가 렌더링 될 때, 특정 작업을 수행하도록 설정할 수 있는 Hook 입니다. 클래스 형 컴포넌트에서의 `componentDidMount`와 `componentDidUpdate`를 합친 형태입니다.

```javascript
[Hooks_1.js]

import React , { useState, useEffect } from "react";

const HookTest = () => {
    const [name, setName] = useState("이름");
    const [message, setMessage] = useState("내용");

    useEffect(() => {
        console.log("렌더링 완료!");
        console.log(name, message);
    });
    
    const onChangeName = (e) => {
        setName(e.target.value);
    }

    const onChangeMessage = (e) => {
        setMessage(e.target.value);
    }

    return(
        <div>
            <div>
                <input value={name} onChange={onChangeName}></input>
                <input value={message} onChange={onChangeMessage}></input>
            </div>

            <div>
                <span>{name}님 </span>
                <span>{message}.</span>
            </div>
        </div>
    )

}

export default HookTest;
```

상단에 useEffect 코드를 추가했습니다. 화면에 나타난 input 태그에 값을 입력할 때마다, 렌더링이 완료 됐다는 메시지가 출력됩니다.

마운트가 될 때만, 어떤 기능을 실행하고 싶다면, useEffect에서 `[]`를 추가하면 됩니다.

```javascript
[Hooks_1.js]

(생략)
    useEffect(() => {
        console.log("마운트 완료!");
        console.log(name, message);
    }, []);
(생략)
```

위 코드 처럼, `[]`를 추가하고 input 태그에 값을 넣어보면 더 이상 출력되지 않습니다.


또한, 어떤 값이 변경될 때만 호출하고 싶은 경우에는 `[name]` 처럼 `[]` 안에 해당 변수를 넣어주면 됩니다.

```javascript
[Hooks_1.js]

    useEffect(() => {
        console.log("마운트 완료!");
        console.log(name, message);
    }, [name]);

````

`name`의 태그에 값을 넣으면 출력이되고, `message` 태그에 값을 넣으면 나타나지 않습니다.


useEffect는 렌더링 `직후`마다 실행됩니다. 컴포넌트가 언마운트 되기 전이나, 업데이트 전에 어떤 기능을 실행하고자 하는 경우에 `return()`을 이용합니다.


```javascript
[Hooks_1.js]

    useEffect(() => {
        console.log("마운트 완료!");
        console.log(name, message);

        return() => {
            console.log("cleanup");
            console.log(name);
        }
    });
```

콘솔을 확인하면 계속해서 메시지가 나타납니다. 여기서 주의할 점은 `return()` 안의 변수는 `이전의 값`이 출력된다는 점 입니다.

추가적으로, `언마운트`될 때만 실행되게 하고 싶다면 `[]`를 넣어주면 됩니다.

