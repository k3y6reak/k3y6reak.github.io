---
layout: post
title:  "Redux - 기초"
subtitle: "Redux에 대해 알아봅시다."
date:   2020-01-29 09:02:00 +0900
categories: ['technique']
tags: React
comments: true
---

### Redux 기초

앞서배운 Context API를 이용해 상태관리를 했었습니다. 이전에 Context API는 사용하는데 불편함이 있어, 많은 사람들이 Redux를 사용했었지만, 업데이트가 이루어지면서 Context API를 사용하는데 편리해졌습니다.

Redux 또한 많은 사람들이 사용하기 때문에 먼저, 간단한 개념에 대해 알아보겠습니다.


리덕스에는 크게 `action`, `reducer`, `store`, `dispatch`, `subcribe`, 5가지가 있습니다.



1. action

action은 항상 `type`을 가지고 있어야 합니다. 이 type에 따라 특정 작업을 진행하게 됩니다.

2. reducer

reducer는 변화 발생시킵니다. reducer가 변할때, 액션을 발생시켜 현재 상태와 전달받은 액션을 파라미터로 받아 새로운 상태를 만들어 전달합니다.


3. store

store는 프로젝트당 하나만 만들 수 있습니다. 이 안에 state와 reducer가 있고, 기타 내장함수가 있습니다.

4. dispatch

dispatch는 action을 발생 시키는 것이라 생각하면 됩니다. `dispatch(action)`과 같은 형태로 파라미터로 넣어 호출하게 되고, store는 redcuer 함수를 실행해 새로운 상태를 만들어줍니다.

5. subscribe

subscribe는 store의 내장함수 중 하나입니다. subscribe 함수에서 인자로 리스너 함수를 넣어주면 리스너 함수의 action이 dispatch로 돼, 상태가 업데이트될 때마다 호출됩니다.


#### 실습

redux의 기초 실습을 진행하기 위해서 바닐라JS와 Redux를 이용하겠습니다. 바닐라JS를 사용하기 위해 parcel로 프로젝트를 생성하여 진행합니다.

`yarn global add parcel-bundler` 명령어로 parcel을 사용할 수 있도록 준비합니다. 프로젝트를 진행하기 위해서 폴더를 새로 만들고 해당 폴더 안에서 `yarn init -y` 명령어를 통해 package.json을 생성합니다.


해당 프로젝트에서 index.html과 index.js, index.css 파일을 생성하고 아래 코드를 작성합니다.


```html
[index.html]

<html>
    <head>
        <link rel="stylesheet" type="text/css" href="index.css"/>
    </head>
    <body>
        <div class="toggle"></div>
        <hr/>

        <h1>0</h1>
        <button id="increase">+1</button>
        <button id="decrease">-1</button>

        <script src="./index.js"></script>
    </body>
</html>
```

```javascript
console.log("hello");
```

```css
[index.css]

.toggle {
    border: 2px solid black;
    width: 64px;
    height: 64px;
    border-radius: 32px;
    box-sizing: border-box;
}

.toogle.active {
    background: yellow;
}
```

이후, `parcel index.html` 명령어로 서버를 실행합니다. `http://localhost:1234` 주소가 생성되며 해당 주소로 접속하면 화면과 콘솔창에 입력한 내용이 나타납니다.


### Redux의 5가지 적용

바닐라JS에서 Redux에 존재하는 5가지에 대해 만들어보겠습니다.

1. 초기 세팅

```javascript
[index.js]

const divToggle = document.querySelector(".toggle");
const counter = document.querySelector("h1");
const btnIncrease = document.querySelector("#increase");
const btnDecrease = document.querySelector("#decrease");

const TOGGLE_SWITCH = "TOGGLE_SWITCH";
const INCREASE = "INCREASE";
const DECREASE = "DECREASE";

const toggleSwitch = () => {
    {type: TOGGLE_SWITCH}
};

const increase = (difference) => {
    {
        type: INCREASE,
        difference
    }
};

const decrease = () => {
    {type: DECREASE}
};

const initialState = {
    toggle: false,
    counter: 0
};
```

index.html에 선언된 class, 태그, id를 변경시키기 위해 코드를 작성했습니다. 앞서, action은 `type`이 반드시 존재해야 한다 했는데, `increase`를 예로들면, 안에 `{}`에 type이 명시된 것을 볼 수 있습니다.


2. reducer 만들기

위 코드에서 reducer 함수를 생성합니다. reducer에는 state와 action을 인자로 받습니다.

```javascript
[index.js]

(생략)

const reducer = (state = initialState, action) => {
    switch(action.type) {
        case TOGGLE_SWITCH:
            return {
                ...state,
                toggle: !state.toggle
            };
        case INCREASE:
            return {
                ...state,
                counter: state.counter + action.difference
            };
        case DECREASE:
            return {
                ...state,
                counteR: state.counter - 1
            }
        default:
            return state;
    }
}
```

처음 state는 undefined 상태로 initialState를 기본값으로 사용하기 위해 `=`으로 넣어줍니다.


2. store 만들기

store는 하나만 존재할 수 있다고 했기 때문에, 하나만 작성합니다. 이때 redux를 import해 사용합니다.

기존 코드에서 상단에 redux를 import하고 하단에 `createStore`를 이용해 store를 생성합니다.

```javascript
[index.js]

import { reateStore } from "redux";

(생략)

const store = createStore(reducer);
```


3. render 만들기

render는 상태가 업데이트 될 때마다 호출됩니다.

```javascript
[index.js]

(생략)

const render = () => {
    const state = store.getState();

    if(state.toggle) {
        divToggle.classList.add("active");
    } else {
        divToggle.classList.remove("active");
    }

    counter.innerText = state.counter;
}

render();
```

store에서 state를 가져오고 해당 state에 따라 클래스를 추가하고 삭제하며 counter 값을 변경해줍니다.

3. subscribe 만들기

store의 상태가 변경될 때마다 render 함수가 호출되는데 이때, subscribe를 사용하면 됩니다.

subscribe 함수는 파라미터로 `함수` 형태로 전달합니다.

```javascript
[index.js]

(생략)

const listener = () => {
    console.log("state update");
}

const unsubscribe = store.subscribe(listener);

unsubscribe(render);
```

4. action 실행

이제 어떤 action이 발생할 때 처리를 해보도록 하겠습니다.

```javascript
[index.js]

divToggle.onclick = () => {
    store.dispatch(toggleSwitch());
};

btnIncrease.onclick = () => {
    store.dispatch(increase(1));
};

btnDecrease.onclick = () => {
    store.dispatch(decrease());
}
```

위 코드를 넣어 클릭 시 dispatch가 발생할 수 있도록 합니다.

책의 예제를 적용해보았지만 작동하지 않습니다. async/await를 사용해야 하는 것 같은데, 이 부분은 해결이 된다면 다시 올리도록 하겠습니다.

```javascript
[index.js]

import { createStore } from "redux";

const divToggle = document.querySelector(".toggle");
const counter = document.querySelector("h1");
const btnIncrease = document.querySelector("#increase");
const btnDecrease = document.querySelector("#decrease");

const TOGGLE_SWITCH = "TOGGLE_SWITCH";
const INCREASE = "INCREASE";
const DECREASE = "DECREASE";

const toggleSwitch = () => {
    {type: TOGGLE_SWITCH}
};

const increase = (difference) => {
    {
        type: INCREASE,
        difference
    }
};

const decrease = () => {
    {type: DECREASE}
};

const initialState = {
    toggle: false,
    counter: 0
};

const reducer = (state = initialState, action) => {
    switch(action.type) {
        case TOGGLE_SWITCH:
            return {
                ...state,
                toggle: !state.toggle
            };
        case INCREASE:
            return {
                ...state,
                counter: state.counter + action.difference
            };
        case DECREASE:
            return {
                ...state,
                counteR: state.counter - 1
            }
        default:
            return state;
    }
}

const listener = () => {
    console.log("state update");
}

const unsubscribe = store.subscribe(listener);

unsubscribe();

const store = createStore(reducer);

const render = () => {
    const state = store.getState();

    if(state.toggle) {
        divToggle.classList.add("active");
    } else {
        divToggle.classList.remove("active");
    }

    counter.innerText = state.counter;
}

render();
store.subscribe(render);


divToggle.onclick = () => {
    store.dispatch(toggleSwitch());
};

btnIncrease.onclick = () => {
    store.dispatch(increase(1));
};

btnDecrease.onclick = () => {
    store.dispatch(decrease());
}
```