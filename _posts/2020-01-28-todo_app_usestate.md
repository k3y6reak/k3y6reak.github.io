---
layout: post
title:  "Todo App - useState 기능 구현"
subtitle: "Todo App을 만들면서 React를 다루어 봅시다."
date:   2020-01-28 10:20:00 +0900
categories: ['technique']
tags: React
comments: true
---

#### todo 상태관리

할 일을 추가, 수정, 삭제 기능을 사용하기 위해서는 각 데이터에 대한 상태관리가 필요합니다.

먼저, App.js에서 todo를 사용하기 위해서 useState를 사용해야합니다.

```javascript
[App.js]

import React, { useState } from 'react';
import './App.css';
import TodoTemplate from './TodoTemplate';
import TodoInsert from "./TodoInsert"
import TodoList from "./TodoList";

const App = () => {
  const [todos, setTodos] = useState([{

  }])

  return (
    <TodoTemplate>
      <TodoInsert></TodoInsert>
      <TodoList todos={todos}></TodoList>
    </TodoTemplate>
  );
};


export default App;
```

기존 App.js에서 useState를 이용해 todos, setTods를 사용할 준비를 합니다.

TodoListItem.js 에서는 할 일에 대해 완료된 경우와 진행중인 경우에 대해 서로다른 표시를 해줍니다.

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

const TodoListItem = ( { todo }) => {
    const { text, checked } = todo;

    return (
        <div className="TodoListItem">
            <div className={cn("checkbox", { checked })}>
                {checked ? <MdCheckBox></MdCheckBox> : <MdCheckBoxOutlineBlank></MdCheckBoxOutlineBlank>}
                <div className="text">{text}</div>
            </div>
            
            <div className="remove">
                <MdRemoveCircleOutline></MdRemoveCircleOutline>
            </div>
        </div>
    )
};

export default TodoListItem;
```

할 일이 완료된 경우에는 `MdCheckBox`가 그렇지 않은 경우네 `MdCheckBoxOutlineBlack`가 나타납니다.

TodoList.js에서는 기존에 디자인을 위해 3개의 TodoListItem 컴포넌트를 추가했었지만, 실제 기능을 구현하기 위해서 해당 컴포넌트를 map을 이용해 데이터가 존재하는 만큼 반복될 수 있도록 합니다.

```javascript
[TodoList.js]

import React from "react";
import TodoListItem from "./TodoListItem";
import "./TodoList.scss";

const TodoList = ({ todos }) => {
    return (
        <div className="TodoList">
            {todos.map(todo => (
                <TodoListItem todo={todo} key={todo.id}></TodoListItem>
            ))}
        </div>
    );
};

export default TodoList;
```

