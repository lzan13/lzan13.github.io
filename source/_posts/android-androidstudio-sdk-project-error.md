---
title: Android开发之新版AndroidStudio以及新版sdk开发环境创建新项目可能会出现的一些错误问题
date: 2015-11-22 09:34:09
comments: true
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Error
  - SDK
id: 10008
---

### 首先说下我这边开发的环境：
    AndroidStudio version：1.4.1
    jdk version：1.7.0
    SDKTools version：24.4.x
    Build-tools version：23.0.1
    Compile SDK version：API 22 （5.1）
    Minimum SDK version：API 15（4.0.3）
    Gradle version：2.4
### AndroidStudio等工具地址
[Android官网](http://developer.android.com)
[国内收集工具 AndroidDevTools](http://www.androiddevtools.cn)
[gradle v2.4下载（as有时自动下载不成功，所以要自己下载](https://downloads.gradle.org/distributions/gradle-2.4-all.zip)
关于gradle首次创建项目卡住的问题可以看下这篇文章[AndroidStudio 新建及clone项目关于gradle出现的问题](http://melove.net/lzan13/develops/android-develop/androidstudio-gradle-down-error-915.html)

### 创建项目
这里使用AndroidStudio创建一个空的项目就OK了，
打开AndroidStudio 点击Start a new Android Studio project创建新项目

#### 问题一
```java
Error:A problem occurred configuring project ':app'.
> Could not download junit.jar (junit:junit:4.12)
   > Could not get resource 'https://jcenter.bintray.com/junit/junit/4.12/junit-4.12.jar'.
      > Could not GET 'https://jcenter.bintray.com/junit/junit/4.12/junit-4.12.jar'.
         > peer not authenticated 
Error:A problem occurred configuring project ':app'.
> Could not download hamcrest-core.jar (org.hamcrest:hamcrest-core:1.3): No cached version available for offline mode
```
这个是因为最新的创建项目会使用junit库来进行代码测试，在下载这个库的内容的时候发现他引用了hamcrest这个框架，不过国内下载这个框架的这个接啊日报hamcrest-core.jar不成功，所以会报这个错误，可以把build.gradle里引用的junit给删除或注释

#### 问题二
```java
D:\develop\android\workspace\studio\EaseUICustomer\app\build\intermediates\exploded-aar\com.android.support\appcompat-v7\23.1.0\res\values-v23\values-v23.xml
Error:(2) Error retrieving parent for item: No resource found that matches the given name 'android:TextAppearance.Material.Widget.Button.Inverse'.
Error:(2) Error retrieving parent for item: No resource found that matches the given name 'android:Widget.Material.Button.Colored'.
Error:Execution failed for task ':app:processDebugResources'.
> com.android.ide.common.process.ProcessException: org.gradle.process.internal.ExecException: Process 'command 'D:\develop\android\android_sdk\build-tools\build-tools-23.0.1\aapt.exe'' finished with non-zero exit value 1
```
这个是因为创建项目默认使用`appcompat-v7` `23.0.1`版本，这个版本好像有问题，手动把这个版本号改下就ok了，改成22.+然后同步 就ok