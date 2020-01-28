---
layout: post
title:  "Todo App - 최적화"
subtitle: "Todo App을 만들면서 React를 다루어 봅시다."
date:   2020-01-28 11:46:00 +0900
categories: ['technique']
tags: React
comments: true
---

### 최적화

지금까지 Todo App을 만들면서 추가, 수정, 삭제 기능을 구현했습니다. 몇몇개의 할 일을 기준으로 개발했기 때문에 실제 사용하는데 있어서 불편함을 느끼지 못했습니다.

하지만, 개발을 진행할때 항상 `엄청난 데이터`에 대해 고민해야 합니다.

가령, 할 일이 1만개 이상이 된다면 이 데이터를 모두 화면에 보여줄지, 스크롤하면서 나타내 줄지 등. 다양한 고민을 해봐야합니다.


테스트를 하기 위해 App.js에 할 일을 5000개를 추가해보겠습니다.

```javascript
[App.js]

import React, { useState, useRef, useCallback } from 'react';
import './App.css';
import TodoTemplate from './TodoTemplate';
import TodoInsert from "./TodoInsert"
import TodoList from "./TodoList";

const InsertTodo = () => {
  const arr = [];
  for(let i = 1; i <= 5000; i++) {
    arr.push({
      id: i,
      text: `test ${i}`,
      checked: false
    })
  }

  return arr;
}

const App = () => {
  const [todos, setTodos] = useState(InsertTodo);

  const nextId = useRef(5001);

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

InsertTodo 라는 함수를 만들어, 5000개를 추가합니다. 렌더링 되는데 많은 시간이 걸리는 것을 확인할 수 있습니다.


렌더링을 하는데 `많은 시간`이 걸리는 환경을 생각해야 합니다.

1. props가 변경될 때
2. state가 변경될 대
3. 부모 컴포넌트가 리렌더링될 때
4. forceUpdate가 실행될 때

위 조건들이 발생할때 많은 시간이 발생합니다. 물론, 데이터가 적은 경우에는 그렇지 않겠지만요.


#### React.memo

함수형 컴포넌트로 개발하는 경우에는 shouldComponentUpdate를 사용할 수 없기 때문에, React.memo를 사용합니다.

`export default React.memo(TodoListItem)`과 같이 사용합니다.

이렇게 사용하면 todo, onRemove, onToggle이 변경되지 않으면 리렌더링을 하지 않습니다.

하지만, todo, onRemove, onToggle이 변경되는 경우에도 생각을 해야 합니다.


```javascript
[App.js]

(생략)
const onInsert = useCallback( text => {
	const todo = {
	  id: nextId.current,
	  text,
	  checked: false
	};
	  setTodos(todos.concat(todo));
	  nextId.current += 1;
}, []);

(생략)

const onToggle = useCallback(id => {
	setTodos(todos => todos.map(todo => todo.id === id ? {...todo, checked: !todo.checked}: todo));
}, []);

(생략)
```

onInsert와 onToggle 함수를 위 코드로 변경합니다. 


또한, useState를 사용하지 않고, useReducer를 사용합니다. useReducer는 다양한 컴포넌트 상황에 상태를 업데이트 해주고 싶을 때 사용하는 hook 입니다.

이때, switch ~ case 구문을 사용해서, 사용하게 됩니다.


```javascript
[App.js]

import React, { useReducer, useRef, useCallback } from 'react';
import './App.css';
import TodoTemplate from './TodoTemplate';
import TodoInsert from "./TodoInsert"
import TodoList from "./TodoList";

const InsertTodo = () => {
  const arr = [];
  for(let i = 1; i <= 5000; i++) {
    arr.push({
      id: i,
      text: `test ${i}`,
      checked: false
    })
  }

  return arr;
}

const todoReducer = (todos, action) => {
  switch(action.type) {
    case "INSERT":
      return todos.concat(action.todo);
    case "REMOVE":
      return todos.filter(todo => todo.id !== action.id);
    case "TOGGLE":
      return todos.map(todo => todo.id === action.id ? {...todo, checked: !todo.checked } : todo);
    default:
      return todos;
  }
}

const App = () => {
  const [todos, dispatch] = useReducer(todoReducer, undefined, InsertTodo);

  const nextId = useRef(5001);

  const onInsert = useCallback( text => {
    const todo = {
      id: nextId.current,
      text,
      checked: false
    };
      dispatch({ type: "INSERT", todo});
      nextId.current += 1;
    }, []);

  const onRemove = useCallback(id => {
    dispatch({ type: "REMOVE", id});
  }, []);

  const onToggle = useCallback(id => {
    dispatch({ type: "TOGGLE", id})
  }, []);

  return (
    <TodoTemplate>
      <TodoInsert onInsert={onInsert}></TodoInsert>
      <TodoList todos={todos} onRemove={onRemove} onToggle={onToggle}></TodoList>
    </TodoTemplate>
  );
};


export default App;
```

reducer를 사용해서 변경된 내용만 렌더링해주게 됩니다. Todo App 화면에서 할 일을 체크하면 기존보다 더 빠르게 작동하는 것을 확인할 수 있습니다.

또한, TodoListItem.js 에서 React.memo를 사용했으니, TodoList.js에서도 React.memo를 사용합니다.


### react-virtualized

서두에 최적화 할 수 있는 방법에 대해서 설명했습니다. 그 중에서 데이터를 한 번에 모두 볼 필요가 없는 경우에는 스크롤 시 데이터가 나타날 수 있도록 하는 것이 최적화를 하는데 매우 중요한 요소라 할 수 있습니다.

이때 사용하는 것이 react-virtualized 입니다. react-virtualized를 사용하기 위해서는 현재 실행중인 서버를 중지하고 `yarn add react-virtaulized` 명령어를 통해 사용할 수 있도록 추가합니다.

실제 데이터가 나타나는 TodoList.js와 TodoListItem.js를 수정합니다.


```javascript
[TodoList.js]

import React, { useCallback } from "react";
import TodoListItem from "./TodoListItem";
import "./TodoList.scss";
import { List } from "react-virtualized";

const TodoList = ({ todos, onRemove, onToggle }) => {
    const rowRenderer = useCallback( ({index, key, style}) => {
        const todo = todos[index];

        return (
            <TodoListItem
                todo={todo}
                key={key}
                onRemove={onRemove}
                onToggle={onToggle}
                style={style}>
            </TodoListItem>
        )
    }, [onRemove, onToggle, todos]);

    return (
        <List
            className="TodoList"
            width={512}
            height={513}
            rowCount={todos.length}
            rowHeight={57}
            rowRenderer={rowRenderer}
            list={todos}
            style={{outline: "none"}}>
        </List>
    );
};

export default React.memo(TodoList);
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

export default React.memo(
    TodoListItem,
    (prevProps, nextProps) => prevProps.todo === nextProps.todo
);
```

위 코드로 수정하면 클릭하는 순간 변경되는 것을 확인할 수 있습니다.