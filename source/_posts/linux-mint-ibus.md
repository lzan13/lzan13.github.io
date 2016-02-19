---
title: Linux Mint 14安装好之后要做的几件事
categories:
  - System
tags:
  - Linux
  - Linux Mint
id: 372
date: 2013-05-30 22:09:13
---

都说`linux`下开发效果好，而且能学到的东西也多，因为`linux`操作不是很好，对命令行不怎么了解，所以我选择来`Ubuntu`开发，不过不知道怎么回事，也许电脑抽风来，试了好多次，现在装`Ubuntu`老是抽风，所以就我就选择了`linux mint 14`系统！

装过`linux mint 14`之后有很多问题的，首先就是更新软件源问题，系统默认的源是真慢，我测试了好多个源，感觉还是中科大的最快速，推荐中科大，也有人说`163`的源不错，但是我用来好多次，都出错，所以选择中科大！

首先为了不出问题，或者出问题有法解决，先将原来的源备份一下，使用命令`sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`

然后用命令`sudo gedit /etc/apt/source.list`
编辑源文件，将原来的源地址清空替换为下边的中科大源！
```bat
deb http://mirrors.ustc.edu.cn/linuxmint nadia main upstream import
deb http://mirrors.ustc.edu.cn/ubuntu quantal main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu quantal-updates main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu quantal-security main restricted universe multiverse
deb http://archive.canonical.com/ubuntu/ quantal partner
deb http://packages.medibuntu.org/ quantal free non-free
```
替换过软件源之后还有一个非常蛋疼的问题，就是`linux mint 14`是没有中文输入法的，对与国人加上我这个英语文盲来说很纠结蛋疼，所以每次都是很恼火，要自己装输入法，经过我多次尝试，多次卸载重装系统，有卸载重装输入法之后，得出，我的电脑不适合装fcitx，也不适合装`scim`，只能装`ibus`，因为输入法我都重装了两次系统 呃……
所以，更新过源地址之后，就是要装输入法了，终端输入命令，`root`账户下，非`root`用户的话，先使用命令`sudo passwd root`更改`root`密码，然后`su root`切换到`root`用户，
输入`sudo apt-get install ibus-pinyin`安装`ibus`拼音输入法！