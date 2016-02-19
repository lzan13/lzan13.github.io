---
title: Android判断设备网络连接状态并判断连接方式
categories:
  - Develop
  - Android
tags:
  - Android
  - NewWork
  - WIFI
id: 348
date: 2013-05-20 23:00:13
---

现在是互联网高速发展的时代，`Android`开发过程中，对于一个需要连接网络的`Android`设备，对设备的网络状态检测是很有必要的！好多的App都需要连接网络，所以抽时间就写了一个检测`Android`设备网络连接状态的`demo`

这个小例子可以判断设备是否已经连接网络，并且在连接网络的状态下可以判断是`WIFI`无线连接还是`GPRS`手机网络连接，这样就可以在不同的网络连接下去调用不同的方法，处理不同的事情，比如一个有下载功能的`app`可以判断只有当`WIFI`连接的是后去下载文件，`GPRS`流量连接则不下载！

在没有连接的网络的情况下会弹出一个对话框，让用户选择是否去设置网络连接！

[![wifistate](http://wp-melove.qiniudn.com/blogimg/2013/05/wifi-state.png)](http://wp-melove.qiniudn.com/blogimg/2013/05/wifi-state.png)

贴一下主要代码： 
```java
/**
* 检测网络是否连接
* @return
*/
boolean checkNetworkState() {
	boolean flag = false;
	//得到网络连接信息
	manager = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
	//去进行判断网络是否连接
	if (manager.getActiveNetworkInfo() != null) {
		flag = manager.getActiveNetworkInfo().isAvailable();
	}
	if (!flag) {
		setNetwork();
	} else {
		isNetworkAvailable();
	}
	return flag;
}

/**
 * 网络未连接时，调用设置方法
 */
private void setNetwork(){
	Toast.makeText(this, "wifi is closed!", Toast.LENGTH_SHORT).show();
	AlertDialog.Builder builder = new AlertDialog.Builder(this);
	builder.setIcon(R.drawable.ic_launcher);
	builder.setTitle("网络提示信息");
	builder.setMessage("网络不可用，如果继续，请先设置网络！");
	builder.setPositiveButton("设置", new OnClickListener() {
		@Override
		public void onClick(DialogInterface dialog, int which) {
			Intent intent = null;
			/**
			 * 判断手机系统的版本！如果API大于10 就是3.0+
			 * 因为3.0以上的版本的设置和3.0以下的设置不一样，调用的方法不同
			 */
			if (android.os.Build.VERSION.SDK_INT > 10) {
				intent = new Intent(android.provider.Settings.ACTION_WIFI_SETTINGS);
			} else {
				intent = new Intent();
				ComponentName component = new ComponentName(
						"com.android.settings",
						"com.android.settings.WirelessSettings");
					intent.setComponent(component);
					intent.setAction("android.intent.action.VIEW");
				}
				startActivity(intent);
			}
		});

	builder.setNegativeButton("取消", new OnClickListener() {
		@Override
		public void onClick(DialogInterface dialog, int which) {
		}
	});
	builder.create();
	builder.show();
}

/**
 * 网络已经连接，然后去判断是wifi连接还是GPRS连接
 * 设置一些自己的逻辑调用
 */
private void isNetworkAvailable(){
	State gprs = manager.getNetworkInfo(ConnectivityManager.TYPE_MOBILE).getState();
    State wifi = manager.getNetworkInfo(ConnectivityManager.TYPE_WIFI).getState();
    if(gprs == State.CONNECTED || gprs == State.CONNECTING){
    	Toast.makeText(this, "wifi is open! gprs", Toast.LENGTH_SHORT).show();
    }
    //判断为wifi状态下才加载广告，如果是GPRS手机网络则不加载！
    if(wifi == State.CONNECTED || wifi == State.CONNECTING){
    	Toast.makeText(this, "wifi is open! wifi", Toast.LENGTH_SHORT).show();
    	loadAdmob();
    }
}

/**
 * 在wifi状态下 加载admob广告
 */
private void loadAdmob(){
	ll = (LinearLayout) findViewById(R.id.load_ads);
	ll.removeAllViews();
	adsView = new AdView(this, AdSize.BANNER, "a15194a1ac9505d");
	ll.addView(adsView);
	adsView.loadAd(new AdRequest());
}
```
我这里为了能明确的说明`WIFI`和`GPRS`连接的不同情况，设置了`WIFI`连接下加载一个`admob`的广告条，`GPRS`下不去加载广告，这也算是在实际的开发中提升用户体验的一个途径吧！ 

代码稍后放到github:[检测网络连接demo](https://github.com/lzan13/lzan13_wifi)