---
title: Cocos2d-Html5调用的Accelerometer来使用重力感应
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - Cocos2d-Html5
  - CocosEditor
  - Accelerometer
id: 705
date: 2014-05-21 11:16:32
---

在使用大牛`touchsnow`开发的插件`Cocoseditor`开发游戏时遇到了一些问题，然后就试着解决，最近想试下`coocs2d-html5`能否使用重力感应，发现是可以的，不过这个只能在移动真机上测试，电脑上的模拟器是不行的，
首先需要在`onDidloadFromCCB()`方法内设置可以使用`Accelerometer`：
```javascript
this.rootNode.onAccelerometer = function (event) {
    this.controller.onAccelerometer(event);
};
cc.log("sys.os: " + sys.os);
if(sys.os == "android" || sys.os == "ios"){
    this.rootNode.setAccelerometerEnabled(true);
}
```
这样就说明可以使用加速器了，
然后就是要重写onAccelerometer();方法
```javascript
MainLayer.prototype.onAccelerometer = function (event) {
    cc.log("event: x." + event.x + "; y." + event.y + "; z." + event.z);
}
```

经过真机测试可以输出x、y、z坐标，OK