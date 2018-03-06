---
layout: post
title:  "Easy KeygenMe"
subtitle: "Reversing.kr의 Easy Keygen 풀이방법입니다."
date:   2018-03-05 14:31:00 +0900
categories: ['wargame']
tags: reversing-kr
comments: true
---

## 풀이

reversing.kr에서 파일을 다운로드 받으면 `Easy_KeygenMe.zip` 이 생성됩니다.

이 파일을 압축 해제하면 `Easy Keygen.exe`와 `ReadMe.txt` 파일이 생성됩니다. 먼저 ReadMe.txt 파일을 살펴보겠습니다.


## ReadMe.txt

ReversingKr KeygenMe
Find the Name when the Serial is 5B134977135E7D13

시리얼 넘버가 `5B134977135E7D13`인 이름을 찾는 문제입니다.


## 풀이

`Easy Keygen.exe`를 실행하여 어떻게 작동하는지 알아봅시다.

{% highlight bash %}
C:\Users\k3y6reak\Desktop\Easy_KeygenMe>"Easy Keygen.exe"
Input Name: k3y6reak
Input Serial: 5B134977135E7D13
Wrong
{% endhighlight %}

이 문제도 마찬가지로 흔한 크랙미 문제로, 이름의 입력값에 따라 일정 연산을 통해서 시리얼을 만드는 프로그램으로 추측할 수 있습니다. 여기서 시리얼 값을 고정이므로 이에 맞는 `Name`을 찾는 것이 중요합니다.


### 언어, 패킹 유무 확인하기

![exe_info](/img/wargame/reversing_kr/easy_keygenme/exe_info.png)

exeinfo를 이용하여 easy_keygen.exe 을 분석하면 위 그림과 같이 C++로 만들어졌고, 패킹은 안돼있는 것을 알 수 있습니다.


### 디버깅

이전 easy_crack 포스팅에서 크랙미 문제는 `CMP ~ JMP 구문`을 이용해 값을 비교한다고 했습니다. 마찬가지로 이번 문제도 이름에 따라 시리얼이 생성되는 부분을 찾아야 합니다. 또한 실행 결과에 따라 `wrong` `correct`가 출력되므로 해당 문자열을 찾아 이동 후 분석을 하는 것이 좋습니다. 

correct 부분으로 이동 후 `k3y6reak`와 `5B134977135E7D13`을 입력해 봅니다.

![wong](/img/wargame/reversing_kr/easy_keygenme/wrong.png)

위 그림처럼 왼쪽 화살표를 살펴보면 해당 `JNZ` 부분에서 `wrong` 부분으로 이동하는 것을 알 수 있습니다.

그렇다면 `이름을 입력 후 연산하는 곳`을 찾기 위해서 `4010C8`에 breakpoint를 걸어두고 다시 실행 해 보겠습니다.

{% highlight bash %}
004010C1 | 68 44 80 40 00           | PUSH easy keygen.408044                          | 408044:"Input Serial: "
004010C6 | F3 AB                    | REP STOSD DWORD PTR ES:[EDI], EAX                | edi:"7B1349265255714B"
004010C8 | E8 EC 00 00 00           | CALL easy keygen.4011B9                          |
004010CD | 83 C4 04                 | ADD ESP, 4                                       |

EAX : 00000000
ECX : 00000000
EDX : 0019FE8B
EBX : 002CE000
EDI : 0019FE7C     "7B1349265255714B"
EBP : 00000008
ESI : 00000002
ESP : 0019FE04     &"Input Serial: "
EIP : 004010C8     easy keygen.004010C8
{% endhighlight %}

`4010C8`까지 진행 결과 입니다. `k3y6reak`라는 이름을 넣고 아직 시리얼은 넣지 않은 상태입니다. 그런데, `EDI`레지스터를 살펴보면 `7B1349265255714B`라는 시리얼과 비슷한 값이 나타난 것을 알 수 있습니다.

혹시 모르니 프로그램을 새로 실행 후 k3y6reak와 7B1349265255714B를 넣어보도록 하겠습니다.

{% highlight bash %}
C:\Users\k3y6reak\Desktop\Easy_KeygenMe>"Easy Keygen.exe"
Input Name: k3y6reak
Input Serial: 7B1349265255714B
Correct!
{% endhighlight %}

`k3y6reak`에 대한 시리얼 값이 `7B1349265255714B`라는 것을 알 수 있습니다. 자 그렇다면 여기서 알 수 있는 것은 `시리얼을 입력하기 전 이름을 입력 후 시리얼을 생성`하는 것입니다. 그렇다면 한 줄 한 줄 실행하면서 분석해 보겠습니다.



{% highlight bash %}
00401077 | 83 FE 03                 | CMP ESI, 3                                       |
0040107A | 7C 02                    | JL easy keygen.40107E                            |
0040107C | 33 F6                    | XOR ESI, ESI                                     |
0040107E | 0F BE 4C 34 0C           | MOVSX ECX, BYTE PTR SS:[ESP + ESI + C]           |
00401083 | 0F BE 54 2C 10           | MOVSX EDX, BYTE PTR SS:[ESP + EBP + 10]          |
00401088 | 33 CA                    | XOR ECX, EDX                                     |
0040108A | 8D 44 24 74              | LEA EAX, DWORD PTR SS:[ESP + 74]                 |
0040108E | 51                       | PUSH ECX                                         |
0040108F | 50                       | PUSH EAX                                         |
00401090 | 8D 4C 24 7C              | LEA ECX, DWORD PTR SS:[ESP + 7C]                 |
00401094 | 68 54 80 40 00           | PUSH easy keygen.408054                          | 408054:"%s%02X"
00401099 | 51                       | PUSH ECX                                         |
0040109A | E8 B1 00 00 00           | CALL easy keygen.401150                          |
0040109F | 83 C4 10                 | ADD ESP, 10                                      |
004010A2 | 45                       | INC EBP                                          |
004010A3 | 8D 7C 24 10              | LEA EDI, DWORD PTR SS:[ESP + 10]                 |
004010A7 | 83 C9 FF                 | OR ECX, FFFFFFFF                                 |
004010AA | 33 C0                    | XOR EAX, EAX                                     |
004010AC | 46                       | INC ESI                                          |
004010AD | F2 AE                    | REPNE SCASB AL, BYTE PTR ES:[EDI]                |
004010AF | F7 D1                    | NOT ECX                                          |
004010B1 | 49                       | DEC ECX                                          |
004010B2 | 3B E9                    | CMP EBP, ECX                                     |
004010B4 | 7C C1                    | JL easy keygen.401077                            |
{% endhighlight %}

한 줄 씩 분석을 하다보면 `401077`부터 `4010B4` 부분을 계속해서 반복하는 것을 알 수 있습니다. 이 부분을 계속 진행하다보면 시리얼과 같은 값을 생성하는 것을 볼 수 있습니다. 따라서, 이 부분이 시리얼을 만드는 구간으로 추측할 수 있습니다.

이 부분을 반복하다 보면 입력한 `k3y6reak`와 `XOR` 연산을 하는 값을 확인 할 수 있습니다.

{% highlight bash %}
00401088 | 33 CA                    | XOR ECX, EDX                                     |
{% endhighlight %}

`401088` 부분에서 `ECX`와 `EDX` 값을 `XOR`하는 것을 알 수 있는데, 이 부분에서 XOR하는 값이 `0x10, 0x20, 0x30` 순으로 계속해서 반복합니다.

해당 부분은 직접 찾아보시길 바랍니다.

그렇다면 시리얼을 알려주었고 이에 대한 이름을 찾아야 합니다.

ReadMe.txt에서 알려준 시리얼 값은 `5B134977135E7D13` 이므로 `5B 13 49 77 13 5E 7D 13` 각 자리마다 `0x10, 0x20, 0x30` 순으로 연산을 하면 이름을 찾을 수 있습니다.


{% highlight bash %}
C:\Users\k3y6reak\Desktop\Easy_KeygenMe>"Easy Keygen.exe"
Input Name: K3yg3nm3
Input Serial: 5B134977135E7D13
Correct!
{% endhighlight %}


`flag: K3yg3nm3`