---
layout: post
title:  "R과 R Studio 설치하기"
subtitle: "R과 R Studio를 설치하고 기본 문법에 대해 알아봅시다."
date:   2019-05-28 14:00:00 +0900
categories: ['programming']
tags: R
comments: true
---

### R 언어란?
R은 뉴질랜드의 오클랜드 대학교 교수 로스 이하카와 로버트 젠틀맨이 만들었으며, SAS, SPSS처럼 전통적으로 많이 사용 툴은 유료지만 R은 오픈소스이며 무료라는 점이 강점입니다.
또한, R은 새로운 분석 기법이 등장하면 빠른 시간 내에 업로드되어 다운로드하여 사용할 수 있습니다. 이러한 패키지들은 [CRAN](https://cran.r-project.org/)에서 받을 수 있습니다.

### R과 R Studio 설치하기
R과 R Studio는 [CRAN](https://cran.r-project.org/)에서 받을 수 있습니다. 해당 사이트 접속 후 메인 화면에 나타난 `Download R for [운영체제]` 항목 중 자신의 운영체제에 맞는 것을 선택 후 `base` 항목을 선택하고, 상단의 `Download R 3.6.0 for Windows`를 클릭하여 다운로드 받을 수 있습니다.

다운받은 후 바탕화면에 아래와 같은 아이콘이 생성되었다면 64비트 운영체제의 경우 `R x64`를 32비트 운영체제의 경우 `R i386`을 실행하면 됩니다.
![r_icon](/img/r/basic/r_icon.png)

각 운영체제에 맞는 파일을 실행했다면 아래와 같은 `R Gui`가 실행되는 것을 확인할 수 있습니다.
![r_gui](/img/r/basic/r_gui.png)

위 그림의 `R Console`에 아래 코드를 작성하여 실행 해보세요.

```r
> 1 + 1
 [1] 2
> 10 + 20
 [1] 30
> a+b
 에러: 객체 'a'를 찾을 수 없습니다
> 1/0
 [1] Inf
```

이제, R Studio를 설치해보겠습니다. R Studio는 [여기](https://www.rstudio.com/)에서 설치할 수 있습니다.

해당 웹 사이트 상단의 `Download RStudio`를 클릭하고 `RStudio Desktop` 항목을 다운로드 받습니다. 마찬가지로 자신의 운영체제에 맞는 파일을 다운로드하여 설치하면 됩니다.

R Studio를 설치 후 실행하면 아래와 같은 화면이 나타나게 됩니다.
![r_studio](/img/r/basic/r_studio.png)

콘솔 창은 위에서 `R Console`에서 작성한 곳과 동일한 영역입니다.
콘솔 창의 상단 오른쪽에 소스코드를 작성할 수 있는 버튼이 있으며 클릭 시 소스 코드 창이 나타나게 됩니다.

소스 코드창을 클릭하여 아래와 같이 코드를 작성해 봅시다.
![r_studio_source.png](/img/r/basic/r_studio_source.png)

```r
1+1
10*2
1+2+3+4+5+6+7+8+9+10
```

위 코드를 작성 후 실행하고자 하는 라인에 커서를 둔 후에 `Ctrl + Enter` 혹은 상단의 `Run`을 클릭하면 그 결과가 하단의 Console창에 나타납니다.

전체 코드를 실행하고 싶은 경우 모든 코드를 선택 후 실행하면 됩니다.

환경 창의 경우 아래와 같이 변수를 이용하면 나타나게 되며 환경 창 상단의 History는 그간 실행한 코드들의 기록들이 남아있게 됩니다.
![r_env](/img/r/basic/r_env.png)


