---
title: Cocos2d-x 3.x 引用CocoStudio等第三方库
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - CocoStudio
  - Extensions
  - Library
date: 2014-07-11 15:21:54
id: 1405063314000
comments: true
---

在使用`Cocos2d-x 3.0`的时候有时会需要用到一些扩展，比如`CocoStudio`的扩展等，这时就需要在`VS`中添加第三方库的引用`我是用的是VS 2012`
首先【右击解决方案】->【添加】->【现有项】
找到以下几个目录下的`lib`库进行添加
```c++
CocoStudio库：C:WorkHelloCocoscocos2dcocoseditor-supportcocostudioproj.win32libCocosStudio.vcxproj  
Extensions库：C:WorkHelloCocoscocos2dextensionsproj.win32libExtensions.vcxproj  
GUI库：C:WorkHelloCocoscocos2dcocosuiproj.win32libGUI.vcxproj  
```
添加完之后解决方案里边就会多出三个库，此时要给项目添加引用：
`右击项目名称`->`引用`，在弹出框选择新加的三个库
[![import-library](http://lzan13.qiniudn.com/blog/uploads/images/2014/07/import-library.png)](http://lzan13.qiniudn.com/blog/uploads/images/2014/07/import-library.png)

在引用之后，需要`右击项目`->`属性`->`配置属性`->`C/C++`->`常规`->`附加包含目录`里添加几项路径：
```c++
$(EngineRoot)
$(EngineRoot)cocos
$(EngineRoot)cocoseditor-support
```
[![import-library2](http://lzan13.qiniudn.com/blog/uploads/images/2014/07/import-library2.png)](http://lzan13.qiniudn.com/blog/uploads/images/2014/07/import-library2.png)
至此第三方库就添加完成了，在cpp类中就可以添加第三方的.h文件，然后编译OK通过
在移植到`Android`上的时候，因为添加了第三方库，需要在`Android`的`jni/Android.mk`的文件添加配置
可以看下：[Cocos2d-x v3.0 开发之引用Extensions第三方库后打包成apk遇到的问题及解决办法](http://www.melove.net/lzan13/cocos2d-x-library-android-mk-773.html) 这篇文章