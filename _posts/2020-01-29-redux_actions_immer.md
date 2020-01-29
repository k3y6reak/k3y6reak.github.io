---
layout: post
title:  "Redux - actions & immer"
subtitle: "Redux에 대해 알아봅시다."
date:   2020-01-29 15:29:00 +0900
categories: ['technique']
tags: React
comments: true
---

### Redux ations

기존에 작성했던 코드들을 조금더 간편하게 작성하기 위해 Redux actions를 사용합니다.

Redux actions를 사용하기 위해, `yarn add redux-actions` 명령어로 추가해 줍니다.

```javascript
[modules/Counter.js]

import { createAction, handleActions } from "redux-actions";

const INCREASE = "counter/INCREASE";
const DECREASE = "counter/DECREASE";

export const increase = createAction(INCREASE);
export const decrease = createAction(DECREASE);

const initialState = {
    number: 0
};

const Counter = handleActions(
  {
      [INCREASE]: (state, action) => ({number: state.number + 1}),
      [DECREASE]: (state, action) => ({number: state.number - 1})
  },
  initialState,
);

export default Counter;
```

`createAction`과 `handleActions` 함수를 이용하면 코드가 깔끔해집니다.


```javascript
[modules/Todos.js]

import { createAction, handleActions } from "redux-actions";

const CHANGE_INPUT = "todos/CHANGE_INPUT";
const INSERT = "todos/INSERT";
const TOGGLE = "todos/TOGGLE";
const REMOVE = "todos/REMOVE";

export const changeInput = createAction(CHANGE_INPUT, input => input);

let id = 3;

export const insert = createAction(INSERT, text=> (
    {
        type: INSERT,
        todo: {
            id: id++,
            text,
            done: false
        }
    }
));

export const toggle = createAction(TOGGLE, id => id);
export const remove = createAction(REMOVE, id => id);

const initialState = {
    input: "",
    todos: [
        {
            id: 1,
            text: "redux test 1",
            done: true
        },
        {
            id: 2, 
            text: "redux test 2",
            done: false
        }
    ]
};

const Todos = handleActions(
   {
        [CHANGE_INPUT]: (state, action) => ({...state, input: action.payload}),
        [INSERT]: (state, action) => ({...state, todos: state.todos.concat(action.payload)}),
        [TOGGLE]: (state, action) => ({...state, todos: state.todos.map(todo => todo.id === action.payload ? {...todo, done: !todo.done} : todo)}),
        [REMOVE]: (state, action) => ({...state, todos: state.todos.filter(todo => todo.id !== action.id)})
   },
   initialState
);

export default Todos;
```

훨씬 눈에 보기 좋게 코드가 작성됐습니다. 여기서 `action.payload` 부분이 존재하는데, 한눈에 파악하기 어렵습니다. 이 부분을 아래와 같이 수정합니다.

```javascript
[modules/Todos.js]

const Todos = handleActions(
   {
        [CHANGE_INPUT]: (state, {payload: input}) => ({...state, input}),
        [INSERT]: (state, {payload: todo}) => ({...state, todos: state.todos.concat(todo)}),
        [TOGGLE]: (state, {payload: id}) => ({...state, todos: state.todos.map(todo => todo.id === id ? {...todo, done: !todo.done} : todo)}),
        [REMOVE]: (state, {payload: id}) => ({...state, todos: state.todos.filter(todo => todo.id !== id)})
   },
   initialState
);
```

#### immer 적용

immer를 사용하기 위해 `yarn add immer` 명령어로 추가합니다.

```javascript
[modules/Todos.js]

import { createAction, handleActions } from "redux-actions";
import produce from "immer";

const CHANGE_INPUT = "todos/CHANGE_INPUT";
const INSERT = "todos/INSERT";
const TOGGLE = "todos/TOGGLE";
const REMOVE = "todos/REMOVE";

export const changeInput = createAction(CHANGE_INPUT, input => input);

let id = 3;

export const insert = createAction(INSERT, text=> (
    {
        type: INSERT,
        todo: {
            id: id++,
            text,
            done: false
        }
    }
));

export const toggle = createAction(TOGGLE, id => id);
export const remove = createAction(REMOVE, id => id);

const initialState = {
    input: "",
    todos: [
        {
            id: 1,
            text: "redux test 1",
            done: true
        },
        {
            id: 2, 
            text: "redux test 2",
            done: false
        }
    ]
};

const Todos = handleActions(
   {
        [CHANGE_INPUT]: (state, {payload: input}) => produce(state, draft => {draft.input = input}),

        [INSERT]: (state, {payload: todo}) => produce(state, draft => {draft.todos.push(todo)}),

        [TOGGLE]: (state, {payload: id}) => produce(state, draft => {
            const todo = draft.todos.find(todo => todo.id === id);
            todo.done = !todo.done;
        }),

        [REMOVE]: (state, {payload: id}) => produce(state, draft => {
            const index = draft.todos.findIndex(todo => todo.id === id);
            draft.todos.splice(index, 1);
        })
   },
   initialState
);

export default Todos;
```

`...state`와 같이 사용하면 코드가 길어지고 관리하기 어려워지면 불변성을 지키기 힘들어집니다. 이때, `immer`의 `produce`를 사용하여 관리합니다.

