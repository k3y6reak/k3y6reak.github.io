---
layout: post
title:  "Hackingcamp 2018 What is END?"
subtitle: "Hackingcamp 2018 What is END? 풀이 입니다."
date:   2018-09-07 15:48:00 +0900
categories: ['ctfs']
comments: true
---

## 풀이

바이너리를 다운받아 파일을 확인하면 ELF 64 임을 확인할 수 있다.

{% highlight bash %}
file theend.dms
theend.dms: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=3a818b70bc51d9d32a21154a640333386b9807ee, stripped
{% endhighlight %}

IDA를 이용해 확인하면 값을 입력받기 전에 아래와 같이 값을 저장한다.

{% highlight asm %}
mov     byte ptr [rbp+var_F0], 43h
mov     byte ptr [rbp+var_F0+1], 5Fh
mov     byte ptr [rbp+var_F0+3], 30h
mov     byte ptr [rbp+var_F0+4], 2Bh
mov     byte ptr [rbp+var_F0+5], 1Ch
mov     byte ptr [rbp+var_F0+6], 0Dh
mov     byte ptr [rbp+var_F0+7], 3Ah
mov     byte ptr [rbp+var_E8], 3Ah
mov     byte ptr [rbp+var_E8+1], 0Bh
mov     byte ptr [rbp+var_E8+2], 0Ah
mov     byte ptr [rbp+var_E8+3], 3Bh
mov     byte ptr [rbp+var_E8+4], 36h
mov     byte ptr [rbp+var_E8+5], 1Ah
mov     byte ptr [rbp+var_E8+6], 2Ch
mov     byte ptr [rbp+var_E8+7], 25h
mov     byte ptr [rbp+var_E0], 1Fh
mov     byte ptr [rbp+var_E0+1], 17h
mov     byte ptr [rbp+var_E0+2], 1Dh
mov     byte ptr [rbp+var_E0+3], 30h
mov     byte ptr [rbp+var_E0+4], 30h
mov     byte ptr [rbp+var_E0+5], 1Dh
mov     byte ptr [rbp+var_E0+6], 2Dh
mov     byte ptr [rbp+var_E0+7], 31h
mov     byte ptr [rbp+var_D8], 1Bh
mov     byte ptr [rbp+var_D8+1], 19h
mov     byte ptr [rbp+var_D8+3], 53h
mov     byte ptr [rbp+var_D4], 3Fh
{% endhighlight %}

그 후에 입력 받은 값을 검증하는데 코드는 아래와 같다.

{% highlight c %}
for ( i = 0; i <= 0x1D; ++i )
{
  if ( (*((_BYTE *)&v6 + (signed int)i) ^ (unsigned __int8)v11[i]) != v11[i + 1] )
    v5 = 0;
}
{% endhighlight %}

여기서 코드를 살펴보면 미리 저장한 값과 사용자가 입력한 값을 xor하고 사용자가 다음에 입력하는 값과 일치해야 한다.

{% highlight python %}
root@ubuntu:/home/k3y6reak/Desktop# python
Python 2.7.12 (default, Nov 20 2017, 18:23:56)
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> chr(ord("s") ^ 0x43)
'0'
>>> chr(ord("0") ^ 0x5f)
'o'
>>> chr(ord("o") ^ 0x30)
'_'
>>> chr(ord("_") ^ 0x2b)
't'
{% endhighlight %}


값을 찾는 과정은 스스로 생각해보길 바란다. 첫 문자 "s" 만 찾는다면 다음 값을 찾을 수 있고, 값을 넣어도 맞지 않는다면 실제 디버깅을 통해서 값을 구할 수 있다.

{% highlight bash %}
root@ubuntu:/home/k3y6reak/Desktop# ./the
INPUT: s0oo_the_end_is_zero_or_null?
The Flag is HCAMP{s0oo_the_end_is_zero_or_null?}
{% endhighlight %}

`flag: HCAMP{s0oo_the_end_is_zero_or_null?}`