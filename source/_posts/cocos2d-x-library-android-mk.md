---
title: Cocos2d-x 3.x 开发之引用Extensions第三方库后打包成apk遇到的问题及解决办法
categories:
  - Develop
  - Cocos2d-x
tags:
  - Android
  - Apk
  - Cocos2d-x
  - Library
  - Extensions
id: 10040
date: 2014-07-11 15:24:21
---

在使用`Cocos2d-x`写游戏想使用`9.png`的时候遇到了一些问题，需要用到`Scale9Sprite`类，这个算是第三方库里的类，在`win32`环境下加入头文件
`cocos-ext.h`编译没问题，但是在打包到`Android`的`apk`时出现找不到文件，这让我很蛋疼，发现原来需要把路径写成`extensions/cocos-ext.h` 单单是这样还是不能编译通过的，要在`Android`项目`jni`目录下的`Android.mk`文件中引入`extensions`的静态库和相关模块儿
下边是我的示例`Android.mk`
```C++
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE := cocos2dcpp_shared

LOCAL_MODULE_FILENAME := libcocos2dcpp

LOCAL_SRC_FILES := hellocpp/main.cpp 
                   ../../Classes/AppDelegate.cpp 
                   ../../Classes/HelloWorldScene.cpp 
             ../../Classes/MyFunc.cpp 
             ../../Classes/CellSprite.cpp 
             ../../Classes/LoadScene.cpp 
             ../../Classes/MainScene.cpp 
             ../../Classes/GameScene.cpp

LOCAL_C_INCLUDES := $(LOCAL_PATH)/../../Classes

LOCAL_WHOLE_STATIC_LIBRARIES := cocos2dx_static
LOCAL_WHOLE_STATIC_LIBRARIES += cocosdenshion_static
LOCAL_WHOLE_STATIC_LIBRARIES += box2d_static
LOCAL_WHOLE_STATIC_LIBRARIES += cocos_extensions_static    #添加cocos_extension 静态库
LOCAL_WHOLE_STATIC_LIBRARIES += cocostudio_static          #添加cocostudio 静态库

include $(BUILD_SHARED_LIBRARY)

$(call import-module,2d)
$(call import-module,audio/android)
$(call import-module,Box2D)
$(call import-module,extensions)                    #导入 extensions 模块儿
$(call import-module,editor-support/cocostudio)     #导入 cocostudio 模块儿
```

OK了，如上所说在使用了其他的第三方库的时候，要在这个`Android.mk`文件中添加引用模块儿，这样在编译就OK了；
另外在项目中添加`cocostudio`以及`GUI`和`extensions`库可以看我的另一片文章：
[Cocos2d-x 3.x 引用CocoStudio等第三方库](http://www.melove.net/lzan13/cocos2d-x-import-library-774.html)

最近又看了下，好像3.x引擎版本新建的项目在`Android.mk`中已经包含了所有的模块儿，不过都是处于注释状态，要手动打开你需要的库就行了