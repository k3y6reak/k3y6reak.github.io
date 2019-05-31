---
layout: post
title:  "다양한 데이터 분석하기4"
subtitle: "여러가지 데이터 분석을 해봅시다."
date:   2019-05-31 15:40:00 +0900
categories: ['programming']
tags: R
comments: true
---

## 한국 복지 패널 데이터 분석

### 종교 유무에 따른 이혼율

종교 속성은 `religion`이라는 이름으로 되어있으며 1은 있음, 2는 없음, 9는 무응답이므로 1인 경우 yes 그 외는 no로 전처리를 해줍니다.

```r
> welfare$religion = ifelse(welfare$religion == 1, "yes", "no")
> table(welfare$religion)

  no  yes 
8617 8047 
```

이혼율을 따져보아야하므로 marriage 속성을 이용하며, 0은 비해당, 1은 유배우, 2는 사별, 3은 이혼, 4는 별겨, 5는 미혼, 6은 기타 로 나타내어져 있습니다. 

여기서는 이혼 유무에 대해서 살펴볼 것이므로 결혼한 상태라면 1 그 외 이혼한 사람이 아니라면 모두 비어있는 값으로 처리합니다.

```r
> welfare$gourp_marriage = ifelse(welfare$marriage == 1, "marriage", ifelse(welfare$marriage == 3, "divorce", NA))
> table(welfare$gourp_marriage)

 divorce marriage 
     712     8431 
```

종교와 이혼 유무에 관한 데이터를 산출했으므로 두 속성간의 통계를 산출해야 합니다.

```r
> religion_marriage = welfare %>% filter(!is.na(gourp_marriage)) %>% group_by(religion, gourp_marriage) %>% summarise(n = n()) %>% mutate(tot_group = sum(n)) %>% mutate(pct = round(n/tot_group*100, 1))
> religion_marriage
# A tibble: 4 x 5
# Groups:   religion [2]
  religion gourp_marriage     n tot_group   pct
  <chr>    <chr>          <int>     <int> <dbl>
1 no       divorce          384      4602   8.3
2 no       marriage        4218      4602  91.7
3 yes      divorce          328      4541   7.2
4 yes      marriage        4213      4541  92.8
```

위와 같이 이혼유무와 종교간의 통계가 산출되었습니다. 이제 `이혼`에 대한 값만 추출하여 이혼율 표를 산출합니다.

```r
> divorce = religion_marriage %>% filter(gourp_marriage == "divorce") %>% select(religion, pct)
> divorce
# A tibble: 2 x 2
# Groups:   religion [2]
  religion   pct
  <chr>    <dbl>
1 no         8.3
2 yes        7.2
```

추가적으로 연령대를 포함하여 통계를 산출해 보겠습니다.

```r
> ageg_religion_marriage = welfare %>% filter(!is.na(gourp_marriage) & ageg != "young") %>% group_by(ageg, religion, gourp_marriage) %>% summarise(n = n()) %>% mutate(tot_group = sum(n)) %>% mutate(pct = round(n/tot_group*100, 1))
> ageg_religion_marriage
# A tibble: 8 x 6
# Groups:   ageg, religion [4]
  ageg   religion gourp_marriage     n tot_group   pct
  <chr>  <chr>    <chr>          <int>     <int> <dbl>
1 middle no       divorce          260      2681   9.7
2 middle no       marriage        2421      2681  90.3
3 middle yes      divorce          177      2237   7.9
4 middle yes      marriage        2060      2237  92.1
5 old    no       divorce          123      1884   6.5
6 old    no       marriage        1761      1884  93.5
7 old    yes      divorce          150      2281   6.6
8 old    yes      marriage        2131      2281  93.4
```

연령대, 종료, 결혼유부를 기준으로 통계를 산출했습니다.

이제 이혼율에 대한 표를 만들어야 합니다.

```r
> df_divorce = ageg_religion_marriage %>% filter(gourp_marriage == "divorce") %>% select(ageg, religion, pct)
> df_divorce
# A tibble: 4 x 3
# Groups:   ageg, religion [4]
  ageg   religion   pct
  <chr>  <chr>    <dbl>
1 middle no         9.7
2 middle yes        7.9
3 old    no         6.5
4 old    yes        6.6
```

그래프는 직접 나타내어 보세요.

### 지역별 연령대 비율

지역을 나타내는 속성은 code_region 입니다. 이는 직업과 마찬가지로 숫자로 나타내어져 있기 때문에 이를 쉽게 알 수 있도록 문자로 변환해야 합니다.

```r
> list_region = data.frame(code_region = c(1:7), region = c("서울", "수도권(인천/경기)", "부산/경남/울산", "대구/경북", "대전/충남", "강원/충북", "광주/전남/전북/제주도"))
> list_region
  code_region                region
1           1                  서울
2           2     수도권(인천/경기)
3           3        부산/경남/울산
4           4             대구/경북
5           5             대전/충남
6           6             강원/충북
7           7 광주/전남/전북/제주도
```

list_region 데이터 프레임을 welfare 데이터 프레임에 붙여줍니다.

```r
> welfare = left_join(welfare, list_region, id = "code_region")
Joining, by = "code_region"
> welfare %>% select(code_region, region) %>% head
  code_region region
1           1   서울
2           1   서울
3           1   서울
4           1   서울
5           1   서울
6           1   서울
```

이제, 지역별 연령대 비율표를 만들어보겠습니다. 

```r
> region_ageg = welfare %>% group_by(region, ageg) %>% summarise(n = n()) %>%mutate(tot_group = sum(n)) %>% mutate(pct = round(n/tot_group*100, 2))
> region_ageg
# A tibble: 21 x 5
# Groups:   region [7]
   region                ageg       n tot_group   pct
   <fct>                 <chr>  <int>     <int> <dbl>
 1 강원/충북             middle   417      1257  33.2
 2 강원/충북             old      555      1257  44.2
 3 강원/충북             young    285      1257  22.7
 4 광주/전남/전북/제주도 middle   947      2922  32.4
 5 광주/전남/전북/제주도 old     1233      2922  42.2
 6 광주/전남/전북/제주도 young    742      2922  25.4
 7 대구/경북             middle   637      2036  31.3
 8 대구/경북             old      928      2036  45.6
 9 대구/경북             young    471      2036  23.1
10 대전/충남             middle   548      1467  37.4
# … with 11 more rows
```

위 데이터를 이용하여 노년층 비율이 높은 순으로 막대 그래프를 산출해 보겠습니다.

```r
> list_order_old = region_ageg %>% filter(ageg == "old") %>% arrange(pct)
> list_order_old
# A tibble: 7 x 5
# Groups:   region [7]
  region                ageg      n tot_group   pct
  <fct>                 <chr> <int>     <int> <dbl>
1 수도권(인천/경기)     old    1109      3711  29.9
2 서울                  old     805      2486  32.4
3 대전/충남             old     527      1467  35.9
4 부산/경남/울산        old    1124      2785  40.4
5 광주/전남/전북/제주도 old    1233      2922  42.2
6 강원/충북             old     555      1257  44.2
7 대구/경북             old     928      2036  45.6
> order = list_order_old$region
> order
[1] 수도권(인천/경기)     서울                  대전/충남             부산/경남/울산        광주/전남/전북/제주도
[6] 강원/충북             대구/경북            
Levels: 강원/충북 광주/전남/전북/제주도 대구/경북 대전/충남 부산/경남/울산 서울 수도권(인천/경기)

> ggplot(data = region_ageg, aes(x = region, y = pct, fill = ageg)) + geom_col() + coord_flip() + scale_x_discrete(limits = order)
```

![ageg_region](/img/r/data_analysis/ageg_region.png)










