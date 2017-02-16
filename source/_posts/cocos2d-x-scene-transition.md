---
title: Cocos2d-x 3.x 切换场景动画Transition整理
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - Scene
  - Transiion
date: 2014-06-18 11:22:17
id: 1403061737000
comments: true
---

在做游戏的时候一般都是有多个场景的，比如游戏的`Start`、`Menu`、`Setting`、`Loading`等，都是一个个的`场景(Scene)`； 我们选择不同的功能就会切换跳转到不同的场景，如果是干巴巴的切换未免不雅，`Cocos2d-x`引擎提供了多种的切换过渡效果，这里整理贴出来，方便自己以后的查找

值得提醒一句，`3.x`和`2.x`的版本游戏的地方是不一样的，比如那几个在`2.x`中有三个参数的过渡效果在`3.x`中找不到了，只剩下两个参数

下边这个是我的一个返回主`MainScene`的方法：
```C++
void GameScene::backMainSceneCallBack(){
     auto director = Director::getInstance();
     float delayTime = 2.0f;
     auto scene = MainScene::createScene();
     auto slidTran = TransitionPageTurn::create(delayTime, scene, true);
     director->replaceScene(slidTran);

}
```

### 收集的一些常用过度效果
```C++
/**
* 这些过渡效果的前两个参数都是一样的：切换所用时间delayTime、要切换到的场景Scene
*/
//     创建一个跳动的过渡动画
//     tranScene = TransitionJumpZoom::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个扇形条形式的过渡动画， 逆时针方向
//     tranScene = TransitionProgressRadialCCW::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个扇形条形式的过渡动画， 顺时针方向
//     tranScene = TransitionProgressRadialCW::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个水平条形式的过渡动画，
//     tranScene = TransitionProgressHorizontal ::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个垂直条形式的过渡动画，
//     tranScene = TransitionProgressVertical::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个由里向外扩展的过渡动画，
//     tranScene = TransitionProgressInOut::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个由外向里扩展的过渡动画，
//     tranScene = TransitionProgressOutIn::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个逐渐透明的过渡动画
//     tranScene = TransitionCrossFade::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个翻页的过渡动画
//     参数3：是否逆向翻页：true为翻过来，false 翻过去
//     tranScene = TransitionPageTurn::create(delayTime, scene, false);
//     director->replaceScene(tranScene);
//     创建一个部落格过渡动画， 从左下到右上
//     tranScene =TransitionFadeTR::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个部落格过渡动画， 从右上到左下
//     tranScene = TransitionFadeBL::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个从下到上，条形折叠的过渡动画
//     tranScene= TransitionFadeUp::create(delayTime, scene);
//     director->replaceScene(s);
//     创建一个从上到下，条形折叠的过渡动画
//     tranScene = TransitionFadeDown::create(delayTime, scene);
//     director->replaceScene(tranScene)
//     创建一个随机方格消失的过渡动画
//     tranScene= TransitionTurnOffTiles::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个分行划分切换的过渡动画
//     tranScene = TransitionSplitRows::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个分列划分切换的过渡动画
//     tranScene = TransitionSplitCols::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个逐渐过渡到目标颜色的切换动画
//     参数3：目标颜色
//     tranScene= TransitionFade::create(delayTime, scene, ccc3(255, 0, 0));
//     director->replaceScene(tranScene);
//     创建一个X轴反转的切换动画
//     tranScene  = TransitionFlipX::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个Y轴反转的切换动画
//     tranScene = TransitionFlipY::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     TransitionZoomFlipX
//     创建一个带有缩放的X轴反转切换的动画
//     tranScene=TransitionZoomFlipX::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个带有缩放的Y轴反转切换的动画
//     tranScene=TransitionZoomFlipY::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个带有反转角切换动画
//     tranScene = TransitionFlipAngular::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个带有缩放 ，反转角切换的动画
//     tranScene=TransitionZoomFlipAngular::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个放缩交替的过渡动画
//     tranScene = TransitionShrinkGrow::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个旋转放缩交替的过渡动画
//     tranScene = TransitionRotoZoom::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个从左边推入覆盖的过渡动画
//     tranScene = TransitionMoveInL::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个从右边推入覆盖的过渡动画
//     tranScene = TransitionMoveInR::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个从下边推入覆盖的过渡动画
//     tranScene = TransitionMoveInB::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个从上边推入覆盖的过渡动画
//     tranScene = TransitionMoveInT::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个从左侧推入并顶出旧场景的过渡动画
//     tranScene  =TransitionSlideInL::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个从右侧推入并顶出旧场景的过渡动画
//     tranScene  =TransitionSlideInR::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个从顶部推入并顶出旧场景的过渡动画
//     tranScene  =TransitionSlideInT::create(delayTime, scene);
//     director->replaceScene(tranScene);
//     创建一个从下部推入并顶出旧场景的过渡动画
//     tranScene  =TransitionSlideInB::create(delayTime, scene);
//     director->replaceScene(tranScene);
```
OK 至此`Cocos2d-x 3.x`场景切换的方法就记录完了，开始继续垒码