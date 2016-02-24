---
title: Cocos2d-x 3.x调用重力加速器Acceleration
categories:
  - Develop
  - Cocos2d-x
tags:
  - Acceleration
  - Cocos2d-x
  - OpenGL
id: 10036
date: 2014-06-20 16:00:32
---

今天解决了一个问题心情很爽 哈哈；

今天解决的就是`Cocos2d-x 3.x`调用重力加速器的问题，网上搜了很多的资料 发现都是不行的，不能解决问题，不知道是不是我使用的是3.0 的版本问题，但是网上有些教程说的也是`3.0`的版本啊，难道`beta`版和正式版也有那么大差别么？坑爹啊，

就说这个重写的方法吧：网上搜索的到好多都是重写父类的`didAccelerate(CCAcceleration* pAccelerationValue);`方法，但是我用的`3.0`根本就没有了这个方法，搜索的`3.0`还是有说这个，蛋疼；

最后在看官方的`testCpp`例子的时候发现了现在调用重力加速器的方法；例子的位置`cocos2d-xtestscpp-testsClassesAccelerometerTest`其中要重写的方法为`onAcceleration(Acceleration* acc, Event* event);`TMD差别好大有么有；

`Cocos2d-x每`个版本都有变化有么有啊，坑爹……

不过坑爹归坑爹，引擎还是不错的，只能说咱学的不好吧；

OK 废话太多了，开始说正事；

### 解决办法 .h 头文件
既然找到了正确的方法，那就好解决了，首先是在.h头文件中定义好方法
```C++
#include "cocos2d.h"

class GameScene : public cocos2d::Layer
{
public:
    static cocos2d::Scene* createScene();
    virtual bool init();   
    //重写的父类的方法，用来处理重力加速器方法     
    void onAcceleration(cocos2d::Acceleration* acc, cocos2d::Event* event);
    CREATE_FUNC(GameScene);
};
```
### 解决办法 .cpp 实现
接着就是在cpp文件中去实现这个方法就OK了
```C++
//重写重力加速器方法
void GameScene::onAcceleration(Acceleration* acc, Event* event){
auto director = Director::getInstance();
if(img == NULL){
return;
}

auto imgSize = img->getContentSize();
auto imgPosition = img->getPosition();
auto imgX = img->getPositionX();
auto imgY = img->getPositionY();

imgX += acc->x * gravityValue;
//imgY += acc->y * gravityValue;
//auto imgTemp = director->convertToUI(imgPosition);

//imgTemp.x += acc->x * gravityValue;
//imgTemp.y -= acc->y * gravityValue;
//auto imgNext = director->convertToGL(imgTemp);

FIX_POS(imgX, (origin.x + imgSize.width/2), (origin.x + visibleSize.width - imgSize.width/2));
FIX_POS(imgY, (origin.y + + imgSize.height/2), (origin.y + visibleSize.height - imgSize.height/2));
img->setPosition(Point(imgX, imgY));

log("onAcceleration: acc->x: %f, acc->y: %f, acc->z: %f, imgX: %f, imgY: %f", 
acc->x, acc->y, acc->z, imgX, imgY);
}
```

可以看到其中有我注释掉的一部分代码，这是本来官方示例文件中的根据`屏幕坐标系`以及`OpenGL坐标系`互相转换的方法，将例子中精灵的坐标互相转换，然后进行设置精灵坐标

其实后来根据输出`log`测试，我发现`Cocos2d-x`的默认的`getPosition()`获取的坐标是和`OpenGL`的坐标是一致的，所以最后我就注释掉了例子中的转化方式，直接根据坐标点来设置精灵位置，这样是和经过转化的一样的效果的；

下边我画了几张图来简单的说明一下`OpenGL`坐标系，屏幕`UI`坐标系
首先这个立方的 是我绘制的`OpenGL`的重力加速器的三维空间坐标系，旁边的蓝色表示一个手机平放时与坐标系的对应情况；根据图中箭头的方向，`x、y、z`三个坐标轴的值都是趋向于`-1`的；
[![openGL_coordinate](http://lzan13.qiniudn.com/blog/uploads/images/2014/06/openGL_coordinate.jpg)](http://lzan13.qiniudn.com/blog/uploads/images/2014/06/openGL_coordinate.jpg)

下面这两个是平面的`UI`坐标系和`OpenGL`坐标系
`UI`坐标系的坐标原点是在左上角，`Y`轴向下延伸，其中精灵`A`位置为`(10,10)`
[![ui_coordinate](http://lzan13.qiniudn.com/blog/uploads/images/2014/06/ui_coordinate.jpg)](http://lzan13.qiniudn.com/blog/uploads/images/2014/06/ui_coordinate.jpg)

[![gl_coordinate](http://lzan13.qiniudn.com/blog/uploads/images/2014/06/gl_coordinate.jpg)](http://lzan13.qiniudn.com/blog/uploads/images/2014/06/gl_coordinate.jpg)
`OpenGL`坐标系的原点是在左下角，`Y`轴向上延伸，精灵`B(相当于UI坐标戏中的精灵A)`位置为`(10, 1280)`(屏幕分辨率为720*1280时)
这就是在官方`Test`例子中`UI`坐标系和`OpenGL`坐标系为什么要进行转换

`Cocos2d-x`中的默认坐标系是和`OpenGL`的坐标系是相同的，所以我直接根据`getPosition()`方式获取了精灵的位置，这样就少去了复杂的转换，不过既然官方进行转换可能有他们的意图吧，现在也不用深究了；

OK继续垒码了