---
layout: post
title:  "데이터 프레임과 외부 데이터 사용하기"
subtitle: "데이터 프레임과 외부 데이터 사용하는 방법에 대해 알아봅시다."
date:   2019-05-29 13:30:00 +0900
categories: ['programming']
tags: R
comments: true
---

### 데이터 프레임
가장 많이 사용하는 형태이며, 행과 열로 구성되어 있습니다. 데이터베이스에 대해 공부해 보셨다면 쉽게 이해할 수 있습니다.

아래 표와 같은 형태를 데이터 프레임이라 합니다.

| 이름 | 영어 | 수학 |
|:----:|:----:|:---:|
| AAA | 80 | 90 |
| BBB | 70 | 80 |
| CCC | 60 | 70 |

실제 코드를 작성하여 데이터 분석이 진행되므로 행과 열을 구분할 필요가 있습니다. 행(가로)는 특정 한 사람의 정보를 뜻하게 되며, 열(세로)는 하나의 속성이 됩니다.

위 표에서는 3개의 행, 3개의 열이 존재하는 것입니다.

단순하게 영어의 평균점수, 수학의 평균점수 등 간단한 데이터를 추출하는데 있어서 행(가로)의 개수가 많아져도 별다른 문제가 발생하지 않습니다. 이때는 데이터를 처리하는 속도가 중요하게 됩니다.
반면, 열(세로)가 많아지는 경우에는 각 속성들의 관계를 분석하게 된다면 분석 방법에 따라 결과가 나타나게 됩니다.

### 데이터 프레임 만들기
이제 직접 데이터 프레임을 만들어 보겠습니다.

```r
> eng = c(80, 70, 60)
> math = c(90, 80, 70)
> df_test = data.frame(eng, math)
> df_test
  eng math
1  80   90
2  70   80
3  60   70
```

위와 같이 eng 속성에는 80, 70, 60 점에 대한 내용이 들어가며, math 속성에는 90, 80, 70 점에 대한 내용이 들어가게 됩니다. 단순히 값을 저장하는 것은 변수를 선언하여 값을 설정하는 것 뿐이지만 더 나아가, df_test 변수에는 `data.frame`을 이용하여 eng와 math를 하나의 데이터 프레임으로 만들어 주게 됩니다.

여기서 df_test에 존재하는 영어점수들의 평균을 구해봅시다.

```r
> mean(df_test.eng)
Error in mean(df_test.eng) : object 'df_test.eng' not found
> mean(df_test$eng)
[1] 70
```

위와 같이 코드를 작성하면 df_test에 있는 eng 점수들에 대한 평균값이 출력되게 됩니다. 기타 다른 프로그래밍 언어를 학습하셨던 분들이라면 `.`을 이용하여 접근을 많이 하셨을텐데 R 에서는 `$`로 접근하게 됩니다.


### 엑셀파일 사용하기
외부 데이터를 사용하기 위해서 특정 파일을 R로 읽어, 값을 분석하여 출력하게 됩니다.
[Do_it_쉽게_배우는_R_데이터_분석](https://github.com/youngwoos/Doit_R/#4)에서 엑셀 파일을 받을 수 있습니다.

해당 엑셀 파일에는 id, class, math, english, science 총 5개 속성이 있습니다.

R에서 엑셀 데이터를 읽어오기 위해서 먼저 `install.packages("readxl")`을 이용해 설치합니다.
그 후 `libary(readxl)`로 readxl을 사용할 준비를 합니다.

다운로드 받은 엑셀 파일을 현재 프로젝트에 넣어줍니다.

```r
> install.packages("readxl")
> library(readxl)
> df_test = read_excel("excel_exam.xlsx")
> df_test
# A tibble: 20 x 5
      id class  math english science
   <dbl> <dbl> <dbl>   <dbl>   <dbl>
 1     1     1    50      98      50
 2     2     1    60      97      60
 3     3     1    45      86      78
 4     4     1    30      98      58
 5     5     2    25      80      65
 6     6     2    50      89      98
 7     7     2    80      90      45
 8     8     2    90      78      25
 9     9     3    20      98      15
10    10     3    50      98      45
11    11     3    65      65      65
12    12     3    45      85      32
13    13     4    46      98      65
14    14     4    48      87      12
15    15     4    75      56      78
16    16     4    58      98      65
17    17     5    65      68      98
18    18     5    80      78      90
19    19     5    89      68      87
20    20     5    78      83      58
```
위와 같이 에러가 발생하지 않고 잘 출력이 된 것을 확인할 수 있습니다.

해당 엑셀 파일의 데이터를 가져왔으니, 영어점수 평균, 과학점수 평균, 수학점수 평균을 구해봅시다.
```r
> mean(df_test$math)
[1] 57.45
> mean(df_test$english)
[1] 84.9
> mean(df_test$science)
[1] 59.45
```

하지만 모든 데이터가 형식에 맞춰 구성된 것은 아닙니다. 특정 엑셀 파일은 id, class, math 등과 같이 구분하지 않고 점수만 저장된 경우라면 R은 최상단의 값을 이름이라 생각하게 됩니다.

따라서, `read_excel("엑셀파일명", col_names = F)`와 같이 col_names를 이용하면 해당 이름을 넣어줄 수 있습니다. 여기서 col_names에 F 라는 값이 있는데 이는 `boolean(참, 거짓)` 타입으로 T, F로 설정할 수 있습니다. T는 열 이름을 가져오는 것이며, F는 가져오지 않습니다.

또한 엑셀에 sheet가 여러개라면 `read_excel("엑셀파일명, sheet=2)`와 같이 sheet 번호를 지정하여 사용할 수 있습니다.


### CSV 파일 사용하기
csv는 엑셀 파일과 동일하게 사용할 수 있습니다. 다만, csv는 R의 기본 내장함수가 존재하므로 `read.csv()`를 이용하여 읽을 수 있습니다. 해당 csv 파일도 상단의 엑셀 파일과 같은 사이트에서 받을 수 있습니다.

```r
> df_test = read.csv("csv_exam.csv")
> df_test
   id class math english science
1   1     1   50      98      50
2   2     1   60      97      60
3   3     1   45      86      78
4   4     1   30      98      58
5   5     2   25      80      65
6   6     2   50      89      98
7   7     2   80      90      45
8   8     2   90      78      25
9   9     3   20      98      15
10 10     3   50      98      45
11 11     3   65      65      65
12 12     3   45      85      32
13 13     4   46      98      65
14 14     4   48      87      12
15 15     4   75      56      78
16 16     4   58      98      65
17 17     5   65      68      98
18 18     5   80      78      90
19 19     5   89      68      87
20 20     5   78      83      58
```

### csv 파일로 저장하기
특정 데이터를 이용하여 적절한 정보로 가공했다면 이를 저장할 경우가 생기기 마련입니다.
이때, `write.csv(데이터 프레임, file = 파일명)`을 이용하여 파일을 저장할 수 있습니다.

```r
> write.csv(df_test, file="df_test.csv")
> df_test2 = read.csv("df_test.csv")
> df_test2
    X id class math english science
1   1  1     1   50      98      50
2   2  2     1   60      97      60
3   3  3     1   45      86      78
4   4  4     1   30      98      58
5   5  5     2   25      80      65
6   6  6     2   50      89      98
7   7  7     2   80      90      45
8   8  8     2   90      78      25
9   9  9     3   20      98      15
10 10 10     3   50      98      45
11 11 11     3   65      65      65
12 12 12     3   45      85      32
13 13 13     4   46      98      65
14 14 14     4   48      87      12
15 15 15     4   75      56      78
16 16 16     4   58      98      65
17 17 17     5   65      68      98
18 18 18     5   80      78      90
19 19 19     5   89      68      87
20 20 20     5   78      83      58
```

여기서 RData(R 전용 데이터)로 저장하고 싶을 때는 `save(데이터 프레임, file=파일명.rda)`로 가능합니다. 읽어 올때는 `load(파일명.rda)`로 가능합니다.

중간에 해당 데이터 프레임의 정보를 제거할 때는 `rm(데이터 프레임)`으로 삭제할 수 있습니다.

특히 rda파일을 읽어올 때는 저장할 때 사용한 데이터 프레임에 자동으로 들어가게 됩니다.

