---
layout: post
title:  "Todo App - 할 일 삭제 기능 구현"
subtitle: "Todo App을 만들면서 React를 다루어 봅시다."
date:   2020-01-28 10:52:00 +0900
categories: ['technique']
tags: React
comments: true
---


### 삭제 기능 구현

할 일을 삭제하기 위해서 할 일의 `번호`를 사용합니다. 할 일은 `배열`로 관리되고 있는 상태이기 때문에 `filter`를 사용합니다.

App.js에서 `onRemove`함수를 구현합니다. 이때 해당 id를 가져와 todo의 상태를 변경합니다.

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

  return (
    <TodoTemplate>
      <TodoInsert onInsert={onInsert}></TodoInsert>
      <TodoList todos={todos} onRemove={onRemove}></TodoList>
    </TodoTemplate>
  );
};


export default App;
```

App.js에서 onRemove가 발생하는 경우 배열에서 삭제할 수 있도록 구현했습니다. 실제, TodoList에서도 반경이 되어야 하기 때문에 마찬가지로 onRemove를 설정합니다.

```javascript
[TodoList.js]

import React from "react";
import TodoListItem from "./TodoListItem";
import "./TodoList.scss";

const TodoList = ({ todos, onRemove }) => {
    return (
        <div className="TodoList">
            {todos.map(todo => (
                <TodoListItem todo={todo} key={todo.id} onRemove={onRemove}></TodoListItem>
            ))}
        </div>
    );
};

export default TodoList;
```

마찬가지로 TodoListItem.js에서 삭제 버튼을 클릭했을때를 구현해야 합니다.

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

const TodoListItem = ( { todo, onRemove }) => {
    const { id, text, checked } = todo;

    return (
        <div className="TodoListItem">
            <div className={cn("checkbox", { checked })}>
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

삭제하기 위해서 해당 id가 필요하므로 todo 에서 id를 받아옵니다. 또한, 삭제버튼 클릭시 `onClick`이벤트를 이용해서 onRemove가 실행되고, 해당 id를 인자로 보내줍니다.

