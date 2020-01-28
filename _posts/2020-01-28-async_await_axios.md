---
layout: post
title:  "async/await & axios"
subtitle: "async/await & axios에 대해 알아봅시다."
date:   2020-01-28 15:17:00 +0900
categories: ['technique']
tags: React
comments: true
---

### News API 사용

외부 API를 활용하여 데이터를 가져오고, 처리하는 방법에 대해 알아보겠습니다.

먼저, [뉴스API](https://newsapi.org/register)에 가입해 Register key를 발급받습니다.

대부분 외부 API를 사용하기 위해서는 key를 발급받습니다.

새로운 react 프로젝트를 생성하고 `yarn add axios` 명령어를 통해 axios를 사용할 수 있도록 합니다.

```javascript
[App.js]

import React, { useState } from 'react';
import './App.css';
import axios from "axios";

const App = () => {
  const apiKey = "발급받은 키";

  const [data, setData] = useState(null);
  const onClick = async() => {
    try {
      const res = await axios.get("https://newsapi.org/v2/top-headlines?country=kr&apiKey=" + apiKey);
      setData(res.data);
    } catch(e) {
      console.log(e);
    }
  };

  return (
    <div>
      <button onClick={onClick}>가져오기</button>
      {data && <p>{JSON.stringify(data)}</p>}
    </div>
  );
}

export default App;
```

apiKey 변수에 발급받은 키를 넣어줍니다. 화면의 가져오기 버튼을 클릭하면 JSON 형식의 데이터가 화면에 출력됩니다.

해당 데이터를 살펴보겠습니다.

```json
{ 
   "status":"ok",
   "totalResults":32,
   "articles":[ 
      { 
         "source":{ 
            "id":null,
            "name":"Kbs.co.kr"
         },
         "author":null,
         "title":"신종 코로나 바이러스 확산 우려…뉴욕·유럽 증시 '급락' - KBS뉴스",
         "description":"[앵커] 신종 코로나 바이러스 확산 우려가 전세계로 확대되면서 밤새 뉴욕과 유럽 증시가 급락했습니다. 5명의 감염 확진자 확인 이후 현재까지 미국 내 추가 감염자가 없는 것으로 전해진 가운데, 정부 기관과",
         "url":"http://news.kbs.co.kr/news/view.do?ncd=4370137",
         "urlToImage":"https://nsimg.kbs.co.kr/data/news/2020/01/28/4370137_90.jpg",
         "publishedAt":"2020-01-28T03:16:00Z",
         "content":null
      },

(생략)
}
```

json 형태의 데이터를 받아오니, 크게 `status`, `totalResults`, `articles`가 있습니다. 여기서 articles는 다시, `source`, `author`, `title`, `description`, `url`, `urlToImage`, `publishedAt`, `content`가 있습니다.

UI/UX를 제외하고 해당 데이터를 깔끔하게 보여줄 수 있도록 구성해 보겠습니다.

```javascript
[App.js]

import React, { useState } from 'react';
import './App.css';
import axios from "axios";

const App = () => {
  const apiKey = "발급받은 키";

  const [articles, setArticles] = useState(null);
  const onClick = async() => {
    try {
      const res = await axios.get("https://newsapi.org/v2/top-headlines?country=kr&apiKey=" + apiKey);
      setArticles(res.data.articles);
    } catch(e) {
      console.log(e);
    }
  };

  return (
    <div>
      <button onClick={onClick}>가져오기</button>

      <div>
        {articles && articles.map(article => (<div key={article.url}>{article.title}</div>))}
      </div>

    </div>
  );
}

export default App;
```

기존의 코드에서는 res.data로 받아왔었지만, res.data.articles로 데이터를 받아왔습니다.

위 JOSN 구조를 살펴보면 요청해서 받은 데이터는 `data`에 있고, 그 안에 각 항목에 대해서 `.`을 붙여 접근할 수 있습니다.

`articles`로 접근한 데이터를 map을 이용해 데이터를 하나씩 꺼내 화면에 `title`를 출력해 주는 코드입니다.



실제 개발 단계에서는 컴포넌트별로 구조화하여 데이터를 화면에 출력해주면 됩니다.

