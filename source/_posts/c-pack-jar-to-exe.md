---
title: 使用C++调用.exe可执行文件并传递参数
categories:
  - Develop
  - C/C++
tags:
  - C/C++
  - Exe
  - Jar
  - JSmooth
date: 2013-06-26 16:56:57
id: 1372237017000
comments: true
---

最近要使用`C++`去调用由`java`编写好了的一个模块，之前没接触过`C`和`C++`，就`google`了好久，虽然找到了`C/C++`调用`java`的方法，自己试着写了一个`Demo`也成功了，但是当去调用我的那个已经做好的模块的时候，不知道哪里抽风了，就是通不过，我那个郁闷啊！

所以就想另辟新径，将已经写好的`java`模块打包为可执行`exe`文件，然后让`C/C++`去调用这个可执行程序！这样就不会出现问题了！

说整就整，又是`Google`了N久，找到了一个`JSmooth`将可执行`jar`包编译成可执行`exe`文件的软件，通过这个确实实现了可执行程序！

言归正传，今天我们这里不讨论`JSmooth`可以自己`google`
只说`C/C++`代码调用`exe`文件，并传递参数：我这里是参考了[sharep 的BLOG](http://sharep.blog.51cto.com/539048/151384)这位仁兄的一篇教程（他这里介绍了几种方法的主要使用方式），使用了其中的`ShellExecute`方法，测试通过！
```c++
#include "windows.h"
#include "shellapi.h"
#include <iostream>

using namespace std;

int WINAPI WinMain(HINSTANCE hInstance,
HINSTANCE hPrevInstance,
LPSTR lpCmdLine,
int nCmdShow
)
{
	//::ShellExecute(NULL, "open", "OfficeToPDF.exe", "F:\myfile\test.ppt F:\myfile\test.pdf", NULL, SW_SHOW); 	
	ShellExecute( 
		NULL,			//父窗口句柄 
		"open",			//操作, 打开方式 "edit","explore","open","find","print","NULL" 
		"OfficeToPDF.exe",	//文件名,前面可加路径 
		"参数1 参数2 参数3",	//参数 可以多个参数用空格隔开
		NULL,			//默认文件夹 
		SW_SHOWNORMAL		//显示方式 
		); 
	return 0;
}
```

刚开始在使用的编码的过程中，由于对`C/C++`不是很了解，没有使用系统的`Mian`方法，所以，一定要使用`int WINAPI WinMain()`这个方法！