---
title: Android开发之AndroidStudio一个工程内查看多个项目的实现
date: 2015-11-21 17:44:37
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Module
id: 10007
---

现在已经有很多人开始使用`AndroidStudio`开始开发`Android`了，但是这货有一点儿不好，一个界面只能打开一个项目，当我们在开发自己的项目时，如果想像`clipse`查看别的`demo`的代码或者功能，只能再另外打开一个`Window`新开项目，其实呢`AndroidStudio`可以在一个项目中导入多个`Module`，这里以导入环信最新版的几个`demo`来实现在`AndroidStudi`中查看多个项目；
首先导入3.0的项目，`3.0 demo`引入了`EaseUI`库，在导入`AndroidStudio`的时候会以`Module`的形式自动导入`EaseUI`，`EaseUI`库中放着`simpledemo`这个小`demo`，我们先把它剪切出来，和其他的`demo`放在一起，等下我们就在一个`AndroidStudio`项目中导入环信的这些全部`demo`
首先导入`3.0 Demo`：
![photo000](http://lzan13.qiniudn.com/blog/uploads/images/2015/11/photo000.png)

因为要导入所有的`demo`所以给导入的项目重新命名下
![photo001](http://lzan13.qiniudn.com/blog/uploads/images/2015/11/photo001.png)

![photo002](http://lzan13.qiniudn.com/blog/uploads/images/2015/11/photo002.png)

导入完成后就可以开始导入其他项目了，不过想在当前项目中导入其他项目要选择`Import Module`的方式，下边以导入环信老版本的`demo`为例
![photo003](http://lzan13.qiniudn.com/blog/uploads/images/2015/11/photo003.png)

![photo004](http://lzan13.qiniudn.com/blog/uploads/images/2015/11/photo004.png)

OK 导入成功，可以看到项目中有三个`Module`了 `easeUI`、`easeUIDemo`、`oldDemo`，其中`oldDemo`就是刚才导入的老版本的`demo`，只是改了个名字
![photo005](http://lzan13.qiniudn.com/blog/uploads/images/2015/11/photo004.png)

然后看下`Run/Debug configuration`可以发现这里有两个配置项，就说明当前工程内有两个可运行的`Android`项目
![photo006](http://lzan13.qiniudn.com/blog/uploads/images/2015/11/photo004.png)

下边再导入那个简单的`simpledemo`看下
导入之后看下`Project Structure`可以看到当前项目有四个`Module`，一个`EaseUI library`库，其他三个是`Android app`项目`easeUIDemo`和`easeUISimpleDemo`引用了`EaseUI`库，`oldDemo`是老版本直接集成的`demo`
![photo007](http://lzan13.qiniudn.com/blog/uploads/images/2015/11/photo004.png)

可以根据这里的选择来运行不同的`module`，并且可以在一个工程内查看所有的代码
![photo008](http://lzan13.qiniudn.com/blog/uploads/images/2015/11/photo004.png)