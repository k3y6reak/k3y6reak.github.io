---
layout: post
title:  "Easy Crack"
subtitle: "Reversing.kr의 Easy Crack 풀이방법입니다."
date:   2018-01-22 14:43:00 +0900
categories: ['wargame']
tags: reversing-kr
comments: true
---

## 문제
![crackme](/img/wargame/reversing_kr/easy_crack.png)


## 풀이

먼저 Easy Crack이 어떤 언어로 만들어 졌는지 확인하기 위해 Exeinfo를 이용해 확인합니다.

![exe_info](/img/wargame/reversing_kr/exe_info.png)

위 그림을 보면 C++로 만들어졌진 것을 알 수 있습니다.

이 문제는 전형적인 `CrackMe`로 올바른 `키 값`을 입력을 해야 합니다.

IDA를 사용하면 엄청 쉽게 해결할 수 있지만, 분석하는 방법을 익히기 위해서 하나씩 살펴보도록 하겠습니다.


### 직접 실행하기

리버싱 문제를 해결하기 위해서 항상 프로그램을 직접 실행하여 어떻게 동작하는지, 어떤 문자열이 나타나는지 확인해야 합니다.

![incorrect](/img/wargame/reversing_kr/incorrect.png)

`123`이라는 문자열을 넣고 확인 버튼을 누르면 `incorrect password`라는 문자열이 나타납니다. 그렇다면 올바른 password를 입력하면 아마도 `correct`가 나타날 것이라고 생각할 수 있습니다.


### 언어, 패킹 유무 확인하기

앞서 미리 Exeinfo를 이용해 확인을 했지만 다시 확인하자면 C++로 만들어졌고, 패킹은 안돼 있는 것을 알 수 있습니다.


### 디버깅

분석할 때 사용할 프로그램은 x86DBG 입니다. 과거 ollydbg를 많이 사용했으나 현재는 x86dbg를 많이 사용하고 있습니다.

`키 값`을 찾는 문제에서는 반드시 올바른 값인지에 대한 여부를 꼭 확인합니다.

여기서 잠시 코딩을 생각해본다면 어떤 특정 값이 맞는지에 대한 여부는 보통 `조건문`을 이용해 검사할 수 있습니다. (물론 여러 방법을 이용해 확인 할 수 있습니다.)

그렇다면 어셈블리어에서 조건문에 해당하는 부분을 찾아야 하는데, 대부분의 조건문을 나타내는 어셈블리어는 `CMP ~ JMP(비교 ~ 점프)`구문으로 이루어져 있습니다.


### 문자열 찾기

조건문 부분을 찾기 위해서는 `문자열`을 찾아야 합니다. 앞서 직접 실행하면서 `Incorrect Password`라는 문자열을 찾았습니다. 그렇다면 `확인`버튼을 누르고 해당 문자열이 나타났기 때문에 문자열이 있는 근처에서 조건문이 실행됐다고 생각할 수 있습니다.


![string](/img/wargame/reversing_kr/string.png)

x86DBG에서는 `오른쪽 마우스 - Search for - Current Region(또는 All Modules) - String references`에서 문자열을 찾을 수 있습니다.

{% highlight bash %}
Address  Disassembly                       String                                     
004010BD push easy_crackme.406078          "5y"
004010D1 mov esi,easy_crackme.40606C       "R3versing"
00401116 push easy_crackme.406058          "EasyCrackMe"
0040111B push easy_crackme.406044          "Congratulation !!"
00401137 push easy_crackme.406058          "EasyCrackMe"
0040113C push easy_crackme.406030          "Incorrect Password"
00401C04 push easy_crackme.4050D4          "__MSVCRT_HEAP_SELECT"
00401C43 push easy_crackme.4050BC          "__GLOBAL_HEAP_SELECTED"
00401FE2 push easy_crackme.4053C4          "<program name unknown>"
00402024 push easy_crackme.4053C0          "..."
00402038 push easy_crackme.4053A4          "Runtime Error!\n\nProgram: "
00402056 push easy_crackme.4053A0          "\n\n"
00402061 push dword ptr ds:[esi+406134]    &"R6002\r\n- floating point not loaded\r\n"
0040207E push easy_crackme.405378          "Microsoft Visual C++ Runtime Library"
00402092 lea esi,dword ptr ds:[esi+406134] &"R6002\r\n- floating point not loaded\r\n"
00403CE1 push easy_crackme.40540C          "user32.dll"
00403CF8 push easy_crackme.405400          "MessageBoxA"
00403D09 push easy_crackme.4053F0          "GetActiveWindow"
00403D11 push easy_crackme.4053DC          "GetLastActivePopup"
{% endhighlight %}

문자열 중 직접 실행해서 찾은 `Incorrect Password`가 존재하는 것을 확인 할 수 있고 `Congratulation !!`이라는 문자열도 존재하는 것을 알 수 있습니다.

그렇다면 올바른 키 값을 입력하면 `Congratulation !!`이라는 문자열이 나타날 것으로 추측할 수 있습니다.

`Incorrect Password` 부분을 클릭하면 해당 문자열이 존재하는 주소로 이동하게 됩니다.


### CMP ~ JMP 구문 찾기

![cmp_jmp](/img/wargame/reversing_kr/cmp_jmp.png)

문자열을 찾아 이동한 후 위, 아래를 쭉 살펴봅니다. 위 그림을 살펴보면 `4010B0` 주소에서 `0x61`과 비교를 한 후 `JNE`가 실행되는 것을 볼 수 있습니다. 그림의 왼쪽 화살표를 확인하면 좀 더 쉽게 살펴볼 수 있습니다.

화살표들을 살펴보면 `4010B5`, `4010CD`, `40110B`, `401112` 에서 조건을 확인하며 값이 정확하지 않을 경우 모두 `Incorrect Password` 부분으로 넘어가는 것을 알 수 있습니다.

그렇다면 이 부분들의 값을 정확히 맞춰주면 될 것이라 생각할 수 있습니다. 따라서 해당 부분 모두 BreakPoint를 걸어두고 `k3y6reak`를 입력하고 진행해 보겠습니다.

{% highlight bash %}
004010B0 | 80 7C 24 05 61           | CMP BYTE PTR SS:[ESP + 5], 61                    | 61:'a'
004010B5 | 75 7E                    | JNE easy_crackme.401135                          |

BYTE [ESP+5]=[0019F6ED]=33 '3'
61 'a'
.text:004010B0 easy_crackme.exe:$10B0 #10B0

{% endhighlight %}

먼저 `4010B0` 위치에서 문자 `a`와 `3`을 비교하고 있습니다. `k3y6reak`를 입력했을 때 두 번째 값과 a를 비교하고 있다는 것을 알 수 있고 해당 값은 틀렸기 때문에 `Incorrect Password`로 넘어갈 것입니다. 따라서 다시 실행하여 `kay6reak`를 입력해 보겠습니다.

{% highlight bash %}
004010B0 | 80 7C 24 05 61           | CMP BYTE PTR SS:[ESP + 5], 61                    | 61:'a'
004010B5 | 75 7E                    | JNE easy_crackme.401135                          |
004010B7 | 6A 02                    | PUSH 2                                           |
004010B9 | 8D 4C 24 0A              | LEA ECX, DWORD PTR SS:[ESP + A]                  |
004010BD | 68 78 60 40 00           | PUSH easy_crackme.406078                         | 406078:"5y"
004010C2 | 51                       | PUSH ECX                                         |
004010C3 | E8 88 00 00 00           | CALL easy_crackme.401150                         |
004010C8 | 83 C4 0C                 | ADD ESP, C                                       |
004010CB | 85 C0                    | TEST EAX, EAX                                    |
004010CD | 75 66                    | JNE easy_crackme.401135                          |

EAX : 00000001
ECX : 00000001
EDX : 00000000
EBX : 00000001
EDI : 0039053C
EBP : 0019FA18
ESI : 0039053C
ESP : 0019F980
EIP : 004010CD     easy_crackme.004010CD
{% endhighlight %}

`4010B0`위치는 원하는 방향대로 넘어갔지만 `4010CD` 부분은 통과하지 못했습니다. `EAX`의 값은 `1`인데 `JNE`에서 `Incorrect Password`로 넘어가기 때문에 단순히 생각하면 `EAX`의 값이 `0`이 되면 넘어갈 것으로 추측할 수 있습니다.

하지만 `4010B0`부터 `4010CD`의 어셈블리어를 살펴보면 어떤 부분도 `EAX`를 이용한 연산은 눈에 보이지 않습니다. 여기서 한 가지 중요한 것은 `함수의 리턴 값은 EAX에 저장된다.`라는 것을 알아야 합니다.

어셈블리어에서 함수의 호출은 `CALL` 명령을 통해서 해당 함수의 주소로 이동하게 됩니다. `4010B0`부터 `4010CD` 중에서 함수를 호출하는 부분은 `4010C3`이 있는데 해당 부분에 BreakPoint를 걸고 들어가 보도록 하겠습니다.

{% highlight bash %}
00401150 | 55                       | PUSH EBP                                         |
00401151 | 8B EC                    | MOV EBP, ESP                                     |
00401153 | 57                       | PUSH EDI                                         |
00401154 | 56                       | PUSH ESI                                         |
00401155 | 53                       | PUSH EBX                                         |
00401156 | 8B 4D 10                 | MOV ECX, DWORD PTR SS:[EBP + 10]                 |
00401159 | E3 26                    | JECXZ easy_crackme.401181                        |
0040115B | 8B D9                    | MOV EBX, ECX                                     | ecx:"y6reak"
0040115D | 8B 7D 08                 | MOV EDI, DWORD PTR SS:[EBP + 8]                  |
00401160 | 8B F7                    | MOV ESI, EDI                                     |
00401162 | 33 C0                    | XOR EAX, EAX                                     |
00401164 | F2 AE                    | REPNE SCASB AL, BYTE PTR ES:[EDI]                |
00401166 | F7 D9                    | NEG ECX                                          | ecx:"y6reak"
00401168 | 03 CB                    | ADD ECX, EBX                                     | ecx:"y6reak"
0040116A | 8B FE                    | MOV EDI, ESI                                     |
0040116C | 8B 75 0C                 | MOV ESI, DWORD PTR SS:[EBP + C]                  |
0040116F | F3 A6                    | REPE CMPSB BYTE PTR DS:[ESI], BYTE PTR ES:[EDI]  |
00401171 | 8A 46 FF                 | MOV AL, BYTE PTR DS:[ESI - 1]                    |
00401174 | 33 C9                    | XOR ECX, ECX                                     | ecx:"y6reak"
00401176 | 3A 47 FF                 | CMP AL, BYTE PTR DS:[EDI - 1]                    |
00401179 | 77 04                    | JA easy_crackme.40117F                           |
0040117B | 74 04                    | JE easy_crackme.401181                           |
0040117D | 49                       | DEC ECX                                          | ecx:"y6reak"
0040117E | 49                       | DEC ECX                                          | ecx:"y6reak"
0040117F | F7 D1                    | NOT ECX                                          | ecx:"y6reak"
00401181 | 8B C1                    | MOV EAX, ECX                                     | ecx:"y6reak"
00401183 | 5B                       | POP EBX                                          |
00401184 | 5E                       | POP ESI                                          |
00401185 | 5F                       | POP EDI                                          |
00401186 | C9                       | LEAVE                                            |
00401187 | C3                       | RET                                              |
{% endhighlight %}

`401150`의 함수 부분인데 이 부분에서 어떤 값을 검사하는지 알아야 합니다.

{% highlight bash %}
00401176 | 3A 47 FF                 | CMP AL, BYTE PTR DS:[EDI - 1]                    | edi-1:"y6reak"
00401179 | 77 04                    | JA easy_crackme.40117F                           |
0040117B | 74 04                    | JE easy_crackme.401181                           |

al=35 '5'
BYTE [EDI-1]=[0019F986]=79 'y'
.text:00401176 easy_crackme.exe:$1176 #1176
{% endhighlight %}

`401176` 에서 `5`와 `y`를 비교하고 있습니다. 입력했던 `kay6reak`의 `y`와 비교를 한다는 것이므로 `ka56reak`가 돼야 한다는 것을 알 수 있습니다. 다시 실행하여 `ka56reak`를 입력한 후 `EAX`의 값을 살펴보도록 하겠습니다.

{% highlight bash %}
004010B0 | 80 7C 24 05 61           | CMP BYTE PTR SS:[ESP + 5], 61                    | 61:'a'
004010B5 | 75 7E                    | JNE easy_crackme.401135                          |
004010B7 | 6A 02                    | PUSH 2                                           |
004010B9 | 8D 4C 24 0A              | LEA ECX, DWORD PTR SS:[ESP + A]                  |
004010BD | 68 78 60 40 00           | PUSH easy_crackme.406078                         | 406078:"5y"
004010C2 | 51                       | PUSH ECX                                         |
004010C3 | E8 88 00 00 00           | CALL easy_crackme.401150                         |
004010C8 | 83 C4 0C                 | ADD ESP, C                                       |
004010CB | 85 C0                    | TEST EAX, EAX                                    |
004010CD | 75 66                    | JNE easy_crackme.401135                          |

EAX : FFFFFFFF
ECX : FFFFFFFF
EDX : 00000000
EBX : 00000001
EDI : 002B05AC
EBP : 0019FA18
ESI : 002B05AC
ESP : 0019F980
EIP : 004010CB     easy_crackme.004010CB
{% endhighlight %}

`EAX`의 값이 `FFFFFFFF`이 됐지만 마찬가지로 `JNE`에서 `Incorrect Password`로 넘어갑니다. 다시 실행하여 `ka56reak`를 입력해서 함수로 들어가 다시 확인해 보겠습니다.

{% highlight bash %}
al=79 'y'
BYTE [EDI-1]=[0019F6EF]=36 '6'
.text:00401176 easy_crackme.exe:$1176 #1176
{% endhighlight %}

이번에는 `ka56reak` 중 `6`과 `y`를 비교하고 있습니다. 그렇다면 `ka5yreak`가 돼야 한다는 것을 알 수 있습니다.

`ka5yreak`를 입력한 후 실행하면 `004010CD`의 `JNE`가 실행이 안되는 것을 알 수 있습니다.

{% highlight bash %}
004010CF | 53                       | PUSH EBX                                         |
004010D0 | 56                       | PUSH ESI                                         | esi:"R3versing"
004010D1 | BE 6C 60 40 00           | MOV ESI, easy_crackme.40606C                     | esi:"R3versing", 40606C:"R3versing"
004010D6 | 8D 44 24 10              | LEA EAX, DWORD PTR SS:[ESP + 10]                 |
004010DA | 8A 10                    | MOV DL, BYTE PTR DS:[EAX]                        | eax:"reak"
004010DC | 8A 1E                    | MOV BL, BYTE PTR DS:[ESI]                        | esi:"R3versing"
004010DE | 8A CA                    | MOV CL, DL                                       |
004010E0 | 3A D3                    | CMP DL, BL                                       |
004010E2 | 75 1E                    | JNE easy_crackme.401102                          |

dl=72 'r'
bl=52 'R'
.text:004010E0 easy_crackme.exe:$10E0 #10E0
{% endhighlight %}

`4010E0` 부분에서 `r`와 `R`을 비교하고 있습니다. 입력했던 값 `ka5yreak` 중 `r`과 비교를 한다는 것인데, 위 어셈블리어 코드를 살펴보면 `R3versing`이라는 문자열이 존재하는 것을 알 수 있습니다.

그렇다면 `reak` 부분과 `R3versing`을 비교한다고 추측할 수 있습니다. 재실행하여 `ka5yR3versing`을 입력하고 진행해 보겠습니다.

{% highlight bash %}
0040110D | 80 7C 24 04 45           | CMP BYTE PTR SS:[ESP + 4], 45                    | 45:'E'
00401112 | 75 21                    | JNE easy_crackme.401135                          |

BYTE [ESP+4]=[0019F984]=6B 'k'
45 'E'
.text:0040110D easy_crackme.exe:$110D #110D
{% endhighlight %}

`ka5yR3versing`을 입력하고 진행했더니 `40110D`까지 진행된 것을 알 수 있습니다. 이 부분에서는 `k`와 `E`를 비교하고 있기 때문에 `Ea5yR3versing`을 입력해 보겠습니다.

![Congratulation](/img/wargame/reversing_kr/congratz.png)

입력한 결과가 모두 일치한 것을 알 수 있습니다.

`Flag: Ea5yR3versing`


