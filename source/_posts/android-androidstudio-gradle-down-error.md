---
title: AndroidStudio新建及clone项目关于gradle出现的问题
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Gradle
id: 915
date: 2015-11-18 21:35:40
---
在我们使用`AndroidStudio`创建或者`clone`一个新的项目的时候，一般会遇到一些问题，如下图那样，一般是因为在`AndroidStudio`每次更新版本都会更新`Gradle`这个插件，但由于墙的问题每次更新都是失败，又是停止在`Refreshing Gradle Project`,有时新建项目的时候报`Gradle Project Compile Error`等等相关的问题
解决这些问题办法是
![tupian015](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian015.png)

打开`AndroidStudio`项目，找到项目目录`gradle\wrapper\gradle-wrapper.properties`这个文件内容如下
```java
#Wed Jul 08 23:06:25 CST 2015
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-2.5-all.zip
```
最重要的就是最下面一句，android studio会联网下载符合当前版本的gradle插件，而这个网址虽然可以访问但速度实在太慢，所以每次下载需要花很长时间或直接超时

解决办法就是自己去手动下载当前项目定义的版本，可以直接复制这个路径去下载，也可以去gradle官网自己选择版本下载
然后把下载下来的压缩包拷贝到`C:\Users\lzan13\.gradle\wrapper\dists\gradle-2.5-all\d3xh0kipe7wr2bvnx5sk0hao8`目录下`后边这个长串的目录不一定相同，以自己的为准`

重启`AndroidStudio`或选择工具栏`Tools->Android->Sync Project Gradle Files`
![tupian016](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian016.png)
等待更新完成，就ok了