---
layout: post
title:  "Context API"
subtitle: "Context API에 대해 알아봅시다."
date:   2020-01-28 16:21:00 +0900
categories: ['technique']
tags: React
comments: true
---


### Context API

컴포넌트간 어떤 값을 전달하기 위해서 props를 이용했던 적이 있습니다. 이런 방식은 컴포넌트 구조가 부모-자식이 많아지고 복잡해지는 경우, props로 계속 전달 전달 해야 합니다. 그러면 매우 불편하게 작성해야 합니다.

위와 같은 불편을 해소하고자, Context라는 것을 사용합니다. 전달되어야 하는 값을 하나의 공간에 두고, 각 컴포넌트에서 가져와 사용하면 props를 통해 전달하는 것보다 효율적입니다.


context 폴더를 만들고 그 안에, `Color.js` 파일을 생성합니다.

```javascript
[context/Color.js]

import { createContext } from "react";

const Colorcontext = createContext({color: "black"});

export default Colorcontext;
```

새로운 context를 사용하기 위해서는 `createContext`를 이용합니다.

다음으로, Consumer/Provider(소비자/생산자) 모델 방식으로 코드를 작성합니다.

```javascript
[App.js]

import React from 'react';
import './App.css';
import ColorBox from './ColorBox';

const App = () => {
  return (
    <div>
      <ColorBox></ColorBox>
    </div>
  )
}

export default App;
```

```javascript
[ColorBox.js]

import React from "react";
import ColorContext from "./context/Color";

const ColorBox = () => {
    return (
        <ColorContext.Consumer>
            {value => (
                <div
                    style={
                        {width: "64px", height: "64px", background: value.color}
                    }
                >
                </div>
            )}
        </ColorContext.Consumer>
    )
}

export default ColorBox;
```

위와 같이 ColorBox.js(Consumer) 코드를 작성하면 화면에 검정색 박스가 생성됩니다. 여기서 App.js(Provider) 코드를 수정합니다.

```javascript
[App.js]

import React from 'react';
import './App.css';
import ColorBox from './ColorBox';
import ColorContext from "./context/Color";

const App = () => {
  return (
    <ColorContext.Provider value={{color: "red"}}>
      <div>
        <ColorBox></ColorBox>
      </div>
    </ColorContext.Provider>
  )
}

export default App;
```

Provider 코드에서는 value 값을 명시해야 제대로 작동합니다.

위 코드에서 Comsumer/Provider는 고정된 값으로만 사용했습니다. 개발을 진행하다 보면 고정값이 아닌 변하는 값으로 작업을 진행하게 됩니다. 

```javascript
[context/Color.js]

import React, { createContext, useState } from "react";

const ColorContext = createContext({
    state: {color: "black", subcolor: "red"},
    actions: {
        setColor: () => {},
        setSubcolor: () => {}
    }
});

const ColorProvider = ({ children }) => {
    const [color, setColor] = useState("black");
    const [subcolor, setSubcolor] = useState("red");

    const value = {
        state: {color, subcolor},
        actions: {setColor, setSubcolor}
    };

    return (
        <ColorContext.Provider value={value}>{children}</ColorContext.Provider>
    )
};

const { Consumer: ColorConsumer } = ColorContext;

export {ColorProvider, ColorConsumer};
export default ColorContext;
```

Color.js를 위 코드로 변경합니다. 

위 코드에서 ColorProvider 함수를 새로 만들었고, `ColorContext.Provider` 컴포넌트에서 `value`를 통해 값을 전달하고 있습니다.

이때, value는 `state`와 `actions`로 값을 갖고 있는데, 나중에 다른 컴포넌트에서 Context를 사용할 때 편리함을 주게 됩니다.



```javascript
[App.js]

import React from 'react';
import './App.css';
import ColorBox from './ColorBox';
import { ColorProvider } from "./context/Color";

const App = () => {
  return (
    <ColorProvider>
      <div>
        <ColorBox></ColorBox>
      </div>
    </ColorProvider>
  )
}

export default App;
```

```javascript
[ColorBox.js]

import React from "react";
import { ColorConsumer } from "./context/Color";

const ColorBox = () => {
    return (
        <ColorConsumer>
            {
                value => (
                 <>
                    <div
                        style = {
                            {wdith: "64px", height: "64px", background: value.state.color}
                        }
                    >
                    </div>
                    <div
                        style = {
                            {wdith: "32px", height: "32px", background: value.state.subcolor}
                        }
                    >
                    </div>
                 </>   
                )
            }
        </ColorConsumer>
    )
}

export default ColorBox;
```

앞서 Provider에서 value로 값을 보낸 데이터를 Consumer에서는 value로 받아 style을 적용시켰습니다.

이렇게 다른 컴포넌트에서 쉽게 사용이 가능합니다.