---
layout: post
title:  "다양한 데이터 분석하기1"
subtitle: "여러가지 데이터 분석을 해봅시다."
date:   2019-05-31 13:46:00 +0900
categories: ['programming']
tags: R
comments: true
---

## 한국 복지 패널 데이터 분석
한국 복지 패널 데이터 분석을 위해 [여기]에서 해당 데이터를 다운받습니다.

해당 데이터는 SPSS 전용 파일로 R로 분석하기 위해서는 `foreign`패키지를 다운받아야합니다.

```r
> install.packages("foreign")
> library("foreign")
> library("dplyr")
> library("ggplot2")
> library("readxl")
raw_welfare = read.spss(file = "Koweps_hpc10_2015_beta1.sav", to.data.frame = T)
welfare = raw_welfare
```

위 코드는 foreign 패키지는 다운받아 불러온뒤, raw_welfare 변수에 spss 파일을 다운받아 저장한 후 welfare에 복사하는 코드입니다. 통계를 산출하다 임의적으로 변경된 경우 다시 원본으로 복사하기위해 welfare 데이터 프레임을 사용합니다.


* 변수명 바꾸기
다운받은 데이터의 속성이 쉽게 알 수 없으므로 해당 데이터의 속성을 아래와 같이 변경합니다.

```r
welfare = rename(welfare, sex = h10_g3, 
	birth = h10_g4,
	marriage = h10_g10,
	religion = h10_g11,
	income = p1002_8aq1,
	code_job = h10_eco9,
	code_region = h10_reg7
	)
```

### 성별에 따른 월급 통계 산출

* sex 변수 검토 및 전처리

```r
> class(welfare$sex)
[1] "numeric"
> table(welfare$sex)

   1    2 
7578 9086
```

`class()`함수를 이용하여 해당 속성의 타입에 대해서 알아본 후, `table()`함수를 이용하여 어떤 값들이 몇개가 존재하는지 살펴봅니다.

위와 같은 작업을 통해 비정상 값을 제외하여 통계를 산출해야 합니다.

남자는 1, 여자는 2 이므로 `ifelse()` 함수를 이용하여 1과 2가 아닌 경우에는 모두 `NA`처리를 해줍니다. 데이터의 양이 많기 때문에 하나씩 살펴보지 않고 `is.na()`함수를 통해 비어있는 값이 존재하는지 살펴봅니다.

sex의 값이 1이면 남자, 2면 여자라고 말했지만 우리 눈에 쉽게 들어오지 않기 때문에 각 값을 male, female로 변경해 주겠습니다.

```r
> welfare$sex = ifelse(welfare$sex == 1, "male", "female")
```

* income 변수 검토 및 전처리

마찬가지로 `class()`, `summary()` 함수들을 이용하여 해당 변수들에 대해 확인합니다. 9999인 경우에는 모름/무응답의 경우이므로 이를 비어있는 값으로 변경해야 합니다.

```r
> summary(welfare$income)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    0.0   122.0   192.5   241.6   316.6  2400.0   12030 
```
summary 함수의 결과로 NA의 값이 12030개가 나타났습니다. 여기서 중요한 부분은 income의 값이 `0`인 경우와 `9999`인 경우에는 없는 것과 마찬가지 이므로 이를 비어있는 값으로 변경해야 합니다.

```r
> welfare$income = ifelse(welfare$income %in% c(0, 9999), NA, welfare$income)
```

* 성별에 따른 월급 평균표 만들기

```r
> sex_income = welfare %>% filter(!is.na(income)) %>% group_by(sex) %>% summarise(mean_income = mean(income))
> sex_income
# A tibble: 2 x 2
  sex    mean_income
  <chr>        <dbl>
1 female        163.
2 male          312.
```

이 데이터를 이용하여 막대그래프로 표현하면 아래와 같습니다.

```r
ggplot(data = sex_income, aes(x = sex, y = mean_income)) + geom_col()
```

![sex_income](/img/r/data_analysis/sex_income.png)


### 나이와 월급에 대한 통계 산출

* 변수 검토하기
마찬가지로 나이에 관한 속성을 사용하기 때문에 먼저 확인합니다. 하지만 나이에 대한 속성이 존재하지 않기 때문에 태어난 날을 이용하도록 하겠습니다.

```r
> class(welfare$birth)
[1] "numeric"
> summary(welfare$birth)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   1907    1946    1966    1968    1988    2014 
```

* 전처리

언제 태어났는지 모르는 경우 `9999`라고 정의되어 있습니다. 마찬가지로 비 정상적 값을 비어있는 값으로 변경해야 합니다.

```r
> welfare$birth = ifelse(welfare$birth == 9999, NA, welfare$birth)
> table(is.na(welfare$birth))
```

* 파생 변수 만들기
'조사 연도' - '태어난 연도' + 1 을 이용하여 `age`라는 속성에 추가합니다.

```r
> welfare$age = 2015 - welfare$birth + 1
> summary(welfare$age)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   2.00   28.00   50.00   48.43   70.00  109.00
```

* 나이에 따른 월급 평균 구하기

나이와 월급에 대한 데이터를 잘 정제하였으므로 이제 도표로 산출하면 됩니다.

```r
> age_income = welfare %>% filter(!is.na(income)) %>% group_by(age) %>% summarise(mean_income = mean(income))
> age_income
# A tibble: 69 x 2
     age mean_income
   <dbl>       <dbl>
 1    20        121.
 2    21        106.
 3    22        130.
 4    23        142.
 5    24        134.
 6    25        145.
 7    26        158.
 8    27        188.
 9    28        205.
10    29        189.
# … with 59 more rows
> ggplot(data = age_income, aes(x = age, y = mean_income)) + geom_line()
```
위와 같이 데이터를 도표로 산출하면 아래와 같습니다.

![age_income](/img/r/data_analysis/age_income.png)





















