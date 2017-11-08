---
layout: post
title:  "WITHCON2017 crackme"
subtitle: "Whitehat Contest 2017 crackme 풀이방법입니다."
date:   2017-11-08 13:15:00 +0900
categories: ['ctfs']
comments: true
---

### 문제
![crackme](/img/ctfs/withcon2017/crackme.png)


### 풀이

crackme 파일은 ELF 64bit 입니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# file crackme
crackme: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=d4153567d0a264a1fa799425563693cc1ad86149, stripped
{% endhighlight %}

crackme 파일을 실행하면 `PASSCODE` 입력을 요구하고 이에 따라 `CONGRATZ`와 `FAILED`가 출력됩니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# chmod +x crackme
root@ubuntu:/home/k3y6reak/Desktop# ./crackme
PASSCODE : k3y6reak
FAILED
{% endhighlight %}

IDA Pro를 이용해 Decompile을 하면 main 함수에서 많은 연산을 하고 있다는 것을 알 수 있습니다.

![crackme_pseudocode](/img/ctfs/withcon2017/pseudocode.png)

Main 함수를 분석하는 것은 많은 시간이 필요할 것 같아 Intel PIN의 `icount.so`를 이용해 count을 확인했습니다.

{% highlight python %}
import popen2, string

strings = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789~!@#$%^&*()_+-=,./:;"
Input = "input"
sh = "./pin -t source/tools/SimpleExamples/obj-intel64/icount.so -- ./crackme < " + Input

def Execute(command):
    fin, fout = popen2.popen2(command)
    result1 = fin.readline()
    result2 = fin.readline()
    print result2

def WriteFile(data):
    f = open(Input, "w")
    f.write(data)
    f.close()

for idx in range(0, len(strings)):
    key = '' + strings[idx]
    print "> ", key
    WriteFile(key)
    Execute(sh)
{% endhighlight %}

위에서 작성한 python 코드를 count.py로 저장 후 실행하면 입력 값에 따른 count 값이 출력된다.

{% highlight Shell %}
k3y6reak@ubuntu: ~/pintools# python count.py
> D
Count 152301

> E
Count 152301

> F
Count 152301

> G
Count 152301

> H
Count 152359

> I
Count 152301

> J
Count 152302

> K
Count 152302

> L
Count 152302
{% endhighlight %}

위의 실행 결과를 살펴보면 다른 문자들은 152301과 비슷한 값이 출력되는 반면 `'H'`는 `152359`로 큰 차이로 출력됐다.

{% highlight Python %}
for idx in range(0, len(strings)):
    key = '' + strings[idx]
    print "> ", key
    WriteFile(key)
    Execute(sh)
{% endhighlight %}

`key = ''` 부분에 H를 추가하여 다음 값을 찾는다.

{% highlight Shell %}
k3y6reak@ubuntu: ~/pintools# python count.py
> H3
Count 152360

> H4
Count 152636

> H5
Count 152359
{% endhighlight %}

{% highlight Shell %}
k3y6reak@ubuntu: ~/pintools# python count.py
> H4O
Count 152636

> H4P
Count 152694

> H4Q
Count 152635
{% endhighlight %}

{% highlight Shell %}
k3y6reak@ubuntu: ~/pintools# python count.py
> H4PO
Count 152693

> H4PP
Count 153022

> H4PPQ
Count 152693
{% endhighlight %}

{% highlight Shell %}
k3y6reak@ubuntu: ~/pintools# python count.py
> H4PPx
Count 153023

> H4PPy
Count 153081

> H4PPz
Count 153022
{% endhighlight %}

위 실행 결과를 살펴보면 `'H4'`, `'H4P'`, `'H4PP'`, `'H4PPy'`가 큰 편차로 출력되는 것을 볼 수 있다.

이렇게 편차가 큰 문자를 찾아 key에 추가하여 실행하면 `H4PPyW1THC0nCTF!` 일 때 `CONGRATZ`가 출력되는 것을 볼 수 있다.

{% highlight Shell %}
k3y6reak@ubuntu: ~/pintools# python count.py
> H4PPyW1THC0nCTF~
PASSCODE : FAILED

Count 154799
> H4PPyW1THC0nCTF!
PASSCODE : CONGRATZ

Count 154018
> H4PPyW1THC0nCTF@
PASSCODE : FAILED
{% endhighlight %}

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# ./crackme
PASSCODE : H4PPyW1THC0nCTF!
CONGRATZ
{% endhighlight %}

`flag: H4PPyW1THC0nCTF!`