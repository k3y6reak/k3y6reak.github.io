---
layout: post
title:  "React 시작하기"
subtitle: "React를 설치하고 실행하는 방법에 대해 알아봅시다."
date:   2020-01-20 09:14:00 +0900
categories: ['technique']
tags: React
comments: true
---

### Node.js 설치

React, Vue와 같은 웹 프레임워크를 사용하려면 Node.js가 필요합니다. 

[node.js](https://nodejs.org/ko/download/) 에서 자신의 운영체제에 맞게 설치를 합니다.



### Yarn 설치

yarn은 javascript 패키지 관리 도구입니다. React, Vue 등 웹 프레임워크를 개발하고 필요한 모듈을 설치하고 관리하는데 사용됩니다.

yarn 이외에 `npm` 이라는 것도 있지만, react를 사용하는데 있어, yarn을 사용하도록 하겠습니다.



yarn을 설치하는 2가지 방법이 있습니다.


#### 1. installer 이용

[Yarn](https://yarnpkg.com/en/) 공식 사이트에서 yarn 설치를 위한 installer를 다운받아 설치합니다.


#### 2. chocolatey 이용

chocolatey는 windows에서 사용하는 패키지 관리자 입니다. Ubuntu에서는 apt를 사용하는 것 처럼 윈도우에서는 `choco`라는 명령어를 이용합니다.

`chocolatey` 공식 사이트에서 설치 명렁어를 찾아 설치합니다. 이때, `powershell 관리자`권한을 이용해서 설치해야 합니다.


```shell
C:\> choco -v 
0.10.15
```

choco를 설치한 후 위와 같이 `choco -v` 명령어를 입력해서 버전이 나타난다면 정상적으로 설치가 된 것입니다.


choco가 정상적으로 설치가 됐다면, 아래 코드처럼 `choco install yarn` 명령어를 통해 yarn을 설치합니다.

```shell
C:\> choco install yarn
```

yarn을 설치한 후, `yarn -v` 명령어를 통해, 버전 정보가 나타나면 정상적으로 설치가 된 것입니다.

```shell
C:\> yarn -v
1.21.1
```


yarn을 정상적으로 설치했다면, 아래 명령어를 통해 react 초기 프로젝트 파일을 생성할 수 있습니다.

```shell
C:\> yarn create react-app test-react
```

기본 프로젝트 설치 후, `cd test-react`를 통해 해당 프로젝트 위치로 이동하고, `yarn start` 명령어를 통해 react 프로젝트를 실행할 수 있습니다.

```shell
C:\test-react> yarn start

Compiled successfully!

You can now view hello-react in the browser.

  Local:            http://localhost:3000/
  On Your Network:  http://10.0.75.1:3000/

Note that the development build is not optimized.
To create a production build, use yarn build.
```

위와 같이 주소가 나타난다면 정상적으로 서버가 실행 된 것입니다.

[react_main_page](https://github.com/k3y6reak/k3y6reak.github.io/blob/master/img/react/start_react/start_react.png?raw=true)

