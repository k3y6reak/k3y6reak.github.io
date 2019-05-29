---
layout: post
title:  "R Studio 프로젝트 생성 및 변수, 함수, 패키지"
subtitle: "R Studio의 프로젝트 생성 방법에 대해 알아봅시다."
date:   2019-05-28 15:26:00 +0900
categories: ['programming']
tags: R
comments: true
---


### 프로젝트 생성
R Studio에서 오른쪽 상단의 `Porject` 를 클릭하면 메뉴가 나타나며 `New Proejct`를 클릭하여 프로젝트를 생성할 수 있습니다.
![new_project](/img/r/project/new_project.png)

그러면 하단의 그림처럼 3가지 방식이 나타나게 됩니다. `New Directory`는 새로운 프로젝트 생성 시 사용하며, `Existing Directory`는 기존 프로젝트가 있는 경우, `Version Control`은 Git과 같은 버전관리 시 사용하게 됩니다.
![create_project](/img/r/project/create_project.png)

New Directory 선택 후 새로운 프로젝트를 선택하면 아래와 같이 해당 프로젝트의 이름, 경로를 설정할 수 있습니다. 각 내용을 설정 후 `Create Project` 버튼을 눌러 새로운 프로젝트를 생성할 수 있습니다.
![set_project](/img/r/project/set_project.png)



### 변수
변수는 쉽게 `변하는 수`라 생각하면 좋습니다. 보통 다른 언어들은 `a = 1`과 같이 equal을 사용하는 반면 R은 `a <- 1`을 사용합니다. 여기서 `a`가 변수 명이 되며 `a <- 1`과 같은 식을 실행했다면 a라는 이름의 변수가 1이라는 값을 갖게 되는 것입니다.

```r
> a <- 1
> a
[1] 1
> b <- 2
> a + b
[1] 3
>
```

위 코드는 a에는 1이라는 값을 넣어두고 b에는 2를 넣어 둔 후 a와 b를 더한 값을 계산하는 코드입니다.

여러개의 값을 하나의 변수에 넣고자 하는 경우 (타 언어에서는 리스트, 배열 등으로 불림) R에서 c 함수를 사용하게 됩니다. 이는 combine의 약자입니다.

```r
> var1 <- c(1, 2, 3)
> var1
[1] 1 2 3
> var2 = c("1", 2, 3)
> var2
[1] "1" "2" "3"
```

var1에 c 함수를 이용하여 숫자 1, 2, 3을 저장하는 코드와 var2에는 문자 "1"과 숫자 2, 3을 저장하는 코드입니다.

숫자들끼리 저장하는 경우 모두 숫자로 저장되지만 중간에 문자가 포함된 경우에는 모두 문자로 처리되는 것을 확인할 수 있습니다.

또한 <- 대신 =을 사용해도 가능하네요.

```r
> var3 = c(1:10)
> var3
 [1]  1  2  3  4  5  6  7  8  9 10
```

위 코드 중 var3에 `c(1:10)` 을 확인할 수 있을텐데 이것은 1부터 10까지의 수를 var3에 저장하는 것을 말합니다.

c 함수 대신 seq 함수도 동일한 기능을 제공합니다. 다만 c 함수에서는 연속적인 수를 넣기 위해 `:`를 사용했다면 seq는 `,`를 사용합니다.
seq 함수에서는 `by` 파라미터를 이용하여 간격을 줄 수도 있습니다.
```r
> var1 = c(1:10)
> var2 = seq(1, 10)
> var1
 [1]  1  2  3  4  5  6  7  8  9 10
> var2
 [1]  1  2  3  4  5  6  7  8  9 10
> var3 = seq(1, 10, by=3)
> var3
[1]  1  4  7 10
 ```


### 함수
위 변수 설명에서 c, seq 두 가지 간단한 함수도 살펴보았습니다. 여기서는 mean(), max(), min(), paste()에 대해서 알아보겠습니다.

```r
> var1 = c(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
> mean(var1)
[1] 5.5
> max(var1)
[1] 10
> min(var1)
[1] 1
```

mean() 함수는 해당 수들의 평균을 계산하여 출력합니다. 1부터 10까지의 평균 5.5를 출력해주며, max는 해당 수들의 최대값을 출력하고 min은 최소값을 출력합니다.

```r
> str1 = c("h", "e", "l", "l", "o")
> str1
[1] "h" "e" "l" "l" "o"
> paste(str1, collapse=",")
[1] "h,e,l,l,o"
> paste(str1, collapse="")
[1] "hello"
```

str1에는 h, e, l, l, o 각 5개의 문자가 저장되어 있습니다. 이 문자들을 paste() 함수를 이용하여 붙일 수 있는데, `collapse` 파라미터를 이용하여 해당 문자를 넣어 붙일 수 있습니다. 


### 패키지
R에서 그래프, 텍스트 데이터 분석, 머신러닝 알고리즘 구현 등 다양한 기능을 사용하기 위한 함수들이 존재합니다. 하지만 이런 함수들을 사용하기 위해서는 해당 함수가 포함된 패키지를 설치해야 합니다. 패키지를 설치하기 위해서는 아래와 같이 `install.packages("ggplot2")를 입력합니다.

```r
> install.packages("ggplot2")
WARNING: Rtools is required to build R packages but is not currently installed. Please download and install the appropriate version of Rtools before proceeding:

https://cran.rstudio.com/bin/windows/Rtools/
Installing package into ‘C:/Users/student/Documents/R/win-library/3.6’
(as ‘lib’ is unspecified)
also installing the dependencies ‘backports’, ‘zeallot’, ‘glue’, ‘magrittr’, ‘stringi’, ‘colorspace’, ‘assertthat’, ‘utf8’, ‘vctrs’, ‘Rcpp’, ‘stringr’, ‘labeling’, ‘munsell’, ‘R6’, ‘RColorBrewer’, ‘cli’, ‘crayon’, ‘fansi’, ‘pillar’, ‘pkgconfig’, ‘digest’, ‘gtable’, ‘lazyeval’, ‘plyr’, ‘reshape2’, ‘rlang’, ‘scales’, ‘tibble’, ‘viridisLite’, ‘withr’

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/backports_1.1.4.zip'
Content type 'application/zip' length 66791 bytes (65 KB)
downloaded 65 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/zeallot_0.1.0.zip'
Content type 'application/zip' length 62173 bytes (60 KB)
downloaded 60 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/glue_1.3.1.zip'
Content type 'application/zip' length 173348 bytes (169 KB)
downloaded 169 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/magrittr_1.5.zip'
Content type 'application/zip' length 158182 bytes (154 KB)
downloaded 154 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/stringi_1.4.3.zip'
Content type 'application/zip' length 15298362 bytes (14.6 MB)
downloaded 14.6 MB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/colorspace_1.4-1.zip'
Content type 'application/zip' length 2551203 bytes (2.4 MB)
downloaded 2.4 MB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/assertthat_0.2.1.zip'
Content type 'application/zip' length 55060 bytes (53 KB)
downloaded 53 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/utf8_1.1.4.zip'
Content type 'application/zip' length 214971 bytes (209 KB)
downloaded 209 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/vctrs_0.1.0.zip'
Content type 'application/zip' length 621784 bytes (607 KB)
downloaded 607 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/Rcpp_1.0.1.zip'
Content type 'application/zip' length 4494964 bytes (4.3 MB)
downloaded 4.3 MB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/stringr_1.4.0.zip'
Content type 'application/zip' length 216937 bytes (211 KB)
downloaded 211 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/labeling_0.3.zip'
Content type 'application/zip' length 62860 bytes (61 KB)
downloaded 61 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/munsell_0.5.0.zip'
Content type 'application/zip' length 245391 bytes (239 KB)
downloaded 239 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/R6_2.4.0.zip'
Content type 'application/zip' length 58828 bytes (57 KB)
downloaded 57 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/RColorBrewer_1.1-2.zip'
Content type 'application/zip' length 55572 bytes (54 KB)
downloaded 54 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/cli_1.1.0.zip'
Content type 'application/zip' length 176543 bytes (172 KB)
downloaded 172 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/crayon_1.3.4.zip'
Content type 'application/zip' length 749378 bytes (731 KB)
downloaded 731 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/fansi_0.4.0.zip'
Content type 'application/zip' length 221289 bytes (216 KB)
downloaded 216 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/pillar_1.4.0.zip'
Content type 'application/zip' length 181105 bytes (176 KB)
downloaded 176 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/pkgconfig_2.0.2.zip'
Content type 'application/zip' length 22298 bytes (21 KB)
downloaded 21 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/digest_0.6.19.zip'
Content type 'application/zip' length 211466 bytes (206 KB)
downloaded 206 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/gtable_0.3.0.zip'
Content type 'application/zip' length 434255 bytes (424 KB)
downloaded 424 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/lazyeval_0.2.2.zip'
Content type 'application/zip' length 172487 bytes (168 KB)
downloaded 168 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/plyr_1.8.4.zip'
Content type 'application/zip' length 1302273 bytes (1.2 MB)
downloaded 1.2 MB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/reshape2_1.4.3.zip'
Content type 'application/zip' length 627584 bytes (612 KB)
downloaded 612 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/rlang_0.3.4.zip'
Content type 'application/zip' length 1089110 bytes (1.0 MB)
downloaded 1.0 MB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/scales_1.0.0.zip'
Content type 'application/zip' length 1072570 bytes (1.0 MB)
downloaded 1.0 MB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/tibble_2.1.1.zip'
Content type 'application/zip' length 338274 bytes (330 KB)
downloaded 330 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/viridisLite_0.3.0.zip'
Content type 'application/zip' length 60694 bytes (59 KB)
downloaded 59 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/withr_2.1.2.zip'
Content type 'application/zip' length 152043 bytes (148 KB)
downloaded 148 KB

trying URL 'https://cran.rstudio.com/bin/windows/contrib/3.6/ggplot2_3.1.1.zip'
Content type 'application/zip' length 3637902 bytes (3.5 MB)
downloaded 3.5 MB

package ‘backports’ successfully unpacked and MD5 sums checked
package ‘zeallot’ successfully unpacked and MD5 sums checked
package ‘glue’ successfully unpacked and MD5 sums checked
package ‘magrittr’ successfully unpacked and MD5 sums checked
package ‘stringi’ successfully unpacked and MD5 sums checked
package ‘colorspace’ successfully unpacked and MD5 sums checked
package ‘assertthat’ successfully unpacked and MD5 sums checked
package ‘utf8’ successfully unpacked and MD5 sums checked
package ‘vctrs’ successfully unpacked and MD5 sums checked
package ‘Rcpp’ successfully unpacked and MD5 sums checked
package ‘stringr’ successfully unpacked and MD5 sums checked
package ‘labeling’ successfully unpacked and MD5 sums checked
package ‘munsell’ successfully unpacked and MD5 sums checked
package ‘R6’ successfully unpacked and MD5 sums checked
package ‘RColorBrewer’ successfully unpacked and MD5 sums checked
package ‘cli’ successfully unpacked and MD5 sums checked
package ‘crayon’ successfully unpacked and MD5 sums checked
package ‘fansi’ successfully unpacked and MD5 sums checked
package ‘pillar’ successfully unpacked and MD5 sums checked
package ‘pkgconfig’ successfully unpacked and MD5 sums checked
package ‘digest’ successfully unpacked and MD5 sums checked
package ‘gtable’ successfully unpacked and MD5 sums checked
package ‘lazyeval’ successfully unpacked and MD5 sums checked
package ‘plyr’ successfully unpacked and MD5 sums checked
package ‘reshape2’ successfully unpacked and MD5 sums checked
package ‘rlang’ successfully unpacked and MD5 sums checked
package ‘scales’ successfully unpacked and MD5 sums checked
package ‘tibble’ successfully unpacked and MD5 sums checked
package ‘viridisLite’ successfully unpacked and MD5 sums checked
package ‘withr’ successfully unpacked and MD5 sums checked
package ‘ggplot2’ successfully unpacked and MD5 sums checked

The downloaded binary packages are in
	C:\Users\student\AppData\Local\Temp\RtmpUveCMr\downloaded_packages
```

위와 같이 설치 기록들이 나타나면서 패키지를 설치할 수 있습니다.

해당 패키지를 사용하고 싶은 경우 library() 함수를 사용하면 됩니다.

```r
> library(ggplot2)
Registered S3 methods overwritten by 'ggplot2':
  method         from 
  [.quosures     rlang
  c.quosures     rlang
  print.quosures rlang
> x = c("a", "a", "b", "c")
> qplot(x)
```

위와 같이 코드를 작성하면 R Studio의 하단에 그래프가 나타나게 됩니다. 
![qplot](/img/r/project/qplot.png)