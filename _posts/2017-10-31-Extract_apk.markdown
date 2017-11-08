---
layout: post
title:  "Extract APK"
subtitle: "apk를 추출하는 방법에 대해서 알아보겠습니다."
date:   2017-10-31 19:55:00 +0900
categories: ['technique']
tags: android
comments: true
---


### APK 추출하기

APK를 분석하기 위해서는 APK 파일의 구조와 어떤 방식으로 동작하는지에 대해서 알아야 합니다. 먼저 APK 내에는 어떤 파일들이 존재하는지 알아보도록 합시다.



#### Unzip

먼저 Chrome.apk를 받아 `Hex Editor`를 이용해 열어봅니다.

{% highlight hex %}
Offset(d)00 01 02 03 04 05 06 07 08 09 10 11 12 13 14 15
00000000 50 4B 03 04 0A 00 00 08 00 00 00 00 21 3A 64 68 PK..........!:dh
{% endhighlight hex %}

Hex 값을 살펴본 결과 `PK`로 시작하는 것을 알 수 있습니다. PK로 시작하면 압축해제를 통해서 APK 안의 내용을 추출할 수 있습니다.

`unzip [apk 이름] -d [디렉토리 이름]`을 이용해 해제할 디렉토리에 chrome.apk를 해제합니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# unzip chrome.apk -d Unzip_chrome/
Archive:  chrome.apk
 extracting: Unzip_chrome/assets/chrome_100_percent.pak  
 extracting: Unzip_chrome/assets/icudtl.dat  
 extracting: Unzip_chrome/assets/natives_blob.bin  
 extracting: Unzip_chrome/assets/resources.pak  
 extracting: Unzip_chrome/assets/snapshot_blob_32.bin  
 extracting: Unzip_chrome/assets/webapk_dex_version.txt  
 extracting: Unzip_chrome/lib/armeabi-v7a/crazy.libchrome.so  
 extracting: Unzip_chrome/lib/armeabi-v7a/libchrome.1847.114.so  
 extracting: Unzip_chrome/lib/armeabi-v7a/libchrome.1916.122.so  
 extracting: Unzip_chrome/lib/armeabi-v7a/libchrome.1916.138.so  
(중략...)
  inflating: Unzip_chrome/res/xml/usb_device_preferences.xml  
  inflating: Unzip_chrome/res/xml/website_preferences.xml  
  inflating: Unzip_chrome/META-INF/CERT.SF  
  inflating: Unzip_chrome/META-INF/CERT.RSA  
  inflating: Unzip_chrome/META-INF/MANIFEST.MF  
root@ubuntu:/home/k3y6reak/Desktop# 
{% endhighlight %}

해당 디렉토리에 접근하여 어떤 파일이 존재하는지 살펴보면 아래와 같습니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop/Unzip_chrome# ls -al
total 10368
drwxr-xr-x  6 root     root        4096 Oct 31 20:05 .
drwxr-xr-x  4 k3y6reak k3y6reak    4096 Oct 31 20:05 ..
-rw-r--r--  1 root     root       95976 Jan  1  2009 AndroidManifest.xml
drwxr-xr-x  2 root     root        4096 Oct 31 20:05 assets
-rw-r--r--  1 root     root     6282576 Jan  1  2009 classes.dex
drwxr-xr-x  3 root     root        4096 Oct 31 20:05 lib
drwxr-xr-x  2 root     root        4096 Oct 31 20:05 META-INF
drwxr-xr-x 70 root     root        4096 Oct 31 20:05 res
-rw-r--r--  1 root     root     4206860 Jan  1  2009 resources.arsc
{% endhighlight %}


#### Apktool

이번에는 `apktool`을 이용해서 추출해 보도록 하겠습니다. apktool은 자바가 필요하며 사전에 설치가 돼있어야 합니다.
[자바 설치](https://k3y6reak.github.io/misc/2017/11/01/Java_install.html)

먼저 apktool을 설치해 보겠습니다. [여기](https://ibotpeaches.github.io/Apktool/install)에 접속하면 각 운영체제별 설치방법이 나와있습니다. Ubuntu를 기준으로 Linux 설치방법을 따르면 됩니다.

여기서 주의할 점은 Ubuntu 내에서 apt-get 을 이용해 미리 설치하신 분은 낮은 버전으로 실행되기 때문에 `apt-get purge apktool`을 한 뒤 설치를 해주면 됩니다.

wrapper script 파일을 apktool로 저장합니다. apktool_2.3.0.jar을 apktool.jar로 저장합니다.
이 두 파일을 `/usr/bin`에 저장한 뒤 `chmod +x apktool apktool.jar`을 해줍니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# mv wrapperscript.txt apktool
root@ubuntu:/home/k3y6reak/Desktop# mv apktool_2.3.0.jar apktool.jar
root@ubuntu:/home/k3y6reak/Desktop# chmod +x apktool apktool.jar
root@ubuntu:/home/k3y6reak/Desktop# mv apktool apktool.jar /usr/bin
root@ubuntu:/home/k3y6reak/Desktop# apktool
Apktool v2.3.0 - a tool for reengineering Android apk files
with smali v2.2.1 and baksmali v2.2.1
Copyright 2014 Ryszard Wiśniewski <brut.alll@gmail.com>
Updated by Connor Tumbleson <connor.tumbleson@gmail.com>

usage: apktool
 -advance,--advanced   prints advance information.
 -version,--version    prints the version then exits
usage: apktool if|install-framework [options] <framework.apk>
 -p,--frame-path <dir>   Stores framework files into <dir>.
 -t,--tag <tag>          Tag frameworks using <tag>.
usage: apktool d[ecode] [options] <file_apk>
 -f,--force              Force delete destination directory.
 -o,--output <dir>       The name of folder that gets written. Default is apk.out
 -p,--frame-path <dir>   Uses framework files located in <dir>.
 -r,--no-res             Do not decode resources.
 -s,--no-src             Do not decode sources.
 -t,--frame-tag <tag>    Uses framework files tagged by <tag>.
usage: apktool b[uild] [options] <app_path>
 -f,--force-all          Skip changes detection and build all files.
 -o,--output <dir>       The name of apk that gets written. Default is dist/name.apk
 -p,--frame-path <dir>   Uses framework files located in <dir>.

For additional info, see: http://ibotpeaches.github.io/Apktool/ 
For smali/baksmali info, see: https://github.com/JesusFreke/smali

{% endhighlight %}

이제 apktool을 사용할 수 있으므로 이를 이용해 chrome.apk를 Decompile을 해보겠습니다.

{% highlight Shell %}
root@ubuntu:/home/k3y6reak/Desktop# apktool d chrome.apk
I: Using Apktool 2.3.0 on chrome.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
S: WARNING: Could not write to (/root/.local/share/apktool/framework), using /tmp instead...
S: Please be aware this is a volatile directory and frameworks could go missing, please utilize --frame-path if the default storage directory is unavailable
I: Loading resource table from file: /tmp/1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
root@ubuntu:/home/k3y6reak/Desktop# cd chrome
root@ubuntu:/home/k3y6reak/Desktop/chrome# ls
AndroidManifest.xml  apktool.yml  assets  lib  original  res  smali
{% endhighlight %}

`apktool 버전이 낮은 경우 main함수에서 null point exception이 발생할 수 있습니다.`


#### jadx

또 다른 방법으로는 jadx를 이용하는 방법이 있습니다. 마찬가지로 Java가 필요하기 때문에 자바를 설치해야 합니다. 여기서 주의할 점은 8버전을 설치하면 jadx가 실행되지 않을 수 있습니다. `7버전으로 설치해야 합니다.`

`git clone https://github.com/skylot/jadx.git`을 통해 해당 파일을 다운로드 받습니다.
다운받은 jadx 디렉토리에 접근하여 `./gradlew dist`를 입력하여 컴파일을 합니다.


{% highlight Shell %}
root@ubuntu:~# git clone https://github.com/skylot/jadx.git
Cloning into 'jadx'...
remote: Counting objects: 11711, done.
remote: Total 11711 (delta 0), reused 0 (delta 0), pack-reused 11710
Receiving objects: 100% (11711/11711), 5.31 MiB | 1.43 MiB/s, done.
Resolving deltas: 100% (6143/6143), done.
Checking connectivity... done.
root@ubuntu:~# cd jadx
root@ubuntu:~/jadx# ./gradlew dist
:jadx-core:compileJava
warning: [options] bootstrap class path not set in conjunction with -source 1.6
/root/jadx/jadx-core/src/main/java/jadx/core/xmlgen/ParserConstants.java:156: warning: [serial] serializable class <anonymous jadx.core.xmlgen.ParserConstants$1> has no definition of serialVersionUID
        protected static final Map<Integer, String> PLURALS_MAP = new HashMap<Integer, String>() {
                                                                                                 ^
/root/jadx/jadx-core/src/main/java/jadx/core/dex/instructions/args/LiteralArg.java:68: warning: [deprecation] literalToString(long,ArgType) in TypeGen has been deprecated
                        String value = TypeGen.literalToString(literal, getType());
                                              ^
3 warnings
:jadx-core:compileGroovy UP-TO-DATE
:jadx-core:processResources
:jadx-core:classes
:jadx-core:jar
:jadx-cli:compileJava
warning: [options] bootstrap class path not set in conjunction with -source 1.6
(중략...)
:jadx-gui:installDist
:copyArtifacts
:pack
:dist

BUILD SUCCESSFUL

Total time: 7.471 secs

This build could be faster, please consider using the Gradle Daemon: https://docs.gradle.org/2.14.1/userguide/gradle_daemon.html
root@ubuntu:~/jadx#
{% endhighlight %}

컴파일을 모두 마치면 `build`라는 디렉토리가 생성되는데 `build/jadx/bin/`에 jadx 실행파일이 생성된 것을 볼 수 있습니다.

{% highlight Shell %}
root@ubuntu:~/jadx# ls
build  build.gradle  gradle  gradlew  gradlew.bat  jadx-cli  jadx-core  jadx-gui  jadx-samples  jadx-test-app  LICENSE  NOTICE  README.md  settings.gradle  version
root@ubuntu:~/jadx# cd build/
root@ubuntu:~/jadx/build# ls
jadx  jadx-0.6.1.zip
root@ubuntu:~/jadx/build# cd jadx
root@ubuntu:~/jadx/build/jadx# ls
bin  lib  LICENSE  NOTICE  README.md
root@ubuntu:~/jadx/build/jadx# cd bin
root@ubuntu:~/jadx/build/jadx/bin# ls
jadx  jadx.bat  jadx-gui  jadx-gui.bat
{% endhighlight %}

`jadx [apk 이름]` 또는 `jadx-gui`로 실행 후 apk 파일을 넣어주면 됩니다.


이 외에 몇가지 부분은 나중에 추가하도록 하겠습니다.