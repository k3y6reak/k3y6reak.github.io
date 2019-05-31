---
layout: post
title:  "다양한 데이터 분석하기2"
subtitle: "여러가지 데이터 분석을 해봅시다."
date:   2019-05-31 14:42:00 +0900
categories: ['programming']
tags: R
comments: true
---

## 한국 복지 패널 데이터 분석

### 연령대에 따른 월급 통계

이번에는 연령대에 따른 월급 통계를 산출해보겠습니다.


* 추가 변수 만들기
30세 미만을 초년, 30~59세를 중년, 60세 이상을 노년이라 정의하겠습니다.


```r
> welfare = welfare %>% mutate(ageg = ifelse(age < 30, "young", ifelse(age <= 59, "middle", "old")))
> table(welfare$ageg)

middle    old  young 
  6049   6281   4334
```

위 코드는 `mutate()` 함수를 이용하여 ageg라는 속성에 초년, 중년, 노년으로 구분하여 데이터를 구분하고 있습니다.


* 연령별 평균 월급 산출

연령별 월급을 산출하기 위해서 income의 값이 비어있지 않아야 합니다. 이 과정은 이전 포스트에서 다루었으니 건너 뛰겠습니다.

```r
ageg_income = welfare %>% filter(!is.na(income)) %>% group_by(ageg) %>% summarise(mean_income = mean(income))
```


* 그래프 산출
마찬가지로 ggplot을 이용하여 그래프를 산출하면 됩니다. 추가적으로 ggplot은 알파벳 순 정렬이 기본적이므로 연령별로 정렬하고자 하는 경우에는 `scale_x_discreate(limits = c("young", "middle", "old"))`로 지정해주면 됩니다.



### 연령대 + 성별 월급 차이

성별과 연령대라는 두 개의 데이터를 이용하여 아래와 같이 월급을 산출합니다.

```r
> sex_income = welfare %>% filter(!is.na(income)) %>% group_by(ageg, sex) %>% summarise(mean_income = mean(income))
> ggplot(data = sex_income, aes(x = ageg, y = mean_income, fill = sex)) + geom_col() + scale_x_discrete(limits = c("young", "middle", "old"))
```

![ageg_sex_income](/img/r/data_analysis/ageg_sex_income.png)

male, female을 나누어 표현하고자 하는 경우에는 `geom_col(position="dodge")`로 설정하면 됩니다.


























