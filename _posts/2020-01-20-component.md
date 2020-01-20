---
layout: post
title:  "컴포넌트 생성"
subtitle: "컴포넌트를 사용하는 방법에 대해 알아봅시다."
date:   2020-01-20 13:15:00 +0900
categories: ['technique']
tags: React
comments: true
---


### 컴포넌트

컴포넌트는 `어떤 한 단위`라고 생각하면 쉽습니다. 코드를 작성하는데 있어, 모든 기능을 한 곳에 작성하기 보단, 각 기능을 하나의 단위에 작성하고 다른 곳에서 불러와 사용하는 것이 코드를 개선하는데 더 높은 효율을 가져옵니다.


하나의 함수, 클래스와 같이 React에서는 `컴포넌트` 라는 단위로 사용하게 됩니다.



### 컴포넌트 생성

생성한 React 프로젝트 폴더에서 `src` 폴더 밑에 `FirstComponent.js` 라는 파일을 생성하고 아래 코드를 작성합니다.

```JSX
import React from "react";

const FirstComponent = () => {
    return <div>"첫번째 컴포넌트!"</div>;
};

export default FirstComponent;
```

`import React from "react";`는 해당 js 파일에서 react를 사용한다는 의미입니다. 그래야 react로 코드가 동작하게 됩니다.

`const FirstComponent = () => {}` FirstComponent 라는 변수를 생성합니다. 이 변수에는 `() => {}`가 할당되는데, 이를 `화살표 함수`라 합니다.

즉, FirstComponent는 화살표 함수가 할당된 것이며, `const`로 상수형태가 되어 변할 수 없게 됩니다.


마지막으로, `export default FirstComponent;`는 FirstComponent를 다른 곳에서 사용할 수 있도록 내보내는 작업입니다.

이때, firstComponent는 파일의 이름을 따라가는 것이 아니라, 화살표 함수의 이름을 따라갑니다.



생성한 컴포넌트를 App.js에서 확인하기 위해 아래와 같이 코드를 추가합니다.

```JSX
[App.js]

import React, { Fragment } from 'react';
import FirstComponent from "./FirstComponent";
import "./App.css";

function App() { // 함수형 컴포넌트
  let name = "관리자";
  return (
    
    <Fragment>
      <div className="react"> {/*주석입니다. */}
        {name === "관리자" && <h2>{name}님 안녕하세요. (관리자)</h2>}
      </div>
      <FirstComponent></FirstComponent>
    </Fragment>
    
  );
}

export default App;
```

