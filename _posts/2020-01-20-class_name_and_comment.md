---
layout: post
title:  "ClassName과 주석"
subtitle: "ClassName과 주석에 대해 알아봅시다."
date:   2020-01-20 12:00:00 +0900
categories: ['technique']
tags: React
comments: true
---

### ClassName

웹 프론트엔드를 개발하다 보면, 해당 영역을 예쁘게 꾸미기 위해 다양한 색상을 넣게 됩니다. 또한, 반응형을 위해서도 css를 많이 사용하게 됩니다.

React에서는 `class="이름"`을 사용하지 않고 `className="이름"`을 이용합니다.

```JSX
[app.js]

import React, { Fragment } from 'react';
import "./App.css";

function App() { // 함수형 컴포넌트
  let name = "관리자";
  return (
    <Fragment>
      <div className="react">
        {name === "관리자" && <h2>{name}님 안녕하세요. (관리자)</h2>}
      </div>
    </Fragment>
    
  );
}

export default App;
```

```css
[app.css]

.react {
  background: yellow;
  color: black;
  font-size: 20pt;
  font-weight: bold;
  padding: 10px;
}
```

app.css에서 `react`라는 클래스 이름에 대해 꾸미고자 하는 경우, app.js에서 `className`으로 활용할 수 있습니다.


### 주석

JSX에서 주석을 작성하는 방법은 여러가지가 있습니다.

```JSX
import React, { Fragment } from 'react';
import "./App.css";

function App() { // 함수형 컴포넌트
  let name = "관리자";
  return (
    <Fragment> // 주석입니다.
      <div className="react"> {/*주석입니다. */}
        {name === "관리자" && <h2>{name}님 안녕하세요. (관리자)</h2>}
      </div>
    </Fragment>
    
  );
}

export default App;
```

4번째 `// 함수형 컴포넌트`라는 주석은 javascript에서의 주석입니다. 해당 내용이 출력되지 않습니다.

하지만, 7번째 `// 주석입니다.` 부분은 return 안에서 작성되어, 해당 문자열을 HTML 코드 상 문자열로 인식하여 주석이 되지 않습니다.

주석을 사용하고 싶은 경우, 8번째 `{/*주석입니다.*/}` 처럼 사용해야 합니다.