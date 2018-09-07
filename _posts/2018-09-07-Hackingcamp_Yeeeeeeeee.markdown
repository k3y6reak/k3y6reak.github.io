---
layout: post
title:  "Hackingcamp 2018 Yeeeeeeeee"
subtitle: "Hackingcamp 2018 Yeeeeeeeee 풀이 입니다."
date:   2018-09-07 16:10:00 +0900
categories: ['ctfs']
comments: true
---

## 풀이

바이너리를 다운받아 파일을 확인하면 PE32 EXE 임을 확인할 수 있다.

{% highlight bash %}
file Yeeeeeeeee.exe
Yeeeeeeeee.exe: PE32 executable (console) Intel 80386, for MS Windows
{% endhighlight %}


x86dbg를 이용해 분석을 하면 `00411BB2`에서 ESI, ESP를 비교하는데 `0x27`과 비교한다. 즉, 39자리를 입력해야 한다.

{% highlight bash %}
00411A2C         | C7 85 3C FB FF FF  | MOV DWORD PTR SS:[EBP - 4C4], 120                |
00411A36         | C7 85 40 FB FF FF  | MOV DWORD PTR SS:[EBP - 4C0], 10C                |
00411A40         | C7 85 44 FB FF FF  | MOV DWORD PTR SS:[EBP - 4BC], 104                |
00411A4A         | C7 85 48 FB FF FF  | MOV DWORD PTR SS:[EBP - 4B8], 134                |
00411A54         | C7 85 4C FB FF FF  | MOV DWORD PTR SS:[EBP - 4B4], 140                |
00411A5E         | C7 85 50 FB FF FF  | MOV DWORD PTR SS:[EBP - 4B0], 1EC                |
00411A68         | C7 85 54 FB FF FF  | MOV DWORD PTR SS:[EBP - 4AC], CC                 |
00411A72         | C7 85 58 FB FF FF  | MOV DWORD PTR SS:[EBP - 4A8], D0                 |
00411A7C         | C7 85 5C FB FF FF  | MOV DWORD PTR SS:[EBP - 4A4], E0                 |
00411A86         | C7 85 60 FB FF FF  | MOV DWORD PTR SS:[EBP - 4A0], 184                |
00411A90         | C7 85 64 FB FF FF  | MOV DWORD PTR SS:[EBP - 49C], D0                 |
00411A9A         | C7 85 68 FB FF FF  | MOV DWORD PTR SS:[EBP - 498], D0                 |
00411AA4         | C7 85 6C FB FF FF  | MOV DWORD PTR SS:[EBP - 494], E0                 |
00411AAE         | C7 85 70 FB FF FF  | MOV DWORD PTR SS:[EBP - 490], 184                |
00411AB8         | C7 85 74 FB FF FF  | MOV DWORD PTR SS:[EBP - 48C], D4                 |
00411AC2         | C7 85 78 FB FF FF  | MOV DWORD PTR SS:[EBP - 488], C4                 |
00411ACC         | C7 85 7C FB FF FF  | MOV DWORD PTR SS:[EBP - 484], 190                |
00411AD6         | C7 85 80 FB FF FF  | MOV DWORD PTR SS:[EBP - 480], C4                 |
00411AE0         | C7 85 84 FB FF FF  | MOV DWORD PTR SS:[EBP - 47C], 194                |
00411AEA         | C7 85 88 FB FF FF  | MOV DWORD PTR SS:[EBP - 478], C0                 |
00411AF4         | C7 85 8C FB FF FF  | MOV DWORD PTR SS:[EBP - 474], 198                |
00411AFE         | C7 85 90 FB FF FF  | MOV DWORD PTR SS:[EBP - 470], C0                 |
00411B08         | C7 85 94 FB FF FF  | MOV DWORD PTR SS:[EBP - 46C], 198                |
00411B12         | C7 85 98 FB FF FF  | MOV DWORD PTR SS:[EBP - 468], D4                 |
00411B1C         | C7 85 9C FB FF FF  | MOV DWORD PTR SS:[EBP - 464], 194                |
00411B26         | C7 85 A0 FB FF FF  | MOV DWORD PTR SS:[EBP - 460], 194                |
00411B30         | C7 85 A4 FB FF FF  | MOV DWORD PTR SS:[EBP - 45C], 194                |
00411B3A         | C7 85 A8 FB FF FF  | MOV DWORD PTR SS:[EBP - 458], D0                 |
00411B44         | C7 85 AC FB FF FF  | MOV DWORD PTR SS:[EBP - 454], C8                 |
00411B4E         | C7 85 B0 FB FF FF  | MOV DWORD PTR SS:[EBP - 450], CC                 |
00411B58         | C7 85 B4 FB FF FF  | MOV DWORD PTR SS:[EBP - 44C], CC                 |
00411B62         | C7 85 B8 FB FF FF  | MOV DWORD PTR SS:[EBP - 448], DC                 |
00411B6C         | C7 85 BC FB FF FF  | MOV DWORD PTR SS:[EBP - 444], 190                |
00411B76         | C7 85 C0 FB FF FF  | MOV DWORD PTR SS:[EBP - 440], C4                 |
00411B80         | C7 85 C4 FB FF FF  | MOV DWORD PTR SS:[EBP - 43C], C8                 |
00411B8A         | C7 85 C8 FB FF FF  | MOV DWORD PTR SS:[EBP - 438], 184                |
00411B94         | C7 85 CC FB FF FF  | MOV DWORD PTR SS:[EBP - 434], 190                |
00411B9E         | C7 85 D0 FB FF FF  | MOV DWORD PTR SS:[EBP - 430], 18C                |
00411BA8         | C7 85 D4 FB FF FF  | MOV DWORD PTR SS:[EBP - 42C], 1F4                |
00411BB2         | 83 7D E8 27        | CMP DWORD PTR SS:[EBP - 18], 27                  | 27:'''
{% endhighlight %}

이후 `HCAMP{`는 하드코딩된 값을 직접 비교를 한다. 이 부분은 쉽게 찾을 수 있으므로 제외하고, 나머지 부분들을 검증해야하는데 위 코드에서 `mov`를 이용해 값을 저장하고 있다.

{% highlight bash %}
00411D3D         | 8B 85 00 FB FF FF  | MOV EAX, DWORD PTR SS:[EBP - 500]                |
00411D43         | 8B 8D 00 FB FF FF  | MOV ECX, DWORD PTR SS:[EBP - 500]                |
00411D49         | 8B 94 85 E0 FB FF  | MOV EDX, DWORD PTR SS:[EBP + EAX * 4 - 420]      |
00411D50         | 3B 94 8D 3C FB FF  | CMP EDX, DWORD PTR SS:[EBP + ECX * 4 - 4C4]      |
{% endhighlight %}

위 부분에서 입력한 값을 일정 연산을 통해 저장된 값과 비교를 한다. 그러면 입력한 값을 연산하는 부분을 찾아야 한다.

{% highlight bash %}
00411CE1         | 03 85 18 FB FF FF  | ADD EAX, DWORD PTR SS:[EBP - 4E8]                |
00411CE7         | 8A 08              | MOV CL, BYTE PTR DS:[EAX]                        |
00411CE9         | 88 8D 0F FB FF FF  | MOV BYTE PTR SS:[EBP - 4F1], CL                  |
00411CEF         | 6A 02              | PUSH 2                                           |
00411CF1         | 0F BE 85 0F FB FF  | MOVSX EAX, BYTE PTR SS:[EBP - 4F1]               |
00411CF8         | 50                 | PUSH EAX                                         |
00411CF9         | E8 EA F6 FF FF     | CALL yeeeeeeeee.4113E8                           |
{% endhighlight %}

`0x411cf9` 에서 `call 4113e8`를 호출하게 된다. 이 함수를 찾아가면 `lrotl` 함수를 실행하게된다.


{% highlight bash %}
63022712         | 55                 | PUSH EBP                                         |
63022713         | 8B EC              | MOV EBP, ESP                                     |
63022715         | 8B 45 0C           | MOV EAX, DWORD PTR SS:[EBP + C]                  |
63022718         | 83 E0 1F           | AND EAX, 1F                                      |
6302271B         | 89 45 0C           | MOV DWORD PTR SS:[EBP + C], EAX                  |
6302271E         | B9 20 00 00 00     | MOV ECX, 20                                      | 20:' '
63022723         | 2B 4D 0C           | SUB ECX, DWORD PTR SS:[EBP + C]                  |
63022726         | 8B 55 08           | MOV EDX, DWORD PTR SS:[EBP + 8]                  | [ebp+8]:L"HCAMP{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}"
63022729         | D3 EA              | SHR EDX, CL                                      |
6302272B         | 8B 45 08           | MOV EAX, DWORD PTR SS:[EBP + 8]                  | [ebp+8]:L"HCAMP{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}"
6302272E         | 8B 4D 0C           | MOV ECX, DWORD PTR SS:[EBP + C]                  |
63022731         | D3 E0              | SHL EAX, CL                                      |
63022733         | 0B D0              | OR EDX, EAX                                      |
63022735         | 89 55 08           | MOV DWORD PTR SS:[EBP + 8], EDX                  | [ebp+8]:L"HCAMP{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}"
63022738         | 8B 45 08           | MOV EAX, DWORD PTR SS:[EBP + 8]                  | [ebp+8]:L"HCAMP{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}"
6302273B         | 5D                 | POP EBP                                          |
6302273C         | C3                 | RET                                              |
{% endhighlight %}


위 영역이 `lrotl` 함수이며, 사용자가 입력한 값을 `0x1E`만큼 오른쪽으로 쉬프트 연산을 진행하고 `0x2`만큼 왼쪽으로 쉬프트 연산을 진행한다. 여기서 or 연산도 있지만 분석을 해보면 실제로 영향을 주는 부분은 `0x2` 만큼 왼쪽으로 쉬프트 연산만 하는 것과 다를바없다.

따라서 저장된 값이 0x2 만큼 왼쪽으로 쉬프트 연산이 된 값이므로 다시 오른쪽으로 0x2 만큼 쉬프트 연산을 하면 flag를 얻을 수 있다.

{% highlight python %}
flag2 = 0x120, 0x10c, 0x104, 0x134, 0x140, 0x1ec, 0xcc, 0xd0, 0xe0, 0x184, 0xd0, 0xd0, 0xe0, 0x184, 0xd4, 0xc4, 0x190, 0xc4, 0x194, 0xc0, 0x198, 0xc0, 0x198, 0xd4, 0x194, 0x194, 0x194, 0xd0, 0xc8, 0xcc, 0xcc, 0xdc, 0x190, 0xc4, 0xc8, 0x184, 0x190, 0x18c, 0x1f4

flag = ""

for f in flag2:
    flag += chr(f >> 0x2)

print flag
{% endhighlight %}


`flag: HCAMP{348a448a51d1e0f0f5eee42337d12adc}`