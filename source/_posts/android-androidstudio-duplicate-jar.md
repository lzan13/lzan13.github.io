---
title: AndroidStudio导入第三方jar包重复加载错误解决办法
date: 2015-05-08 09:13:41
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Duplicate
  - HttpClient
  - Jar
id: 911
---

最近在使用`Http`时需要实现文件上传，虽然可以使用`HttpURLConnection`实现原生`post`上传，不过这个好像效率很低，然后就选择网上比较多的`HttpClient`通过`HttpPost`的方式上传文件，使用这种方式的时候需要用到`apache`的一个`jar`包`httpmime-xxx.jar`这个包还依赖于`httpcore-xxx.jar`在正常的导入两个`jar`包后，满心欢喜的准备编译，可是一个蛋疼的错误蹦出来了：
```java
Error:duplicate files during packaging of APK D:\Develop\Android\workspace\androidstudio\MLTimeLine\app\build\outputs\apk\app-debug-unaligned.apk
     Path in archive: META-INF/NOTICE
     Origin 1: D:\Develop\Android\workspace\androidstudio\MLTimeLine\app\libs\httpcore-4.3.jar
     Origin 2: D:\Develop\Android\workspace\androidstudio\MLTimeLine\app\libs\httpmime-4.3.1.jar
You can ignore those files in your build.gradle:
     android {
       packagingOptions {
         exclude 'META-INF/DEPENDENCIES'
         exclude 'META-INF/NOTICE'
         exclude 'META-INF/LICENSE'
       }
     }
Error:Execution failed for task ':app:packageDebug'.
> Duplicate files copied in APK META-INF/NOTICE
     File 1: D:\Develop\Android\workspace\androidstudio\MLTimeLine\app\libs\httpcore-4.3.jar
     File 2: D:\Develop\Android\workspace\androidstudio\MLTimeLine\app\libs\httpcore-4.3.jar
```
看字面意思就是说`jar`加载重复了，然后就是需要在项目的便衣文件中加入一些东西，既然能理解，就按照它提示的来嘛，
打开项目的`build.gradle`文件，在`android`标签下加入提示的部分，然后同步`gradle`在修改了`gradle`编译文件后`Androidstudio`右上角会有提示同步`gradle`的按钮，可以直接点击同步`gradle`配置，如果没有可以选择菜单栏里的`Tools - Android - Sync Project with Gradle Files`进行同步
同步完成，继续编译，duang...
还是那个错误，不过这次提示加入的内容不同，继续在`packagingOptions`下边添加，继续同步编译，duang...
继续,duang.duang.duang...
ok duang了三次之后算是没问题了，在编译配置文件中加入的就是这几行代码
```java
// 解决重复加载第三方那个jar包问题
packagingOptions {
     exclude 'META-INF/DEPENDENCIES'
     exclude 'META-INF/NOTICE'
     exclude 'META-INF/LICENSE'
}
```
感觉这个也是根据添加的`jar`包不同而有所不同的；不过只要跟着提示应该都可以解决的！