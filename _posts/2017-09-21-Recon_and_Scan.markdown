---
layout: post
title:  "Recon and Scan"
subtitle: "PowerShell을 이용해 모의해킹하는 방법에 대해서 알아보겠습니다."
date:   2017-09-21 21:00:00 +0900
categories: ['technique']
tags: powershell
comments: true
---



PowerShell을 이용해 모의해킹을 진행하는 방법에 대해서 알아보겠습니다.

임의의 한 사이트에 대해 점검하고자 할 때 해당 서버가 정상적으로 작동하는지, 어떤 포트가 열려있는지에 대해서 알아보겠습니다. `허락받지 않은 곳에서 테스트를 할 경우에는 본인에게 책임이 있습니다.`

점검하기 위해서 여러가지 PowerShell Script를 사용할 수 있습니다.


#### Nishang
[여기](http://github.com/samratashok/nishang)에서 PowerShell Script를 받을 수 있습니다.

해당 스크립트를 다운받고 `Import-Module`명령어를 이용해 nishang.psm1 을 불러옵니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak\Desktop\PowerShell> ls

    디렉터리: C:\Users\k3y6reak\Desktop\PowerShell

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----   2017. 9. 21.   오후 5:57                nishang-master
d-----   2017. 9. 21.   오후 5:57                Posh-SecMod-master
d-----   2017. 9. 21.   오후 5:57                PowerSploit-master
d-----   2017. 9. 21.   오후 5:57                PowerTools-master
-a----   2017. 9. 20.   오후 8:46        1762079 nishang-master.zip
-a----   2017. 9. 20.   오후 8:49        1542881 Posh-SecMod-master.zip
-a----   2017. 9. 20.   오후 8:47        1898185 PowerSploit-master.zip
-a----   2017. 9. 20.   오후 8:48       10422636 PowerTools-master.zip

PS C:\Users\k3y6reak\Desktop\PowerShell> Import-Module .\nishang-master\nishang.psm1
경고: 'nishang' 모듈에서 가져온 일부 명령 이름에 검색 가능성을 낮출 수 있는 승인되지 않은 동사가 포함되어 있습니다.
승인되지 않은 동사가 포함된 명령을 찾으려면 Verbose 매개 변수와 함께 Import-Module 명령을 다시 실행하십시오. 승인된
동사 목록을 보려면 Get-Verb를 입력하십시오.
경고: 가져온 일부 명령 이름에 다음과 같은 제한 문자가 하나 이상 들어 있습니다. # , ( ) [ ] & - / \ $ ^ ; : " ' < | ? @ ` * % + = ~
{% endhighlight %}

위에서 본 결과처럼 nishang.psm1이 제대로 실행되지 않은 것을 볼 수 있습니다.

이러한 경우 사용하고자하는 스크립트를 `Dot Sourcing` 방법으로 사용할 수 있습니다.
Dot Sourcing에 대한 설명은 [여기](https://k3y6reak.github.io/technique/powershell/2017/08/08/Functios.html)를 참고해 주세요.


{% highlight PowerShell %}
PS C:\Users\k3y6reak\Desktop\PowerShell> . .\nishang-master\Scan\Invoke-PortScan.ps1
PS C:\Users\k3y6reak\Desktop\PowerShell> Invoke-PortScan -StartAddress XXX.XXX.XXX.XXX -EndAddress XXX.XXX.XXX.XXX -ResolveHost

IPAddress       HostName            Ports
---------       --------            -----
XXX.XXX.XXX.XXX abcde.abcedf.ab.ab

{% endhighlight %}

`StartAddress`에 시작할 IP주소를 넣어주고 `EndAddress`에 끝날 IP주소를 넣어주면 됩니다. 그러면 시작 IP부터 끝 IP까지 진행하면서 서버가 작동하는 IP 주소를 출력해 줍니다.

또한 열려있는 포트를 찾고자 한다면 -ResolveHost 옵션 대신 `ScanPort`옵션을 사용하면 됩니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak\Desktop\PowerShell> Invoke-PortScan -StartAddress XXX.XXX.XXX.XXX -EndAddress XXX.XXX.XXX.XXX -ScanPort

IPAddress       HostName Ports
---------       -------- -----
XXX.XXX.XXX.XXX          {80, 443}
{% endhighlight %}


#### PowerSploit
이번에는 다른 스크립트를 사용해 보겠습니다. [여기](https://github.com/mattifestation/PowerSploit)에서 PowerShell Script를 받을 수 있습니다. 

DotSourcing 방식으로 `Get-HttpStatus` 스크립트를 가져옵니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak\Desktop\PowerShell> . .\PowerSploit-master\Recon\Get-HttpStatus.ps1
PS C:\Users\k3y6reak\Desktop\PowerShell> Get-HttpStatus -Target XXX.XXX.XXX.XXX -Path .\PowerSploit-master\Recon\Dictionaries\generic.txt -Port 80 | Where-Object {$_.Status -match "ok"}
{% endhighlight %}

테스트 하고자 하는 서버에서 일치하는 결과값이 없기 때문에 출력이 안되지만 결과가 존재한다면 취약점으로 작용할 수 있습니다.


이 외에도 PowerTools, Posh-SecMod 등 여러가지 스크립트들이 있습니다.
