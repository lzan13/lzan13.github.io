---
title: 开始折腾Cocos2d-x新建批处理来创建项目
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - Batch
  - Bat
id: 681
date: 2014-05-02 14:32:32
---

开始抽空学习`Cocos2d-x`了，虽然`C/C++`还都不咋地，不过在开发中学习记忆或许会更深吧；
so决定从今天开始正式学习的用自己的空闲时间折腾它了，正好这个五一没什么事，昨天搭建了一下开发环境，`Visual Studio`安装不是一般的慢啊，或许我的本本配置有点儿低吧，然后就是编译`Cocos2d-x-V2.2.3`，不知道别人的有没有错误，我的在编译到最后提示有`15`个警告，`10`条错误，不过我也没管它，试着运行了示例程序也没事，然后自己创建了一个`HelloWorld`也没错，所以也就没管那个错误；我从开始关注`Cocos2d-x`时 她都已经更新到了`V2.x`了，所以创建新项目也就不和网上曾经的教程一样，还要给`VS`安装什么模版，现在貌似都是使用`Python`去进行创建了，而且这个`Python`的版本也有要求，貌似不能用`3.x`的版本，我用的是`V2.7.6`版本；感觉现在创建这个项目很简单，就一句话的事，就是调用`Cocos2d-xtoolsproject-creatorcreate_project.py`文件进行创建，调用时需要几个参数

### 简单的命令
```bat
create_project.py -project %ProjectName% -package %PackageName% -language cpp
```
其中`ProjectName`是项目名称，`PackageName`是项目包名，`language`是项目使用的寓言，有三种选择`cpp`、`javascript`、`lua`
我当时创建时想选择两种语言但是没有成功！不知道是不是一次只能选择一个呢
根据这句话，看到网上好多人都谢了有一个批处理，我也写了一个，并稍稍美化了下 O(∩_∩)O~~
这个批处理可以判断项目名和包名是否为空，当为空时会要求重新输入项目名称， 并且在创建成功后会自动打开项目，并关闭批处理：

### V2.x批处理
```bat
@echo off
:startProject
echo -----------------------------------------------
echo ---       输入项目名和包名创建项目          ---
echo -----------------------------------------------
set /p ProjectName=ProjectName:
if "%ProjectName%" == "" goto inputError
if "%ProjectName%" == "exit" goto exit
echo Input ProjectName is : %ProjectName%
set /p PackageName=PackageName:
if "%PackageName%" == "" goto inputError
if "%ProjectName%" == "exit" goto exit
echo Input PackageName is : %PackageName%
echo Create Project loading...
D:AndroidCocos2dcocos2d-x-2.2.3toolsproject-creatorcreate_project.py -project %ProjectName% -package %PackageName% -language cpp
echo Create Project Success! loading project ...
D:AndroidCocos2dcocos2d-x-2.2.3projects%ProjectName%proj.win32%ProjectName%.vcxproj
exit
:inputError
@echo ProjectName and PackageName not null!!!
@echo ==============================================
@echo ===        输入为空，请重新输入           ====
@echo ==============================================
goto startProject
:exit
```

### V3.x批处理
在使用`Cocos2d-x 3.0`版本时，创建的方法有些不同，在配置好环境的前提下，这个批处理只需要改变一句命令就可以使用`Cocos2d-x 3.0`来创建项目了下边是适用于`3.0`创建项目的批处理
```bat
@echo off
:startProject
echo -
echo -
echo - 输入项目名和包名创建项目
echo -----------------------------------------------
set /p ProjectName=ProjectName:
if "%ProjectName%" == "" goto inputError
if "%ProjectName%" == "exit" goto exit
echo Input ProjectName is : %ProjectName%
set /p PackageName=PackageName:
if "%PackageName%" == "" goto inputError
if "%ProjectName%" == "exit" goto exit
echo Input PackageName is : %PackageName%
echo Create Project loading...
cocos new -p %PackageName% -l cpp %ProjectName% -d D:DevelopCocos2dxworkspace
echo Create Project Success! loading project ...
D:DevelopCocos2dxworkspace%ProjectName%proj.win32%ProjectName%.vcxproj
exit
:inputError
@echo ProjectName and PackageName not null!!!
@echo ==============================================
@echo =            输入为空，请重新输入            =
@echo ==============================================
goto startProject
:exit
```
这个批处理选用的文件的绝对路径来引用，所以批处理文件可以放在任何位置运行！
我没有选择改变创建的项目的存储路径，还是默认的`Cocos2d-x`的`projects`目录下，貌似更改了这个路径的话还要复制相应的文件，不然会导致编译通不过；
Ok 开始编写代码了！