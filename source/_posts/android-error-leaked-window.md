---
title: Android开发之Activity com.avcit... has leaked window错误的理解，已解决！
categories:
  - Develop
  - Android
tags:
  - Android
  - Error
  - Dialog
  - PopupWindow
date: 2013-02-20 18:12:46
id: 1361355166000
comments: true
---

`logcat`错误提示：
```Java
Activity com.avcit.conference.MainActivity has leaked window android.widget.PopupWindow$PopupViewContainer@406dfc10 that was originally added here  
android.view.WindowLeaked: Activity com.avcit.conference.MainActivity has leaked window android.widget.PopupWindow$PopupViewContainer@406dfc10 that was originally added here  
at android.view.ViewRoot.<init>(ViewRoot.java:258)  
at android.view.WindowManagerImpl.addView(WindowManagerImpl.java:148)  
at android.view.WindowManagerImpl.addView(WindowManagerImpl.java:91)  
at android.view.Window$LocalWindowManager.addView(Window.java:424)  
at android.widget.PopupWindow.invokePopup(PopupWindow.java:907)  
at android.widget.PopupWindow.showAtLocation(PopupWindow.java:767)  
at com.avcit.conference.MainActivity.alertExitDialog(MainActivity.java:352)  
at com.avcit.conference.MainActivity$1.onReceive(MainActivity.java:110)  
at android.app.LoadedApk$ReceiverDispatcher$Args.run(LoadedApk.java:709)  
at android.os.Handler.handleCallback(Handler.java:587)  
at android.os.Handler.dispatchMessage(Handler.java:92)  
at android.os.Looper.loop(Looper.java:130)  
at android.app.ActivityThread.main(ActivityThread.java:3683)  
at java.lang.reflect.Method.invokeNative(Native Method)  
at java.lang.reflect.Method.invoke(Method.java:507)  
at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:839)  
at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:597)  
at dalvik.system.NativeStart.main(Native Method)
```
经过分析和多次的测试，发现`dialog`和`popupwindow`都会出现的，网上有好多的说这是因为`dialog`的没有关闭造成的，在`activity.finish()`之前关闭就行了，但是我这是一个特殊情况，这个`dialog`是在广播中打开的，调用dialog的dismiss方法是没有效果的，就是说dialog不要再广播中`show()`

暂时还没解决在广播中关闭`dialog`！！！

我的解决方法：
在`finish()`的时候，不适用`finish()`方法，使用`System.exit(0);`的方式！
可能这不是最好的方法，但是既然没有异常出现，应该就可以了！