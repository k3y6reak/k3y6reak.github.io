---
layout: post
title:  "Hackingcamp 2018 I LOVE Registry"
subtitle: "Hackingcamp 2018 I LOVE Registry 풀이 입니다."
date:   2018-09-07 16:46:00 +0900
categories: ['ctfs']
comments: true
---

## 풀이

바이너리를 다운받아 파일을 확인하면 PE32 EXE 임을 확인할 수 있다.

{% highlight bash %}
file LOVE_Registry.exe
LOVE_Registry.exe: PE32 executable (console) Intel 80386, for MS Windows
{% endhighlight %}

IDA를 이용해 디컴파일을 하면 아래와 같은 코드를 확인할 수 있다.

{% highlight c %}
strcpy(&v15, "C:\\Temp\\WELCOMEHACKINGCAMP2018");
  sub_401E40(&v6);
  sub_4019D0(&v3);
  if ( a1 == 2 )
  {
    v4 = sub_401B50();
    if ( access("C:\\Temp", 0) == -1 )
      mkdir("C:\\Temp");
    v7 = mkdir(&v15);
    if ( v7 == -1 )
      sub_4010B0("fail!\n");
    else
      sub_4010B0("Success!\n");
    for ( i = 0; i < 5; ++i )
      v14 = sub_4012E0(&v6, byte_405030[i]);
    v9 = &byte_405028;
    v10 = *(char **)(a2 + 4);
    do
    {
      v13 = *v10;
      v2 = v13 < (unsigned __int8)*v9;
      if ( v13 != *v9 )
        goto LABEL_25;
      if ( !v13 )
        break;
      v12 = v10[1];
      v2 = v12 < (unsigned __int8)v9[1];
      if ( v12 != v9[1] )
      {
LABEL_25:
        v8 = -v2 | 1;
        goto LABEL_18;
      }
      v10 += 2;
      v9 += 2;
    }
    while ( v12 );
    v8 = 0;
LABEL_18:
    v5 = v8;
    if ( v8 )
    {
      MessageBoxA(0, &byte_405038, "error", 0);
    }
    else
    {
      sub_4010B0("Welcome %s\n");
      sub_4019F0(&v3);
    }
  }
  else
  {
    ms_exc.registration.TryLevel = 0;
    sub_401AE0();
    ms_exc.registration.TryLevel = -2;
  }
  remove("C:\\Temp\\WELCOMEHACKINGCAMP2018");
  exit(0);
}
{% endhighlight %}

코드를 살펴보면 `C:\\Temp\\WELCOMEHACKINGCAMP2018`에 디렉토리를 생성한다.
또한 argc가 2가 되어야 하므로 매개변수를 받고 있다.

디렉토리를 생성하는 부분은 별다른 영향이 없으므로 넘어가고 아래 코드 부분을 살펴보자.

{% highlight c %}
for ( i = 0; i < 5; ++i )
      v14 = sub_4012E0(&v6, byte_405030[i]);
{% endhighlight %}

`sub_4012E0`함수가 v6과 byte_405030를 매개변수로 받고 있다.

{% highlight c %}
char __stdcall sub_4012E0(int a1, char a2)
{
  byte_405028[dword_405444] = (dword_405444 + 5) ^ a2;
  if ( dword_405444 >= 4 )
    return 1;
  ++dword_405444;
  return -1;
}
{% endhighlight %}

sub_4012E0에서 dword_405444 값이 1씩 증가하면서 a2와 xor 연산을한다.

a2의 값은 byte_405030 값이므로 이 값을 확인해보면 아래와 같다.

{% highlight bash %}
.data:00405030 byte_405030     db 'v'                  ; DATA XREF: sub_401C00+148↑r
.data:00405031 aRfz            db 'rfz}',0
{% endhighlight %}

즉, 1씩 증가하면서 5를 더하고 `vrfz}`를 차례로 xor 연산을 한다.

이 값을 연산하면 `start`라는 문자열이 나타나며 실행하면 아래와 같이 실행된다.

{% highlight bash %}
C:\Users\k3y6reak\Desktop\LOVERegistry>LOVE_Registry.exe start
fail!
Welcome k3y6reak
Registry World
        [0] Search
        [1] Enroll
        [2] Edit
        [3] Delete
        [5] Exit
{% endhighlight %}

함수를 따라 들어가다보면 아래와 같이 switch-case문을 볼 수 있다.

{% highlight c %}
int __thiscall sub_401360(void *this, int a2)
{
  int result; // eax
  void *v3; // [esp+4h] [ebp-4h]

  v3 = this;
  result = a2;
  switch ( a2 )
  {
    case 0:
      sub_4010B0("Let's Search\n");
      result = sub_4013F0(v3);
      break;
    case 1:
      result = sub_401420(this);
      break;
    case 2:
      result = sub_401770(this);
      break;
    case 3:
      result = sub_401790(this);
      break;
    case 5:
      exit(-1);
      return result;
    case 7:
      result = sub_4017B0(this);
      break;
    default:
      return result;
  }
  return result;
}
{% endhighlight %}


여기서 `sub_401420`함수로 들어가면 `HACKINGCAMP_Service.dll`를 호출하는 부분을 찾을 수 있다.

{% highlight c %}
v15 = 0;
  v16 = 0;
  strcpy(
    v11,
    "/x48/x65/x6c/x6c/x6f/x20/x49/x20/x61/x6d/x20/x73/x6f/x72/x72/x79/x20/x54/x68/x69/x73/x20/x44/x75/x6d/x70/x20/x69/x73"
    "/x20/x46/x41/x4b/x45/x20/x4d/x65/x72/x6f/x6e/x67/x7e/x4d/x65/x72/x6f/x6e/x67/x7e/x4d/x65/x72/x6f/x6e/x67/x7e/x4d/x65"
    "/x72/x6f/x6e/x67/x7e/x4d/x65/x72/x6f/x6e/x67/x7e/x4d/x65/x72/x6f/x6e/x67/x7e/x4d/x65/x72/x6f/x6e/x67/x7e/x4d/x65/x72"
    "/x6f/x6e/x67/x7e/x4d/x65/x72/x6f/x6e/x67/x7e/x4d/x65/x72/x6f/x6e/x67/x7e");
  memset(&Dst, 0, 0x4Fu);
  result = fopen("HACKINGCAMP_Service.dll", "rb");
  File = result;
  if ( !result )
    result = (FILE *)sub_4010B0("not file\n");
  if ( File )
  {
    fseek(File, 1200, 2);
    ftell(File);
    sub_4010B0("start offset = 0x%x\n");
    sub_4010B0("size = 0x%x\n");
    Memory = malloc(0x200u);
    memset(Memory, 0, 0x200u);
    fseek(File, 1200, 2);
    GetTempPathW(0x104u, &Buffer);
    v1 = lstrlenW(&Buffer);
    v2 = lstrlenW(L"dump.exe") + v1;
    v9 = L"dump.exe";
    do
    {
      v3 = *v9;
      ++v9;
    }
    while ( v3 );
    v8 = &v13;
    do
    {
      v4 = v8[1];
      ++v8;
    }
    while ( v4 );
    qmemcpy(v8, L"dump.exe", (char *)v9 - (char *)L"dump.exe");
    sub_4010B0("user = %S\n");
    v6 = wfopen(&Buffer, "w");
    if ( v6 )
      puts("Created!\n");
    else
      puts("NOT Created\n");
    for ( i = 0; i < 0x1F4; ++i )
      sub_401070(v6, "%c", v11[i]);
    fclose(File);
    fclose(v6);
    free(Memory);
    result = (FILE *)memset(Memory, 0, 0x83FFu);
  }
  return result;
}
{% endhighlight %}

`HACKINGCAMP_Service.dll` 파일을 열어 `fseek` 함수를 호출하게 된다. 친절하게 `start offset`과 `size` 그리고 `path`도 출력해 준다.


{% highlight bash %}
C:\Users\k3y6reak\Desktop\LOVERegistry>LOVE_Registry.exe start
fail!
Welcome k3y6reak
Registry World
        [0] Search
        [1] Enroll
        [2] Edit
        [3] Delete
        [5] Exit
1
start offset = 0x4b0
size = 0x83ff
user = C:\Users\k3y6reak\AppData\Local\Temp\dump.exe
Created!
{% endhighlight %}

dump.exe를 살펴보면 실제 exe 파일이 아니며, 단순 hex 값이 존재한다.

그렇다면 확인해야하는 부분은 `HACKINGCAMP_Service.dll`에서 `offset`과 `size`를 알려주기 때문에 해당 부분만 가져와 파일을 생성한다.

{% highlight bash %}
00008350  30 20 30 30 20 30 30 20  30 30 20 30 30 20 30 30  |0 00 00 00 00 00|
00008360  20 30 30 20 30 30 20 30  30 20 30 30 20 30 30 20  | 00 00 00 00 00 |
00008370  30 30 20 30 30 20 30 30  20 30 30 20 30 30 20 30  |00 00 00 00 00 0|
00008380  30 20 30 30 20 30 30 20  30 30 20 30 30 20 30 30  |0 00 00 00 00 00|
00008390  20 30 30 20 30 30 20 30  30 20 30 30 20 30 30 20  | 00 00 00 00 00 |
000083a0  30 30 20 30 30 20 30 30  20 30 30 20 30 30 20 30  |00 00 00 00 00 0|
000083b0  30 20 30 30 20 34 30 20  30 30 20 30 30 20 30 30  |0 00 40 00 00 00|
000083c0  20 30 30 20 30 30 20 30  30 20 30 30 20 42 38 20  | 00 00 00 00 B8 |
000083d0  30 30 20 30 30 20 46 46  20 46 46 20 30 30 20 30  |00 00 FF FF 00 0|
000083e0  30 20 30 30 20 30 34 20  30 30 20 30 30 20 30 30  |0 00 04 00 00 00|
000083f0  20 30 33 20 30 30 20 39  30 20 35 41 20 34 44     | 03 00 90 5A 4D|
{% endhighlight %}

마지막 부분을 살펴보면 `5A 4D`라는 것을 볼 수 있다. exe 파일에서 시그니처가 `4D 5A`라는 것을 알고 있다면 이 부분이 거꾸로 되어 있다는 것을 알 수 있다.

이 부분을 거꾸로 파일을 생성하면 아래와 같은 파일을 얻을 수 있다.

{% highlight bash %}
file rev_dump
rev_dump3: PE32+ executable (console) x86-64, for MS Windows
{% endhighlight %}

PE32+ EXE라는 것을 알 수 있으며 실행해보면 아래와 같은 문자열이 출력된다.

{% highlight bash %}
C:\Users\k3y6reak\Desktop\LOVERegistry>rev_dump.exe
HINT : DATA file
{% endhighlight %}

처음 파일을 다운로드 받으면 DATA 파일이 존재하는데 이 파일과 관련이 있다는 것을 알 수 있다.

rev_dump.exe를 IDA를 이용해 디컴파일하면 아래와 같다.


{% highlight c %}
__int64 __fastcall sub_140001070(__int64 a1)
{
  __int64 v1; // rdi
  int v2; // eax
  unsigned int v3; // ebx

  v1 = a1;
  v2 = rand();
  v3 = v2 % 10 + 10;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 1i64) ^= (unsigned __int8)(v2 % 10) + 10;
  sub_140001010("time = %d\n", v3);
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 3i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 5i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 7i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 9i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 11i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 13i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 15i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 17i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 19i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 21i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 23i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 25i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 27i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 29i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 31i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 33i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 35i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 37i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 39i64) ^= v3;
  *(_BYTE *)(*(_QWORD *)(v1 + 8) + 41i64) ^= v3;
  return v1;
}
{% endhighlight %}

rev_dump.exe를 매개변수와 함께 실행하면 time은 14로 항상 고정이며 이 값과 홀수번째 인덱스와 xor연산을 하게된다.

{% highlight python %}
flag = "HMACPuR=S?szrw_^0}sgbbeQEcbkdjej_JazaQXJ}"
t = 14
flag2 = ""

i = 0
for f in flag:
    if i % 2 == 1:
        flag2 += chr(ord(f) ^ t)
    else:
        flag2 += chr(ord(f))
    i+=1

print flag2
{% endhighlight %}

위와 같이 python 코드를 작성해 실행하면 flag를 얻을 수 있다.

`flag: HCAMP{R3S1stry_P0ssible_Embedded_Data_XD}`