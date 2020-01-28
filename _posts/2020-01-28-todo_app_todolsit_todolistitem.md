---
layout: post
title:  "Todo App - TodoList, TodoListItem 만들기"
subtitle: "Todo App을 만들면서 React를 다루어 봅시다."
date:   2020-01-28 09:35:00 +0900
categories: ['technique']
tags: React
comments: true
---

#### TodoListItem

TodoList에서 할 일을 하나씩 추가해서 사용자에게 보여주어야 합니다.

```javascript
[TodoListItem.js]

import React from "react";
import "./TodoListItem.scss";
import {
    MdCheckBoxOutlineBlank,
    MdCheckBox,
    MdRemoveCircleOutline,
} from "react-icons/md";

const TodoListItem = () => {
    return (
        <div className="TodoListItem">
            <div className="checkbox">
                <MdCheckBoxOutlineBlank></MdCheckBoxOutlineBlank>
                <div className="text">할 일</div>
            </div>
            <div className="remove">
                <MdRemoveCircleOutline></MdRemoveCircleOutline>
            </div>
        </div>
    )
};

export default TodoListItem;
```

TodoListItem.js에서는 `react-icons`를 사용하여 체크박스, 삭제 버튼을 만들어줍니다.

```scss
[TodoListItem.scss]

.TodoListItem {
    padding: 1rem;
    display: flex;
    align-items: center;
    &:nth-child(even) {
        background: #f8f9fa;
    }

    .checkbox {
        cursor: pointer;
        flex: 1;
        display: flex;
        align-items: center;

        svg {
            font-size: 1.5rem;
        }

        .text {
            margin-left: 0.5rem;
            flex: 1;
        }

        &:checked {
            svg {
                color: #22b8cf;
            }

            .text {
                color: #adb5bd;
                text-decoration: line-through;
            }
        }
    }

    .remove {
        display: flex;
        align-items: center;
        font-sizE: 1.5rem;
        color: #ff6b6b;
        cursor: pointer;
        &:hover {
            color: #ff8787;
        }
    }

    & + & {
        border-top: 1px solid #dee2e6;
    }
}
```

하나의 할 일을 보여주는 코드를 작성했다면 전체 할 일에 대한 목록을 보여주는 것도 구성해야 합니다.

TodoList.js에서는 TodoListItem.js 항목을 불러옵니다.

```javascript
[TodoList.js]

import React from "react";
import TodoListItem from "./TodoListItem";
import "./TodoList.scss";

const TodoList = () => {
    return (
        <div className="TodoList">
            <TodoListItem></TodoListItem>
            <TodoListItem></TodoListItem>
            <TodoListItem></TodoListItem>
        </div>
    );
};

export default TodoList;
```

현재 화면을 구성하는 단계이므로, TodoListItem 컴포넌트를 3개를 추가했습니다. 이 단계에서 화면을 디자인후, 기능을 구현하도록 합니다.


```scss
[TodoList.scss]

.TodoList {
    min-height: 320px;
    max-height: 513px;
    overflow-y: auto;
}
```

다음 포스팅에서 추가, 삭제하는 기능을 구현해보도록 하겠습니다.

