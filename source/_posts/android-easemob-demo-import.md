---
title: 关于新版AndroidStudio导入环信Demo的一些注意事项
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - EaseUI
  - Easemob
id: 919
date: 2015-11-18 21:45:08
---

这里是以AndroidStudio v1.4版本为例

新版`AndroidStudio`已经可以直接导入`eclipse`的项目了，具体步骤看下边（会的飘过）
有一点要注意：`AndroidStudio`导入`eclipse`的项目，如果项目有引入`library`项目，并且`library`项目路径正确，`AndroidStudio`在导入`eclipse`的项目的同时会自动导入`library`为`module`

启动`AndroidStudio`进入起始页，有些设置的是启动直接打开项目了，可以在设置里先设置下：
![tupian000](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian000.png)

设置了之后，在启动就是进入到启动界面了：
选择`Import project（Eclipse ADT， Gradle， etc）`选项开始选择导入`eclipse`创建的`Android`项目
![tupian001](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian001.png)

然后选择项目路径，这里以最新版环信demo2.2.4为例
注意：环信的`ChatDemoUI`这个`demo`里边因为研发的同事为了照顾老版本的`AndroidStudio`使用者，已经用`eclipse`生成了`build.gradle`文件，所以如果要导入新版`AndroidStudio`请把`build.gradle`删除
![tupian002](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian002.png)
![tupian003](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian003.png)

然后选择项目目的路径（相当于工作空间，带上项目目录名）
![tupian004](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian004.png)
提示不存在，不存在就对了 ok
![tupian005](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian005.png)
继续默认 Finish
![tupian006](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian006.png)
然后就是等待，这个一般有时会卡住，因为有时需要下载gradle的包，会很慢很慢，解决办法就是自己去gradle官网去下载gradle包，至于怎么解决可以看下我的另一篇文章:[AndroidStudio 新建及clone项目关于gradle出现的问题](http://melove.net/lzan13/develops/android-develop/androidstudio-gradle-down-error-915.html)
![tupian007](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian007.png)

导入成功，打开项目就是这样了
![tupian008](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian008.png)
下边多说一句，就是如果自己创建的项目想要引用`EaseUI`这个库（不只是这个库，其他任何的`library`库），可以直接在项目界面，点击菜单栏`File->New->Import Module`然后选择`easeui`路径就ok
![tupian009](http://wp-melove.qiniudn.com/blogimg/2015/11/tupian009.png)