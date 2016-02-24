---
title: Cocos2d-Html5开发获取Scene并添加切换场景动画
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - Cocos2d-Html5
  - Scene
id: 10035
date: 2014-05-23 10:12:17
---

最近在使用牛人`touchsnow`的看开发的插件开发游戏，关于[CocosEditor官方博客](http://blog.makeapp.co/)
做游戏切换时肯定要加入一些场景切换动画，不过在`Cocos2d-Html5`切换场景的时候需要传入场景变量参数，但是当时使用的时`CocosEditor`插件开发，这个每一个`scene`就是一个文件，不知道怎么获得场景的实例，在群里小小黄的帮助下，参考了下`main.js`文件重的方法实现了获取`scene`的实例，可以顺利的使用场景切换动画了：
```javascript
cc.BuilderReader.runScene = function (module, name) {
    var director = cc.Director.getInstance();
    var scene = cc.BuilderReader.loadAsSceneFrom(module, name);
    var runningScene = director.getRunningScene();
    if (runningScene === null) {
        //cc.log("runWithScene");
        director.runWithScene(scene);
    }
    else {
        //cc.log("replaceScene");
        director.replaceScene(scene);
    }
}
```
根据这个runScene方法可以发现，是通过布局文件的名字获取的场景实例，这样就可以根据下边的那些切换动画来实现自己想要的效果了

### 常用动画
在网上找了下有一些切换场景的动画：不过好像没有从上边滑动的；
```javascript
1，cc.TransitionCrossFade.create(t,scene)   // 交叉消失两个场景使用cc.RenderTexture对象。
2，cc.TransitionFad.create(t,scene,color)  // 淡出即将离任的场景,然后消失在传入的场景。 
3，cc.TransitionFadeBL.create(t, scene)  // 向左下波浪退出
4，cc.TransitionFadeDown.create(t, scene)  // 向下百叶窗式换场景
5，cc.TransitionFadeUp.create(t, scene)  // 向上百叶窗式换场景
6，cc.TransitionJumpZoom.create(t, scene)   // 跳跃式替换，场景缩小，再加载进来
7，cc.TransitionMoveInB.create(t,scene)  // 创建一个在底部，覆盖当前场景
8，cc.TransitionMoveInR.create(t,scene)  // 创建一个在右边，覆盖当前场景
9，cc.TransitionMoveInL.create(t,scene)  // 创建一个在左边，覆盖当前场景
10 cc.TransitionPageTurn.create(t,scene, backwards)  // 前翻页式场景替换
11，cc.TransitionRadialCW.create(t,scene)  // 逆时针切入
12，cc.TransitionRotoZoom.create(t,scene)  // 转换角度替换
13，cc.TransitionFlipAngular.create(t,scene, o)  // 按一定角度左翻
14，cc.TransitionFlipX.create(t, scene,o)  // X轴左边翻换
15，cc.TransitionFlipY.create(t,scene, o)  // Y轴左边翻换
16，cc.TransitionZoomFlipAngular.create(t,scene, o)   // 带有缩放效果，有角度的转左翻
17，cc.TransitionZoomFlipX.create(t,scene, o)   // 带有缩放效果，在X轴左翻
18，cc.TransitionZoomFlipY.create(t,scene, o)   // 带有缩放效果，左Y轴左翻
19，cc.TransitionShrinkGrow.create(t,scene) //交叉着替换场景
20，cc.TransitionSlideInB.create(t,scene) //场景有底部进入，
```

### 例子
下边就是我实现的一个向左推动的动画的方法：
```javascript
StartLayer.prototype.onStartGameClicked = function () {
    var director = cc.Director.getInstance();
    var scene = cc.BuilderReader.loadAsSceneFrom("", "MainLayer");
    var moveScene = cc.TransitionSlideInR.create(0.5, scene);
    director.replaceScene(moveScene);
}
```