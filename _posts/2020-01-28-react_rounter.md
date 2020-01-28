---
layout: post
title:  "React Router"
subtitle: "React Router에 대해 알아봅시다."
date:   2020-01-28 13:57:00 +0900
categories: ['technique']
tags: React
comments: true
---

### React Router

리액트 라우터에 대해 알아보겠습니다. 라우터를 학습하기 위해 새로운 프로젝트를 생성하고 `yarn add react-router-dom` 명령어를 이용해 라우터를 사용할 수 있도록 준비합니다.

리액트에서 라우터를 사용하기 위해서 `index.js`를 수정합니다.

index.js에서 `App` 컴포넌트를 `BrowserRouter` 컴포넌트로 감싸 사용합니다.

```javascript
[index.js]

import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from "react-router-dom";
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(
    <BrowserRouter>
        <App />
    </BrowserRouter>,
    document.getElementById('root')
);

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister();
```


이제 Home.js, About.js 를 만들어 보겠습니다.

```javascript
[Home.js]

import React from "react";

const Home = () => {
    return (
        <div>
            <h1>Home</h1>
            <p>Home page</p>
        </div>
    );
};

export default Home;
```

웹 사이트를 접속하면 가장 처음 화면에 나타나는 페이지를 구현합니다.

```javascript
[About.js]

import React from "react";

const About = () => {
    return (
        <div>
            <h1>About</h1>
            <p>About page</p>
        </div>
    );
};

export default About;
```

위 두 코드를 작성했습니다. App.js 에 해당 컴포넌트를 추가하고 클릭 시 해당 페이지로 이동하도록 구현합니다.

```javascript
[App.js]

import React from 'react';
import logo from './logo.svg';
import './App.css';
import { Route } from 'react-router-dom';
import Home from './Home';
import About from './About';

const App = () => {
  return (
    <div>
      <Route path="/" component={Home}></Route>
      <Route paht="/about" component={About}></Route>
    </div>
  );
}

export default App;
```

App.js에 Home과 About을 화면에 나타내도록 합니다. 하지만, Home일때 Home만 About일 때 About만 나타나게 하고 싶다면, Route 컴포넌트에 `exact` props를 `true`로 설정합니다.

간단한 메뉴를 만들고 클릭 시, 해당 페이지로 이동하는 코드를 작성해 보겠습니다. 기본적으로 a 태그를 이용해서 링크를 사용하지만, React 내부 경로 페이지를 사용하기 위해서는 `Link` 컴포넌트를 사용합니다.

```javascript
[App.js]

import React from 'react';
import './App.css';
import { Route, Link } from 'react-router-dom';
import Home from './Home';
import About from './About';

const App = () => {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
      </ul>

      <hr/>
      
      <Route path="/" component={Home} exact={true}></Route>
      <Route paht="/about" component={About}></Route>
    </div>
  );
}

export default App;
```


서로 다른 메뉴가 동일한 페이지로 이동하는 경우에는 `path={["/about", "/about2"]}`와 같이 사용합니다.



#### URL Parameter, Query

특정 사람의 정보를 확인할 수 있는 페이지를 구현해 봅시다.

```javascript
[Person.js]

import React from "react";

const data = {
    p1: {
        name: "ppp1",
        description: "ppp1"
    },
    p2: {
        name: "ppp2",
        description: "ppp2"
    }
};

const Person = ({match}) => {
    const { username } = match.params;
    const person = data[username];

    if(!person) {
        return <div>No user</div>
    }

    return (
        <div>
            <h2>{username}({person.name})</h2>
            <p>{person.description}</p>
        </div>
    )
}

export default Person;
```

```javascript
[App.js]

import React from 'react';
import './App.css';
import { Route, Link } from 'react-router-dom';
import Home from './Home';
import About from './About';
import Person from './Person';

const App = () => {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/person/p1">ppp1</Link>
        </li>
        <li>
          <Link to="/person/p2">ppp2</Link>
        </li>
      </ul>

      <hr/>

      <Route path="/" component={Home} exact={true}></Route>
      <Route path="/about" component={About}></Route>
      <Route path="/person/:username" component={Person}></Route>
    </div>
  );
}

export default App;
```

p1, p2 두 명의 사람이 있습니다. 경로가 `/person/`을 기준으로 해당 사람의 데이터만 화면에 출력하고자 하는데, 이때 Route 컴포넌트에서 `:구분` 형식으로 사용합니다.

위 App.js 코드에서는 `:username`이 있습니다. p1, p2라는 username을 기준으로 Person.js에서 p1, p2에 대한 값을 받아 data에서 찾아 출력합니다.


다음으로 Query 입니다. 인터넷을 사용하다보면 `주소?test=1` 물음표 뒤에 test와 같은 데이터를 본적이 있을겁니다. 이 test가 바로 쿼리입니다.

About.js에서 check가 true일 때, 메시지가 나타나도록 해봅시다.


```javascript
[About.js]

import React from "react";
import qs from "qs";

const About = ({location}) => {
    const query = qs.parse(location.search, {
        ignoreQueryPrefix: true
    });

    const showDetail = query.check === "true";

    return (
        <div>
            <h1>About</h1>
            <p>About page</p>
            {showDetail && <p>check === true</p>}
        </div>
    );
};

export default About;
````

위 코드를 작성하고 `http://localhost:3000/about?check=true`로 접속하면 check === true 문자가 나타나는 것을 확인할 수 있습니다.

#### 서브 라우터

서브 라우터는 라우터를 거쳐 다시 라우터로 이동하는 것을 말합니다.

위 코드들은 1단계를 거쳐 화면에 나타내주었지만, 사용자 목록을 1단계에서 보여주고 2단계에서 그 내용을 보여주도록 구성해 보겠습니다.

```javascript
[App.js]

import React from 'react';
import './App.css';
import { Route, Link } from 'react-router-dom';
import Home from './Home';
import About from './About';
import Persons from './Persons';

const App = () => {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/persons">Persons</Link>
        </li>
      </ul>

      <hr/>

      <Route path="/" component={Home} exact={true}></Route>
      <Route path="/about" component={About}></Route>
      <Route path="/persons" component={Persons}></Route>
    </div>
  );
}

export default App;
```

App.js에서는 1단계로 이동하기 위한 `persons` 라우터를 만들어줍니다.

```javascript
[Persons.js]

import React from "react";
import { Link, Route } from "react-router-dom";
import Person from "./Person";

const Persons = () => {
    return (
        <div>
            <h3> Person List </h3>
            <ul>
                <li>
                    <Link to="/persons/p1">p1</Link>
                </li>
                <li>
                    <Link to="/persons/p2">p2</Link>
                </li>
            </ul>

            <Route
                path="/persons"
                exact
                render={() => <div>select person</div>}>
            </Route>
            <Route path="/persons/:username" component={Person}></Route>
        </div>
    );
}

export default Persons;

```

Persons.js로 이동한 후, 해당 위치에서 사람들의 목록을 보여주고, 해당 정보를 클릭하면 `person.js`로 2단계 이동하여 데이터를 화면에 나타내줍니다.

