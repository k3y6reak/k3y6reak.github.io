---
layout: post
title:  "다양한 데이터 분석하기3"
subtitle: "여러가지 데이터 분석을 해봅시다."
date:   2019-05-31 15:15:00 +0900
categories: ['programming']
tags: R
comments: true
---

## 한국 복지 패널 데이터 분석

### 직업 별 월급 차이

* 변수 검토하기
직업을 나타내는 code_job 속성을 먼저 살펴봅시다.

```r
> class(welfare$code_job)
[1] "numeric"
> table(welfare$code_job)

 111  120  131  132  133  134  135  139  141  149  151  152  153  159  211  212  213  221  222  223  224  231  232  233  234  235 
   2   16   10   11    9    3    7   10   35   20   26   18   15   16    8    4    3   17   31   12    4   41    5    3    6   48 
 236  237  239  241  242  243  244  245  246  247  248  251  252  253  254  259  261  271  272  273  274  281  283  284  285  286 
  14    2   29   12    4   63    4   33   59   77   38   14  111   24   67  109    4   15   11    4   36   17    8   10   26   16 
 289  311  312  313  314  320  330  391  392  399  411  412  421  422  423  429  431  432  441  442  510  521  522  530  611  612 
   5  140  260  220   84   75   15    4   13   87   47   12  124   71    5   14   20   33  154  197  192  353    5  106 1320   11 
 613  620  630  710  721  722  730  741  742  743  751  752  753  761  762  771  772  773  774  780  791  792  799  811  812  819 
  40    2   20   29   30   22   16   27    3   34   34    5   49   69   27   11   61   86    7   17    5   21   45   16    1    6 
 821  822  823  831  832  841  842  843  851  852  853  854  855  861  862  863  864  871  873  874  875  876  881  882  891  892 
   9    9   23    5   17   32   10    4   19   13    7   33    9    3   14   17   31    2  257   34   37    2    2    3    8   19 
 899  910  921  922  930  941  942  951  952  953  991  992  999 1011 1012 
  16  102   31   74  289  325   99  125  122   73   45   12  141    2   17
```

`table()`함수를 이용하여 직업 코드를 살펴보니 해당 값을 숫자로 나타내져있습니다. 이 숫자로는 어떤 직업인지 알 수 없기 때문에 전처리 과정을 통해 쉽게 알 수 있도록 변경해보겠습니다.

[여기](https://github.com/youngwoos/Doit_R/blob/master/Data/Koweps_Codebook.xlsx)에서 Koweps_Codebook.xlsx 파일을 다운받습니다.

해당 xlsx 파일에 2번 sheet에 해당 정보가 저장되어있습니다. 이를 `left_join()`함수를 이용하여 job 속성에 추가해보겠습니다.

```r
> library(readxl)
> list_job = read_excel("Koweps_Codebook.xlsx", col_names = T, sheet = 2)
> welfare = left_join(welfare, list_job, id="code_job")
> welfare %>% filter(!is.na(code_job)) %>% select(code_job, job)
    code_job                                        job
1        942                           경비원 및 검표원
2        762                                     전기공
3        530         방문 노점 및 통신 판매 관련 종사자
4        999                기타 서비스관련 단순 종사원
5        312                            경영관련 사무원
.
.
.
```

위와 같이 Koweps_Codebook.xlsx에서 직업에 대한 내용을 가져온 후, left_join을 이용하여 list_job을 welfare에 붙여줍니다. 이때 code_job 옆에 붙여주도록 합니다.

이번에는 해당 직업을 기준으로 월급 평균을 산출해 보겠습니다.

```r
> job_income = welfare %>% filter(!is.na(job) & !is.na(income)) %>% group_by(job) %>% summarise(mean_income = mean(income))
> job_income
# A tibble: 142 x 2
   job                                mean_income
   <chr>                                    <dbl>
 1 가사 및 육아 도우미                       80.2
 2 간호사                                   241. 
 3 건설 및 광업 단순 종사원                 190. 
 4 건설 및 채굴 기계운전원                  358. 
 5 건설 전기 및 생산 관련 관리자            536. 
 6 건설관련 기능 종사자                     247. 
 7 건설구조관련 기능 종사자                 242. 
 8 건축 및 토목 공학 기술자 및 시험원       378. 
 9 건축마감관련 기능 종사자                 254. 
10 경비원 및 검표원                         134. 
# … with 132 more rows
```

* 그래프 만들기

이번 그래프는 세로 방향의 막대 그래프로 산출해보겠습니다.

```r
> top10 = job_income %>% arrange(desc(mean_income)) %>% head(10)
ggplot(data = top10, aes(x = reorder(job, mean_income), y = mean_income)) + geom_col() + coord_flip()
```

![top10](/img/r/data_analysis/top10.png)

### 성별 직업 빈도

이번에는 성별에 따른 직업 빈도를 산출해보겠습니다.

각 성별로 상위 10개를 산출합니다.

```r
> job_male = welfare %>% filter(!is.na(job) & sex == "male") %>% group_by(job) %>% summarise(n = n()) %>% arrange(desc(n)) %>% head(10)
> job_male
# A tibble: 10 x 2
   job                          n
   <chr>                    <int>
 1 작물재배 종사자            640
 2 자동차 운전원              251
 3 경영관련 사무원            213
 4 영업 종사자                141
 5 매장 판매 종사자           132
 6 제조관련 단순 종사원       104
 7 청소원 및 환경 미화원       97
 8 건설 및 광업 단순 종사원    95
 9 경비원 및 검표원            95
10 행정 사무원                 92
```

```r
> job_female = welfare %>% filter(!is.na(job) & sex == "female") %>% group_by(job) %>% summarise(n = n()) %>% arrange(desc(n)) %>% head(10)
> job_female
# A tibble: 10 x 2
   job                              n
   <chr>                        <int>
 1 작물재배 종사자                680
 2 청소원 및 환경 미화원          228
 3 매장 판매 종사자               221
 4 제조관련 단순 종사원           185
 5 회계 및 경리 사무원            176
 6 음식서비스 종사자              149
 7 주방장 및 조리사               126
 8 가사 및 육아 도우미            125
 9 의료 복지 관련 서비스 종사자   121
10 음식관련 단순 종사원           104
```

각 그래프는 직접 나타내보세요.
















