---
title: Android开发集成环信SDK3.x简单介绍
comments: true
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Easemob
  - SDK
id: 10070
date: 2016-04-07 11:04:07
---

### 前言

这不代表官方教程！
这不代表官方教程！
这不代表官方教程！
重要的事情说三遍！！！

环信已经发部了`SDK3.x`版本，`SDK3.x`相对于`SDK2.x`来说是整个进行了重写，`API`变化还是比较大的，已经熟悉`SDK2.x`的开发者在使用新的`SDK3.x`还是会遇到不少问题的，不过还好官方给出了`SDK2.x`升级`SDK3.x`指南，已经熟悉`SDK2.x`开发者可以根据文档了解`SDK3.x`的变化，新集成的开发者可以直接参考`SDK3.x`进行集成；


### 提供一些地址
当前项目地址，可以直接 clone 运行
[EaseChat Github](https://github.com/lzan13/EaseChat)

AndroidStudio下载
[Android官方下载](http://tools.android.com/download/studio/builds/2-0)
[国内提供 AndroidDevTools](http://androiddevtools.cn/)

模拟器 Genymotion下载
[Genymotion 官网](http://genymotion.com/)

环信官方文档
[SDK3.x 文档](http://docs.easemob.com/im/start)
[SDK3.x API 文档](http://www.easemob.com/apidoc/android/chat3.0/annotated.html)
[SDK2.x 升级 SDK3.x 文档](http://docs.easemob.com/im/200androidcleintintegration/140upgradetov30)


###说下我当前开发环境

AndroidStudio 2.0
Gradle 2.10（跟随AndroidStudio 一起更新）

Android SDK Tool 25.1.1
Android Build-tools 23.0.2
Android Support 最新

Genymotion 2.6

如果你还是用的`Eclipse`，可以下载`AndroidStudio`尝试下，如果你说上不了`Android`官网，不懂怎么翻墙可以找下国内开发提供的一些地址


### 开始集成
这次要实现的功能

* SDK的初始化


* SDK端的注册登录


* 消息的发送和监听

  
