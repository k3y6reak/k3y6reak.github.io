---
layout: post
title:  "Todo App - 할 일 추가 기능 구현"
subtitle: "Todo App을 만들면서 React를 다루어 봅시다."
date:   2020-01-28 10:35:00 +0900
categories: ['technique']
tags: React
comments: true
---

### 추가 기능 구현

기존에 작성했던 Todoinsert.js를 수정하도록 하겠습니다. 

```javascript
[TodoInsert.js]

import React, { useState, useCallback } from "react";
import { MdAdd } from "react-icons/md";
import "./TodoInsert.scss";

const TodoInsert = () => {
    const [value, setValue] = useState("");

    const onChange = useCallback(e => {
        setValue(e.target.value);
    }, []);

    return (
        <form className="TodoInsert">
            <input
                placeholder="할 일 입력"
                value={value}
                onChange={onChange}>
            </input>
            
            <button type="submit"><MdAdd></MdAdd></button>
        </form>
    );
};

export default TodoInsert;
```

할 일을 추가하는 기능을 구현했으니, 실제 화면에서 추가될 수 있도록 App.js를 수정합니다.

```javascript
[App.js]

import React, { useState, useRef, useCallback } from 'react';
import './App.css';
import TodoTemplate from './TodoTemplate';
import TodoInsert from "./TodoInsert"
import TodoList from "./TodoList";

const App = () => {
  const [todos, setTodos] = useState([])

  const nextId = useRef(1);

  const onInsert = useCallback(
    text => {
      const todo = {
        id: nextId.current,
        text,
        checked: false,
      };
      setTodos(todos.concat(todo));
      nextId.current += 1;
    }, [todos]);

  return (
    <TodoTemplate>
      <TodoInsert onInsert={onInsert}></TodoInsert>
      <TodoList todos={todos}></TodoList>
    </TodoTemplate>
  );
};


export default App;
```

할 일에 대한 번호를 지정하기 위해 useRef를 이용해 처음 번호를 1로 설정합니다.

useCallback을 이용해서 해당 할 일에 대한 초기 세팅을 진행합니다. 할 일이 추가될 때마다 nextId의 값을 변경합니다.

기본적으로 추가하는 기능을 구현했습니다. 여기서 한 가지 더 수정해야하는 부분이 있습니다.

```javascript
[TodoInsert.js]

import React, { useState, useCallback } from "react";
import { MdAdd } from "react-icons/md";
import "./TodoInsert.scss";

const TodoInsert = ({ onInsert }) => {
    const [value, setValue] = useState("");

    const onChange = useCallback(e => {
        setValue(e.target.value);
    }, []);

    const onSubmit = useCallback(e => {
            onInsert(value);
            setValue("");
            e.preventDefault();
        }, [onInsert, value]);

    return (
        <form className="TodoInsert" onSubmit={onSubmit}>
            <input
                placeholder="할 일 입력"
                value={value}
                onChange={onChange}>
            </input>
            
            <button type="submit"><MdAdd></MdAdd></button>
        </form>
    );
};

export default TodoInsert;
```

TodoInsert.js 에서 해당 할 일이 추가되는 경우 form으로 submit이 실행되어 html의 기본 submit이 실행됩니다. 이를 막기 위해 `preventDeafult()`를 사용하여 기본 submit을 실행하지 못하도록 합니다.

여기가지 코드를 작성하면 실제 할 일이 추가되는 것을 확인할 수 있습니다.

