---
layout: post
title:  "Save Frame Pointer Overwrite"
date:   2017-07-25 10:05:00 +0900
categories: ['technique', 'system_hacking']
---

<br/><br/>
### 개념

Buffer Overflow가 발생하는 프로그램의 취약점을 이용하여 Save Frame Pinter를 덮어씌워 원하는 주소로 이동하는 방법을 말합니다.

<br/><br/>
### 설명

Buffer Overflow에서 잠깐 언급했던 Save Frame Pointer(SFP)는 `이전 함수의 ebp`를 저장해 두고 함수의 실행이 끝나면 돌아가는 역할을 했습니다.


{% highlight c %}
#include <stdio.h>

int main(void)
{
	printf("Main Func\n");
	sub_func();
	return 0;
}

void sub_func()
{
	char buf[8];
	scanf("%s", buf);
}
{% endhighlight %}

위 소스코드를 보면 main 함수에서는 출력과 sub_func()을 호출하고 sub_func 함수에서는 buf가 8개의 공간이 할당되었고 scanf를 이용해 buf에 값을 저장합니다.


{% highlight bash %}
gdb-peda$ Dump of assembler code for function main:
0x0804846d <+0>:	push	ebp
0x0804846e <+1>:	mov 	ebp,esp
0x08048470 <+3>:	sub 	esp,0x4
0x08048473 <+6>:	mov	DWORD PTR [esp],0x8048540
0x0804847a <+13>:	call	0x8048330 <puts@plt>
0x0804847f <+18>:	call	0x804848b <sub_func>
0x08048484 <+23>:	mov	eax,0
0x08048489 <+28>:	leave
0x0804848a <+29>:	ret
End of assembler dump.
{% endhighlight %}

위 어셈블리 코드에서 puts와 sub_func의 호출을 볼 수 있으며 puts를 호출하기 전 esp에 `0x8048540` 값을 저장하고 있습니다.

{% highlight bash %}
gdb-peda$ x/s 0x8048540
0x8048540:	"Main Func"
{% endhighlight %}

`0x804847f`에 Breakpoint를 걸고 해당 주소까지 실행해 보도록 하겠습니다.

{% highlight bash %}
gdb-peda$ Dump of assembler code for function main:
	0x0804846d <+0>:	push	ebp
	0x0804846e <+1>:	mov 	ebp,esp
	0x08048470 <+3>:	sub 	esp,0x4
	0x08048473 <+6>:	mov	DWORD PTR [esp],0x8048540
	0x0804847a <+13>:	call	0x8048330 <puts@plt>
=>	0x0804847f <+18>:	call	0x804848b <sub_func>
	0x08048484 <+23>:	mov	eax,0
	0x08048489 <+28>:	leave
	0x0804848a <+29>:	ret
End of assembler dump.
{% endhighlight %}

해당 위치에서 esp 부터 10개의 값은 아래와 같습니다.

{% highlight bash %}
gdb-peda$ x/10wx $esp
0xbffff154:	0x08048540	0x00000000	0xb7e2fa83	0x00000001
0xbffff164:	0xbffff1f4	0xbffff1fc	0xb7feccea	0x00000001
0xbffff174:	0xbffff1f4	0xbffff194
{% endhighlight %}

이제 여기서 sub_func 함수 안으로 들어가 보도록 하겠습니다.

{% highlight bash %}
gdb-peda$ Dump of assembler code for function sub_func:
=>	0x0804848b <+0>:	push	ebp
	0x0804848c <+1>:	mov 	ebp,esp
	0x0804848e <+3>:	sub 	esp,0x10
	0x08048491 <+6>:	lea	eax,[ebp-0x8]
	0x08048494 <+9>:	mov	DWORD PTR[esp+0x4],eax
	0x08048498 <+13>:	mov	DWORD PTR[esp],0x804854b
	0x0804849f <+20>:	call	0x8048360 <__isoc99_scanf@plt>
	0x080484a4 <+25>:	leave
	0x080484a5 <+26>:	ret
End of assembler dump.
{% endhighlight %}

sub_func 함수 내로 들어온 후 esp 값을 확인해보면 아래와 같습니다.

{% highlight bash %}
gdb-peda$ x/10wx $esp
0xbffff150:	0x08048484	0x08048540	0x00000000	0xb7e2fa83
0xbffff160:	0x00000001	0xbffff1f4	0xbffff1fc	0xb7feccea
0xbffff170:	0x00000001	0xbffff1f4
{% endhighlight %}

스택에 쌓여진 값을 살펴보면 `0x8048484`라는 값을 확인할 수 있는데 이 값은 main함수에서 sub_func이 있는 곳의 다음 주소를 나타냅니다. `다음 주소를 쌓아두는 이유는 sub_func 함수가 끝나면 main에서 다음 코드를 실행해야 하기 때문에 쌓아둔 것`이며 이 값이 `Return`값이 됩니다.

`0x804849f`에 Breakpoint를 걸고 해당 주소까지 실행 후 스택을 확인해 보도록 하겠습니다.

{% highlight bash %}
gdb-peda$ Dump of assembler code for function sub_func:
	0x0804848b <+0>:	push	ebp
	0x0804848c <+1>:	mov 	ebp,esp
	0x0804848e <+3>:	sub 	esp,0x10
	0x08048491 <+6>:	lea	eax,[ebp-0x8]
	0x08048494 <+9>:	mov	DWORD PTR[esp+0x4],eax
	0x08048498 <+13>:	mov	DWORD PTR[esp],0x804854b
=>	0x0804849f <+20>:	call	0x8048360 <__isoc99_scanf@plt>
	0x080484a4 <+25>:	leave
	0x080484a5 <+26>:	ret
End of assembler dump.
{% endhighlight %}

{% highlight bash %}
gdb-peda$ x/10wx $esp
0xbffff13c:	0x0804854b	0xbffff144	0x00000000	0x00000000
0xbffff14c:	0xbffff158	0x08048484	0x08048540	0x00000000
0xbffff15c:	0xb7e2fa83	0x00000001
{% endhighlight %}

scanf 함수가 사용하는 인자 값은 2개이기 때문에 `0x804854b`와 `0xbffff144`가 인자 값이 됩니다.
0x804854b는 `%s`가 되며 0xbffff144는 `buf의 주소`가 됩니다.

{% highlight bash %}
gdb-peda$ x/10wx 0xbffff144
0xbffff144:	0x00000000	0x00000000	0xbffff158	0x08048484
0xbffff154:	0x08048540	0x00000000	0xb7e2fa83	0x00000001
0xbffff164:	0xbffff1f4	0xbffff1fc
{% endhighlight %}

0xbffff144의 스택을 살펴보면 아직 scanf가 실행되지 않았기 때문에 값이 0x00000000로 되어있는 것을 알 수 있습니다. scanf가 실행되고 난 뒤를 실펴보기 위해 `ni`명령으로 실행 후 `12345678`값을 입력하고 다시 스택을 살펴보면 아래와 같습니다.


{% highlight bash %}
gdb-peda$ x/10wx 0xbffff144
0xbffff144:	0x34333231	0x38373635	0xbffff100	0x08048484
0xbffff154:	0x08048540	0x00000000	0xb7e2fa83	0x00000001
0xbffff164:	0xbffff1f4	0xbffff1fc
{% endhighlight %}

buf의 다음은 Save Frame Pointer 이므로 0xbffff100이 Save Frame Pointer가 됩니다.

값을 입력하기 전에 SFP의 값은 0xbffff158 이었는데 0xbffff100으로 바뀐 이유는 문자열을 입력하면서 `마지막에 Null이 포함`된 것입니다.

<br/><br/>
### 이해
LOB의 golem 소소코드를 이용하여 SFP를 실습해 보도록 하겠습니다.

{% highlight c %}
#include <stdio.h>
#include <stdlib.h>

void proglem_child(char *src)
{
	char buffer[40];
	strncpy(buffer, src, 41);
	printf("%s\n", buffer);
}

int main(int argc, char* argv[])
{
	if(argc < 2)
	{
		printf("argv error\n");
		exit(0);
	}
	prolem_child(argv[1]);
}
{% endhighlight %}

main 함수에서 argv로 인자 값을 받아오고 해당 값을 problem_child 함수에서 src로 받습니다.
src로 받은 값을 buffer에 41개 만큼 저장합니다.

여기서 중요한 것은 `buffer의 크기는 40인데 strncpy를 이용해 41개를 넣어주는 것입니다.`

{% highlight bash %}
gdb-peda$ pdisas main
Dump of assembler code for function main:
	0x080484aa <+0>:	push	ebp
	0x080484ab <+1>:	mov 	ebp,esp
	0x080484ad <+3>:	sub 	esp,0x4
	0x080484b0 <+6>:	cmp	DWORD PTR [ebp+0x8],0x1
	0x080484b4 <+10>:	jg	0x80484ce <main+36>
	0x080484b6 <+12>:	mov	DWORD PTR [esp],0x8048570
	0x080484bd <+19>:	call	0x8048330 <puts@plt>
	0x080484c2 <+24>:	mov	DWORD PTR [esp],0x0
	0x080484c9 <+31>:	call	0x8048350 <exit@plt>
	0x080484ce <+36>:	mov	eax, DWORD PTR [ebp+0xc]
	0x080484d1 <+39>:	add 	eax,0x4
	0x080484d4 <+42>:	mov	eax,DWORD PTR [eax]
	0x080484d6 <+44>:	mov	DWORD PTR [esp],eax
	0x080484d9 <+47>:	call	0x804847d <problem_child>
	0x080484de <+52>:	leave
	0x080484df <+53>:	ret
End of assembler dump.
{% endhighlight %}

problem_child 위치에 Breakpoint를 걸고 \`python -c 'print "A"*40 + "B"'\`를 이용해 argv[1]에 값을 넣고 problem_child 함수 안으로 들어갑니다.

{% highlight bash %}
Dump of assembler code for function problem_child:
=>	0x0804847d <+0>:	push	ebp
	0x0804847e <+1>:	mov 	ebp,esp
	0x08048480 <+3>:	sub 	esp,0x34
	0x08048483 <+6>:	mov	DWORD PTR [esp+0x8],0x29
	0x0804848b <+14>:	mov	eax,DWORD PTR [ebp+0x8]
	0x0804848e <+17>:	mov	DWORD PTR [esp+0x4],eax
	0x08048492 <+21>:	lea 	eax,[ebp-0x28]
	0x08048495 <+24>:	mov 	DWORD PTR [esp],eax
	0x08048498 <+27>:	call 	0x8048370 <strncpy@plt>
	0x0804849d <+32>:	lea 	eax,[ebp-0x28]
	0x080484a0 <+35>:	mov	DWORD PTR [esp],eax
	0x080484a3 <+38>:	call	0x8048330 <puts@plt>
	0x080484a8 <+43>:	leave
	0x080484a9 <+44>:	ret
End of assembler dump.
{% endhighlight %}

`0x804849d`에 Breakpoint를 걸고 buf의 값을 확인해 보도록 하겠습니다.

{% highlight bash %}
Dump of assembler code for function problem_child:
	0x0804847d <+0>:	push	ebp
	0x0804847e <+1>:	mov 	ebp,esp
	0x08048480 <+3>:	sub 	esp,0x34
	0x08048483 <+6>:	mov	DWORD PTR [esp+0x8],0x29
	0x0804848b <+14>:	mov	eax,DWORD PTR [ebp+0x8]
	0x0804848e <+17>:	mov	DWORD PTR [esp+0x4],eax
	0x08048492 <+21>:	lea 	eax,[ebp-0x28]
	0x08048495 <+24>:	mov 	DWORD PTR [esp],eax
	0x08048498 <+27>:	call 	0x8048370 <strncpy@plt>
	0x0804849d <+32>:	lea 	eax,[ebp-0x28]
	0x080484a0 <+35>:	mov	DWORD PTR [esp],eax
	0x080484a3 <+38>:	call	0x8048330 <puts@plt>
	0x080484a8 <+43>:	leave
	0x080484a9 <+44>:	ret
End of assembler dump.
{% endhighlight %}

{% highlight bash %}
gdb-peda$ x/10wx $ebp-0x28
0xbffff0f4:	0x41414141	0x41414141	0x41414141	0x41414141
0xbffff104:	0x41414141	0x41414141	0x41414141	0x41414141
0xbffff114:	0x41414141	0x41414141	0xbffff142 	0x080484de
0xbffff124:	0xbffff39f	0x00000000 	0xb7e2fa83 	0x00000002
0xbffff134:	0xbffff1c4	0xbffff1d0	0xb7feccea	0x00000002
{% endhighlight %}

40개의 A와 B를 넣었더니 위와 같이 buf에 값이 채워졌습니다. 그리고 SFP의 값이 `0xbffff142`로 변조된 것도 확인할 수 있습니다.

이 변조된 값을 `함수의 에필로그`를 이용하여 리턴 값을 조작할 수 있습니다.

함수의 에필로그는 `leave`와 `ret`이 있는데 이 어셈블리어는 각각 `mov esp, ebp` `pop ebp`와 `pop eip` `jmp eip`가 됩니다.

{% highlight bash %}
gdb-peda$ pdisas main
Dump of assembler code for function main:
	0x080484aa <+0>:	push	ebp
	0x080484ab <+1>:	mov 	ebp,esp
	0x080484ad <+3>:	sub 	esp,0x4
	0x080484b0 <+6>:	cmp	DWORD PTR [ebp+0x8],0x1
	0x080484b4 <+10>:	jg	0x80484ce <main+36>
	0x080484b6 <+12>:	mov	DWORD PTR [esp],0x8048570
	0x080484bd <+19>:	call	0x8048330 <puts@plt>
	0x080484c2 <+24>:	mov	DWORD PTR [esp],0x0
	0x080484c9 <+31>:	call	0x8048350 <exit@plt>
	0x080484ce <+36>:	mov	eax, DWORD PTR [ebp+0xc]
	0x080484d1 <+39>:	add 	eax,0x4
	0x080484d4 <+42>:	mov	eax,DWORD PTR [eax]
	0x080484d6 <+44>:	mov	DWORD PTR [esp],eax
	0x080484d9 <+47>:	call	0x804847d <problem_child>
=>	0x080484de <+52>:	leave
	0x080484df <+53>:	ret
End of assembler dump.
{% endhighlight %}

main에서 leave 위치까지 실행한 뒤에 ebp와 esp를 확인하면 아래와 같습니다.

{% highlight bash %}
gdb-peda$ i r ebp
ebp 	0xbffff142 	0xbffff142
{% endhighlight %}

ebp를 확인한 결과 조작된 0xbffff142가 되었습니다. 여기서 leave 명령을 실행하면 `mov esp,ebp`, `pop ebp` 이므로 ebp의 값을 esp로 옮기고 pop하여 ebp에 저장합니다.

{% highlight bash %}
gdb-peda$ i r ebp esp
ebp 	0xf1c40000 	0xf1c40000
esp 	0xbffff146 	0xbffff146
{% endhighlight %}

변조 된 ebp 값이 `mov esp, ebp`를 통해서 esp에 저장이 되었고 `pop ebp`를 하면서 +4 만큼 증가되었습니다.

그래서 esp의 값이 0xbffff146이 됩니다. (0xbffff142 + 0x4)

그 후 0xbffff146에 있는 값을 `pop eip`, `jmp eip`하면서 해당 위치로 이동하게 됩니다.

따라서 `|shellcode 주소|\x90*n|shellcode|stack 주소 - 0x4|`와 같은 payload를 사용할 수 있습니다.

