---
title: AndroidStudio导入环信新版EaseUI库问题
date: 2015-11-18 21:55:13
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - EaseUI
  - Easemob
id: 931
---

环信大牛最新封装了一个供开发者直接使用的`UI`库`EaseUI`，这个可以让大家快速的进行集成环信的`SDK`进行实现聊天，官方也说了老版本的`demo`不会进行维护，重点维护这个`EaseUI`，但是在自己导入的时候有时会有些问题，这里用`1.4`版本的`AndroidStudio`导入`3.0 demo`来说明下；
首先就是打开AndroidStudio导入项目
![tupian010](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian010.png)
我喜欢给他改个名字EaseUIDemo
![tupian011](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian011.png)
导入完成
我这边导入时没有问题的，有时导入`demo`或者我们自己创建的项目 然后导入`EaseUI`库，并加入到自己的项目中去的时候可能会出现下边这样的错误 ，出现问题的原因的大致因为EaseUI默认引入的`v4`包的版本`20.0.0`，但是大家的开发环境不同，`SDK`版本以及编译器和`support`库版本不同，会出现错误；
![tupian012](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian012.png)
解决办法：这个时候就去点击项目设置，选中EaseUI把sdk版本设置成和`build.gradle`里一样的版本就行了，如果过低，建议更新`Android SDK Tool`，还不行，就把自己的项目的`SDK`版本和`EaseUI`都设置成一样，`v4`库也设置成一样
![tupian013](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian013.png)
设置了之后，要记得同步`gradle 1 or 2 `方式都可以
![tupian014](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian014.png)