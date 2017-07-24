---
layout: post
title:  "Buffer Overflow"
date:   2017-07-24 15:03:00 +0900
categories: ['technique', 'system_hacking']
---

<br/><br/>
### 개념

Buffer Overflow (이하 bof)는 크기가 정해진 배열에 해당 크기 만큼 입력해야 하지만 `버퍼보다 큰 값`
을 넣을 수 있는 취약점을 말합니다.

예를들어, `char buf[10]`로 선언했다면 buf는 총 10개의 공간을 갖게 됩니다. 하지만 프로그램이 실행 되면서 buf에 10개가 아닌 11개, 12개 혹은 그 이상의 값이 들어가는 것을 확인하지 않고 실행 하므로서 발생하는 취약점을 뜻 합니다.

그래서 bof는 stack corruption으로도 불립니다.

<br/><br/>
### 설명
{% highlight c %}
#include <stdio.h>

int main(void)
{
	char buf[8];
	scanf("%s", buf);
	return 0;
}
{% endhighlight %}

위 소스코드를 보면 buf라는 char 배열에 총 8개의 값을 넣을 수 있고, scanf를 이용해서 buf에 값을 넣어줍니다. `scanf()`는 `크기 제한`이 없기 때문에 해당 부분에서 취약점이 발생할 수 있습니다.


{% highlight bash %}
gdb-peda$ Dump of assembler code for function main:
0x0804843d <+0>:	push	ebp
0x0804843e <+1>:	mov 	ebp,esp
0x08048440 <+3>:	sub 	esp,0x10
0x08048443 <+6>:	lea 	eax,[ebp-0x8]
0x08048446 <+9>:	mov	DWORD PTR [esp+0x4],eax
0x0804844a <+13>:	mov	DWORD PTR [esp], 0x80484f0
0x08048451 <+20>:	call	0x80484330 <__isoc99_scanf@plt>
0x08048456 <+25>:	mov	eax,0x0
0x0804845b <+30>:	leave
0x0804845c <+31>:	ret
End of assembler dump.
{% endhighlight %}

위 코드에서 `push ebp` `mov ebp,esp`가 함수의 프롤로그가 되며, `leave`, `ret`이 함수의 에필로그가 됩니다.



{% highlight bash %}
0x08048446 <+9>:	mov	DWORD PTR [esp+0x4],eax
0x0804844a <+13>:	mov	DWORD PTR [esp], 0x80484f0
0x08048451 <+20>:	call	0x80484330 <__isoc99_scanf@plt>
{% endhighlight %}

스택 구조에 대해서 설명을 붙이자면 위 어셈블리 코드를 보면 scanf가 실행되기 전 `esp`와 `esp+0x4`에 값을 넣고 있는 것을 알 수 있습니다. 

`scanf`함수는 보통 `scanf("%s", buf)` 형태로 코드를 작성합니다.

여기서 `%s`와 `buf`가 `인자 값`이 되기 때문에 `esp`와 `esp+0x4`에 값을 넣어주게 됩니다.

{% highlight bash %}
gdb-peda$ x/2wx $esp
0xbffff148:	0x080484f0	0xbffff150
gdb-peda$ x/s 0x80484f0
0x80484f0:	"%s"
{% endhighlight %}

esp 위치부터 2개가 값을 확인해 보면 `0x80484f0`와 `0xbffff150`이 있습니다. scanf가 필요한 인자 값이 2개 이므로 각각 `%s`와 `buf`를 뜻하게 됩니다.

scanf를 실행한 뒤에 buf의 주소를 확인해 보면 값이 쌓여있는 것을 알 수 있습니다.

{% highlight bash %}
gdb-peda$ x/10wx 0xbffff150
0xbffff150:	0x34333231	0x00000000	0x00000000	0xb7e1fa83
0xbffff160:	0x00000001	0xbffff1f4	0xbffff1fc	0xb7feccea
0xbffff170:	0x00000001	0xbffff1f4
{% endhighlight %}

`1234`라는 값을 입력했더니 `0x34333231`라는 값이 저장되었습니다. 1234라는 값이 각각 hex 값으로 저장된 것입니다. 하지만 ascii 코드를 살펴보면 4321 순으로 저장된 것을 볼 수 있습니다.

이는 `리틀엔디안과 빅엔디안`차이에 따라서 결정됩니다.


코드를 하나씩 살펴보면서 값이 어떻게 저장되는지 확인해 봤습니다. `scanf`함수에서 취약점이 발생한다고 했으니 어떻게 되는지 알아보도록 하겠습니다.

{% highlight bash %}
gdb-peda$ x/4wx 0xbffff150
0xbffff150:	0x34333231	0x38373635	0x00000000	0xb7e1fa83
{% endhighlight %}

이번에는 `12345678`을 입력했습니다. 위 스택을 살펴보면 `4321 8765` 순으로 쌓인 것을 알 수 있으며 buf의 크기가 8인데 8자리를 모두 사용했습니다.

여기서 `scanf 함수는 크기를 제한하지 않으므로` 값을 더 입력해 보겠습니다.

{% highlight bash %}
gdb-peda$ x/4wx 0xbffff150
0xbffff150:	0x34333231	0x38373635	0x32313039	0x36353433
{% endhighlight %}

값을 더 입력했더니 뒤에 존재했던 값들이 모두 덮어 씌워졌습니다.

이 상태에서 프로그램을 진행 시키면 `0x36353433 in ?? ()` 과 같이 나타납니다. 이 말은 `0x36353433 주소로 이동`을 했다는 것 입니다.

이제 여기서 `Save Frame Pointer`와 `Return`을 알아야 합니다.

Save Frame Pointer는 `이전함수에서의 ebp 값을 저장`해 둡니다. 해당 함수가 실행되고 난 후에 다시 돌아가야 할 때 이 값을 기준으로 돌아가게 됩니다.

Return은 해당 주소로 이동하는 것입니다.

![stack](/img/system_hacking/bof/stack.png)

Stack의 구조는 위와 같이 이루어져 있기 때문에 앞서 값을 넘치게 입력했을 경우 0x36353433로 이동하게 된 것입니다.

이러한 방법으로 `RET 값을 조작해서 system 함수를 호출하거나 프로그램 상에서 호출되지 않는 함수에도 접근이 가능`하게 됩니다.