---
title: 关于环信2.xSDK日志简单分析
categories:
  - Develop
tags:
  - Android
  - Easemob
  - IM
  - Log
  - SDK
id: 1489749784000
comments: true
date: 2017-03-17 19:23:04
---

>我的简书：【[lzan13](http://www.jianshu.com/u/f53bcbbc7c3e) / [关于环信2.xSDK日志简单分析](http://www.jianshu.com/p/3b687c0937a7)】


前言
----
首先说下环信日志保存的机制，这边只要是在开启了`SDK`的`Debug`模式下调用环信的`EMLog.d()`方法输出的日志，日志内容都会在控制台输出的同时记录到设备的日志文件中；

这里就一些常见的日志跟大家略微解释下，一边能给大家在以后排查问题中起到一些帮助；

>PS：这里所列出来的日志内容只是特定情况测试下出现，在不同的设备，不同环境下稍微会有些出入，不过一些关键词的地方都应该是一样的，不要死心眼就好；

还有就是这篇分析是在3.xSDK 日志的基础上分析的，虽然是现有的2.x，但是2.x 的日志不是很熟，所以先写的3.xSDK 的日志分析，不过大致都还是差不多的，2.x 这里稍微简单的说说

### SDK 的初始化
2.xSDK在初始化时和3.x 稍微有些不同，2.x 初始化时不会检查DNS 配置，从日志和也可以看出来，当什么都不做只是初始化 SDK 时，日志中只是输出下当前是否已经有登录过的账户，以及自动登录设置状态，还有就是SDK版本
```log
2017-03-02 15:26:54:721 [EaseMob][ERROR]passed userName : null
2017-03-02 15:26:54:744 [EaseMob][ERROR]is autoLogin : true
2017-03-02 15:26:54:768 [EaseMob][ERROR]lastLoginUser :
2017-03-02 15:26:54:794 [EaseMob][ERROR]Easemob SDK is initialized with version : 2.3.4
2017-03-02 15:26:54:855 [group]add group change listener:com.easemob.chatuidemo.DemoHelper$MyGroupChangeListener
```

### 手动登录
当进行手动登录是，会首先获取 DNS 配置，然后获取完成 DNS 之后就开始登录了，登录要做的就是获取账户的 token，后续的操作都是根据这个 token 进行操作了
```log
2017-03-02 15:30:45:111 [EMChatManager][ERROR]emchat manager login in process:8369
2017-03-02 15:30:45:161 [EMDBManager][ERROR]initDB : lz1
2017-03-02 15:30:45:192 [Session]loginSync : in process 8369
2017-03-02 15:30:45:218 [Session]login with eid:easemob-demo#chatdemoui_lz1
2017-03-02 15:30:45:235 [group]group manager clear
2017-03-02 15:30:45:253 [EMChatManager]
2017-03-02 15:30:45:279 [EMDBManager]created chatdb for :lz1
2017-03-02 15:30:45:302 [EMConnectionManager]configure
2017-03-02 15:30:45:336 [EMDNSManager]getDnsConfig
2017-03-02 15:30:45:365 [EMDNSManager]retrieveDNSConfig
2017-03-02 15:30:45:396 [EMDNSManager]refreshDNSConfig
2017-03-02 15:30:45:429 [EMDNSManager]try to retrieve dns config! with retries number : 0
2017-03-02 15:30:45:458 [EMDNSManager]buildConfigUrl
2017-03-02 15:30:45:488 [EMDNSManager]0 use domain
2017-03-02 15:30:45:550 [EMDNSManager]retrieveDNSConfigWithCountDown
2017-03-02 15:30:45:572 [EMDNSManager]config server url : http://rs.easemob.com/easemob/server.xml?sdk_version=2.3.4&app_key=easemob-demo%23chatdemoui
2017-03-02 15:30:45:592 [group]load all groups from db. size:0
2017-03-02 15:30:45:615 [EMConversationManager]start to load converstations:
2017-03-02 15:30:45:635 [EMDNSManager]returned config content : <ebs><deploy_name>easemob</deploy_name><file_version>62</file_version><valid_before>1496172191</valid_before><gcm_enabled>true</gcm_enabled><resolver><hosts><host><domain>rs.easemob.com</domain><ip>59.110.89.59</ip><port>80</port></host><host><domain>rs.easemob.com</domain><ip>112.126.66.111</ip><port>80</port></host><host><domain>rs.easemob.com</domain><ip>182.92.174.78</ip><port>80</port></host><host><domain>rs.easemob.com</domain><ip>116.62.83.103</ip><port>80</port></host><host><domain>rs.easemob.com</domain><ip>120.26.17.225</ip><port>80</port></host><host><domain>rs.easemob.com</domain><ip>120.26.15.207</ip><port>80</port></host><host><domain>rs.easemob.com</domain><port>443</port><protocol>https</protocol></host></hosts></resolver><tcp_resolver><hosts><host><domain>rs.easemob.com</domain><ip>59.110.89.59</ip><port>2020</port></host><host><domain>rs.easemob.com</domain><ip>112.126.66.111</ip><port>2020</port></host><host><domain>rs.easemob.com</domain><ip>182.92.174.78</ip><port>2020</port></host><host><domain>rs.easemob.com</domain><ip>116.62.83.103</ip><port>2020</port></host><host><domain>rs.easemob.com</domain><ip>120.26.17.225</ip><port>2020</port></host><host><domain>rs.easemob.com</domain><ip>120.26.15.207</ip><port>2020</port></host></hosts></tcp_resolver><im><hosts><host><domain>im1.easemob.com</domain><ip>182.92.20.34</ip><port>443</port></host><host><domain>im1.easemob.com</domain><ip>182.92.20.117</ip><port>443</port></host><host><domain>im1.easemob.com</domain><ip>182.92.23.59</ip><port>443</port></host><host><domain>im1.easemob.com</domain><ip>182.92.26.56</ip><port>443</port></host></hosts></im><xmpp><hosts><host><domain>im-api.easemob.com</domain><ip>im-api.easemob.com</ip><port>80</port><protocol>http</protocol></host><host><domain>im-api.easemob.com</domain><ip>182.92.159.193</ip><port>5280</port><protocol>http</protocol></host><host><domain>im-api.easemob.com</domain><ip>182.92.228.160</ip><port>5280</port><protocol>http</protocol></host><host><domain>im-api.easemob.com</domain><port>443</port><protocol>https</protocol></host></hosts></xmpp><msync-im><hosts><host><domain>msync-im1.easemob.com</domain><ip>60.205.131.133</ip><port>6802</port></host><host><domain>msync-im1.easemob.com</domain><ip>60.205.109.58</ip><port>7829</port></host></hosts></msync-im><rest><hosts><host><domain>a1.easemob.com</domain><port>443</port><protocol>https</protocol></host><host><domain>a1.easemob.com</domain><ip>182.92.159.193</ip><port>80</port><protocol>http</protocol></host><host><domain>a1.easemob.com</domain><ip>182.92.228.160</ip><port>80</port><protocol>http</protocol></host><host><domain>a1.easemob.com</domain><ip>182.92.0.214</ip><port>80</port><protocol>http</protocol></host><host><domain>a1.easemob.com</domain><ip>182.92.4.52</ip><port>80</port><protocol>http</protocol></host></hosts></rest></ebs>
...
获取 DNS 成功之后才是真正的认证登录，获取 TOKEN
2017-03-02 15:30:46:507 [com.easemob.chat.core.EMInternalConfigManager]accesstoken : {"access_token":"YWMtYt7kgP8XEeamVEtR2Q-CgwAAAVvC2ReE0obINNrCP8QG9pLb0WlQlRPIpDM","expires_in":5182813,"user":{"uuid":"603eadf0-ff17-11e6-8180-657de846951b","type":"user","created":1488438654543,"modified":1488439651099,"username":"lz1","activated":true,"notification_display_style":1,"device_token":"/cRO2vRH64mWOH46+We1zxt4+onQRJOe26fvcX02iC0=","nickname":"立正1","notifier_name":"2882303761517426801"}}
...
```

### 自动登录
自动登录基本和手动登录一样，唯一的区别就是自动登录没有输出`emchat manager login in process:8369`，这行是红色的，比较醒目，可以用这个来判断当前登录是自动登录，还是手动登录，还有一点就是自动登录时`lastLoginUser`这个也是有值的；
```log
2017-03-02 16:47:50:054 [EaseMob][ERROR]passed userName : null
2017-03-02 16:47:50:085 [EaseMob][ERROR]is autoLogin : true
2017-03-02 16:47:50:121 [EaseMob][ERROR]lastLoginUser : lz1
2017-03-02 16:47:50:155 [EMDBManager][ERROR]initDB : lz1
2017-03-02 16:47:50:190 [EaseMob][ERROR]Easemob SDK is initialized with version : 2.3.4

下边会进行登录的一些操作，初始化，数据加载，以及推送相关的处理
...
```


### 心跳定时器
一切都准备好了之后，就是定义心跳定时器，第一次设置时`2'00"`后启动心跳定时器，后边会慢慢增加到`4'30"`，然后会按照`4'30"`这样循环下去，当出现心跳死了之后，会重新从头开始
```log
2017-03-02 15:32:47:767 [smart ping]schedule next alarm
2017-03-02 15:32:47:793 [smart ping]current heartbeat interval : 02:00:000 smart ping state : EMEvaluating
2017-03-02 15:32:47:821 [smart ping]use 02:00:000 to start alarm
2017-03-02 15:34:47:776 [EMHeartBeatReceiver]onReceive EMHeartBeatReceiver
2017-03-02 15:34:47:812 [smart ping]post heartbeat runnable
2017-03-02 15:34:47:867 [smart ping]has network connection:true has data conn:true isConnected to easemob server : true
2017-03-02 15:34:47:917 [smart ping]acquire wake lock
2017-03-02 15:34:47:941 [smart ping]check pingpong ...
2017-03-02 15:34:48:876 [smart ping]send ping-pong type heartbeat

发送心跳 ping pang
2017-03-02 15:34:48:919 [SMACK:FileDebugger] SENT to182.92.23.59:443(202029101): <iq id="6T82J-6" type="get"><ping xmlns='urn:xmpp:ping' /></iq>
2017-03-02 15:34:48:960 [SMACK:FileDebugger] RCV from182.92.23.59:443(202029101): <iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='6T82J-6' type='result'/>
2017-03-02 15:34:48:992 [smart ping]success to send ping pong ... with current heartbeat interval : 02:00:000
2017-03-02 15:34:49:018 [smart ping]send ping-pong successed
2017-03-02 15:34:49:045 [smart ping]released the wake lock
2017-03-02 15:34:49:100 [smart ping]schedule next alarm
2017-03-02 15:34:49:135 [smart ping]current heartbeat interval : 02:45:000 smart ping state : EMEvaluating
2017-03-02 15:34:49:176 [smart ping]use 02:45:000 to start alarm
....
2017-03-02 15:41:06:823 [smart ping]schedule next alarm
2017-03-02 15:41:06:845 [smart ping]current heartbeat interval : 04:15:000 smart ping state : EMEvaluating
2017-03-02 15:41:06:868 [smart ping]use 04:15:000 to start alarm
2017-03-02 15:54:24:202 [EMHeartBeatReceiver]onReceive EMHeartBeatReceiver
2017-03-02 15:54:24:248 [smart ping]post heartbeat runnable
2017-03-02 15:54:24:303 [smart ping]has network connection:true has data conn:true isConnected to easemob server : true
2017-03-02 15:54:24:355 [smart ping]acquire wake lock
2017-03-02 15:54:24:384 [smart ping]send white heartbeat
2017-03-02 15:54:24:437 [smart ping]released the wake lock
2017-03-02 15:54:24:493 [smart ping]schedule next alarm
2017-03-02 15:54:24:521 [smart ping]current heartbeat interval : 04:30:000 smart ping state : EMHitted
2017-03-02 15:54:24:559 [smart ping]use 04:30:000 to start alarm
```

### 链接监听与重连
当 SDK 因为某些原因检测到与服务器的连接断开后，会回调连接中断监听，然后会启动重连线程，进行尝试重连，这个重连中如果网络不可用会直接跳过，等待下次的尝试，当网络连接恢复后，SDK 会马上尝试重连，中间会进行一系列的操作，连接成功后，会马上回调通知上层
```log
2017-03-02 16:25:44:566 [sxmppcon](202029101) socket was closed
2017-03-02 16:25:44:620 [EMConnectionManager][ERROR]connectionClosedOnError in java.net.SocketException: recvfrom failed: ETIMEDOUT (Connection timed out)
2017-03-02 16:25:44:699 [EMConnectionManager]register connectivity receiver.
2017-03-02 16:25:44:763 [EMConnectionManager]connectivity receiver onReceiver
2017-03-02 16:25:44:812 [EMConnectionManager]160587117 : enter startReconnectionThread()
2017-03-02 16:25:44:842 [EMConnectionManager]start reconnectionThread()
2017-03-02 16:25:44:934 [net]no data connection
2017-03-02 16:25:44:967 [EMConnectionManager]on disconnected
2017-03-02 16:25:44:994 [EMChatManager]connectionClosedOnError
2017-03-02 16:25:45:015 [EMConnectionManager]run in reconnectionThread
2017-03-02 16:25:45:038 [SMACK:FileDebugger] Connection closed due to an exception (202029101)
2017-03-02 16:25:45:061 [smart ping] onDisconnected ... cnnListener:com.easemob.chat.EMSmartHeartBeatAws$3@f1bae79 EMSmartHeartBeat:com.easemob.chat.EMSmartHeartBeatAws@88857be
2017-03-02 16:25:45:085 [EMConnectionManager]connectivity receiver onReceiver
2017-03-02 16:25:45:111 [net]no data connection
2017-03-02 16:25:45:819 [EMConnectionManager]run in reconnectionThread with connection 202029101
2017-03-02 16:25:45:848 [net]no data connection
2017-03-02 16:25:45:873 [EMConnectionManager]skip the reconnection since there is no data connection!
2017-03-02 16:25:46:842 [EMChatManager]reconnectingIn in 8 
2017-03-02 16:25:47:850 [EMChatManager]reconnectingIn in 7
2017-03-02 16:25:48:922 [EMChatManager]reconnectingIn in 6
2017-03-02 16:25:49:881 [EMChatManager]reconnectingIn in 5
2017-03-02 16:25:50:888 [EMChatManager]reconnectingIn in 4
2017-03-02 16:25:51:895 [EMChatManager]reconnectingIn in 3
2017-03-02 16:25:52:948 [EMChatManager]reconnectingIn in 2
2017-03-02 16:25:53:986 [EMChatManager]reconnectingIn in 1
2017-03-02 16:25:54:990 [EMChatManager]reconnectingIn in 0
2017-03-02 16:25:55:018 [EMConnectionManager]run in reconnectionThread with connection 202029101
2017-03-02 16:25:55:044 [net]no data connection
2017-03-02 16:25:55:071 [EMConnectionManager]skip the reconnection since there is no data connection!
如果后边网络连接恢复后，会马上进行重连操作
2017-03-02 16:33:21:869 [EMConnectionManager]connectivity receiver onReceiver
2017-03-02 16:33:22:046 [net]has wifi connection
2017-03-02 16:33:22:141 [EMConnectionManager]run in reconnectionThread with connection 202029101
2017-03-02 16:33:22:357 [EMConnectionManager]160587117 : enter startReconnectionThread()
2017-03-02 16:33:22:415 [EMConnectionManager]try to reconnectSync
2017-03-02 16:33:22:513 [EMConnectionManager]enter connectSync
2017-03-02 16:33:22:690 [EMConnectionManager]connection manager:connect
2017-03-02 16:33:22:735 [EMConnectionManager]before connect

等待认证响应
2017-03-02 16:33:23:137 [SASLAuthentication]waiting for authentiation response!
2017-03-02 16:33:23:445 [SMACK:FileDebugger]User logged (202029101): easemob-demo#chatdemoui_lz1@easemob.com@easemob.com:443/mobile
2017-03-02 16:33:23:479 [EMConnectionManager]reconnectionSuccessful
2017-03-02 16:33:23:670 [EMChatManager]reconnectionSuccessful

回调链接成功
2017-03-02 16:33:23:737 [smart ping] onConnectred ... cnnListener:com.easemob.chat.EMSmartHeartBeatAws$3@f1bae79 EMSmartHeartBeat:com.easemob.chat.EMSmartHeartBeatAws@88857be
2017-03-02 16:33:23:761 [EMConnectionManager]enter initConnection()
2017-03-02 16:33:23:782 [smart ping]reset interval...
2017-03-02 16:33:23:802 [EMConnectionManager]already login. skip
```

### 被踢&强制下线&移除账户
收到连接断开的错误时，会马上记录断开原因的日志，然后会清空链接数据
remove:表示移除/禁用/强制下线，这三种状态的提示是一样的，只不过错误码不同
Replaced by new connection:表示异地登录

PS：需要注意的一点就是，当收到这几个状态的回调后需要手动调用`logout`，清除当前登录状态，因此在遇到被踢和强制下线的日志中一般都能看到和 logout 相关日志

关键词：`connectionClosedOnError`,`conflict`,`logout`,`connectionClosed`,`remove`,
```log
2017-03-02 16:57:16:011 [EMConnectionManager][ERROR]connectionClosedOnError in stream:error (conflict) text: Replaced by new connection
2017-03-02 16:57:16:067 [EMConnectionManager][ERROR]connection closed caused by conflict. set autoreconnect to false
2017-03-02 16:57:16:108 [EMConnectionManager]on disconnected
2017-03-02 16:57:16:139 [EMChatManager]connectionClosedOnError
2017-03-02 16:57:16:163 [SMACK:FileDebugger] Connection closed due to an exception (12605026)
2017-03-02 16:57:16:187 [smart ping] onDisconnected ... cnnListener:com.easemob.chat.EMSmartHeartBeatAws$3@c21515d EMSmartHeartBeat:com.easemob.chat.EMSmartHeartBeatAws@be82ad2
2017-03-02 16:57:16:209 [EMPushNotificationHelper]push notification helper ondestory
2017-03-02 16:57:16:234 [EMChatManager] SDK Logout
2017-03-02 16:57:16:256 [EMVoiceCallManager][WARN]no active call!
2017-03-02 16:57:16:288 [EMVoiceCallManager]onCallStateChanged with callState = disconnected callError = error_none
2017-03-02 16:57:16:315 [EMCustomerService]cancel helpdesk logout
2017-03-02 16:57:16:336 [EMCustomerService]unregister helpdesk logout receiver
2017-03-02 16:57:16:374 [InvitationsMonitor]invitationPacketListener = org.jivesoftware.smackx.muc.MultiUserChat$InvitationsMonitor$1@5c51e61
2017-03-02 16:57:16:395 [EMChatRoomManager]init chat room manager
2017-03-02 16:57:16:419 [group]group manager logout
2017-03-02 16:57:16:443 [EMDBManager]close msg db
2017-03-02 16:57:16:466 [Session]Session logout
2017-03-02 16:57:16:489 [smart ping]stop heart beat timer
2017-03-02 16:57:16:513 [smart ping]change smart ping state from : EMEvaluating to : EMStopped
2017-03-02 16:57:16:539 [EMConnectionManager]35284046 : enter disconnect()
2017-03-02 16:57:16:566 [EMConnectionManager]unregisterConnectivityReceiver()
2017-03-02 16:57:16:589 [EMConnectionManager][ERROR]connectionClosed
2017-03-02 16:57:16:609 [EMConnectionManager]on disconnected
2017-03-02 16:57:16:629 [EMChatManager]closing connection
2017-03-02 16:57:16:652 [EMConnectionManager]trying to disconnect connection （12605026)
2017-03-02 16:57:16:673 [sxmppcon]enter disconnect (12605026)
2017-03-02 16:57:16:694 [sxmppcon](12605026) shutdown
2017-03-02 16:57:16:728 [sxmppcon]trying to close the socket : Socket[unconnected]
2017-03-02 16:57:16:754 [sxmppcon](12605026) socket was closed
2017-03-02 16:57:16:781 [sxmppcon]shutdown was called
2017-03-02 16:57:16:834 [EMDNSManager]clearDnsConfig
2017-03-02 16:57:16:862 [EMHostResolver]reset
2017-03-02 16:57:16:965 [EMChatManager]do stop service
2017-03-02 16:57:17:014 [EMChatService]onDestroy

后台强制下线
2017-03-02 17:03:01:255 [EMConnectionManager][ERROR]connectionClosedOnError in stream:error (conflict) text: User removed
2017-03-02 17:03:01:299 [EMConnectionManager][ERROR]connection closed caused by conflict. set autoreconnect to false
```

### 推送相关
推送部分2.x 和3.x 基本没有什么变化，都是在登录时检测推送服务是否可用，可以从日志中看到推送功能是否激活，然后获取`devicetoken`发送到服务器等，`devicetoken`这个值一定要存在，否者推送无效；这些可以作为检测集成离线推送本地设置是否有效的一些手段；
关键词：`GCM`,`push`,`devicetoken`,`sendDeviceToServer`
```log
2017-03-02 15:30:46:585 [EMPushNotificationHelper]GCM is enabled : true
2017-03-02 15:30:46:613 [EMPushNotificationHelper]GCM service available : false
2017-03-02 15:30:46:635 [EMPushNotificationHelper]mipush available : true
2017-03-02 15:30:48:395 [EMPushNotificationHelper]third-party push available
2017-03-02 15:30:49:430 [EMMipushReceiver]mi push reigster success
2017-03-02 15:30:49:455 [EMPushNotificationHelper]devicetoken = tyPPd3IQ95+Gg/pYE5RoV1efO/aa4kcZHM+Mc+pPsjI=
2017-03-02 15:30:49:476 [HttpClientManager]try send request, request url: http://182.92.159.193:80/easemob-demo/chatdemoui/devices with number: 0
2017-03-02 15:30:49:497 [EMPushNotificationHelper]sendDeviceToServer SC_OK:
2017-03-02 15:30:49:521 [EMPushNotificationHelper]send device token to server, token = tyPPd3IQ95+Gg/pYE5RoV1efO/aa4kcZHM+Mc+pPsjI=,url = http://182.92.159.193:80/easemob-demo/chatdemoui/users/lz1
2017-03-02 15:30:49:544 [HttpClientManager]try send request, request url: http://182.92.159.193:80/easemob-demo/chatdemoui/users/lz1 with number: 0
2017-03-02 15:30:49:569 [EMPushNotificationHelper]sendTokenToServer SC_OK:
```

消息相关
------
消息这部分就比较简单了，首先就是发送，然后等待服务器返回 ack，更新服务器生成的 id，然后就是对方收到消息，会给自己发送已读 ack，

### 发送新消息
关键词：`SENT`,`RCV`,`chat`,`received`,`acked`
```log
常见消息将要发送
2017-03-03 14:39:12:018 [sender]try to send msg to:<contact jid:easemob-demo#chatdemoui_lz2, username:lz2, nick:null> msg:{"from":"lz1","to":"lz2","bodies":[{"type":"txt","msg":"复合肥减减肥"}],"ext":{}}
SDK 内部开始发送消息
2017-03-03 14:39:12:054 [SMACK:FileDebugger] SENT to182.92.20.34:443(260847652): <message id="c6X5L-4-4c958" to="easemob-demo#chatdemoui_lz2@easemob.com" from="easemob-demo#chatdemoui_lz1@easemob.com/mobile" type="chat"><body>{"from":"lz1","to":"lz2","bodies":[{"type":"txt","msg":"复合肥减减肥"}],"ext":{}}</body><thread>gO9790</thread></message>

收到服务器返回的 ACK，这个时候消息 id 会由临时 id 返回服务器生成的 id
2017-03-03 14:39:12:128 [SMACK:FileDebugger] RCV from182.92.20.34:443(260847652): <message from='easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile'><body>c6X5L-4-4c958</body><received xmlns='urn:xmpp:receipts' mid='304763800656021220'>c6X5L-4-4c958</received></message>
2017-03-03 14:39:12:153 [acklistener]<message to="easemob-demo#chatdemoui_lz1@easemob.com/mobile" from="easemob.com"><body>c6X5L-4-4c958</body><received xmlns="urn:xmpp:receipts" id="null"/></message>
2017-03-03 14:39:12:179 [acklistener] found returned global server msg id : 304763800656021220
2017-03-03 14:39:12:205 [acklistener]received server ack for msg:c6X5L-4-4c958

收到对方已读 ACK
2017-03-03 14:39:13:908 [SMACK:FileDebugger] RCV from182.92.20.34:443(260847652): <message from='easemob-demo#chatdemoui_lz2@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com' id='304763808801359888'><body>304763800656021220</body><acked xmlns='urn:xmpp:receipts' id='304763800656021220'/></message>
2017-03-03 14:39:13:957 [acklistener]<message id="304763808801359888" to="easemob-demo#chatdemoui_lz1@easemob.com" from="easemob-demo#chatdemoui_lz2@easemob.com"><body>304763800656021220</body><acked xmlns="urn:xmpp:receipts"></acked></message>

告诉服务器自己收到对方已读的 ACK
2017-03-03 14:39:13:996 [chat]send ack message back to server:org.jivesoftware.smack.packet.Message@b4fff167
2017-03-03 14:39:14:025 [SMACK:FileDebugger] SENT to182.92.20.34:443(260847652): <message id="304763808801359888" to="easemob.com" from="easemob-demo#chatdemoui_lz1@easemob.com"><received xmlns="urn:xmpp:receipts" id="304763808801359888"/></message>
```

### 接收新消息
2.x收到消息后服务器是直接把消息推给客户端，直接就接收解析了

关键词：`SENT`,`RCV`,`chat`,`received`,`acked`
```log
SDK 内部输出收到消息，这里还没到回调
2017-03-03 14:41:44:038 [SMACK:FileDebugger] RCV from182.92.20.34:443(260847652): <message from='easemob-demo#chatdemoui_lz2@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com' type='chat' id='304764453499439120'><body>{"bodies":[{"msg":"呱呱呱呱呱呱","type":"txt"}],"ext":{},"from":"lz2","to":"lz1"}</body></message>

收到消息后，给对方发送已送达 ACK
2017-03-03 14:41:44:123 [SMACK:FileDebugger] SENT to182.92.20.34:443(260847652): <message id="304764453499439120" to="easemob.com" from="easemob-demo#chatdemoui_lz1@easemob.com"><received xmlns="urn:xmpp:receipts" id="304764453499439120"/></message>

这里是回调部分
2017-03-03 14:41:44:180 [chat]chat listener receive msg from:easemob-demo#chatdemoui_lz2@easemob.com body:{"bodies":[{"msg":"呱呱呱呱呱呱","type":"txt"}],"ext":{},"from":"lz2","to":"lz1"}
2017-03-03 14:41:44:217 [EMConversationManager]save message:304764453499439120
2017-03-03 14:41:44:268 [EMConversationManager]get conversation for user:lz2
2017-03-03 14:41:44:332 [EMDBManager]add converstion with:lz2
2017-03-03 14:41:44:360 [EMDBManager]save msg to db
2017-03-03 14:41:44:389 [EMDBManager]add converstion with:lz2
2017-03-03 14:41:44:417 [notify]publish event, event type: EventNewMessage
2017-03-03 14:41:44:446 [EaseChatFragment]onEvent: From: lz2 to: lz1
2017-03-03 14:41:44:471 [Session]check connection...
2017-03-03 14:41:44:495 [Session]check connection ok
2017-03-03 14:41:44:532 [EMMessageHandler]send ack msg to:lz2 for msg:304764453499439120

自己读了消息，给对方发送已读 ACK
2017-03-03 14:41:44:566 [SMACK:FileDebugger] SENT to182.92.20.34:443(260847652): <message id="c6X5L-5" to="easemob-demo#chatdemoui_lz2@easemob.com" from="easemob-demo#chatdemoui_lz1@easemob.com"><body>304764453499439120</body><acked xmlns="urn:xmpp:receipts" id="304764453499439120"/></message>
```

业务逻辑相关
---------
从日志来看，其实2.x 的也算是通过消息的形式进行操作，这些都是 xmpp 的消息格式，都是发送代表特定操作的消息给服务器，然后服务器会返回这个消息发送成功，接着服务器会以新消息的形式告诉 SDK 操作成功，最后都和收发消息的日志逻辑一样；
需要注意的就是这些操作中的关键词；

### 联系人
联系人的这些操作比较简单，

关键词：`subscribe`,`subscribed`,`roster`,`subscription`,`remove`,`special`,`privacy`
```log
收到好友请求很明显
2017-03-02 17:14:34:815 [SMACK:FileDebugger] RCV from182.92.26.56:443(98255024): <presence from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz2@easemob.com/mobile' id='go1Xg-33' type='subscribe'><status>加个好友呗</status></presence>

同意对方请求
2017-03-02 17:17:16:038 [SMACK:FileDebugger] SENT to182.92.26.56:443(98255024): <presence id="yOWo3-18" to="easemob-demo#chatdemoui_lz1@easemob.com" type="subscribed"><status>[resp:true]</status><priority>24</priority></presence>
2017-03-02 17:17:16:069 [SMACK:FileDebugger] SENT to182.92.26.56:443(98255024): <presence id="yOWo3-19" to="easemob-demo#chatdemoui_lz1@easemob.com" type="subscribe"><status>[resp:true]</status></presence>

2017-03-02 17:17:16:154 [SMACK:FileDebugger] RCV from182.92.26.56:443(98255024): <iq from='easemob-demo#chatdemoui_lz2@easemob.com' to='easemob-demo#chatdemoui_lz2@easemob.com/mobile' id='push413681005' type='set'><query xmlns='jabber:iq:roster' ver='e7f2a8501f158f4bc887cd876c4b0e83359d6a2f'><item subscription='from' jid='easemob-demo#chatdemoui_lz1@easemob.com'/></query></iq>

对方同意了请求
2017-03-02 17:17:15:584 [SMACK:FileDebugger] RCV from182.92.20.117:443(158255556): <iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='push4291800965' type='set'><query xmlns='jabber:iq:roster' ver='05fa1fbab5632f76d0dfd615e3544708fe7fd718'><item subscription='to' jid='easemob-demo#chatdemoui_lz2@easemob.com'/></query></iq><presence from='easemob-demo#chatdemoui_lz2@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='yOWo3-18' type='subscribed'><status>[resp:true]</status><priority>24</priority></presence><iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='push825452103' type='set'><query xmlns='jabber:iq:roster' ver='1af84210f8a52f51ec94d169f67b85a7773ad289'><item subscription='to' jid='easemob-demo#chatdemoui_lz2@easemob.com'/></query></iq><presence from='easemob-demo#chatdemoui_lz2@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='yOWo3-19' type='subscribe'><status>[resp:true]</status></presence><presence from='easemob-demo#chatdemoui_lz2@easemob.com/mobile' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='yOWo3-15'><im_login_time>665</im_login_time><chat_login_time>1416</chat_login_time></presence>

2017-03-02 17:17:15:624 [rosterstorage]updated roster version to:05fa1fbab5632f76d0dfd615e3544708fe7fd718

删除联系人
2017-03-02 18:04:02:519 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <iq id="2f21d-18" type="set"><query xmlns="jabber:iq:roster" ><item jid="easemob-demo#chatdemoui_lz2@easemob.com" name="lz2" subscription="remove"></item></query></iq>

将对方加入黑名单
2017-03-02 18:04:50:625 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <iq id="2f21d-22" from="easemob-demo#chatdemoui_lz1@easemob.com/mobile" type="set"><query xmlns="jabber:iq:privacy"><list name="special"><item action="deny" order="100" type="jid" value="easemob-demo#chatdemoui_lz2@easemob.com"><message/></item></list></query></iq>
2017-03-02 18:04:50:651 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='push1906068296' type='set'><query xmlns='jabber:iq:privacy'><list name='special'/></query></iq>

移除黑名单
2017-03-02 18:08:38:085 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <iq id="2f21d-34" from="easemob-demo#chatdemoui_lz1@easemob.com/mobile" type="get"><query xmlns="jabber:iq:privacy"></query></iq>
2017-03-02 18:08:38:115 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='2f21d-34' type='result'><query xmlns='jabber:iq:privacy'><active name='special'/><default name='special'/><list name='special'/></query></iq>
2017-03-02 18:08:38:140 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <iq id="2f21d-35" from="easemob-demo#chatdemoui_lz1@easemob.com/mobile" type="get"><query xmlns="jabber:iq:privacy"><list name="special"/></query></iq>
2017-03-02 18:08:38:165 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='2f21d-35' type='result'><query xmlns='jabber:iq:privacy'><list name='special'><item type='jid' value='easemob-demo#chatdemoui_lz2@easemob.com' action='deny' order='100'><message/></item></list></query></iq>
2017-03-02 18:08:38:190 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <iq id="2f21d-36" from="easemob-demo#chatdemoui_lz1@easemob.com/mobile" type="get"><query xmlns="jabber:iq:privacy"><list name="special"/></query></iq>
2017-03-02 18:08:38:215 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='2f21d-36' type='result'><query xmlns='jabber:iq:privacy'><list name='special'><item type='jid' value='easemob-demo#chatdemoui_lz2@easemob.com' action='deny' order='100'><message/></item></list></query></iq>
2017-03-02 18:08:38:253 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <iq id="2f21d-37" from="easemob-demo#chatdemoui_lz1@easemob.com/mobile" type="set"><query xmlns="jabber:iq:privacy"><default/></query></iq>
2017-03-02 18:08:38:273 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='2f21d-37' type='result'/>
2017-03-02 18:08:38:293 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <iq id="2f21d-38" from="easemob-demo#chatdemoui_lz1@easemob.com/mobile" type="set"><query xmlns="jabber:iq:privacy"><list name="special"/></query></iq>
2017-03-02 18:08:38:323 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='push3498390976' type='set'><query xmlns='jabber:iq:privacy'><list name='special'/></query></iq>
2017-03-02 18:08:38:342 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <iq from='easemob-demo#chatdemoui_lz1@easemob.com' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile' id='2f21d-38' type='result'/>
2017-03-02 18:08:38:362 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <iq id="push3498390976" from="easemob-demo#chatdemoui_lz1@easemob.com" type="result"></iq>
```


### 群组操作
群组的操作也都是以 xmpp 消息的格式进行操作的，仔细看的话都是一个个的消息体，只是这些消息体和聊天的消息稍微有些差别，不过可以根据中间的一些关键词来了解分析日志

关键词：`muc`,`create`,`201`,`invites`,`join`,`unavailable`,`affiliation`,`member`,`none`,`role`,`participant`,`none`,`321`,`110`,`destroy`
```log
创建新群组
2017-03-02 17:50:13:330 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <presence id="2f21d-6" to="easemob-demo#chatdemoui_1488448213229@conference.easemob.com/lz1"><x xmlns="http://jabber.org/protocol/muc"></x><create xmlns="http://jabber.org/protocol/muc"></create></presence>
2017-03-02 17:50:13:356 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <presence from='easemob-demo#chatdemoui_1488448213229@conference.easemob.com/lz1' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile'><x xmlns='http://jabber.org/protocol/muc#user'><item jid='easemob-demo#chatdemoui_lz1@easemob.com' affiliation='owner' role='moderator'/><status code='201'/></x></presence>

邀请加入群组
2017-03-02 17:53:47:102 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <message id="2f21d-15" to="easemob-demo#chatdemoui_1488448213229@conference.easemob.com"><x xmlns="http://jabber.org/protocol/muc#user"><invite to="easemob-demo#chatdemoui_lz2@easemob.com"><reason>EaseMob-Group</reason></invite></x></message>

对方同意，并加入到群组中
2017-03-02 17:53:47:657 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <presence from='easemob-demo#chatdemoui_1488448213229@conference.easemob.com/lz2' to='easemob-demo#chatdemoui_lz1@easemob.com'><x xmlns='http://jabber.org/protocol/muc#user'><item affiliation='member' role='participant'/></x></presence>

SDK 自动同意邀请会发送一条加入的消息给服务器
2017-03-02 17:39:48:823 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <presence id="2f21d-4" to="easemob-demo#chatdemoui_1479267663683@conference.easemob.com/lz1"><x xmlns="http://jabber.org/protocol/muc"></x><join xmlns="http://jabber.org/protocol/muc"></join></presence>
2017-03-02 17:39:48:845 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <presence from='easemob-demo#chatdemoui_1479267663683@conference.easemob.com/lz1' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile'><x xmlns='http://jabber.org/protocol/muc#user'><item jid='easemob-demo#chatdemoui_lz1@easemob.com' affiliation='member' role='moderator'/></x></presence>

收到被踢出群组
2017-03-02 17:47:53:175 [SMACK:FileDebugger] RCV from182.92.23.59:443(12605026): <message from='easemob-demo#chatdemoui_1479267663683@conference.easemob.com/lz1' to='easemob-demo#chatdemoui_lz1@easemob.com' type='notify' presence_type='unavailable' id='304441338491836376'><x xmlns='http://jabber.org/protocol/muc#user'><item affiliation='none' role='none'><actor nick='lz2'/></item><status code='321'/></x></message><presence from='easemob-demo#chatdemoui_1479267663683@conference.easemob.com/lz1' to='easemob-demo#chatdemoui_lz1@easemob.com' type='unavailable'><x xmlns='http://jabber.org/protocol/muc#user'><item affiliation='none' role='none'/><status code='110'/></x></presence>

群组解散
2017-03-02 17:59:45:100 [SMACK:FileDebugger] SENT to182.92.23.59:443(12605026): <iq id="2f21d-17" to="easemob-demo#chatdemoui_1488448213229@conference.easemob.com" type="set"><query xmlns="http://jabber.org/protocol/muc#owner"><destroy><reason>delete-group</reason></destroy></query></iq>
```

### 聊天室
聊天室的操作基本和群组差不多的，一些操作的命令关键词都是一样的
关键词：`muc`,`join`,`affiliation`,`member`,`none`,`role`,`participant`,`none`,`321`,`chatroom`,
```log
加入聊天室
2017-03-03 15:14:51:992 [SMACK:FileDebugger] SENT to182.92.20.34:443(260847652): <presence id="c6X5L-13" to="easemob-demo#chatdemoui_3759501541378@conference.easemob.com/lz1"><x xmlns="http://jabber.org/protocol/muc"></x><join xmlns="http://jabber.org/protocol/muc"></join></presence>
2017-03-03 15:14:52:045 [SMACK:FileDebugger] RCV from182.92.20.34:443(260847652): <presence from='easemob-demo#chatdemoui_3759501541378@conference.easemob.com/lz1' to='easemob-demo#chatdemoui_lz1@easemob.com/mobile'><x xmlns='http://jabber.org/protocol/muc#user'><item affiliation='member' role='participant'/></x><roomtype xmlns='easemob:x:roomtype' type='chatroom'/></presence>

收到其他人加入聊天室日志
2017-03-03 15:24:25:677 [SMACK:FileDebugger] RCV from182.92.20.34:443(260847652): <presence from='easemob-demo#chatdemoui_3759501541378@conference.easemob.com/lz2' to='easemob-demo#chatdemoui_lz1@easemob.com'><x xmlns='http://jabber.org/protocol/muc#user'><item affiliation='member' role='participant'/></x><roomtype xmlns='easemob:x:roomtype' type='chatroom'/></presence>

其他人离开聊天室
2017-03-03 15:25:15:858 [SMACK:FileDebugger] RCV from182.92.20.34:443(260847652): <presence from='easemob-demo#chatdemoui_3759501541378@conference.easemob.com/lz2' to='easemob-demo#chatdemoui_lz1@easemob.com' type='unavailable'><x xmlns='http://jabber.org/protocol/muc#user'><item affiliation='none' role='none'/></x><roomtype xmlns='easemob:x:roomtype' type='chatroom'/></presence>

服务器把自己踢出聊天室
2017-03-03 15:28:09:622 [SMACK:FileDebugger] RCV from182.92.20.34:443(260847652): <message from='easemob-demo#chatdemoui_3759501541378@conference.easemob.com/lz1' to='easemob-demo#chatdemoui_lz1@easemob.com' type='notify' presence_type='unavailable' id='304776417562855424'><x xmlns='http://jabber.org/protocol/muc#user'><item affiliation='none' role='none'><actor nick='系统管理员'/></item><status code='321'/></x></message>

聊天室解散
2017-03-03 15:29:33:394 [SMACK:FileDebugger] RCV from182.92.20.34:443(260847652): <message from='easemob-demo#chatdemoui_3759501541378@conference.easemob.com/lz1' to='easemob-demo#chatdemoui_lz1@easemob.com' type='notify' presence_type='unavailable' id='304776777249589296'><x xmlns='http://jabber.org/protocol/muc#user'><item affiliation='none' role='none'/><destroy><reason></reason></destroy></x></message>
```


通话相关
-----
由于通话功能大部分都是由底层实现，也就先不写了


结束语
------
没有什么能一眼看出错误的方法，只要记住正常的日志是什么样的，以后只要遇到错误的日志就知道这不正常了，所有能通过日志看出来问题的问题，都不是说一下根据哪一点日志就能知道的，一般都是需要联系下上下文的日志，然后结合场景才好分析

没什么投机取巧的方式，其实都是熟能生巧，然后大家互相交流，分享自己所知道的

最后希望这一篇分析能对大家有所帮助，


错误码参考：[环信官方 API 文档 Error](http://docs.easemob.com/start/450errorcode/20androiderrorcode)