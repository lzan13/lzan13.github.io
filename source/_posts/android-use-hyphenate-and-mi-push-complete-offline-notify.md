---
title: 使用环信3.xSDK集成小米推送实现消息以及通话时的离线通知
categories:
  - Develop
tags:
  - Android
  - Call
  - Easemob
  - Hyphenate
  - MIPush
  - Offline
  - Message
id: 1493976924000
comments: true
date: 2017-05-05 17:35:24
---
>项目源码：[lzan13](https://github.com/lzan13) / [VMChatDemoCall](https://github.com/lzan13/VMChatDemoCall)
>简书博客：[lzan13](https://jianshu.com/) / []()

### 前言
集成聊天，目的无非就是为了让两个人能够方便的沟通，但是有时我们会退到后台，kill 程序，这时候别人再发送消息就收不到了，以及有些用户还想在呼叫通话时也能通知对方，打开 app 接听通话，这些都因为 app 被杀死而实现不了；还好有推送服务可用，苹果有`apns`，虽然`Android`有`gcm`， 但是在国内也用不了，因此就催生了各种各样的第三方推送，环信这边根据市场设备情况，选择了两家推送集成，在国内可以集成小米和华为推送，这样可以保证在他们自家的设备上实现离线消息通知；

>PS：为什么不用极光，因为极光并不是国内那些 rom 厂商自家的推送服务，他们不允许第三方的服务器在自家设备的后台撒欢
>PS：经测试小米的多报名在推送上不好使，不知道现在怎么样，如果有使用多包名测试不行的情况，可以重新创建应用试下

废话不多说，今天就通过集成最新的小米推送来实现下消息的离线推送通知，以及被呼叫方离线时方推送提醒对方启动 app 接听通话；其实都是通过集成推送完成！

### 准备工作
首先你的项目需要集成环信 sdk，并且已经实现了发送消息以及音视频通话功能（这个可以直接用我上边 github 上的项目）；
然后你需要有小米的开发者账户，需要创建一个应用，包名要和你自己的项目一样，然后需要用到的就是应用的`appId`、`appKey`、`appSecret`，这些在环信开发者后台上传小米证书，以及在项目中初始化小米推送需要用到；

### 开始集成
首先这边先把证书弄好了，证书的名字和秘钥以及包名一定要对应：
![QQ20170505-162024.png](http://upload-images.jianshu.io/upload_images/672606-c5449b5706f8eaa7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
然后需要做的就是在代码中集成小米推送，需要做的有两个地方：
1. 在初始化 sdk 的时候调用 options 设置小米的 appId 和 appKey
2. 在 AndroidManifest配置文件配置相应的权限和广播接收器以及服务

```java
    /**
     * 初始化环信sdk，并做一些注册监听的操作，这里把其他的处理都去掉了只写了小米推送
     */
    private void initHyphenate() {
        // 初始化sdk的一些配置
        EMOptions options = new EMOptions();
        // 设置小米推送 appID 和 appKey
        options.setMipushConfig("2882303761517573806", "5981757315806");
        // 初始化环信SDK,一定要先调用init()
        EMClient.getInstance().init(context, options);
        // 开启 debug 模式
        EMClient.getInstance().setDebugMode(true);
    }
```
然后就是`AndroidManifest`配置
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.vmloft.develop.app.demo.call">

    <!-- 项目权限配置 -->
    <!--小米推送相关权限-->
    <permission
        android:name="com.vmloft.develop.app.demo.call.permission.MIPUSH_RECEIVE"
        android:protectionLevel="signature"/>
    <uses-permission android:name="com.vmloft.develop.app.demo.call.permission.MIPUSH_RECEIVE"/>
    <!--小米推送权限 end-->
    <!--程序入口-->
    <application
        android:name="com.vmloft.develop.app.demo.call.AppApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:largeHeap="true"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        ...
        <!--小米推送相关配置-->
        <service
            android:name="com.xiaomi.push.service.XMJobService"
            android:enabled="true"
            android:exported="false"
            android:permission="android.permission.BIND_JOB_SERVICE"
            android:process=":pushservice"/>

        <service
            android:name="com.xiaomi.push.service.XMPushService"
            android:enabled="true"
            android:process=":pushservice"/>

        <service
            android:name="com.xiaomi.mipush.sdk.PushMessageHandler"
            android:enabled="true"
            android:exported="true"/>
        <service
            android:name="com.xiaomi.mipush.sdk.MessageHandleService"
            android:enabled="true"/>

        <!--推送消息广播接收器，这个广播接收器必须继承自环信实现的接收器-->
        <receiver
            android:name=".push.MIPushReceiver"
            android:exported="true">
            <intent-filter>
                <action android:name="com.xiaomi.mipush.RECEIVE_MESSAGE"/>
            </intent-filter>
            <intent-filter>
                <action android:name="com.xiaomi.mipush.MESSAGE_ARRIVED"/>
            </intent-filter>
            <intent-filter>
                <action android:name="com.xiaomi.mipush.ERROR"/>
            </intent-filter>
        </receiver>
        <receiver
            android:name="com.xiaomi.push.service.receivers.NetworkStatusReceiver"
            android:exported="true">
            <intent-filter>
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>

                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </receiver>
        <receiver
            android:name="com.xiaomi.push.service.receivers.PingReceiver"
            android:exported="false"
            android:process=":pushservice">
            <intent-filter>
                <action android:name="com.xiaomi.push.PING_TIMER"/>
            </intent-filter>
        </receiver>
        <!--小米推送配置 end-->
    </application>
</manifest>
```
其中`MIPushReceiver`这个广播接收器可以不用自己实现，如果自己实现，必须继承自环信 sdk 实现的广播接收器`EMMipushReceiver`实现，也可以直接用（这里如果需要自己与自己的业务处理可以继承环信实现的的这个广播接收器，不然收不到离线推送，然后去处理自己的逻辑；详细可以根据小米推送官方 sdk 文档进行了解下）；

当我们做完这些之后在收到离线消息后就可以收到推送通知了，只不过这个推送通知我们不能自定义，因为这些都是服务器推什么我们接受什么，这点比较坑！

### 通话的离线通知
上边已经实现了消息的离线通知，我们下边就要做当呼叫对方时，对方却不在线，我们怎么通知对方打开 app 进行接听呢？
曾经集成过环信用户应该知道，在呼叫对方不在线后会马上结束通话，回调对方不在线，在新版3.2.2的 sdk 中新增设置音视频参数及呼叫时对方离线是否发推送的接口，在初始化的时候进行以下设置：
```java
// 设置通话过程中对方如果离线是否发送离线推送通知，默认 false
EMClient.getInstance().callManager().getCallOptions().setIsSendPushIfOffline(true);
// 设置了这个之后就不会在通话状态监听中回调对方不在线，需要实现另外一个回调
...
// 设置音频通话推送提供者，在 onRemoteOffline()回调中给对方发送消息就行了
EMClient.getInstance().callManager().setPushProvider(EMCallManager.EMCallPushProvider {
    @Override public void onRemoteOffline(String username) {
        EMMessage message = EMMessage.createTxtSendMessage("有人呼叫你，开启 APP 接听吧", username);
        // 设置强制推送
        message.setAttribute("em_force_notification", "true");
        // 设置自定义推送提示
        JSONObject extObj = new JSONObject();
        try {
            extObj.put("em_push_title", "有人呼叫你，开启 APP 接听吧");
            extObj.put("extern", "定义推送扩展内容");
        } catch (JSONException e) {
            e.printStackTrace();
        }
        message.setAttribute("em_apns_ext", extObj);
        message.setMessageStatusCallback(new EMCallBack() {
            @Override public void onSuccess() {
              // 在这里可以删除消息
            }
            @Override public void onError(int i, String s) {
              // 在这里可以删除消息
            }
            @Override public void onProgress(int i, String s) {}
        });
        EMClient.getInstance().chatManager().sendMessage(message);
    }
});
```
实现了上边的这个推送提供者之后，当对方不在线就会回调 onRemoteOffline()方法，就可以发送一条消息给对方，然后上边我们已经集成了小米推送，就可以通过离线推送的方式通知对方有新消息，对方看到后点击通知栏就可以打开 app了，这个时候我们的语音或视频呼叫还在一直呼叫，然后就可以连通了！

### 结语
OK 到这里基本就已经完成了，大家可以运行自己的项目，或者我上边的 demo 测试下，我这边通过小米5测试 OK；其实集成推送部分并不难，只是有几点需要注意：
- 环信开发者后台的推送证书设置时一定要注意应用包名和小米推送后台的应用包名以及自己项目的包名，三个地方一定要一致
- 初始化设置一定要通过环信的 options 去设置小米推送的 appId 和 appKey，不需要用小米的注册方法自己注册；
- Androidmanifest 一定要加上环信的广播接收器，或者继承自环信封装的广播接收器

注意以上几点基本推送就没有问题了，如果不行可以先通过小米开发者后台的推送工具测试推送是否通了，然后检查以上几点；
>PS：华为推送相关其实一样，不过因为华为不允许个人开发者注册账户，所以这里暂时不赘述

### 参考资料
【[小米推送Android SDK文档](http://dev.xiaomi.com/doc/?p=544)】
【[环信推送相关文档](http://docs.easemob.com/im/200androidclientintegration/115thirdpartypush)】