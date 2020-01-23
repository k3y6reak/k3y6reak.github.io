---
layout: post
title:  "Hooks - useReducer, useMemo"
subtitle: "Hooks의 useReducer와 useMemo에 대해 알아봅시다."
date:   2020-01-23 09:16:00 +0900
categories: ['technique']
tags: React
comments: true
---


#### useReducer

useReducer는 useState보다 다양한 컴포넌트 상황에 상태를 업데이트 해주고 싶을 때 사용하는 hook입니다.

나중에 `리덕스`에 대한 내용을 다루는데, 리덕스에서는 `type`이 반드시 있어야 하지만, useReducer는 반드시 있어야 하는 것은 아닙니다.


버튼을 클릭하면 값을 증가, 감소 시키는 코드를 작성해 보겠습니다.

```javascript
[CounterTest.js]

import React, { useReducer } from "react";


const reducer = (state, action) => {
    switch (action.type) {
        case "INCREMENT":
            return {value: state.value + 1};

        case "DECREMENT":
            return {value: state.value - 1};

        default:
            return state;
    }
}

const Counter = () => {
    const [state, dispatch] = useReducer(reducer, {value: 0});

    return (
        <div>
            <p>{state.value}</p>
            <button  onClick={() => dispatch({type: "INCREMENT"})}>+1</button>
            <button  onClick={() => dispatch({type: "DECREMENT"})}>-1</button>
        </div>
    );
};

export default Counter;
```

먼저, Counter 함수에서 useReducer를 사용합니다. 여기서, reducer와 {value: 0}을 인자로 사용하는데, 해당 값은 상단의 reducer 함수에서 받게 됩니다. `{value: 0}` 이라는 값이 기본으로 설정됩니다.


버튼을 클릭할 때마다, `dispatch`가 실행됩니다. dispatch는 액션을 발생시키는 함수이며, 인자로 `{type: 내용}`과 같이 사용됩니다.

해당 액션을 기준으로 reducer 함수에서 switch case 문을 이용해 상태를 관리하게 됩니다.


기존에 계속해서 2개의 input 태그를 이용해 상태관리를 했는데, useReducer를 이용한 코드를 살펴보겠습니다.

```javascript
[InputTest.js]

import React, { useReducer } from "react";

const reducer = (state, action) => {

    return {
        ...state,
        [action.name]: action.value
    };
};

const InputTest = () => {
    const [state, dispatch] = useReducer(reducer, {
        name: "",
        message: ""
    });

    const { name, message } = state;

    const onChange = (e) => { 

        dispatch(e.target);
    };

    return (
        <div>
            <div>
                <input name="name" value={name} onChange={onChange}></input>
                <input name="message" value={message} onChange={onChange}></input>
            </div>

            <div>
                <span>{name}님 </span>
                <span>{message}.</span>
            </div>
        </div>
    )
};

export default InputTest;
```

input 태그는 onChange를 통해서 입력된 값을 보내게 되고, `onChange` 함수에서 `dispatch`함수를 통해 해당 input 태그를 구별하게 됩니다. 

`e.target`으로 구별된 해당 태그는 reducer를 통해서 `action`으로 받게되고, action은 곧, input 태그가 되므로, 해당 input 태그의 이름에 값을 넣게 됩니다.

state에서는 해당 내용을 상태관리하게 되며, name과 message에 변경된 내용이 화면에 출력되게 됩니다.


#### useMemo

먼저, useState를 활용해서 입력한 값을 등록할 때마다, 평균을 계산하는 코드를 살펴보겠습니다.

```javascript
[CalcAvg.js]

import React, {useState} from "react";

const Calc = (numbers) => {
    console.log("Calc..");

    if(numbers.length === 0) {
        return 0;
    }

    const sum = numbers.reduce((a, b) => a+ b);

    return sum / numbers.length;
}

const CalcAvg = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState("");

    const onChange = (e) => {
        setNumber(e.target.value);
    };

    const onInsert = (e) => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber("");
    };

    return (
        <div>
            <input value={number} onChange={onChange}></input>
            <button onClick={onInsert}>등록</button>

            <ul>
                {list.map((value, index) => (<li key={index}>{value}</li>))}
            </ul>

            <div>
                <span>avg: {Calc(list)}</span>
            </div>
        </div>
    )
}

export default CalcAvg;
```

`useReducer`를 사용할때, input 태그의 name 항목이 필요했지만, 위 코드는 name을 사용하지 않아도 됩니다.

input 태그에 입력된 값은 onChange에서 받게되고, 버튼을 클릭 시, `onIncert` 함수가 실행되면서 해당 문자 상태의 숫자를 슷자형으로 변경하여 list에 넣게 됩니다.


평균을 계산하는 Calc는 해당 list를 인자로 받아, list의 개수와 `reduce`를 통해 값을 더하고, 평균을 계산합니다.

실제 웹 브라우저에서 콘솔로 확인하면 계산하는 시점의 메시지가 `입력하는 순간`에도 이루어집니다.

input 값이 변경될 때마다 계산을 한다면 상관 없지만 버튼을 클릭한 후, 결과를 보고 싶은 경우에는 그럴 필요가 없습니다.

이때, `useMemo`를 사용하여 최적화가 가능합니다.

```javascript
[CalcAvg.js]

import React, {useState, useMemo} from "react";

const Calc = (numbers) => {
    console.log("Calc..");

    if(numbers.length === 0) {
        return 0;
    }

    const sum = numbers.reduce((a, b) => a+ b);

    return sum / numbers.length;
}

const CalcAvg = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState("");

    const onChange = (e) => {
        setNumber(e.target.value);
    };

    const onInsert = (e) => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber("");
    };

    const avg = useMemo(() => Calc(list), [list])

    return (
        <div>
            <input value={number} onChange={onChange}></input>
            <button onClick={onInsert}>등록</button>

            <ul>
                {list.map((value, index) => (<li key={index}>{value}</li>))}
            </ul>

            <div>
                <span>avg: {avg}</span>
            </div>
        </div>
    )
}

export default CalcAvg;
```

avg 변수에 useMemo를 사용했습니다. Calc에 list를 넣어 호출합니다. 콘솔로 직접 확인하면 버튼을 누르면 호출되는 것을 확인할 수 있습니다.

