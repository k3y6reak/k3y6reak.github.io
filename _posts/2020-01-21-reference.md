---
layout: post
title:  "Reference"
subtitle: "Reference에 대해 알아봅시다."
date:   2020-01-21 10:07:00 +0900
categories: ['technique']
tags: React
comments: true
---


### Reference

Reference. 즉, ref는 HTML 코드에서 특정 id에 대해 어떤 효과 혹은 변화를 주기 위해서 사용됩니다.
요약하자면, 특정 DOM을 직접적으로 작업하기 위해 사용하는 것입니다.


리액트를 통해서 DOM을 조작하는 방법에 대해 알아보겠습니다.

아래 2개의 코드를 작성해 주세요.


```javascript
[Check.css]

.success {
    background-color: lightgreen;
}

.failure {
    background-color: lightcoral;
}
```

```javascript
[PasswdCheck.js]

import React, { useState } from "react";
import "./Check.css";


const PasswdCheck =  () => {
    const [password, setPassword] = useState("");
    const [clicked, setClicked] = useState("");
    const [validated, setValidated] = useState("");

    const onClick = () => {
        setValidated(password === "0000" ? true : false);
        setClicked(true);
    };

    const onPasswd = (e) => setPassword(e.target.value);


    return (
        <div>
            <input type="password"
                   onChange={onPasswd}
                   className={clicked ? (validated ? "success" : "failure") : ""}>        
            </input>

            <button onClick={onClick}>확인하기</button>

        </div>
    );
};

export default PasswdCheck;
```

css는 성공, 실패에 따라 input 태그 색상이 변경되는 코드 입니다.

PasswdCheck.js 파일에서 `onChange` 부분에서 onPasswd를 호출하는데, onPasswd는 input 태그에 입력된 값을 가져오게 됩니다.

className 부분에서 3항 연산자를 이용하여 클릭했는지, 인증 됐는지를 확인해서 이름을 선택하게 됩니다.

