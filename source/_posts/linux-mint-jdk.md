---
title: Linux Mint 14 下安装Jdk
categories:
  - System
tags:
  - Linux
  - LinuxMint14
  - Java
  - Jdk
  - Oracle
  - Profiel
id: 376
date: 2013-05-30 22:37:47
---

`Linux mint 14`安装完了，软件更新源也更新来，中文输入法也安装好了，Ok 该干正事了，搭建开发环境，准备开发！呵呵～～

首先是先到`jdk`的官方网站下载，`java`现在已经被`oracle`收购
[Oracle官网](http://www.oracle.com) 
[Jdk下载页面](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
我下载的是最新的`jdk1.7.0.21`，好像`1.7`在`linux`上只有`.gz`和`.rpm`版，而且`.rpm`安装需要其他的支持，所以我选择直接解压的`.gz`版

    提示以下，我这都是在`root`用户下操作的，如果非`root`用户或许会提示没有权限操作，建议转到`root`账户下操作，或者你可以更改文件的权限`chmod 755 filename`

我是把我的jdk安装在来`usr`目录下，所以我创建来一个`java`文件夹，你可以自己选择安装路径，
OK，下载好之后，打开终端`mkdir /usr/java` #创建一个文件夹，把下载好的jdk拷贝进去

然后终端输入`tar xzvf you-jdk-name.jar` #输入你下载的jdk文件的全名解压到当前目录；

解压过后就相当于安装安成来，接着就要配置jdk环境变量，
同样，终端输入`sudo gedit /etc/profile` #我喜欢用`gedit`编辑文件，有人喜欢`vi vim`

在弹出的文件编辑器中最下方加入如下内容：
```bat
JAVA_HOME=/usr/java/jdk1.7.0_21 #这里的路径替换为你自己的java路径
JDK_HOME=/usr/java/jdk1.7.0_21  #好像android studio不认识JAVA_HOME，所以要添加一个JDK_HOME环境变量
JAVA_BIN=/usr/java/jdk1.7.0_21/bin
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME JDK_HOME PATH CLASSPATH
```
然后保存，注销重新登录，使`profiel`生效，
如果想让`/etc/profile`文件修改后立即生效 ,可以使用如下命令:`. /etc/profile` # 注意`.`和`/etc/profile`之间有`空格`

终端输入`java -version` #如果出现如下图类似信息，说明你的jdk安装配置成功
[![linu-mint-jdk](http://wp-melove.qiniudn.com/blogimg/2013/05/linu-mint-14.png)](http://wp-melove.qiniudn.com/blogimg/2013/05/linu-mint-14.png)

OK，然后就去配置基于`java`的开发环境吧！