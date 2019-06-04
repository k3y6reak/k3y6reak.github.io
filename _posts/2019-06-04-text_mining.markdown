---
layout: post
title:  "텍스트 마이닝"
subtitle: "텍스트 마이닝에 대해 알아봅시다."
date:   2019-06-04 13:14:00 +0900
categories: ['programming']
tags: R
comments: true
---

### 힙합가사 텍스트 마이닝

데이터에서 가치 있는 `정보`를 얻어 내는 분석 기법을 `텍스트 마이닝`이라 합니다.
한글 문자를 분석하기 위해서는 `형태소 분석`을 통해 어떤 품사로 되어있는지 확인해야 합니다. 먼저 `KoNLP` 패키지를 이용하여 한글의 형태소를 분석해 보도록하겠습니다. KoNLP는 Java를 사용하기 때문에 먼저 Java를 설치해야 합니다.

```r
> install.packages("rJava")
> install.packages("memoise")
> install.packages("KoNLP")
> Sys.setenv(JAVA_HOME='C:\\Program Files\\Zulu\\zulu-8')
> library(KoNLP)
> library(dplyr)
```

위 코드에서 각 해당 패키지를 작성하고 KoNLP, dplyr 패키지를 사용하도록 준비합니다. 여기서 KoNLP 패키지를 사용하지 못하는 경우에는 Java 설치가 안돼 있거나, `JAVA_HOME` 환경설정이 안된 경우입니다.
환경 설정이 안된경우 `Sys.setenv()` 함수를 이용하여 JAVA_HOME 환경설정을 지정해 주어야 합니다.

저의 경우 Zulu라는 OpenJDK를 사용하므로 위와 같이 작성했습니다. 

먼저 힙합 텍스트 데이터를 [여기](https://github.com/youngwoos/Doit_R/blob/master/Data/hiphop.txt)에서 다운받아 프로젝트에 넣어주고 `readLines('hiphop.txt')`로 해당 파일을 불러옵니다.

```r
> txt = readLines("hiphop.txt")
Warning message:
In readLines("hiphop.txt") : incomplete final line found on 'hiphop.txt'
> head(txt)
[1] "\"보고 싶다"                  "이렇게 말하니까 더 보고 싶다" "너희 사진을 보고 있어도"     
[4] "보고 싶다"                    "너무 야속한 시간"             "나는 우리가 밉다"   
```

위 결과를 보면 중간에 특수 문자가 포함되어있는 것을 볼 수 있습니다. 이를 " " 공백으로 변경하도록 하겠습니다.

```r
> install.packages("stringr")
> library(stringr)
> txt = str_replace_all(txt, "\\W", " ")
> head(txt)
[1] " 보고 싶다"                   "이렇게 말하니까 더 보고 싶다" "너희 사진을 보고 있어도"     
[4] "보고 싶다"                    "너무 야속한 시간"             "나는 우리가 밉다"   
```

문자를 처리하기 위해서 `stringr` 패키지가 필요하므로 설치 후 사용할 준비를 하고, `str_replace_all()` 함수를 이용하여 특수문자를 공백으로 변경합니다.

* 명사 추출하기

가사에 존재하는 명사를 추출하려면 KoNLP의 `extractNoun()` 함수를 이용하면 됩니다.

```r
> noun = extractNoun(txt)
> wordcount = table(unlist(noun))
> df_word = as.data.frame(wordcount, stringAsFactors = F)
> df_word = rename(df_word, word = Var1, freq = Freq)
> head(df_word)
  word freq
1        12
2         2
3    1    8
4  100    3
5  168    1
6   17    1
```

extractNoun() 함수를 이용해서 명사들을 추출하고 그 개수를 구한 뒤 데이터 프레임으로 만든 후에 각 속성의 이름을 word, freq로 변경한 코드입니다.

빈도 순으로 정렬하여 상위 20개를 추출하는 코드는 아래와 같습니다.

```r
> df_word= filter(df_word, nchar(word) >= 2)
> top_20 = df_word %>% arrange(desc(freq)) %>% head(20)
> top_20
   word freq
1    my   86
2   YAH   80
3   you   77
4    on   76
5  하나   75
6  오늘   51
7   and   49
8  사랑   49
9  like   48
10 우리   48
11  the   43
12 love   39
13 시간   39
14   to   38
15   we   36
16 I’m   35
17   it   33
18   em   32
19  not   32
20 역사   31
```

* 워드 클라우드 만들기

워드 클라우드를 사용하기 위해서 `wordcloud` 패키지를 설치해야 합니다. 그 후 wordcloud와 RColorBrewer 패키지를 사용할 준비를 합니다. `RColorBrewer`는 워드 클라우드를 만들때 색깔을 넣어주는 패키지 입니다.

```r
> install.packages("wordcloud")
> library(wordcloud)
> library(RColorBrewer)
> pal = brewer.pal(8, "Dark2")
> pal
[1] "#1B9E77" "#D95F02" "#7570B3" "#E7298A" "#66A61E" "#E6AB02" "#A6761D" "#666666"
> set.seed(1234)
```

위 코드는 brewer.pal()을 통해 8개의 색상을 추출합니다. `set.seed()`는 워드 클라우드를 생성할 때 매번 다른 모양으로 만들어지기 때문에 seed 값을 고정시켜 같은 모양으로 나오도록 합니다.


```r
> wordcloud(words = df_word$word, freq = df_word$freq, min.freq = 2, max.words = 200, random.order = F, rot.per = .1, scale = c(4, 0.3), colors = pal)
```

![word_colud](/img/r/mining/wordcloud.png)


### 국정원 트윗 텍스트 마이닝

먼저, [여기](https://raw.githubusercontent.com/youngwoos/Doit_R/master/Data/twitter.csv)에서 twitter.csv 파일을 받아 프로젝트에 넣어주세요. 속성이 한글이므로 쉽게 사용하기 위해서 영어로 변경합니다.

```r
> twitter = read.csv("twitter.csv", header = T, stringsAsFactors = F, fileEncoding = "UTF-8")
> twitter = rename(twitter, no = 번호, id = 계정이름, date = 작성일, tw = 내용)
> twitter$tw = str_replace_all(twitter$tw, "\\w", " ") <- 중간에 데이터가 사라지면 제외하세요.
> head(twitter$tw)
[1] "민주당의 ISD관련 주장이 전부 거짓으로 속속 드러나고있다. 미국이 ISD를 장악하고 있다고 주장하지만 중재인 123명 가운데 미국인은 10명뿐이라고 한다."                                                                               
[2] "말로만 '미제타도', 사실은 '미제환장'! 김정일 운구차가 링컨 컨티넬탈이던데 북한의 독재자나 우리나라 종북들이나 겉으로는 노동자, 서민을 대변한다면서 고급 외제차, 아이팟에 자식들 미국 유학에 환장하는 위선자들인거죠"            
[3] "한나라당이 보수를 버린다네요\n뭔가착각하는모냥인에 국민들이보수를싫어하는게 아니라뻘짓거리하는분들을싫어하는겁니다야당이진보어쩌고저쩌고한다고해서그들을조아한다고생각하면대착각"                                               
[4] "FTA를 대하는 현명한 자세! 사실 자유주의 경제의 가장 큰 수해자는 한국이죠. 농어업분야 피해를 줄이는 정부대안을 최대한, 보완하고 일자리 창출 등 실익을 최대화해 나가는게 현실적인 대처자세일듯!"                                  
[5] "곽노현씨 갈수록 가관입니다. 뇌물질에 아들 병역 의혹까지. 도대체 아이들이 뮐 보고 배우겠습니까? 이래도 자리 연연하시겠습니까?"                                                                                                   
[6] "과거 집권시 한미FTA를 적극 추진하던 세력이 이제 집권하면 폐기하겠다고 주장합니다. 어이없어 말도 안 나오네요. 표만 얻을 수 있다면 국가 안보나 경제가 어떻게 되든 상관없다는 무책임한 행태들, 우리 정치의 후진성을 드러내는 거죠."
```

특수문자를 제거할때 한글이 사리지게되는 경우에는 해당 함수를 실행하지 마세요.

다음으로, 단어 빈도표를 만들어보겠습니다. 힙합가사에서 명사들을 추출한것과 동일하게 하면 됩니다.

```r
> noun = extractNoun(twitter$tw)
> wordcount = table(unlist(noun))
> df_word = as.data.frame(wordcount, stringsAsFactors = F)
> df_word = rename(df_word, word = Var1, freq = Freq)
> df_word= filter(df_word, nchar(word) >= 2)
> top20 = df_word %>% arrange(desc(freq)) %>% head(20)
```

워드 클라우드 또한 동일하게 코드를 사용하면 됩니다.

