---
title: Android开发之google官方下拉刷新SwipeRefreshLayout的使用
categories:
  - Develop
  - Android
tags:
  - Android
  - SwipeRefreshLayout
id: 859
date: 2015-02-24 13:40:04
---

最近在写的一个软件要用到下拉刷新，说到下拉刷新，网上有很多的自定义样式，其实`google`已经出了官方的下拉刷新组建，而且更新过的更好看，是一个不停旋转的彩色缺口圆环，我很喜欢，所以打算用官方的下拉刷新，
首先要使用`SwipeRefreshLayout`，要在布局文件中使用它，而且它只能包含一个子控件，同时必须是可滚动的，比如`ScallView`、`ListView`、`GridView`等；
部分布局代码如下：
```xml
<android.support.v4.widget.SwipeRefreshLayout 
     android:id="@+id/ml_swipe_refresh_layout"
     android:layout_width="match_parent" 
     android:layout_height="match_parent">

    <ListView 
          android:id="@+id/ml_timeline_listview" 
          android:layout_width="match_parent"
          android:layout_height="match_parent">
    </ListView>
</android.support.v4.widget.SwipeRefreshLayout>
```
接着就是在java代码中去获得控件，并监听控件达到出发刷新的目的：
```java
// 初始化下拉刷新组件
private void initSwipeRefreshLayout() {
	mSwipeRefreshLayout.setProgressViewOffset(false, 0, 220);
	mSwipeRefreshLayout.setColorSchemeResources(R.color.ml_blue, R.color.ml_orange, R.color.ml_green, R.color.ml_red, R.color.ml_purple);
	mSwipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
		@Override 
		public void onRefresh() {
			mSwipeRefreshLayout.postDelayed(new Runnable() {
				@Override 
				public void run() {
					mSwipeRefreshLayout.setRefreshing(false);
				}
			},
			5000);
		}
	});
}
```
在上边的代码中主要调用了`SwipeRefreshLayout`的几个方法：
- `setProgressViewOffset(false, 0, 220);`设置刷新进度圈出现的方式、开始位置、结束位置，默认就是从顶部开始放大出现，但是距离太近，所以看了下`SwipeRefreshLayout`的源码，找到了一个属性`mCurrentTargetOffsetTop`,发现是可以自定义刷新进度的位置的；
- `setColorSchemeResources();`为进度圈设置颜色样式；可以设置多个
- `setOnRefreshListener();`给`SwipeRefreshLayout`控件添加监听器
- `setRefreshing();`停止刷新
其中在`OnRefreshListener的onRefresh()`方法中就是我们要实现的刷新需要做的一些事情，这里我用个计时器模拟刷新`5s`，然后关闭进度圈；
OK 设置完这些之后，就可以在应用中使用官方的下拉刷新了！