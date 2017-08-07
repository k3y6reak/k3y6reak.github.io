---
layout: post
title:  "Type and Array"
date:   2017-08-07 19:45:00 +0900
categories: ['technique', 'powershell']
---

<br/><br/>

PowerShell에서 각 변수에 대한 자료형을 알아보는 방법과 배열에 대해서 알아보도록 하겠습니다.

<br/><br/>
### Type

PowerShell에서는 각 변수에 `.GetType()`를 붙여주면 자료형을 알 수 있습니다.


{% highlight ps %}
PS C:\Users\k3y6reak> "3".GetType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     String                                   System.Object


PS C:\Users\k3y6reak> $a = 3
PS C:\Users\k3y6reak> $a.GetType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     Int32                                    System.ValueType


PS C:\Users\k3y6reak> $b = 3.2
PS C:\Users\k3y6reak> $b.GetType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     Double                                   System.ValueType
{% endhighlight %}

다른 언어들과 동일하게 자료형이 정해지는 것을 알 수 있습니다.

문자열을 사용하는 경우 `'`과 `"`에 대한 차이를 알아야 합니다.

{% highlight ps %}
PS C:\Users\k3y6reak> $a = "Hello My name is k3y6reak"
PS C:\Users\k3y6reak> $b = 'Hello My name is k3y6reak'
PS C:\Users\k3y6reak> $a.GetType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     String                                   System.Object


PS C:\Users\k3y6reak> $b.GetType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     String                                   System.Object


PS C:\Users\k3y6reak> "val a is $a"
val a is Hello My name is k3y6reak
PS C:\Users\k3y6reak> 'val b is $b'
val b is $b
PS C:\Users\k3y6reak> "val b is $b"
val b is Hello My name is k3y6reak
{% endhighlight %}

`'`과 `"`는 모두 동일하게 String 자료형을 갖고 있으나 변수의 내용을 출력할 때는 위와 같이 차이를 보입니다.

여러 줄을 이용하여 문자열을 작성하고 싶다면 `@"`으로 시작해 `"@`로 끝나면 됩니다.

{% highlight ps %}
PS C:\Users\k3y6reak> $str = @"
>> "Hi"
>> "My name is"
>> "k3y6reak"
>> "@
PS C:\Users\k3y6reak> $str
"Hi"
"My name is"
"k3y6reak"
{% endhighlight %}

<br/><br/>
### 배열

배열은 다른언어와 동일하게 사용할 수 있습니다. 대부분의 스크립트 언어는 하나의 배열에 여러가지의 형태가 들어가도 상관없는 것 처럼 PowerShell 또한 여러가지 형태가 들어갈 수 있습니다.


{% highlight ps %}
PS C:\Users\k3y6reak> $arr = 1,2,3
PS C:\Users\k3y6reak> $arr
1
2
3
PS C:\Users\k3y6reak> $arr.GetType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     Object[]                                 System.Array
{% endhighlight %}

여기서 배열에 대한 자료형은 Object[]로 나타낸 다는 것에 주의하면 됩니다.

{% highlight ps %}
PS C:\Users\k3y6reak> $arr2 = 1, 2.3, "Hello"
PS C:\Users\k3y6reak> $arr2
1
2.3
Hello
{% endhighlight %}

위 예제처럼 여러가지 자료형을 갖는 배열을 만들 수 있습니다.

아무것도 없는 배열을 만들고 싶다면 `$arr = @()`로 만들 수 있습니다.

{% highlight ps %}
PS C:\Users\k3y6reak> $arr3 = @()
PS C:\Users\k3y6reak> $arr3
PS C:\Users\k3y6reak> $arr3.GetType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     Object[]                                 System.Array
{% endhighlight %}


마찬가지로 `[]`를 이용하여 각 인덱스의 값을 출력할 수 있습니다.

{% highlight ps %}
PS C:\Users\k3y6reak> $arr = "Hello My name is k3y6reak"
PS C:\Users\k3y6reak> $arr[0]
H
PS C:\Users\k3y6reak> $arr[1]
e
{% endhighlight %}



또한 `[]`를 이용하여 값의 자료형을 변환할 수 있습니다.

{% highlight ps %}
PS C:\Users\k3y6reak> $i = 3.2
PS C:\Users\k3y6reak> $i.GetType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     Double                                   System.ValueType


PS C:\Users\k3y6reak> [int]$i
3
{% endhighlight %}