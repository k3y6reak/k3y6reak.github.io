---
layout: post
title:  "Hooks - useCallback, useRef"
subtitle: "Hooks의 useCallback와 useRef에 대해 알아봅시다."
date:   2020-01-23 11:55:00 +0900
categories: ['technique']
tags: React
comments: true
---


#### useCallback

useCallback은 useMemo와 비슷합니다. 렌더링이 많이 발생하는 상황에서 최적화하기 위해 사용합니다.

이전 포스트에서 onChange와 onInsert 함수를 사용했었는데, 컴포넌트가 리렌더링 될 때마다 함수가 새로 생서됩니다.

```javascript
[CalcAvg.js]

import React, {useState, useMemo, useCallback} from "react";

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

    const onChange = useCallback( (e) => {
        setNumber(e.target.value);
    }, []);

    const onInsert = useCallback((e) => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber("");
    }, [number, list])

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


useCallback로 변경했습니다. useCallback 끝에는 list가 있는데, 이는 해당 내용이 변경되는 경우에만 새로 생성할 수 있도록 합니다.

또한, 함수 내부에서 이전 상태에 의존하는 경우, 끝에 list를 넣어주어야 합니다. 이전 상태를 기준으로 새로운 상태로 변경할 수 있기 때문입니다.


#### useRef

useRef는 컴포넌트에서 ref를 쉽게 사용할 수 있도록 합니다. 이전에 input 태그에 값을 입력하고 버튼을 클릭했을 때, 다시 input 태그로 focus가 이동했었습니다. 이를 useRef로 변경해보겠습니다.

```javascript
[CalcAvg.js]

import React, {useState, useMemo, useCallback, useRef} from "react";

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
    const inputEl = useRef(null);

    const onChange = useCallback( (e) => {
        setNumber(e.target.value);
    }, []);

    const onInsert = useCallback((e) => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber("");
        inputEl.current.focus();
    }, [number, list])

    const avg = useMemo(() => Calc(list), [list])

    return (
        <div>
            <input value={number} onChange={onChange} ref={inputEl}></input>
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

ref를 사용하기 위해서 해당 input 태그에 ref 속성을 추가하고, 추가되는 시점에서 `focus`함수를 사용하면 됩니다.

