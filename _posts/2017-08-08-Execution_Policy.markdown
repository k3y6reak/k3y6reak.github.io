---
layout: post
title:  "Execution Policy"
subtitle: "PowerShell의 실행 정책에 대해서 알아보겠습니다."
date:   2017-08-08 17:59:00 +0900
categories: ['technique']
tags: powershell
comments: true
---



PowerShell에서 Script를 바로 실행할 수 없습니다.

notepad를 이용해서 "HelloWorld!"를 작성한 뒤 `HelloWorld.ps1` 파일을 생성합니다. 


{% highlight PowerShell %}
PS C:\Users\k3y6reak\Desktop> .\HelloWorld.ps1
.\HelloWorld.ps1 : 이 시스템에서 스크립트를 실행할 수 없으므로 C:\Users\k3y6reak\Desktop\HelloWorld.ps1 파일을 로드할 수 없습니다. 자세한 내용은 http://go.microsoft.com/fwlink/?Li
nkID=135170의 about_Execution_Policies를 참조하십시오.
위치 줄:1 문자:1
+ .\HelloWorld.ps1
+ ~~~~~~~~~~~~~~~~
    + CategoryInfo          : 보안 오류: (:) [], PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess

{% endhighlight %}

위 에러를 살펴보면 HelloWorld.ps1 파일을 로드할 수 없고 `about_Execution_Policies`를 참조하라고 나옵니다.

먼저 현재 권한이 어떻게 되는지 살펴보겠습니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak\Desktop> Get-ExecutionPolicy
Restricted
{% endhighlight %}

현재 권한은 `Restricted` 이며 `제한된`이라는 뜻을 갖고 있습니다. 말 그대로 제한된 권한을 사용한다는 것입니다. 아무래도 아무 스크립트나 실행할 수 없도록 한 것 같습니다.

여기서 `Get-Help`를 이용해 about_Execution_Policies에 대해서 찾아보겠습니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak\Desktop> Get-Help about_Execution_Policies
TOPIC
    about_Execution_Policies

SHORT DESCRIPTION
    Describes the Windows PowerShell execution policies and explains
    how to manage them.
    ...(중략)

WINDOWS POWERSHELL EXECUTION POLICIES
-------------------------------------

   The Windows PowerShell execution policies are as follows:

   "Restricted" is the default policy.

       Restricted
           - Default execution policy in Windows 8,
             Windows Server 2012, and Windows 8.1.

           - Permits individual commands, but will not run
             scripts.

           - Prevents running of all script files, including
             formatting and configuration files (.ps1xml), module
             script files (.psm1), and Windows PowerShell
             profiles (.ps1).

             ...(중략)

       Bypass
           - Nothing is blocked and there are no warnings or
             prompts.

           - This execution policy is designed for configurations
             in which a Windows PowerShell script is built in to a
             a larger application or for configurations in which
             Windows PowerShell is the foundation for a program
             that has its own security model.


       Undefined
           - There is no execution policy set in the current scope.

           - If the execution policy in all scopes is Undefined, the
             effective execution policy is Restricted, which is the
             default execution policy.


   Note: On systems that do not distinguish Universal Naming Convention (UNC)
         paths from Internet paths, scripts that are identified by a UNC path
         might not be permitted to run with the RemoteSigned execution policy.
         ...(생략)

{% endhighlight %}



위 도움말을 보면 `Restricted` 권한은 Default로 되어있고 이를 해지하려면 `bypass`권한을 이용해야 합니다.

권한을 변경하기 위해서 `관리자 권한`으로 PowerShell를 실행시킨 후 `Set-ExecutionPolicy Bypass`를 입력해 줍니다.

{% highlight PowerShell %}
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\WINDOWS\system32> Set-ExecutionPolicy Bypass

실행 규칙 변경
실행 정책은 신뢰하지 않는 스크립트로부터 사용자를 보호합니다. 실행 정책을 변경하면 about_Execution_Policies 도움말
항목(http://go.microsoft.com/fwlink/?LinkID=135170)에 설명된 보안 위험에 노출될 수 있습니다. 실행 정책을
변경하시겠습니까?
[Y] 예(Y)  [A] 모두 예(A)  [N] 아니요(N)  [L] 모두 아니요(L)  [S] 일시 중단(S)  [?] 도움말 (기본값은 "N"): Y
{% endhighlight %}

그 후 다시 `Get-ExecutionPolicy`를 실행하면 `Bypass`로 권한이 변경된 것을 알 수 있습니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak\Desktop> Get-ExecutionPolicy
Bypass
{% endhighlight %}

다시 HelloWorld.ps1를 실행하면 정상적으로 실행되는 것을 알 수 있습니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak\Desktop> .\HelloWorld.ps1
Hello World
{% endhighlight %}