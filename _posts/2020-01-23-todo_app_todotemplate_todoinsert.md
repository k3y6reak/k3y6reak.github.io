---
layout: post
title:  "Todo App - TodoTemplate, TodoInsert 만들기"
subtitle: "Todo App을 만들면서 React를 다루어 봅시다."
date:   2020-01-23 14:46:00 +0900
categories: ['technique']
tags: React
comments: true
---

### Todo App

지금까지 배워온 React 기본 개념을 통해 Todo App을 만들면서 이해해 보겠습니다.

새로운 react app 프로젝트를 생성하고, `yarn add node-sass classnames react-icons`를 입력해 사용할 수 있도록 준비합니다.


만들어진 Todo 프로젝트에서 index.css를 수정합니다.

```css
body {
  margin: 0;
  padding: 0;
  background: #e8eaed;
}
```

#### TodoTemplate

Todo App의 상단 타이틀을 만들어보겠습니다.

```javascript
[TodoTemplate.js]

import React from "react";
import "./TodoTemplate.scss";

const TodoTemplate = ({children}) => {
    return (
        <div className="TodoTemplate">
            <div className="app-title">일정 관리</div>
            <div className="content">{children}</div>
        </div>
    )
}

export default TodoTemplate;
```

```scss
[TodoTemplate.scss]

.TodoTemplate {
    width: 512px;
    margin-left: auto;
    margin-right: auto;
    margin-top: 6rem;
    border-radius: 4px;
    overflow: hidden;

    .app-title {
        background: #22b8cf;
        color: white;
        height: 4rem;
        font-size: 1.5rem;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .content {
        background: white;
    }
}
```

TodoTemplate.scss를 살펴보면 TodoTemplate라는 클래스 이름을 갖는 태그에 대한 css와 그 안의 app-title, content에 대한 css를 설정하는 코드입니다.


scss는 html 코드를 작성하면서, 태그의 깊이에 따라 css도 같은 구조를 갖고 있습니다.


#### TodoInsert

Todo App에서는 할 일을 추가, 삭제와 같은 기능이 제공되어야 합니다. 그 중에서도 `추가` 할 수 있도록 화면을 구성합니다.

```javascript
[TodoInsert.scss]

.TodoInsert {
    display: flex;
    background: #495057;

    input {
        background: none;
        outline: none;
        border: none;
        padding: 0.5rem;
        font-size: 1.125rem;
        line-height: 1.5;
        color: white;
        &::placeholder {
            color: #dee2e6;
        }
        flex: 1;
    }

    button {
        background: none;
        outline: none;
        border: none;
        background: #868e96;
        color: white;
        padding-left: 1rem;
        padding-right: 1rem;
        font-size: 1.5rem;
        display: flex;
        align-items: center;
        cursor: pointer;
        transition: 0.1s background ease-in;
        &:hover {
            background: #adb5db;
        }
    }
}
```

```javascript
[TodoInsert.js]

import React from "react";
import { MdAdd } from "react-icons/md";
import "./TodoInsert.scss";

const TodoInsert = () => {
    return (
        <form className="TodoInsert">
            <input placeholder="할 일 입력."></input>
            <button type="submit"><MdAdd></MdAdd></button>
        </form>
    );
};

export default TodoInsert;
```


TodoInsert.js를 살펴보면 `MdAdd`가 있습니다. 이는, React에서 아이콘을 사용할 수 있도록 해주는 편리한 기능입니다.



* 반드시 scss 코드를 하나씩 작성하고 변화하는 것을 살펴보셔야 합니다.