---
title: 使用第三方库出现找不到so库UnsatisfiedLinkError错误的原因以及解决方案
categories:
  - Develop
tags:
  - Android
  - Armeabi
  - Easemob
  - Error
  - HyphenateChatSDK
  - Library
  - UnsatisfiedLinkError
id: 1487659244
comments: true
date: 2017-02-21 14:40:44
---

>首先这边引用`环信SDK工程师`总结的出现这个问题的一些原因（稍作排版修改）：
引用 / [环信SDK工程师分析 UnsatisfiedLinkError](http://www.imgeek.org/article/825307895)

### 前言
在开发项目的时候我们免不了使用一些第三方的库来进行快速开发，有些第三方库只是简单的一个`jar`包，但是有些使用了`jni`开发，因此会包含`so`库文件，这个时候如果不消息我们就会遇到一个错误:`java.lang.UnsatisfiedLinkError`；

最近经常遇到有开发者在问使用环信`sdk`的时候出现这个错误；这里分享下问题原因以及解决方案；

### 相关信息
这里需要先解释一下相关信息
`hyphenatechatsdk`提供的指令集类型仅提供`armeabi`、`arm64-v8a`、`x86`三种;
`armeabi`和`armeabi-v7a`是相近似的指令集，`v7a`是增强型指令集，运行速度，效率均有所提高，他们都是`32`位指令，并且兼容；`arm64-v8a`对应`arm64`位指令集；
`arm64`位策略和`intel IA32`不一样：`intel64`位指令是兼容`intel32`位指令，`intel32`位指令编译的程序可以直接在`intel64`位机器上运行；但是`arm`不是，`arm64`位和`arm32`位是彼此独立的指令系统，不兼容；`arm`这样设计的原因是因为运行在嵌入式上，设计指标更趋向于效率，和耗电考量；实际上`arm64`位芯片上同时包含着`arm64`指令处理器和`arm32`位指令处理器，只不过两个处理器彼此独立；

### 导致产生`UnsatisfiedLinkError`的几个原因
影响链接的限制条件: `armeabi`实际上可以运行在`arm64`位机器上，只不过`Google`增加了限制条件：
1. `Android4.x`只要能找到`so`，就可以运行，`so`可以在`armeabi`、`armeabi-v7a`、`arm64-v8a`，`so`位置可以很随意；

2. `Android5.x`开始，检查更加严格，会只有和芯片型号对应目录的`so`会安装到手机中；
>举个例子：
开发环境下目录结构如下
    `libs/armeabi:libhyphenate.so libhyphenate_av.so`
    `libs/armeabi-v7a:libmediadata.so` 
手机对应的指令集是`armeabi-v7a`，然后安装到手机的只有`libmediadata.so`；

3. `Android6.x`下，检查更加严格，有一条规则，之前测试有遇到，现在不太确认；
`libs/armeabi/: libhyphenate.so libhyphenate_av.so`
`libs/arm64-v8a`(没有此目录)
在`arm64`位机器上也可以运行，但是作为开发者通常会依赖其他开发包，比如`baiduMap`，也会用其他`so`，不能让所有开发者都删掉`libs/arm64-v8a`的目录；不过开发者可以尝试下删除`arm64-v8a`，只留`armeabi`，这样安装包会很小，在各个平台上也能运行；
`Google`考量点是执行速率，更流畅的用户体验，作为开发者，服务提供者，我们希望`apk`尽可能小，对执行速度要求不高；

4. `armeabi`和`armeabi-v7a`可以互换，现在市面上的手机很少有`armeabi`的，基本上是`armeabi-v7a`或`arm64`位的高端机器。

5. 查看手机芯片型号：`cat /proc/cpuinfo`, 仔细看一下打印信息，能够看明白手机指令集，是`32`位还是`64`位。

6. `x86`目录，通常对应虚拟机，很多开发者喜欢在`genymotion`上开发调试，这个就对应`86`，`x86`和前面说的`intel IA32`是一回事，所以只提供`32`位的，也能在`x86-64`位机器上运行；

7. 我们的`so`还依赖于`libsqlite.so`，不过由于这个包从来没有变化，使用的是系统默认提供的`/system/lib`，在`Android 6.x`及以下的平台可以运行，`Android7.x`执行更严格的安全检查，禁止使用系统目录的内容，所以如果希望在`Android7.x`以上版本，需要把系统目录的`libsqlite.so`拷贝出来，也放在自己`app`对应指令目录下，由于目前`Android7.x`市面上没有机型，所以目前不在考虑范围；

8. `mips`指令集的手机很少见，听说联想有出过，没见过;

9. `libs/armeabi/libhyphenate.so`和`libs.without.audio/armeabi/libhyphenate.so`是不同，`libs/armeabi/libhyphenate.so`会依赖于`libs/armeabi/libhyphenate_av.so`，如果找不到会报`java.lang.UnsatisfiedLinkError`；

10. 还有一个比较容易忽略的一点，现在我们的的项目一般都是引入好多个第三方库，第六点也提到了使用`baiduMap`这点，就是当一个项目引入多个库，不同的库有不同的`so`文件夹，当其中一个支持的比较少，但是另一个比较全时，也会出现这样的错误，解决方法就是不支持的`so`文件夹删除
>举个例子：
环信支持的指令集`arm64-v8a`、`armeabi`、`x86`
百度地图支持的指令集`arm64-v8a`、`armeabi`、`armeabi-v7a`、`x86`、`x86_64`、`mips`、`mips64`
如果想在所有设备上都能运行，需要把`armeabi-v7a`、`x86_64`、`mips`、`mips64`这些删除；
根据前边所说可以保留`armeabi-v7a`文件夹，然后复制`armeabi`里的`so`文件到`armeabi-v7a`就行了

### 结语
所以如果大家再遇到这样的问题，可以先根据以上信息排查下，无非就是某个库的 so 文件放多了，或者某个 so 库的文件放少了，或者是 jar 包和 so 不匹配了，这些只要细心看下 ide 的日志提示，很容易就解决，希望此篇文章能帮大家解决问题，谢谢！

文笔有限，如果问题，欢迎指正 ^_^~
