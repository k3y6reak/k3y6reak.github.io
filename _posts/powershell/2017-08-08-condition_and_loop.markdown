---
layout: post
title:  "Condition_and_Loop"
date:   2017-08-08 12:58:00 +0900
categories: ['technique', 'powershell']
---

<br/><br/>

이번에는 PowerShell에서 사용하는 조건문과 반복문에 대해서 알아보도록 하겠습니다.

<br/><br/>
### 조건문

다른 언어와 마찬가지로 PowerShell에서 `if`, `if~else`, `if~elseif~else`, `switch`를 사용합니다.


{% highlight ps %}
PS C:\Users\k3y6reak> if (1 -gt 0) {"1 > 0"}
1 > 0
PS C:\Users\k3y6reak> if (1 -gt 5) { "1 > 0" } else{ "1 < 5" }
1 < 5
PS C:\Users\k3y6reak> $num = 2
PS C:\Users\k3y6reak> if ($num -eq 1) { "num is 1" } elseif( $num -eq 2 ) { "num is 2" } else { "i don't know" }
num is 2
PS C:\Users\k3y6reak> $num = 3
{% endhighlight %}

위와 같이 다른 언어에서 사용하는 것과 동일하게 PowerShell에서 사용할 수 있습니다.


`switch` 구문도 동일하게 사용하지만 좀 더 추가된 것이 있습니다. `*`를 이용하는 방법인데, 아래 예제를 보겠습니다.

{% highlight ps %}
PS C:\Users\k3y6reak> switch ($num) {1{"num is 1"} 2{"num is two"} default{"i don't know"}}
i don't know
PS C:\Users\k3y6reak> $str = "abcd"
PS C:\Users\k3y6reak> switch -wildcard ($str) { a*{"a~"} *b*{"~b~"} c*{"c~"}}
a~
~b~
{% endhighlight %}

`*`를 사용해서 switch 구문을 이용할 수 있습니다.



<br/><br/>
### 반복문

마찬가지로 `while`, `do~while`, `for`를 사용할 수 있으며 추가적으로 `do~until`, `foreach`를 사용할 수 있습니다.



{% highlight ps %}
PS C:\Users\k3y6reak> $count = 3
PS C:\Users\k3y6reak> while ( $count -gt 0)
>> {
>> "count is $count"
>> $count--
>> }
count is 3
count is 2
count is 1
PS C:\Users\k3y6reak> $count = 3
PS C:\Users\k3y6reak> do
>> {
>> "count is $count"
>> $count--
>> }
>> while( $count -gt 0)
count is 3
count is 2
count is 1
{% endhighlight %}

`while`, `do~while`은 동일하게 실행됩니다.

{% highlight ps %}
PS C:\Users\k3y6reak> $count = 3
PS C:\Users\k3y6reak> do
>> {
>> "count is $count"
>> $count--
>> }
>> until( $count -gt 0)
count is 3
PS C:\Users\k3y6reak> do
>> {
>> "count is $count"
>> $count--
>> }
>> until( $count -lt 0)
count is 2
count is 1
count is 0
{% endhighlight %}

`until`은 `~때 까지`로 처음 실행한 `$count -gt 0`의 의미는 `0보다 클 때까지만`실행하기 때문에 count는 3이됩니다.

`while`과 `until`은 서로 반대되는 개념이라고 생각하면 됩니다.

for문 또한 동일하게 사용할 수 있습니다.

{% highlight ps %}
PS C:\Users\k3y6reak> for ($i = 0; $i -lt 10; $i++)
>> {
>> "i is $i"
>> }
i is 0
i is 1
i is 2
i is 3
i is 4
i is 5
i is 6
i is 7
i is 8
i is 9
{% endhighlight %}

`foreach` 구문은 python 에서 for문과 동일합니다. java에도 foreach구문이 있죠.

{% highlight ps %}
PS C:\Users\k3y6reak> $arr = "Hello World!"
PS C:\Users\k3y6reak> foreach ($str in $arr)
>> {
>> $str
>> }
Hello World!
PS C:\Users\k3y6reak> $arr = 1,2,3,4,5
PS C:\Users\k3y6reak> foreach($num in $arr)
>> {
>> $num
>> }
1
2
3
4
5
{% endhighlight %}

위 예제를 보면 문자열인 경우 각 인덱스별로 값을 출력하는 것이 아니라 한 번에 출력하는 것을 알 수 있습니다. 

이 외에도 ForEach-Object와 Where-Object가 있습니다.

{% highlight ps %}
PS C:\Users\k3y6reak> Get-Process | ForEach-Object {$_.Name}
acrotray
ApplicationFrameHost
armsvc
coherence
coherence
coherence
conhost
csrss
csrss
dasHost
dllhost
dwm
explorer
fontdrvhost
fontdrvhost
Idle
IpOverUsbSvc
lsass
Memory Compression
MSASCuiL
msdtc
MsMpEng
NisSrv
OneDrive
powershell
{% endhighlight %}



Get-Process를 이용해 얻은 값을 ForEach-Object를 통해서 `_.`에 넣어준 후 `Name`만 출력해주는 방법입니다.


{% highlight ps %}
PS C:\Users\k3y6reak> Get-ChildItem C:\Users\k3y6reak\Desktop | Where-Object {$_.Name -match "exe"}


    디렉터리: C:\Users\k3y6reak\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----    2017. 8. 2.  오전 12:35        1368442 asby.exe
{% endhighlight %}

Where-Object를 이용해 `grep` 처럼 사용할 수 있습니다.
