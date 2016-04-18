---
title: Android开发集成环信SDK3.x简单介绍
comments: true
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Easemob
  - SDK
id: 10070
date: 2016-04-07 11:04:07
---

### 前言
这不代表官方教程！  
这不代表官方教程！  
这不代表官方教程！  
重要的事情说三遍！！！  

环信已经发部了`SDK3.x`版本，`SDK3.x`相对于`SDK2.x`来说是整个进行了重写，`API`变化还是比较大的，已经熟悉`SDK2.x`的开发者在使用新的`SDK3.x`还是会遇到不少问题的，不过还好官方给出了`SDK2.x`升级`SDK3.x`指南，已经熟悉`SDK2.x`开发者可以根据文档了解`SDK3.x`的变化，新集成的开发者可以直接参考`SDK3.x`进行集成；


### 提供一些地址
* 当前项目地址，可以直接 clone 运行  
[EaseChat Github](https://github.com/lzan13/EaseChat)  

* AndroidStudio下载  
[Android官方下载](http://tools.android.com/download/studio/builds/2-0)  
[国内提供 AndroidDevTools](http://androiddevtools.cn/)  

* 模拟器 Genymotion下载  
[Genymotion 官网](http://genymotion.com/)  

* 环信官方文档  
[SDK3.x 文档](http://docs.easemob.com/im/start)  
[SDK3.x API 文档](http://www.easemob.com/apidoc/android/chat3.0/annotated.html)  
[SDK2.x 升级 SDK3.x 文档](http://docs.easemob.com/im/200androidcleintintegration/140upgradetov30)  


###说下我当前开发环境

>AndroidStudio 2.0  
Gradle 2.10（跟随AndroidStudio 一起更新）  
Android SDK Tool 25.1.1  
Android Build-tools 23.0.2  
Android Support 最新  
Genymotion 2.6  

如果你还是用的`Eclipse`，可以下载`AndroidStudio`尝试下，如果你说上不了`Android`官网，不懂怎么翻墙可以找下国内开发提供的一些地址

### 开始集成
这次要实现 `SDK的初始化`、`SDK端的注册登录`、`消息的发送和监听`这三步
 
#### SDK的初始化  
这里定义了一个方法去初始化环信的SDK，并在其中进行了一些设置
```java
private void initEasemob() {
    // 获取当前进程 id 并取得进程名
    int pid = android.os.Process.myPid();
    String processAppName = getAppName(pid);
    /**
     * 如果app启用了远程的service，此application:onCreate会被调用2次
     * 为了防止环信SDK被初始化2次，加此判断会保证SDK被初始化1次
     * 默认的app会在以包名为默认的process name下运行，如果查到的process name不是app的process name就立即返回
     */
    if (processAppName == null || !processAppName.equalsIgnoreCase(mContext.getPackageName())) {
        // 则此application的onCreate 是被service 调用的，直接返回
        return;
    }
    if (isInit) {
        return;
    }
    /**
     * SDK初始化的一些配置
     */
    EMOptions options = new EMOptions();
    // 设置Appkey，如果配置文件已经配置，这里可以不用设置
    // options.setAppKey("lzan13#hxsdkdemo");
    // 设置自动登录
    options.setAutoLogin(true);
    // 设置是否需要发送已读回执
    options.setRequireAck(true);
    // 设置是否需要发送回执，TODO 这个暂时有bug，上层收不到发送回执
    options.setRequireDeliveryAck(true);
    // 设置是否需要服务器收到消息确认
    options.setRequireServerAck(true);
    // 收到好友申请是否自动同意，如果是自动同意就不会收到好友请求的回调，因为sdk会自动处理，默认为true
    options.setAcceptInvitationAlways(false);
    // 设置是否自动接收加群邀请，如果设置了当收到群邀请会自动同意加入
    options.setAutoAcceptGroupInvitation(false);
    // 设置（主动或被动）退出群组时，是否删除群聊聊天记录
    options.setDeleteMessagesAsExitGroup(false);
    // 设置是否允许聊天室的Owner 离开并删除聊天室的会话
    options.allowChatroomOwnerLeave(true);
    // 设置google GCM推送id，国内可以不用设置
    // options.setGCMNumber(MLConstants.ML_GCM_NUMBER);
    // 设置集成小米推送的appid和appkey
    // options.setMipushConfig(MLConstants.ML_MI_APP_ID, MLConstants.ML_MI_APP_KEY);
    // 调用初始化方法初始化sdk
    EMClient.getInstance().init(mContext, options);
    // 设置开启debug模式
    EMClient.getInstance().setDebugMode(true);
}
```

关于上边方法中获取当前进程名字的方法可以看这里
```java
    /**
     * 根据Pid获取当前进程的名字，一般就是当前app的包名
     *
     * @param pid 进程的id
     * @return 返回进程的名字
     */
    private String getAppName(int pid) {
        String processName = null;
        ActivityManager activityManager = (ActivityManager) mContext.getSystemService(Context.ACTIVITY_SERVICE);
        List list = activityManager.getRunningAppProcesses();
        Iterator i = list.iterator();
        PackageManager pm = mContext.getPackageManager();
        while (i.hasNext()) {
            ActivityManager.RunningAppProcessInfo info = (ActivityManager.RunningAppProcessInfo) (i.next());
            try {
                if (info.pid == pid) {
                    CharSequence c = pm.getApplicationLabel(pm.getApplicationInfo(info.processName, PackageManager.GET_META_DATA));
                    // Log.d("Process", "Id: "+ info.pid +" ProcessName: "+
                    // info.processName +"  Label: "+c.toString());
                    // processName = c.toString();
                    processName = info.processName;
                    return processName;
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return processName;
    }
```


#### SDK端的注册登录


#### 消息的发送和监听

  
