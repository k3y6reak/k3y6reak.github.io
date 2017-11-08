---
layout: post
title:  "PowerShell 기초"
subtitle: "PowerShell의 기초내용에 대해서 알아보자."
date:   2017-08-03 13:20:00 +0900
categories: ['technique']
tags: powershell
comments: true
---

<br/><br/>
### 개념

Linux에서 터미널을 이용하여 ls, pwd 등과 같은 명령어를 사용하는 것 처럼 Windows에서도 명령어들을 이용하여 사용할 수 있도록 하는 것이 PowerShell 입니다.

물론 Windows에서는 cmd라고 하는 것도 있지만 이보다 더 많은 명령어들이 포함되어 있습니다. 

![powershell_and_cmd](/img/powershell/basic/ps_cmd.png)

<br/><br/>
### 설명
{% highlight PowerShell %}
PS C:\Users\k3y6reak> ls


    디렉터리: C:\Users\k3y6reak


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---   2017. 7. 31.   오후 8:13                Contacts
d-r---    2017. 8. 2.   오후 2:33                Desktop
d-r---    2017. 8. 2.   오후 2:09                Documents
d-r---    2017. 8. 2.   오후 2:33                Downloads
d-r---   2017. 7. 31.   오후 8:13                Favorites
d-r---   2017. 7. 31.   오후 8:13                Links
d-r---   2017. 7. 31.   오후 8:13                Music
d-r---   2017. 7. 31.   오후 5:35                OneDrive
d-r---   2017. 7. 31.   오후 8:13                Pictures
d-r---   2017. 7. 31.   오후 8:13                Saved Games
d-r---   2017. 7. 31.   오후 8:13                Searches
d-r---   2017. 7. 31.   오후 8:13                Videos


PS C:\Users\k3y6reak> dir


    디렉터리: C:\Users\k3y6reak


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---   2017. 7. 31.   오후 8:13                Contacts
d-r---    2017. 8. 2.   오후 2:33                Desktop
d-r---    2017. 8. 2.   오후 2:09                Documents
d-r---    2017. 8. 2.   오후 2:33                Downloads
d-r---   2017. 7. 31.   오후 8:13                Favorites
d-r---   2017. 7. 31.   오후 8:13                Links
d-r---   2017. 7. 31.   오후 8:13                Music
d-r---   2017. 7. 31.   오후 5:35                OneDrive
d-r---   2017. 7. 31.   오후 8:13                Pictures
d-r---   2017. 7. 31.   오후 8:13                Saved Games
d-r---   2017. 7. 31.   오후 8:13                Searches
d-r---   2017. 7. 31.   오후 8:13                Videos


PS C:\Users\k3y6reak>
{% endhighlight %}

PowerShell에서는 PowerShell에서만 사용할 수 있는 명령어도 있지만 기존에 사용하던 Windows 명령어도 사용이 가능하고, Linux에서 사용하던 명령어도 사용할 수 있습니다.

Linux에서 명령어를 사용할 때 alias라는 명령어가 있는데 이는 명령어의 별칭을 붙여서 사용할 수 있습니다. 즉, a라고 입력을 하면 ls가 실행이 되도록 하는 것이죠.

PowerShell에서도 ls, dir이 모두 같은 결과값을 출력하는데 alias를 통해서 같은 명령어를 실행할 것입니다.

alias에 대한 명령어가 어떤것이 있는지 확인하기 위해서 `Get-Help *alias`를 입력합니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> Get-Help *alias

Name                              Category  Module                    Synopsis
----                              --------  ------                    --------
Export-Alias                      Cmdlet    Microsoft.PowerShell.U... ...
Get-Alias                         Cmdlet    Microsoft.PowerShell.U... ...
Import-Alias                      Cmdlet    Microsoft.PowerShell.U... ...
New-Alias                         Cmdlet    Microsoft.PowerShell.U... ...
Set-Alias                         Cmdlet    Microsoft.PowerShell.U... ...
{% endhighlight %}

위 결과값에서 Get-Alias라는 명령어를 볼 수 있는데 모든 명령어를 확인 하기위해서 `Get-Alias *`를 입력합니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> Get-Alias *

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
...
Alias           dir -> Get-ChildItem
...
Alias           ls -> Get-ChildItem
...
{% endhighlight %}

`ls` 명령어와 `dir` 명령어는 PowerShell에서 Get-ChildItem 명령어가 실행되도록 되어있는 것을 알 수 있습니다.

PowerShell에서는 명령어에 대한 예시들을 `Get-Help`를 통해서 알 수 있습니다. 사용하는 OS의 환경에 따라, Get-Help 명령어가 안 될 수도 있습니다.

{% highlight PowerShell %}
PS C:\Users\k3y6reak> Get-Help Get-Alias

이름
    Get-Alias

구문
    Get-Alias [[-Name] <string[]>]  [<CommonParameters>]

    Get-Alias  [<CommonParameters>]


별칭
    gal


설명
    Get-Help가 이 컴퓨터에서 이 cmdlet에 대한 도움말 파일을 찾을 수 없습니다. 일부 도움말만 표시합니다.
        -- 이 cmdlet을 포함하는 모듈에 대한 도움말 파일을 다운로드하여 설치하려면 Update-Help를 사용하십시오.
        -- 이 cmdlet에 대한 도움말 항목을 온라인으로 보려면 "Get-Help Get-Alias -Online"을 입력하거나
           http://go.microsoft.com/fwlink/?LinkID=113306(으)로 이동하십시오.
{% endhighlight %}

위 경우 `설명` 부분을 보면 일부 도움말만 표시한다고 되어있는데 Update-Help를 통해서 도움말을 다운로드 합니다. 여기서 중요한 것은 반드시 `관리자 권한`으로 실행해야 합니다.

![powershell_and_cmd](/img/powershell/basic/update_help.png)

설치가 완료되면 `Get-Help Get-Alias -examples`를 입력해 봅니다.


{% highlight PowerShell %}
PS C:\Users\k3y6reak> Get-Help Get-Alias -examples

이름
    Get-Alias

개요
    Gets the aliases for the current session.


    Example 1: Get all aliases in the current session

    PS C:\>Get-Alias
    CommandType     Name
    -----------     ----
    Alias           % -> ForEach-Object
    Alias           ? -> Where-Object
    Alias           ac -> Add-Content
    Alias           asnp -> Add-PSSnapin
    Alias           cat -> Get-Content
    Alias           cd -> Set-Location
    Alias           chdir -> Set-Location
    Alias           clc -> Clear-Content
    Alias           clear -> Clear-Host
    Alias           clhy -> Clear-History â€¦

    This command gets all aliases in the current session.

    The output shows the <alias> -> <definition> format that was introduced in Windows PowerShell 3.0. This format is u
    sed only for aliases that do not include hyphens, because aliases with hyphens are typically preferred names for cm
    dlets and functions, rather than nicknames.
    Example 2: Get aliases by name

    PS C:\>Get-Alias -Name g*, s* -Exclude Get-*

    This command gets all aliases that begin with g or s, except for aliases that begin with Get-.
    Example 3: Get aliases for a cmdlet

    PS C:\>Get-Alias -Definition Get-ChildItem

    This command gets the aliases for the Get-ChildItem cmdlet.

    By default, the Get-Alias cmdlet gets the item name when you know the alias. The Definition parameter gets the alia
    s when you know the item name.
    Example 4: Get aliases by property

    PS C:\>Get-Alias | Where-Object {$_.Options -Match "ReadOnly"}

    This command gets all aliases in which the value of the Options property is ReadOnly. This command provides a quick
     way to find the aliases that are built into Windows PowerShell, because they have the ReadOnly option.

    Options is just one property of the AliasInfo objects that Get-Alias gets. To find all properties and methods of Al
    iasInfo objects, type `Get-Alias | get-member`.
    Example 5: Get aliases by name and filter by beginning letter

    PS C:\>Get-Alias -Definition "*-PSSession" -Exclude e* -Scope Global

    This example gets aliases for commands that have names that end in -PSSession, except for those that begin with e.

    The command uses the Scope parameter to apply the command in the global scope. This is useful in scripts when you w
    ant to get the aliases in the session.
{% endhighlight %}

위와 같이 해당 명령어에 대한 예제들도 출력하는 것을 볼 수 있습니다.
