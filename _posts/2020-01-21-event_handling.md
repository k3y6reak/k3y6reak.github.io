---
layout: post
title:  "이벤트 핸들링"
subtitle: "이벤트 핸들링에 대해 알아봅시다."
date:   2020-01-21 10:07:00 +0900
categories: ['technique']
tags: React
comments: true
---


### 이벤트 핸들링

이벤트 핸들링이란 특정 이벤트 (어떤 발생?)를 직접적으로 제어하는 것을 말합니다.

리액트에서는 아래와 같이 몇가지 주의사항이 있습니다.


1. 이벤트 이름은 카멜 표기법으로 작성
| HTML에서 onclick은 리액트에서 onClick으로, onkeyup은 onKeyUp으로 작성합니다.

2. 이벤트를 발생시킬 자바스크립트 코드를 전달하는 것이 아니라, 함수 형태로 값을 전달합니다
| HTML에서 `""`로 감싸, 해당 이벤트를 발생시켰다면, 리액트에서는 함수 형태의 객체로 전달합니다.

3. DOM 요소에서만 이벤트를 설정
| div, button, input과 같은 기본 HTML 태그에서만 가능합니다. 리액트에서 직접 컴포넌트를 만들어서 해당 이벤트를 사용할 수 없습니다. onClick과 같은 이벤트를 설정하게 된다면, 앞서 props에 대한 규칙이 깨지기 때문입니다.


```javascript
[TestEvent.js]

import React from "react";


const TestEvent = () => {

    return (
        <div>
            <h1>Test Event</h1>
            <input type="text" 
                   name="message"
                   placeholder="input text"
                   onChange={(e) => {console.log(e.target.value)}}></input>
        </div>
    );
};

export default TestEvent;
```

위 코드처럼, TestEvent 코드를 작성합니다. input 태그에 내용을 작성하면, 그 내용이 `개발자도구`의 `콘솔`에 나타납니다.

그렇다면, 해당 내용을 직접 화면에 출력해 줄 수 있도록, state를 이용해 보겠습니다.

```javascript
[TestEevent.js]

import React, { useState } from "react";


const TestEvent = () => {
    const [message, setMessage] = useState("윗 칸에 입력해주세요.");

    return (
        <div>
            <h1>Test Event</h1>
            <input type="text" 
                   name="message"
                   placeholder="input text"
                   onChange={(e) => {setMessage(e.target.value)}}></input>
            <p>{message}</p>
        </div>
    );
};

export default TestEvent;
```

onChange 함수에서 `e`라는 변수로 값을 전달 받습니다. e에는 `target`이라는 멤버가 존재하고, target은 `value`라는 멤버를 갖고 있습니다.

여기서, target은 해당 input 태그를 말하는 것이고, 해당 input 태그 안의 내용이 value가 됩니다.

해당 value의 값을 setMessage에 전달하게 되며, useSate를 통해 해당 message가 저장되고, 화면에 나타나게 됩니다.

위 코드를 조금 더 개선해보자면, 아래와 같습니다.

```javascript
[TestEvent.js]

import React, { useState } from "react";


const TestEvent = () => {
    const [message, changeMessage] = useState("윗 칸에 입력해주세요.");
    const onChangeMessage = (e) => changeMessage(e.target.value);

    return (
        <div>
            <h1>Test Event</h1>
            <input type="text" 
                   name="message"
                   placeholder="input text"
                   onChange={onChangeMessage}></input>
            <p>{message}</p>
        </div>
    );
};

export default TestEvent;
```

위 코드는 input 태그가 한 개인 경우에 대한 예제이므로, 2개 이상 태그가 존재한다면 `e.target.name`을 이용해서 해당 태그의 이름을 가져오고, 그 태그에 e.target.value를 넣어주면 됩니다.

```javascript
[TestEvent.js]

import React, { useState } from "react";


const TestEvent = () => {
    const [message, changeMessage] = useState("윗 칸에 입력해주세요.");
    const [name, ChangeName] = useState("이름")
    const onChangeMessage = (e) => changeMessage(e.target.value);
    const onChangeName = (e) => ChangeName(e.target.value);

    return (
        <div>
            <h1>Test Event</h1>
            <input type="text" 
                   name="name"
                   placeholder="input text"
                   onChange={onChangeName}>
            </input>

            <input type="text" 
                   name="message"
                   placeholder="input text"
                   onChange={onChangeMessage}>
            </input>

            <p>{name}님이 작성한 내용: {message}</p>
        </div>
    );
};

export default TestEvent;
```

먼저, 위 코드에서 2개의 input 태그를 이용해서 `OO님이 작성한 내용: OO` 처럼 화면에 출력해주는 코드 입니다.

각각의 이벤트를 핸들링하지만, 이를 하나로 묶어 해당 태그를 찾아 값을 넣어주도록 바꿔보겠습니다.

```javascript
[TestEvent.js]

import React, { useState } from "react";


const TestEvent = () => {
    const [text, setText] = useState({
        name: "",
        message: ""
    });

    const onChange = (e) => {
        const nextText = {
            ...text,
            [e.target.name]: e.target.value
        };
        setText(nextText); // 상단의 [text, setText]로 넘어감.
    };

    return (
        <div>
            <h1>Test Event</h1>
            <input type="text" 
                   name="name"
                   placeholder="input text"
                   onChange={onChange}>
            </input>

            <input type="text" 
                   name="message"
                   placeholder="input text"
                   onChange={onChange}>
            </input>

            <p>{text.name}님이 작성한 내용: {text.message}</p>
        </div>
    );
};

export default TestEvent;
```

return 내 에서는 기존의 input 태그 마다의 함수가 존재했지만, 현재 하나의 `onChange` 함수가 존재합니다.

입력된 내용이 onChange로 이동하게 되고, onChange에서는 `e`로 받아 해당 해당 태그에 해당 값을 넣어주고 있습니다.

onChange를 살펴보면, 내부에 const nextText 객체가 존재하는 것을 확인할 수 있는데, nextText라는 객체에 기존의 text를 포함 `(...text)`와 각 태그에 내한 내용을 담고 있습니다.

그 후, `setText(nextText)`에 의해 상단의 useState 구문으로 넘어가며, name과 message에 값을 저장합니다.

