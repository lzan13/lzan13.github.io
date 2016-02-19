---
title: Cocos2d-x 3.x 开发之让C++通过jni调用java中的方法为后续添加第三方sdk做准备
categories:
  - Develop
  - Cocos2d-x
tags:
  - Android
  - Cocos2d-x
  - Jni
id: 782
date: 2014-07-23 17:54:47
---

`Cocos2d-x`是用`C++`写的引擎`废话`，可以跨平台`废话`，既然可以跨平台那么我们肯定会移植到特定的平台上，比如我就是做`Android`开发的，所以我做的游戏大多都要移植到`Android`平台，以后或许会开发`ios`平台，那都是后话了；

移植到`Android`平台后有时需要进行底层和上层的交互，比如为`Cocos2d-x`开发的`Android`游戏添加广告、分享等功能时，需要用到第三方`sdk`，这就要让`C++`和`java`代码进行互相调用了，`google`是有提供jni接口来实现他们的互相调用的；
这里有一篇文章[Cocos2d-x 3.x 开发之添加游戏截图分享到新浪微博](http://melove.net/lzan13/cocos2d-x-share-sina-weibo-787.html)

废话不多说，开始办正事：
我的开发环境：

    VS：2012
    Eclipse：3.7.1
    Jdk：1.7
    SDK：23.0
    Python：2.7.6
    Cocos2d-x：3.2

### 准备工作
要使用`jni`，就要在`cpp`类中引入`jni`的包，但是在`VS`中使用`#include <jni>`时提示找不到`jni`库文件，这让我很是蛋疼，最后搜索了好久终于找到了解决办法：

    `右击项目`->`属性`->`配置属性`->`VC++目录`->`包含目录`：

在这个选项内添加你的`jdk`下的`include`目录和`include`下的`win32`目录，然后就OK了
[![image_jni_h](http://wp-melove.qiniudn.com/blogimg/2014/07/image_jni_h.png)](http://wp-melove.qiniudn.com/blogimg/2014/07/image_jni_h.png)

### 定义方法
解决了这个问题就可以继续了：
首先创建一个`MyJniFunc.h`文件，并在引入库文件时添加平台判断代码，只有在`Android`平台下在引入`jni`库，因为`jni`毕竟是只有`Android`才用的玩意：
```c++
#if(CC_TARGET_PLATFORM == CC_PLATFORM_ANDROID)
#include <jni .h>
#include "platform/android/jni/JniHelper.h"
#endif
#include "cocos2d.h"

USING_NS_CC;

class MyJniFunc {
public:
    static void testJniMethod(int i);
};
```

### 在C++中实现方法
接着就是在cpp文件中实现所定义的方法：
```C++
#include "MyJniFunc.h"

USING_NS_CC;

void MyJniFunc::testJniMethod(int i){
#if(CC_TARGET_PLATFORM == CC_PLATFORM_WIN32)
    log("win32 is not use jni!");
#endif

#if(CC_TARGET_PLATFORM == CC_PLATFORM_ANDROID)
    log("testJniMethod %d", i);
    JniMethodInfo methodInfo;
    bool isHave = JniHelper::getStaticMethodInfo(methodInfo, "com.lzan13.game.hello.HelloActivity", "testJniMethod", "(I)V");
    if(isHave){
        methodInfo.env->CallStaticVoidMethod(methodInfo.classID, methodInfo.methodID, i);
    }
#endif
}
```
其中`getStaticMethodInfo()`就是获得你要调用的`java`类中的静态方法，其中有四个参数：
> 1、JniMethodInfo：就是保存方法信息
> 2、你要调用的方法所在的类的完全路径（包名+类名）
> 3、方法名
> 4、方法需要的参数类型和返回值类型(可以传递多种参数类型)
           
### Jni和Java数据类型对应关系
下边列出`jni`和`java`的数据类型对应关系：

| jni类型签名        | jni的数据类型 | java的数据类型 |
| ------------------ |:------------- |:-------------- |
| V                  | void          | void           |
| Z                  | jboolean      | boolean        |
| I                  | jint          | int            |
| J                  | jlong         | long           |
| D                  | jdouble       | double         |
| F                  | jfloat        | float          |
| B                  | jbyte         | byte           |
| C                  | jchar         | char           |
| S                  | jshort        | short          |
| Ljava/long/String; | jstring       | String         |

这里边`String`是个比较特殊的类型，他的写法是前边以`L`开头，后边以`;`结尾，中间加上以`/`分割的包名
结合这个对照表就可以看出我调用的`java`方法是传递了一个`int`类型的参数，返回值为`void`
如果要传递多个参数，可以直接写在一起，比如`(ILjava/long/String;)V`就是传递一个`int`和一个`String`的参数，返回为`void`；

### 定义Java方法
接着就是要在`java`中定义好在`C++`中要调用的方法：
```c++
public static void testJniMethod(int i) {
     switch (i) {
     case 0:
          //判断传进来的参数
          Log.i("lzan13", "test jni method!");
          break;
     }
}
```
然后就是 编译运行，要在手机上测试，不然`win32`是不会调用这段代码的！看到log输出信息就说明调用成功了！