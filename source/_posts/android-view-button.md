---
title: Android开发控件详解之Button
categories:
  - Develop
  - Android
tags:
  - Android
  - Button
id: 10021
date: 2013-02-24 19:35:03
---

今天是第一天，简单点儿开始，先弄`android`的控件和布局！先说`Button`！
在android中布局一般都是用xml文件来定义的：
```xml
<LinearLayout xmlns:android ="http://schemas.android.com/apk/res/android"
    xmlns:tools= "http://schemas.android.com/tools"
    android:layout_width= "match_parent"
    android:layout_height= "match_parent"
    android:orientation= "vertical" >
    <button android:id= "@+id/id_1"
        android:layout_width= "wrap_content"
        android:layout_height ="wrap_content"
        android:text ="这是一个普通Button"/>
    <button android:id= "@+id/id_2"
        android:layout_width= "wrap_content"
        android:layout_height ="wrap_content"
        android:background= "@drawable/button_nor"
        android:padding= "10dp"
        android:layout_margin ="10dp"
        android:text ="定义背景图的Button"/>
    <button android:id= "@+id/id_3"
        android:layout_width= "wrap_content"
        android:layout_height ="wrap_content"
        android:background= " @drawable/button_bg_1"
        android:padding= "10dp"
        android:layout_margin ="10dp"
        android:text ="用xml定义背景Button"/>
```
上边的xml布局文件定义了三个button按钮
第一个是普通的，第二个是定义了背景图的，第三个是使用xml定义背景的按钮，
[![Image](http://lzan13.qiniudn.com/blog/uploads/images/2013/02/Image.png)](http://lzan13.qiniudn.com/blog/uploads/images/2013/02/Image.png)
使用xml定义按钮的背景，可以实现按下改变背景"button_bg_1.xml"按下效果就是这样！下面是xml定义button背景的代码
[![Image-2](http://lzan13.qiniudn.com/blog/uploads/images/2013/02/Image-2.png)](http://lzan13.qiniudn.com/blog/uploads/images/2013/02/Image-2.png)
```xml
<?xml version= "1.0" encoding ="utf-8"?>
<selector xmlns:android ="http://schemas.android.com/apk/res/android" >
    <item android:drawable= "@drawable/button_press" android:state_pressed ="true" />
    <item android:drawable= "@drawable/button_nor" android:state_pressed ="false" />
```
下面说说button的点击事件，首先要先获得Button控件
```java
public class MainActivity extends Activity {
      private Button but1;
      private Button but2;
      private Button but3;
      @Override
      protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
          setContentView(R.layout. activity_main);
           // 通过ID获得Button控件
           but1 = (Button) findViewById(R.id. id_1);
           but2 = (Button) findViewById(R.id. id_2);
           but3 = (Button) findViewById(R.id. id_3);
           // 设置button的点击事件
           but1.setOnClickListener( listener);
           but2.setOnClickListener( listener);
           but3.setOnClickListener( listener);
     }
      // 实现button点击事件方法；
      private OnClickListener listener = new OnClickListener() {
           @Override
           public void onClick(View v) {

              Button but = (Button) v;
              String str = "";
               switch (but.getId()) {
               case R.id. id_1:
                   str = "我是普通按钮" ;
                    break;
               case R.id. id_2:
                   str = "我有背景图" ;
                    break;
               case R.id. id_3:
                   str = "点击我，我的背景图会改变" ;
                    break;
              }
              // Toast弹出信息
              Toast. makeText(MainActivity. this, str, Toast.LENGTH_LONG ).show();
          }
     };
}
```
最后说下Button的一些属性设置
```java
    // 设置but3不可点击；
    //but3.setClickable(false);
    // 设置but3不可激活，
    //but2.setEnabled(false);
```
同时xml文件要设置enabled状态
```xml
<item android:drawable= "@drawable/button_press" android:state_enabled ="false />
```
就简单写这些吧