---
title: Android设置OnTouchListener监听Button的onDown与onUp
categories:
  - Develop
  - Android
tags:
  - Android
  - Button
  - OnTouchListener
id: 10020
date: 2014-03-25 23:43:52
---

`Android`中的`Button`有个很简单的监听事件方法，只要给`Button`设置上`OnClickListener`事件就行了，这个只要写`Button`控件一般都会用到此方法，不然你的`button`就是个摆设，不过这种设置方法只能监听`Button`的点击事件，并不能去监听按钮的按下和提起，本来以为要自定义`Button`才能监听，发现`Button`控件 有个`setOnTouchListener(OnTouchListener listener)`方法，然后重写`onTouch()`方法，自己去判断是`onDown`、`onUp`、`onMove`事件，
```java
private OnTouchListener butTouchListener= new OnTouchListener() {
	/**
	 * 要自己重写onTouch()方法，具体事件都是在这里边处理，
	 * 但要记得在判断过MotionEvent的事件之后要正确的处理返回值，不然点击事件会直接向下传递，
	 * 就监听不到按下和提起事件了，以及按下的效果也都会显示不出来
	 */
	@Override
	public boolean onTouch(View v, MotionEvent event) {
		// TODO Auto-generated method stub
		switch (event.getAction()) {
		case MotionEvent.ACTION_DOWN:
		//这里填写你按下要处理的事情，这里要返回true才能触发提起事件
			return true;
		case MotionEvent.ACTION_MOVE:
		//这里一般留空，
			return false;
		case MotionEvent.ACTION_UP:
		//这里写上按钮提起时处理的事情，这里要返回true才能触发提起事件
			return true;
		}
		return false;
	}
}
```
然后只要给`Button.setOnTouchListener(butTouchListener);`这样就可以监听按钮的提起和按下了，其中的`Action_MOVE return false`也是必须的，不然会看不到按钮的按下效果
如果是自定义控件的话这个监听就更灵活了，现在先不说自定义控件了！