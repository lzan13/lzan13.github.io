---
title: Android开发设置Activity全屏状态
date: 2013-05-29 22:39:37
categories:
  - Develop
  - Android
tags:
  - Android
  - Activity
  - Screen
id: 368
---

有时候我们要将软件设置为全屏状态，特别是在软件启动的时候，如果设置为全屏，会比带有状态栏启动要显得大方、美观！设置全屏的方式也有两种，一种是在软件启动最开始的时候，在`java`代码中设置，另一种是在xml配置文件中设置！

在java代码中设置的方法就是隐藏掉“状态栏”和“标题栏”！
```java
protected void onCreate(Bundle savedInstanceState) {
    //隐藏标题栏
    this.requestWindowFeature(Window.FEATURE_NO_TITLE);
    //隐藏状态栏
    this.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);
    //上边那两句代码，一定要放在设置显示界面之前（即下边这句代码之前）：
    setContentView(R.layout.startscreen);
}
```

上边这种设置`Activity`全屏的方式有一点瑕疵；就是在启动的瞬间会闪现一下状态栏和标题栏！
所以在xml配置文件中设置是最好的！即为`Activity`添加主题样式：
```xml
<activity android:name=".StartScreenActivity" android:label="@string/app_name"
    android:theme="@android:style/Theme.Light.NoTitleBar.Fullscreen">
    <!-- 其中Theme.Light.NoTitleBar.Fullscreen 为白色全屏主题； Theme.NoTitleBar.Fullscreen为系统色全屏主题 -->
    <intent -filter>
        <action android:name="android.intent.action.MAIN"></action>
        <category android:name="android.intent.category.LAUNCHER"></category>
    </intent>
</activity>
```