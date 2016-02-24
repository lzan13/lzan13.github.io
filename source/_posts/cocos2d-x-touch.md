---
title: Cocos2d-x 3.x添加触摸事件监测
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - Touch
id: 10044
date: 2014-05-02 17:19:01
---

在使用`Cocos2d-x`给游戏添加触摸事件检测时，遇到了一些问题，特别注意的一些就是`Cocos2d-x 2.x`和`3.x`的版本是有差别的，比如`3.x`的`Cocos2d-x`的类名取消了前边的`CC`前缀，`3.0`版本添加触摸事件好像很灵活，可以为单个的精灵进行设置`onToucheBegan`等事件
```C++
virtual bool onTouchBegan(cocos2d::CCTouch *touch, cocos2d::CCEvent *event);
virtual void onTouchMoved(cocos2d::CCTouch *touch, cocos2d::CCEvent *event);
virtual void onTouchEnded(cocos2d::CCTouch *touch, cocos2d::CCEvent *event);
```

然后就是要在`cpp`类文件中去实现重载的方法，以及添加事件监听器
```C++
void MainScene::initTouchEvent(){
	//初始化触摸事件监听器
	auto listener = EventListenerTouchOneByOne::create();
	//设置触摸事件是否向下传递
	listener->setSwallowTouches(true);
	//添加触摸监听器的回调函数
	listener->onTouchBegan = CC_CALLBACK_2(MainScene::onTouchBegan, this);
	listener->onTouchMoved = CC_CALLBACK_2(MainScene::onTouchBegan, this);
	listener->onTouchEnded = CC_CALLBACK_2(MainScene::onTouchEnded, this);
	//将触摸监听添加到eventDispacher中去
	_eventDispatcher->addEventListenerWithSceneGraphPriority(listener, startMenu);
}

//重写触摸方法
bool HelloTZFE::ccTouchBegan(cocos2d::CCTouch *touch, cocos2d::CCEvent *event){

	auto touchPoint = touch->getLocation();
	touch->getLocationInView();
	startX = touchPoint.x;
	startY = touchPoint.y;
	return true;
}
```
只要在`onTouch`等方法中写上处理代码就OK了

还有一种方法就是可以将方法直接写在`Listener->onTouchEvent`后
```C++
listener->onTouchBegan = [](Touch* touch, Event* event){
	//相关处理代码...
	//在处理代码少的情况下，可以省去新建回调函数的麻烦
}
```