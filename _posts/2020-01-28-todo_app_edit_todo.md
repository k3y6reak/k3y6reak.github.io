---
layout: post
title:  "Todo App - 할 일 수정 기능 구현"
subtitle: "Todo App을 만들면서 React를 다루어 봅시다."
date:   2020-01-28 11:16:00 +0900
categories: ['technique']
tags: React
comments: true
---


### 수정 기능 구현

수정 기능 또한 삭제, 추가하는 것과 크게 다르지 않습니다. 해당 id를 기준으로 text를 변경하거나, 완료 표시를 진행중으로 변경하면 됩니다.

수정기능은 `onToggle` 함수로 구현하도록 하겠습니다.

마찬가지로 App.js에서 onToggle 함수를 구현합니다.

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

  const onRemove = useCallback(id => {
    setTodos(todos.filter(todo => todo.id !== id));
  }, [todos]);

  const onToggle = useCallback(id => {
    setTodos(todos.map(todo => todo.id === id ? {...todo, checked: !todo.checked}: todo));
  }, [todos]);

  return (
    <TodoTemplate>
      <TodoInsert onInsert={onInsert}></TodoInsert>
      <TodoList todos={todos} onRemove={onRemove} onToggle={onToggle}></TodoList>
    </TodoTemplate>
  );
};


export default App;
```

삭제처럼 TodoList.js, TodoListItem.js를 모두 수정합니다.

```javascript
[TodoList.js]

import React from "react";
import TodoListItem from "./TodoListItem";
import "./TodoList.scss";

const TodoList = ({ todos, onRemove, onToggle }) => {
    return (
        <div className="TodoList">
            {todos.map(todo => (
                <TodoListItem todo={todo} key={todo.id} onRemove={onRemove} onToggle={onToggle}></TodoListItem>
            ))}
        </div>
    );
};

export default TodoList;
```

```javascript
[TodoListItem.js]

import React from "react";
import cn from "classnames";
import "./TodoListItem.scss";
import {
    MdCheckBoxOutlineBlank,
    MdCheckBox,
    MdRemoveCircleOutline,
} from "react-icons/md";

const TodoListItem = ( { todo, onRemove, onToggle }) => {
    const { id, text, checked } = todo;

    return (
        <div className="TodoListItem">
            <div className={cn("checkbox", { checked })} onClick={() => onToggle(id)}>
                {checked ? <MdCheckBox></MdCheckBox> : <MdCheckBoxOutlineBlank></MdCheckBoxOutlineBlank>}
                <div className="text">{text}</div>
            </div>
            
            <div className="remove" onClick={() => onRemove(id)}>
                <MdRemoveCircleOutline></MdRemoveCircleOutline>
            </div>
        </div>
    )
};

export default TodoListItem;
```

