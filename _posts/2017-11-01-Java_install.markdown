---
layout: post
title:  "Install Java"
subtitle: "Ubuntu에서 Java를 설치하는 방법에 대해서 알아보겠습니다."
date:   2017-11-01 19:43:00 +0900
categories: ['misc']
comments: true
---

<br/><br/>
### Ubuntu에서 Java 설치하기

자바를 설치하는 여러 블로그를 살펴보면 각각 다른 방법들로 설치를 많이 합니다.
여러 방법들을 시도해보고 적성합니다.

[Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html)에 접속하면 최신버전의 Java SE를 받을 수 있습니다.

하지만 개발된 자바 프로그램들이 버전에 맞지 않아 실행이 안되는 경우도 발생합니다. Android를 분석하는데 Jadx 툴을 사용하려면 `Java SE 7` 버전을 사용해야 합니다.

위 사이트에 접속 후 맨 밑의 `Java Archive` 항목의 Download를 눌러 해당 OS에 맞는 파일을 다운로드 합니다.

Ubuntu에서 설치하기 때문에 `Linux x64 (jdk-7u80-linux-x64.tar.gz)`를 다운받습니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# mv jdk-7u80-linux-x64.tar.gz /var/cache/oracle-jdk7-installer/
root@ubuntu:/home/k3y6reak/Desktop# apt-get install python-software-properties
root@ubuntu:/home/k3y6reak/Desktop# add-apt-repository ppa:webupd8team/java
root@ubuntu:/home/k3y6reak/Desktop# apt-get update
{% endhighlight Shell%}

여기서 JDK 버전에 따라서 `java`뒤에 숫자를 다르게 합니다. 7버전을 받았으므로 java7이 됩니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# apt-get install oracle-java7-installer
{% endhighlight Shell%}

그 다음으로 Java를 사용할 수 있도록 환경변수를 설정해 주어야 합니다.

`vim /etc/bash.bashrc/` 를 이용하여 bash.bashrc의 하단에 `export JAVA_HOME=/usr/lib/jvm/java-7-oracle`를 작성합니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# vim /etc/bash.bashrc

# if the command-not-found package is installed, use it                                                       
 54 if [ -x /usr/lib/command-not-found -o -x /usr/share/command-not-found/command-not-found ]; then               
 55     function command_not_found_handle {                                                                        56     ┆   ┆   # check because c-n-f could've been removed in the meantime                                       
 57     ┆   ┆   ┆   if [ -x /usr/lib/command-not-found ]; then                                                    
 58         ┆  /usr/lib/command-not-found -- "$1"                                                                 
 59     ┆   ┆   ┆   ┆  return $?                                                                                  
 60     ┆   ┆   ┆   elif [ -x /usr/share/command-not-found/command-not-found ]; then                              
 61         ┆  /usr/share/command-not-found/command-not-found -- "$1"                                             
 62     ┆   ┆   ┆   ┆  return $?                                                                                   63         else                                                                                                  
 64         ┆  printf "%s: command not found\n" "$1" >&2                                                          
 65         ┆  return 127                                                                                         
 66         fi                                                                                                    
 67     }                                                                                                         
 68 fi                                                                                                            
 69                                                                                                               
 70 export JAVA_HOME=/usr/lib/jvm/java-7-oracle/  
{% endhighlight Shell %}

그리고 `source /etc/bash.bashrc`를 입력해 줍니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# source /etc/bash.bashrc
{% endhighlight Shell %}

정상적으로 Java가 설치가 되었는지 확인하기 위해서 `java -version`을 입력합니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
{% endhighlight Shell %}

정상적으로 설치가 완료 됐습니다.