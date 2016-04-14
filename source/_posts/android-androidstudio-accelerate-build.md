---
title: AndroidStudio优化编译速度
comments: true
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Accelerate
  - Build
  - Compile
  - InstantRun
  - Offline
id: 10071
date: 2016-04-13 20:37:26
---
前几天AndroidStudio发不了`2.0`正式版，又不少好的功能，所以就更新了下，不过碍于电脑配置不够，刚升级完编译项目竟然要5分钟之多，我了个去，这还搞毛啊，所以扒一扒有什么能优化的方法，稍微加速点编译速度，其实在`1.x`的版本时就有过配置优化，经过优化配置后，我的`AndroidStudio`编译一个全新的项目还是需要`1:30`没有网上别人说的那么快，应该是电脑配置不够的原因，内存8G，同事的`MacBook`16G配置，不优化都比我的快，说这么多就是配置够好就够快

废话太多了，开始优化

### 第一步修改gradle.properties
首先在当前用户的目录下`C:\Users\lzan13\.gradle`创建 `gradle.properties` 文件，并加入如下配置
```gradle
#---------------------------------------------------------------------------------------------------------
# 2016-4-12 by lzan13
#
# 此文件是对通过 Gradle 编译项目的全局配置，所有项目都有效，其实这些都是根据
# AndroidStudio 创建的项目的 gradle.properties 文件配置的，原文内容如下注释
#
#---------------------------------------------------------------------------------------------------------
#=========================================================================================================
# Project-wide Gradle settings.
# IDE (e.g. Android Studio) users:
# Gradle settings configured through the IDE *will override*
# any settings specified in this file.
# For more details on how to configure your build environment visit
# http://www.gradle.org/docs/current/userguide/build_environment.html
# Specifies the JVM arguments used for the daemon process.
# The setting is particularly useful for tweaking memory settings.
# Default value: -Xmx10248m -XX:MaxPermSize=256m
# org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
# When configured, Gradle will run in incubating parallel mode.
# This option should only be used with decoupled projects. More details, visit
# http://www.gradle.org/docs/current/userguide/multi_project_builds.html#sec:decoupled_projects
# org.gradle.parallel=true
#=========================================================================================================
# 开启Gradle 守护进程
org.gradle.daemon=true
# 使用多线程编译
org.gradle.parallel=true
# 设置JVM 虚拟机内存大小， 后边是设置保存错误日志
org.gradle.jvmargs=-Xmx1024m -XX:MaxPermSize=256m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
# 使用按需配置
org.gradle.configureondemand=ture
```
### 第二步设置 offline
打开设置面板，找到Build, Execution, Deployment>Build Tools>Gradle
![offline](http://lzan13.qiniudn.com/blog/uploads/images/2016/04/accelerate-image-offline.png)

### 然后选中Compiler，
其中四项复选框都可以选中
>1) Compile independent modules in parallel (may require larger heap size) this option is in "incubation" and should only be used with decoupled projects. 表示使用多线程编译，需要更多的堆栈内存
2) Make project automatically (only works while not running / debugging). 使项目自动(只工作而不是运行/调试
3) use in process build. Faster, and integrated with the "Gradle Console". 使用过程中构建。更快,结合“Gradle控制台”。
4) configure on demand. This option may speed up builds. This option is in "incubation". Please read Gradle's documentation. 按需配置这个选项可能会加速构建。此选项在“孵化”。请读它的文档

其中的命令行需要单独配置
可以看下文档 [Gradle Command](http://www.gradle.org/docs/current/userguide/gradle_command_line.html)
![command line](http://lzan13.qiniudn.com/blog/uploads/images/2016/04/accelerate-image-compiler.png)

`--offline`是开启脱机工作
`--profile`生成编译记录文件，用来分析哪里耗时
（文件目录：`CurrProject/build/reports/profile/`）
[这里讲解记录文件的分析](http://liaohuqiu.net/posts/speed-up-your-build/)

###以上配置做完了之后就可以新建项目使用了
以上都是全局配置，配置完之后，我们新建项目就会应用这些配置，如果是我们之前创建的项目时不会应用在studio新配置的项，需要打开项目重新配置下；
然后又是我们需要更新下项目的一些配置

### 首先是项目根目录的build.gradle文件更新gradle插件到最新
    classpath 'com.android.tools.build:gradle:2.0.0'

开启Instant Run，这个默认一般都开启的
![instant run](http://lzan13.qiniudn.com/blog/uploads/images/2016/04/accelerate-image-instant-run.png)







