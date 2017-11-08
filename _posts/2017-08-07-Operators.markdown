---
layout: post
title:  "Operators"
subtitle: "PowerShell에서 사용되는 연산자에 대해서 알아보자."
date:   2017-08-07 15:20:00 +0900
categories: ['technique']
tags: powershell
comments: true
---

이번에는 PowerShell에서 사용하는 연산자에 대해서 알아보도록 하겠습니다.

PowerShell에서도 마찬가지로 다른 언어와 동일하게 연산자가 사용됩니다. 산술, 대입, 비교, 리다이렉션, 논리, 타입 등이 존재합니다.


### 산술 연산자

산술 연산자는 `덧셈, 뺄셈, 나눗셈, 곱셈`을 하는 것을 뜻합니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> 3 + 1
4
PS C:\Users\k3y6reak> 3 - 1
2
PS C:\Users\k3y6reak> 3 * 2
6
PS C:\Users\k3y6reak> 3 / 2
1.5
PS C:\Users\k3y6reak> 3 % 2
1
{% endhighlight %}

다른 언어의 경우 나눗셈을 할 때 `정수값`을 출력해주지만 PowerSell에서는 `소수`로 출력하는 것이 다릅니다.


### 대입 연산자

대입 연산자는 `변수에 값을 넣는 것`을 뜻합니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> $a = 3
PS C:\Users\k3y6reak> $a
3
PS C:\Users\k3y6reak> $a *= 2
PS C:\Users\k3y6reak> $a
6
PS C:\Users\k3y6reak> $a -= 1
PS C:\Users\k3y6reak> $a
5
PS C:\Users\k3y6reak> $a += 3
PS C:\Users\k3y6reak> $a
8
PS C:\Users\k3y6reak> $a /= 3
PS C:\Users\k3y6reak> $a
2.66666666666667
PS C:\Users\k3y6reak> $a %= 2
PS C:\Users\k3y6reak> $a
0.666666666666667
{% endhighlight %}

### 비교 연산자

비교 연산자는 `값을 비교하는 것`을 뜻합니다. 다른 언어와는 다르게 ==, !=, < 등 을 사용하지 못합니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> 2 < 3
위치 줄:1 문자:3
+ 2 < 3
+   ~
'<' 연산자는 나중에 사용하도록 예약되어 있습니다.
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : RedirectionNotSupported

PS C:\Users\k3y6reak> 2 == 3
위치 줄:1 문자:1
+ 2 == 3
+ ~
대입 식이 잘못되었습니다. 대입 연산자의 입력은 변수 또는 속성과 같이 대입을 허용할 수 있는 개체여야 합니다.
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : InvalidLeftHandSide
{% endhighlight %}

위와 같이 에러를 발생하기 때문에 다른 연산자를 사용해야 합니다.

`-eq, -ne, -gt, -lt, -le, -ge, -match, -notmatch, -replace, -like, -notlike, -in, -notin, -contains, -notcontains`가 있습니다.

`-eq`는 equal 로 값이 같은지를 확인합니다. 숫자와 문자 모두 비교가 가능합니다.
문자를 비교할 때는 대소문자를 따지지 않습니다.
{% highlight PowerShell %}
PS C:\Users\k3y6reak> 2 -eq 3
False
PS C:\Users\k3y6reak> 2 -eq 2
True
PS C:\Users\k3y6reak> "Hello" -eq "Hello"
True
PS C:\Users\k3y6reak> "Hello" -eq "HellO"
True
{% endhighlight %}

`-ne`는 not equal 로 값이 다른지를 확인합니다. 숫자와 문자 모두 비교가 가능합니다.
문자를 비교할 때는 대소문자를 따지지 않습니다.
{% highlight PowerShell %}
PS C:\Users\k3y6reak> 2 -ne 3
True
PS C:\Users\k3y6reak> "Hello" -ne "World"
True
{% endhighlight %}

`-gt`는 greater than 로 값이 큰지를 확인합니다. 숫자와 문자 모두 비교가 가능합니다.
문자를 비교할 때는 각 문자의 ascii 값으로 비교합니다.
{% highlight PowerShell %}
PS C:\Users\k3y6reak> 4 -gt 3
True
PS C:\Users\k3y6reak> "Hello" -gt "Hell0"
True
PS C:\Users\k3y6reak> "Hello" -gt "Helln"
True
PS C:\Users\k3y6reak> "Hello" -gt "Hellp"
False
{% endhighlight %}

`-lt`는 less than 로 값이 작은지를 확인합니다. 숫자와 문자 모두 비교가 가능합니다.
문자를 비교할 때는 각 문자의 ascii 값으로 비교합니다.
{% highlight PowerShell %}
PS C:\Users\k3y6reak> "Hello" -lt "Hell0"
False
PS C:\Users\k3y6reak> 2 -lt 3
True
{% endhighlight %}

이외의 연산자는 직접 실습해 보시길 바랍니다.


### 리다이렉션

리다이렉션은 linux와 동일하게 사용됩니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> "Hello" > hello.txt
PS C:\Users\k3y6reak> cat hello.txt
Hello
PS C:\Users\k3y6reak> "World" >> hello.txt
PS C:\Users\k3y6reak> cat hello.txt
Hello
World
{% endhighlight %}


### 논리 연산자

논리 연산자는 `and, or, not, !`를 말합니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> (2 -eq 3) -and (4 -eq 4)
False
PS C:\Users\k3y6reak> (2 -eq 3) -or (4 -eq 4)
True
{% endhighlight %}



### 분할, 병합

분할과 병합은 `split, join`으로 할 수 있습니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> "Hello World My name is k3y6reak" -split " "
Hello
World
My
name
is
k3y6reak
PS C:\Users\k3y6reak> "Hello", "My name is k3y6reak" -join "World"
HelloWorldMy name is k3y6reak
{% endhighlight %}


### 타입

타입은 값의 형태를 확인거나 변환할 수 있습니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> 3 -is "int"
True
PS C:\Users\k3y6reak> 3 -is "float"
False
PS C:\Users\k3y6reak> "3" -is "string"
True
PS C:\Users\k3y6reak> 0x12 -as "int"
18
{% endhighlight %}
