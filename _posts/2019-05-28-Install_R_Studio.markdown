---
layout: post
title:  "R과 R Studio 설치하기"
subtitle: "R과 R Studio를 설치하고 기본 문법에 대해 알아봅시다."
date:   2019-05-28 14:00:00 +0900
categories: ['programming']
tags: r
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