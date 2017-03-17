---
title: 关于环信3.xSDK日志简单分析
categories:
  - Develop
tags:
  - Android
  - Easemob
  - Hyphenate
  - IM
  - Log
  - SDK
id: 1489749364000
comments: true
date: 2017-03-17 19:16:04
---

>我的简书：【[lzan13](http://www.jianshu.com/u/f53bcbbc7c3e) / [关于环信3.xSDK日志简单分析](http://www.jianshu.com/p/a194fa19bd6a)】

前言
-----
首先说下环信日志保存的机制，这边只要是在开启了`SDK`的`Debug`模式下调用环信的`EMLog.d()`方法输出的日志，日志内容都会在控制台输出的同时记录到设备的日志文件中；

这里就一些常见的日志跟大家略微解释下，一边能给大家在以后排查问题中起到一些帮助；

>PS：这里所列出来的日志内容只是特定情况测试下出现，在不同的设备，不同环境下稍微会有些出入，不过一些关键词的地方都应该是一样的，不要死心眼就好；

网络相关
------

### SDK 的初始化
SDK 初始化的时候会首先检查 DNS 配置信息是否存在，如果不存在，在初始化时会首先请求 DNS 配置列表，请求成功后就会返回一个`json`串，2.x 是`xml`格式内容；如果当对方的网络情况有问题的时候，这一步就可以看出一些问题，比如请求 dns 列表失败等

关键词：`getDnsListFromServer`,`parseDnsServer`,`dns_time`
```
[2017/3/1 16:23:31:497]: =============EMChatClientImpl::init()==================
[2017/3/1 16:23:31:497]: dns config mode is 1
[2017/3/1 16:23:31:497]: EMConfigManager::init() 
[2017/3/1 16:23:31:497]: No config file, do nothing
[2017/3/1 16:23:31:497]: addConnectionListener
[2017/3/1 16:23:31:499]: =============== Call Manager init ===============
[2017/3/1 16:23:31:499]: addConnectionListener
[2017/3/1 16:23:31:506]: registerContactListener
[2017/3/1 16:23:31:511]: restBaseUrl()
[2017/3/1 16:23:31:511]: EMDNSManager::getCurrentHost: type: 2
[2017/3/1 16:23:31:512]: EMSessionManager::checkDNS()
[2017/3/1 16:23:31:512]: valid_time: 
[2017/3/1 16:23:31:512]: no saved dns list, download it
[2017/3/1 16:23:31:514]: getDnsListFromServer()
[2017/3/1 16:23:31:514]: buildUrl(): http://rs.easemob.com/easemob/server.json?sdk_version=3.2.3&app_key=easemob-demo%23chatdemoui&file_version=1
[2017/3/1 16:23:31:631]: [EMARHttpAPI] httpExecute code: 200
[2017/3/1 16:23:31:631]: 1 time retry
[2017/3/1 16:23:31:631]: DNS List size: 2115
[2017/3/1 16:23:31:631]: EMSessionManager::parseDnsServer: {"file_version":"61","resolver":{"hosts":[{"port":"80","domain":"rs.easemob.com","ip":"59.110.89.59"},{"port":"80","domain":"rs.easemob.com","ip":"112.126.66.111"},{"port":"80","domain":"rs.easemob.com","ip":"182.92.174.78"},{"port":"80","domain":"rs.easemob.com","ip":"116.62.83.103"},{"port":"80","domain":"rs.easemob.com","ip":"120.26.17.225"},{"port":"80","domain":"rs.easemob.com","ip":"120.26.15.207"},{"protocol":"https","port":"443","domain":"rs.easemob.com"}]},"rest":{"hosts":[{"protocol":"https","port":"443","domain":"a1.easemob.com"},{"protocol":"http","port":"80","domain":"a1.easemob.com","ip":"182.92.159.193"},{"protocol":"http","port":"80","domain":"a1.easemob.com","ip":"182.92.228.160"},{"protocol":"http","port":"80","domain":"a1.easemob.com","ip":"182.92.0.214"},{"protocol":"http","port":"80","domain":"a1.easemob.com","ip":"182.92.4.52"}]},"valid_before":"1496085791","im":{"hosts":[{"port":"443","domain":"im1.easemob.com","ip":"182.92.20.34"},{"port":"443","domain":"im1.easemob.com","ip":"182.92.20.117"},{"port":"443","domain":"im1.easemob.com","ip":"182.92.23.59"},{"port":"443","domain":"im1.easemob.com","ip":"182.92.26.56"}]},"deploy_name":"easemob","tcp_resolver":{"hosts":[{"port":"2020","domain":"rs.easemob.com","ip":"59.110.89.59"},{"port":"2020","domain":"rs.easemob.com","ip":"112.126.66.111"},{"port":"2020","domain":"rs.easemob.com","ip":"182.92.174.78"},{"port":"2020","domain":"rs.easemob.com","ip":"116.62.83.103"},{"port":"2020","domain":"rs.easemob.com","ip":"120.26.17.225"},{"port":"2020","domain":"rs.easemob.com","ip":"120.26.15.207"}]},"msync-im":{"hosts":[{"port":"6801","domain":"msync-im1.easemob.com","ip":"60.205.131.133"},{"port":"7830","domain":"msync-im1.easemob.com","ip":"60.205.109.58"}]},"xmpp":{"hosts":[{"protocol":"http","port":"80","domain":"im-api.easemob.com","ip":"im-api.easemob.com"},{"protocol":"http","port":"5280","domain":"im-api.easemob.com","ip":"182.92.159.193"},{"protocol":"http","port":"5280","domain":"im-api.easemob.com","ip":"182.92.228.160"},{"protocol":"https","port":"443","domain":"im-api.easemob.com"}]},"gcm_enabled":"true"}
[2017/3/1 16:23:31:631]: current time: 1488356611631
[2017/3/1 16:23:31:631]: valid time: 1496085791000
[2017/3/1 16:23:31:631]: saveConfigs()
[2017/3/1 16:23:31:632]: write to config file: {
    "dns_time":"1496085791000"
}
```

### 手动登录
当进行手动登录是，会首先获取 DNS 配置，（上边初始化时不是已经获取了么，为什么还要获取？因为 sdk 就这样写的，所以它又获取了一次😓）如果不存在就去获取，获取失败的情况下会进行多次尝试，然后获取完成 DNS 之后就开始登录了，登录要做的就是获取账户的 token，后续的操作都是根据这个 token 进行操作了

主要关键词：`login`,`getDnsListFromServer`,`fetchToken`,`token`
```log
[2017/3/1 16:39:6:481]: [EMClient] emchat manager login in process:19581
[2017/3/1 16:39:6:539]: EMSessionManager::login(): lz1
[2017/3/1 16:39:6:539]: getDnsListFromServer()
...
这里会同样的去获取 DNS 配置
...
[2017/3/1 10:1:41:794]: current host: domain: 182.92.0.214 port: 80
[2017/3/1 10:1:41:794]: fetchTokenForUser()http://182.92.0.214:80/shztzn/smartfarm/token
[2017/3/1 10:1:41:895]: [retrieve token time] 0: 0:101
[2017/3/1 10:1:41:895]: fetchToken success 
[2017/3/1 10:1:41:895]: saveToken(): user: pad8a7195bffad4b6bdb65e533e969e43c2 time: 1488333701895
[2017/3/1 10:1:41:921]: savetoken() result: 1
[2017/3/1 10:1:41:921]: EMDNSManager::getCurrentHost: type: 1
[2017/3/1 10:1:41:922]: EMSessionManager::checkDNS()
[2017/3/1 10:1:41:922]: EMDNSManager::getHost: type: 1
[2017/3/1 10:1:41:922]: current host: domain: 60.205.131.133 port: 6801
[2017/3/1 10:1:41:922]: token is valid
[2017/3/1 10:1:41:922]: setServer: 60.205.131.133
[2017/3/1 10:1:41:922]: Calling connect...
[2017/3/1 10:1:41:922]: doConnect()
[2017/3/1 10:1:41:922]: current connectState: 0
[2017/3/1 10:1:41:922]: log: level: 0, area: 1, ChatClient::connect() 
[2017/3/1 10:1:41:922]: log: level: 0, area: 2, getSocket(): 41
[2017/3/1 10:1:41:922]: log: level: 1, area: 2, connectSocket(): start to connecting...
[2017/3/1 10:1:41:958]: log: level: 1, area: 2, connectSocket(): connect finished
...
```

### 自动登录
在3.x中确定自动登录只需要一个关键词就好：`autoLogin`
需要注意一点，自动登录和重连不一样，自动登录包含初始化以及 DNS 的检测，重连一般不检测 DNS，自动登录最后输出的日志基本和重连的一样，都是进行网络链接；
```log
[2017/3/1 17:30:17:640]: autoLogin
[2017/3/1 17:30:17:641]: EMSessionManager::login(): lz1
[2017/3/1 17:30:17:641]: EMDNSManager::getCurrentHost: type: 1
[2017/3/1 17:30:17:641]: EMSessionManager::checkDNS()
[2017/3/1 17:30:17:641]: valid_time: 1496085791000
[2017/3/1 17:30:17:641]: EMSessionManager::parseDnsServer: autologin
[2017/3/1 17:30:17:642]: current time: 1488360617642
[2017/3/1 17:30:17:642]: valid time: 1496085791000
[2017/3/1 17:30:17:642]: saveConfigs()
[2017/3/1 17:30:17:643]: write to config file: {
    "dns_time":"1496085791000"
}
[2017/3/1 17:30:17:649]: checkDNS finished
[2017/3/1 17:30:17:651]: Calling connect...
[2017/3/1 17:30:17:651]: doConnect()
[2017/3/1 17:30:17:651]: current connectState: 0
[2017/3/1 17:30:17:651]: log: level: 0, area: 1, ChatClient::connect() 
[2017/3/1 17:30:17:653]: log: level: 0, area: 2, getSocket(): 35
[2017/3/1 17:30:17:653]: log: level: 1, area: 2, connectSocket(): start to connecting...
[2017/3/1 17:30:17:665]: log: level: 1, area: 2, connectSocket(): connect finished
```

### 退出登录
推出的登录的日志很简单，清空一些数据，然后断开连接等;

关键词：`logout`,`disconnect`,`cleanup`
```log
[2017/3/1 10:1:39:707]: begin logout ..
[2017/3/1 10:1:39:707]: EMSessionManager::disconnect()
[2017/3/1 10:1:39:707]: clearDnsConfig()
[2017/3/1 10:1:39:707]: logout complete
[2017/3/1 10:1:39:708]: saveConfigs()
[2017/3/1 10:1:39:708]: write to config file: {
    "dns_time":"-1"
}
[2017/3/1 10:1:40:265]: stopReceive()
[2017/3/1 10:1:40:265]: log: level: 0, area: 1, ChatClient::disconnect()
[2017/3/1 10:1:40:265]: log: level: 1, area: 2, cleanup() 41
[2017/3/1 10:1:40:265]: log: level: 1, area: 2, closeSocket() 41
[2017/3/1 10:1:40:265]: log: level: 2, area: 1, handleDisconnect:14
```

### 心跳定时器
一切都准备好了之后，就是定义心跳定时器，第一次设置时`2'00"`后启动心跳定时器，后边会慢慢增加到`4'30"`，然后会按照`4'30"`这样循环下去
```log
[2017/3/1 16:39:7:52]: [smart ping] change smart ping state from : EMReady to : EMEvaluating
[2017/3/1 16:39:7:52]: [smart ping] reset interval...
[2017/3/1 16:39:7:52]: [smart ping] change smart ping state from : EMEvaluating to : EMEvaluating
[2017/3/1 16:39:7:55]: [smart ping]  onConnectred ...
[2017/3/1 16:39:7:55]: [smart ping] reset interval...
[2017/3/1 16:39:7:66]: [smart ping] prevWifi:false isWifi:true prevWIFISSID: SSID"huanxin-unifi"
[2017/3/1 16:39:7:67]: [smart ping] change smart ping state from : EMEvaluating to : EMEvaluating
[2017/3/1 16:39:7:68]: [smart ping] reset currentInterval:02:00:000
[2017/3/1 16:39:7:68]: [smart ping] schedule next alarm
[2017/3/1 16:39:7:68]: [smart ping] current heartbeat interval : 02:00:000 smart ping state : EMEvaluating
```

### 链接监听与重连
连接监听主要是实现 EMConnectionListener 接口来监听设备和服务器的连接情况，其中牵扯到网络恢复后与服务器的重连，下边这部分日志是当检测到网络连接断开后，SDK和服务器的连接情况，下边的日志中当SDK 检测到链接不到服务器后会首先清空 socket 等一些链接相关信息，然后会回到链接监听的`onDisConnect()`方法，接着 SDK 会进行尝试重连 3 次，当尝试全部失败后就不在尝试，会安排一个定时器，定时器的时间是`2'45"`，到时间后会触发心跳重新进行尝试连接服务器，如果在这个心跳触发之前，网络状况有新的变化会直接重新尝试连接服务器；

关键词：`onDisconnect`,`disconnect`,`onNetworkChanged`,`EMHeartBeatReceiver`,`heartbeat`,`reconnect`,`doConnect`
```log
[2017/3/1 19:30:28:214]: log: level: 1, area: 2, cleanup() 34
[2017/3/1 19:30:28:214]: log: level: 1, area: 2, closeSocket() 34
[2017/3/1 19:30:28:214]: log: level: 2, area: 2, recv(): recv() failed. errno: 110: Connection timed out
[2017/3/1 19:30:28:214]: log: level: 1, area: 2, cleanup() -1
[2017/3/1 19:30:28:214]: log: level: 2, area: 1, handleDisconnect:1
[2017/3/1 19:30:28:214]: EMSessionManager::onDisConnect(): 1
[2017/3/1 19:30:28:214]: stopReceive()
[2017/3/1 19:30:28:214]: log: level: 0, area: 1, ChatClient::disconnect()
[2017/3/1 19:30:28:214]: log: level: 1, area: 2, cleanup() -1
[2017/3/1 19:30:28:214]: log: level: 2, area: 1, handleDisconnect:14
[2017/3/1 19:30:28:214]: network issue, just reconnect after random time
[2017/3/1 19:30:28:215]: EMSessionManager::scheduleReconnect() updateServer: 0 updateToken: 0
[2017/3/1 19:30:28:215]: EMSessionManager::delayReconnect()
[2017/3/1 19:30:28:215]: getDelayedTime(): 7
[2017/3/1 19:30:28:215]: notify state change to connection listener
[2017/3/1 19:30:28:216]: [global listener] onDisconnect303
[2017/3/1 19:30:28:216]: [smart ping]  onDisconnected ...303
[2017/3/1 19:30:28:616]: [EMClient] connectivity receiver onReceiver
[2017/3/1 19:30:28:629]: [EMClient] no data connection
[2017/3/1 19:30:28:630]: onNetworkChanged(): 0
[2017/3/1 19:30:28:630]: notify network broken
[2017/3/1 19:30:28:630]: EMSessionManager::disconnect()
[2017/3/1 19:30:28:632]: notify state change to connection listener
[2017/3/1 19:30:28:632]: [global listener] onDisconnect2
[2017/3/1 19:30:28:632]: [smart ping]  onDisconnected ...2
[2017/3/1 19:30:31:440]: [EMClient] connectivity receiver onReceiver
[2017/3/1 19:30:31:447]: [EMClient] no data connection
[2017/3/1 19:30:31:447]: onNetworkChanged(): 0
[2017/3/1 19:30:31:447]: notify network broken
[2017/3/1 19:30:31:447]: EMSessionManager::disconnect()
[2017/3/1 19:30:31:448]: notify state change to connection listener
[2017/3/1 19:30:31:449]: [global listener] onDisconnect2
[2017/3/1 19:30:31:449]: [smart ping]  onDisconnected ...2
[2017/3/1 19:30:31:492]: [EMClient] connectivity receiver onReceiver
[2017/3/1 19:30:31:511]: [EMClient] no data connection
[2017/3/1 19:30:31:511]: onNetworkChanged(): 0
[2017/3/1 19:30:31:511]: notify network broken
[2017/3/1 19:30:31:512]: EMSessionManager::disconnect()
[2017/3/1 19:30:31:512]: notify state change to connection listener
[2017/3/1 19:30:31:512]: [global listener] onDisconnect2
[2017/3/1 19:30:31:512]: [smart ping]  onDisconnected ...2
[2017/3/1 19:33:4:560]: [EMHeartBeatReceiver] onReceive EMHeartBeatReceiver
[2017/3/1 19:33:4:567]: [net] no data connection
[2017/3/1 19:33:4:570]: [smart ping] schedule next alarm
[2017/3/1 19:33:4:573]: [smart ping] current heartbeat interval : 02:45:000 smart ping state : EMEvaluating
[2017/3/1 19:33:4:579]: [net] no data connection
[2017/3/1 19:33:4:580]: [smart ping] is not connected to server, so use idle interval : 3 mins
[2017/3/1 19:36:19:566]: [EMClient] connectivity receiver onReceiver
[2017/3/1 19:36:19:577]: [net] wifi is connected
[2017/3/1 19:36:19:577]: [EMClient] has wifi connection
[2017/3/1 19:36:19:577]: onNetworkChanged(): 2
[2017/3/1 19:36:19:577]: network comes back, retry to connect
[2017/3/1 19:36:19:577]: EMSessionManager::reconnect()
[2017/3/1 19:36:19:577]: doConnect()
[2017/3/1 19:36:19:577]: current connectState: 0
[2017/3/1 19:36:19:577]: log: level: 0, area: 1, ChatClient::connect() 
[2017/3/1 19:36:19:596]: log: level: 0, area: 2, getSocket(): 67
[2017/3/1 19:36:19:596]: log: level: 1, area: 2, connectSocket(): start to connecting...
[2017/3/1 19:36:19:602]: log: level: 1, area: 2, connectSocket(): connect finished
```

### 被踢&强制下线&移除账户
和账户相关的操作其实也都是以消息的形式和服务器进行交互的，其中一个特殊属性为`ns`，类型是`STATISTIC`，然后具体的操作通过 `operation`进行细分：1.表示移除/禁用/强制下线，2.表示异地登录
这几个状态都会触发网络链接监听的断开`onDisconnect()`回调；

PS：需要注意的一点就是，当收到这几个状态的回调后需要手动调用`logout`，清除当前登录状态，因此在遇到被踢和强制下线的日志中一般都能看到和 logout 相关日志

关键词：`STATISTIC`,`operation`,`logout`,`onDisconnected`,`206`,`heart beat`
```log
异地登录
[2017/3/1 20:0:26:592]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { metas : [ { id : 304104413746698288, from : @easemob.com, to : easemob-demo#chatdemoui_lz1@easemob.com/mobile, timestamp : 1488369626401, ns : STATISTIC, payload : { operation : 2 } } ], queue : @easemob.com } }
[2017/3/1 20:0:26:592]: log: level: 0, area: 1, ChatClient::disconnect()
[2017/3/1 20:0:26:592]: log: level: 1, area: 2, cleanup() 35
[2017/3/1 20:0:26:592]: log: level: 1, area: 2, closeSocket() 35
[2017/3/1 20:0:26:592]: log: level: 2, area: 1, handleDisconnect:13
[2017/3/1 20:0:26:592]: EMSessionManager::onDisConnect(): 13
[2017/3/1 20:0:26:592]: stopReceive()
[2017/3/1 20:0:26:592]: log: level: 0, area: 1, ChatClient::disconnect()
[2017/3/1 20:0:26:592]: log: level: 1, area: 2, cleanup() -1
[2017/3/1 20:0:26:592]: log: level: 2, area: 1, handleDisconnect:14
[2017/3/1 20:0:26:593]: ConnUserLoginAnotherDevice
[2017/3/1 20:0:26:593]: begin logout ..
[2017/3/1 20:0:26:593]: EMSessionManager::disconnect()
[2017/3/1 20:0:26:593]: clearDnsConfig()
[2017/3/1 20:0:26:593]: logout complete
[2017/3/1 20:0:26:593]: notify state change to connection listener
[2017/3/1 20:0:26:618]: [smart ping]  onDisconnected ...206
[2017/3/1 20:0:26:640]: [EMPushHelper] push notification helper ondestory
[2017/3/1 20:0:26:641]: [EMClient]  SDK Logout
[2017/3/1 20:0:26:643]: [smart ping] stop heart beat timer
[2017/3/1 20:0:26:643]: [smart ping] change smart ping state from : EMEvaluating to : EMStopped
[2017/3/1 20:0:26:644]: [smart ping] reset interval...
[2017/3/1 20:0:26:645]: [smart ping] change smart ping state from : EMStopped to : EMEvaluating
[2017/3/1 20:0:26:650]: logout, user not login
[2017/3/1 20:0:26:650]: saveConfigs()
[2017/3/1 20:0:26:651]: write to config file: {
    "dns_time":"-1"
}

移除/禁用/强制下线
[2017/3/2 14:14:55:351]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { metas : [ { id : 304386456690886672, from : @easemob.com, to : easemob-demo#chatdemoui_lz1@easemob.com/mobile, timestamp : 1488435294646, ns : STATISTIC, payload : { operation : 1 } } ], queue : @easemob.com } }
```

### 网络有问题的日志 TODO
下边这段日志是在当前网络有问题的情况下进行登录请求，可以看到请求结果是`408`，正常情况重连服务器是不需要获取DNS 的，但是这个应该是 DNS 配置已经失效，这边一直在尝试重新获取 DNS；不过从后边的日志可以看出是网络连接有问题；

关键词：`disconnect`,`handleDisconnect`,`checkDNS`,`408`,`reconnect`,`unreachable`
```log
[2017/3/1 9:59:52:649]: log: level: 0, area: 1, ChatClient::disconnect()
[2017/3/1 9:59:52:649]: log: level: 1, area: 2, cleanup() -4
[2017/3/1 9:59:52:649]: log: level: 2, area: 1, handleDisconnect:14
[2017/3/1 9:59:52:649]: dns error, reconnect using different IP
[2017/3/1 9:59:52:649]: EMSessionManager::scheduleReconnect() updateServer: 1 updateToken: 0
[2017/3/1 9:59:52:649]: EMDNSManager::getNextAvailableHost: type: 1
[2017/3/1 9:59:52:649]: EMSessionManager::checkDNS()
[2017/3/1 9:59:52:649]: tried all candidates, download again
[2017/3/1 9:59:52:649]: getDnsListFromServer()
[2017/3/1 9:59:52:649]: EMDNSManager::getHost: type: 0
[2017/3/1 9:59:52:649]: current host: domain: 120.26.17.225 port: 80
[2017/3/1 9:59:52:649]: buildUrl(): http://120.26.17.225/easemob/server.json?sdk_version=3.2.3&app_key=shztzn%23smartfarm&file_version=61
[2017/3/1 9:59:52:651]: notify state change to connection listener
[2017/3/1 9:59:52:664]: 1 time retry
[2017/3/1 9:59:52:664]: getDnsListFromServer code: 408 response: 
[2017/3/1 9:59:52:664]: EMDNSManager::getHost: type: 0
[2017/3/1 9:59:52:665]: current host: domain: 59.110.89.59 port: 80
[2017/3/1 9:59:52:665]: buildUrl(): http://59.110.89.59/easemob/server.json?sdk_version=3.2.3&app_key=shztzn%23smartfarm&file_version=61
[2017/3/1 9:59:52:673]: 2 time retry
[2017/3/1 9:59:52:673]: getDnsListFromServer code: 408 response: 
[2017/3/1 9:59:52:674]: EMDNSManager::getHost: type: 0
[2017/3/1 9:59:52:674]: current host: domain: 112.126.66.111 port: 80
[2017/3/1 9:59:52:674]: buildUrl(): http://112.126.66.111/easemob/server.json?sdk_version=3.2.3&app_key=shztzn%23smartfarm&file_version=61
[2017/3/1 9:59:52:674]: EMDNSManager::getNextAvailableHost: new index: 0
[2017/3/1 9:59:52:675]: EMDNSManager::getHost: type: 1
[2017/3/1 9:59:52:675]: current host: domain: 60.205.131.133 port: 6801
[2017/3/1 9:59:52:675]: setServer: 60.205.131.133
[2017/3/1 9:59:52:676]: EMSessionManager::delayReconnect()
[2017/3/1 9:59:52:676]: getDelayedTime(): 17
[2017/3/1 9:59:52:676]: Calling connect result: 0
[2017/3/1 9:59:53:505]: asyncSendMessage: 14883335935043542 retry = 3
[2017/3/1 9:59:53:505]: EMSessionManager::reconnect()
[2017/3/1 9:59:53:506]: doConnect()
[2017/3/1 9:59:53:506]: current connectState: 0
[2017/3/1 9:59:53:506]: log: level: 0, area: 1, ChatClient::connect() 
[2017/3/1 9:59:53:506]: log: level: 0, area: 2, getSocket(): 41
[2017/3/1 9:59:53:506]: log: level: 1, area: 2, connectSocket(): start to connecting...
[2017/3/1 9:59:53:508]: log: level: 2, area: 2, connectSocket(): 60.205.131.133 error: Network is unreachable
[2017/3/1 9:59:53:508]: log: level: 1, area: 2, closeSocket() 41
[2017/3/1 9:59:53:508]: log: level: 1, area: 2, connectSocket(): connect finished
[2017/3/1 9:59:53:508]: log: level: 2, area: 2, connect(): connection refused
[2017/3/1 9:59:53:508]: log: level: 1, area: 2, cleanup() -4
[2017/3/1 9:59:53:508]: log: level: 2, area: 1, handleDisconnect:4
[2017/3/1 9:59:53:508]: EMSessionManager::onDisConnect(): 4
[2017/3/1 9:59:53:508]: stopReceive()
```

### 推送相关
和推送相关的日志都是和`EMPushHelper`或者`EMPushManager`相关的，可以从日志中看到推送功能是否激活，然后获取`devicetoken`发送到服务器等，`devicetoken`这个值一定要存在，否者推送无效；这些可以作为检测集成离线推送本地设置是否有效的一些手段；

关键词：`GCM`,`push`,`devicetoken`,`sendDeviceToServer`
```log
[2017/3/1 16:39:7:202]: isEnabledGCM: 1
[2017/3/1 16:39:7:202]: [EMPushHelper] GCM is enabled : true
[2017/3/1 16:39:7:207]: [EMPushHelper] GCM service available : false
[2017/3/1 16:39:7:217]: [EMPushHelper] mipush available : true
[2017/3/1 16:39:7:218]: [EMPushHelper] third-party push available
[2017/3/1 16:39:7:221]: [EMPushManager] nick name is null or empty
[2017/3/1 16:39:7:708]: [EMMipushReceiver] mi push reigster success
[2017/3/1 16:39:7:708]: [EMPushHelper] devicetoken = /cRO2vRH64mWOH46+We1zxt4+onQRJOe26fvcX02iC0=
[2017/3/1 16:39:7:715]: [HttpClientManager] try send request, request url: http://182.92.4.52:80/easemob-demo/chatdemoui/devices with number: 0
[2017/3/1 16:39:7:792]: [EMPushHelper] sendDeviceToServer SC_OK:

修改离线推送相关
[2017/3/2 13:11:53:423]: SetGroupsOfNotificationDisabled 1
[2017/3/2 13:11:53:564]: update user configs:   {
  "action" : "put",
  "application" : "4d7e4ba0-dc4a-11e3-90d5-e1ffbaacdaf5",
  "path" : "/users",
  "uri" : "https://a1.easemob.com/easemob-demo/chatdemoui/users",
  "entities" : [ {
    "uuid" : "479d9cea-134d-11e6-8943-534ab75c4508",
    "type" : "user",
    "created" : 1462513331630,
    "modified" : 1488431513381,
    "username" : "lz1",
    "activated" : true,
    "notification_display_style" : 1,
    "device_token" : "/cRO2vRH64mWOH46+We1zxt4+onQRJOe26fvcX02iC0=",
    "nickname" : "立正1",
    "notification_ignore_1479267663683" : true,
    "notification_ignore_1483610597352" : true,
    "notification_ignore_1488431342804" : false,
    "notifier_name" : "2882303761517426801"
  } ],
  "timestamp" : 1488431513377,
  "duration" : 33,
  "organization" : "easemob-demo",
  "applicationName" : "chatdemoui"
}
```

消息相关
------
消息相关的日志还是比较明确的，

### 发送消息
发送新消息时会首先生成一个临时的 msgId，这个 msgId 是当前时间戳+四位拼成；当消息发送成功后，服务器会返回对应的服务器生成的 msgId 然后更新到本地，这算是一个消息的发送完成（可以根据这部分来判断消息是否发送成功）；
然后如果开启了已读回执，当对方读了消息之后，发送者会收到一条通知（NOYICE 的消息）告诉你有因消息，因为 ack 也是新消息，所以也是需要自己去服务器同步拉取（注意日志中的 REAF_ACK）；当做完这些之后，SDK 会重新发一条同步的消息，来看下是否还有新的消息

关键词：`asyncSendMessage`,`SEND`,`RECV`,`NOTICE`,`server_id`,`READ_ACK`
```log
[2017/3/1 21:7:0:649]: asyncSendMessage: 14883736206090002 retry = 3
[2017/3/1 21:7:0:650]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 14883736206090002, to : lz2, ns : CHAT, payload : { chattype : CHAT, from : lz1, to : lz2, contents : [ { contenttype : TEXT, text : www问问 } ] } } } }

消息发送成功，返回服务器生成的 msgId
[2017/3/1 21:7:0:743]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, meta_id : 14883736206090002, server_id : 304121568710101040, timestamp : 1488373620639 } }

服务器告诉 SDK 有新消息了
[2017/3/1 21:7:1:314]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : NOTICE, payload : { queue : lz2/mobile } }

发送请求来同步新消息
[2017/3/1 21:7:1:314]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { queue : lz2/mobile } }

收到已读的 ACK
[2017/3/1 21:7:1:333]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304121571285403696, timestamp : 1488373621196, ns : CHAT, payload : { chattype : READ_ACK, from : lz2, to : lz1, ack_message_id : 304121568710101040 } } ], next_key : 304121571285403696, queue : lz2, timestamp : 1488373621267 } }

查询是否还有新消息
[2017/3/1 21:7:1:333]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { key : 304121571285403696, queue : lz2 } }
[2017/3/1 21:7:1:515]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, queue : lz2, is_last : true, timestamp : 1488373621351 } }

...
发送已读 ACK
[2017/3/2 11:7:8:116]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 775, to : admin, ns : CHAT, payload : { chattype : READ_ACK, from : lz1, to : admin, ack_message_id : 304337511725925492 } } } }
```

### 收到新消息
3.x收到新消息后服务器并不会直接下发消息，而是给SDK端下发一个通知（NOTICE 的消息），告诉你有新消息，就像下边日志的第一行，sdk 收到这条消息之后，会给服务器发送一个我要获取新消息的消息（有点绕），然后服务器才会把这条消息给下发，当接收完这次通知的消息后，SDK 还会给服务器在发送一条同步消息，用来询问服务器是否还有新的消息，当返回结果包含`is_last:true`时，表示已经没有新消息；
当消息接收完成之后就是SDK内部给上层发送通知了；最后就是当自己看了新消息之后，还要给对方发送一条ACK 的信息，然后还要收到一条服务器返回的一条状态信息才算完事；

关键词：`RECV`,`NOTICE`,`CHAT`,`compress_algorimth`,`callbackReceievedMessages`,`onMessageReceived`,`COMMAND`,`action`,`READ_ACK`,`is_last`
```log
服务器告诉 SDK 有新消息了
[2017/3/1 20:58:54:6]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : NOTICE, payload : { queue : lz2/mobile } }

发送请求来同步新消息
[2017/3/1 20:58:54:6]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { queue : lz2/mobile } }

[2017/3/1 20:58:54:32]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304119478168324144, timestamp : 1488373133870, ns : CHAT, payload : { chattype : CHAT, from : lz2, to : lz1, contents : [ { contenttype : TEXT, text : qqqqqqqqqqqq } ] } } ], next_key : 304119478168324144, queue : lz2, timestamp : 1488373133959 } }

查询是否还有新消息
[2017/3/1 20:58:54:32]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { key : 304119478168324144, queue : lz2 } }
[2017/3/1 20:58:54:106]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, queue : lz2, is_last : true, timestamp : 1488373134006 } }

发送 ACK 给对方
[2017/3/1 21:5:55:298]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 259, to : lz2, ns : CHAT, payload : { chattype : READ_ACK, from : lz1, to : lz2, ack_message_id : 304119478168324144 } } } }
[2017/3/1 21:5:55:364]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, meta_id : 259, server_id : 304121288056637488, timestamp : 1488373555277 } }

收到 cmd 消息
[2017/3/2 11:6:13:653]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304337832351106168, timestamp : 1488423973404, ns : CHAT, payload : { chattype : CHAT, from : admin, to : lz1, contents : [ { contenttype : COMMAND, action : action1 } ] } } ], next_key : 304337832351106168, queue : admin, timestamp : 1488423973440 } }

收到离线消息，这个直接是一个 json 数组
[2017/3/2 12:46:37:98]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304343301819992124, timestamp : 1488425246875, ns : CHAT, payload : { chattype : CHATROOM, from : qazqaz123321, to : 3759501541378, contents : [ { contenttype : VOICE, displayname : qazqaz12332120170302T112724.amr, remotepath : https://a1.easemob.com/easemob-demo/chatdemoui/chatfiles/289e8240-fef8-11e6-b120-0b193a44693b, secretkey : KJ6CSv74EeacsQ3KDqwnqF9amIMbc6fNBK-qXRVNwd4MkZR7, filelength : 2134, duration : 2 } ] } }, { id : 304343752070137864, timestamp : 1488425351703, ns : CHAT, payload : { chattype : CHATROOM, from : lingo, to : 3759501541378, contents : [ { contenttype : VOICE, displayname : lingo20170302T112907.amr, remotepath : https://a1.easemob.com/easemob-demo/chatdemoui/chatfiles/671458b0-fef8-11e6-bf54-a356d485d240, secretkey : ZxRYuv74EeankPOIMACFAftORz04rIiMWfHPQUKLvcXi_Jgo, filelength : 2566, duration : 3 } ] } }, { id : 304345086324377616, timestamp : 1488425662350, ns : CHAT, payload : { chattype : CHATROOM, from : 15515625700, to : 3759501541378, contents : [ { contenttype : TEXT, text : [):] } ], exts : [ { key : userIcon, type : 7, value : https://116.255.227.78:1008/FileUpload/UserImage/7521a766-64db-4a18-8772-d22c5e065d0b.jpg }, { key : userName, type : 7, value : hl_4a8fe2f } ] } }, { id : 304347342838958040, timestamp : 1488426187748, ns : CHAT, payload : { chattype : CHATROOM, from : kimpeng, to : 3759501541378, contents : [ { contenttype : TEXT, text : [+o(] } ] } }, { id : 304347456840140856, timestamp : 1488426214277, ns : CHAT, payload : { chattype : CHATROOM, from : 15172330957, to : 3759501541378, contents : [ { contenttype : TEXT, text : 基本 } ] } }, { id : 304348603307001816, timestamp : 1488426481208, ns : CHAT, payload : { chattype : CHATROOM, from : kimpeng, to : 3759501541378, contents : [ { contenttype : TEXT, text : [:(] } ] } }, { id : 304349346936129496, timestamp : 1488426654367, ns : CHAT, payload : { chattype : CHATROOM, from : kimpeng, to : 3759501541378, contents : [ { contenttype : TEXT, text : [:(][(a)] } ] } }, { id : 304349681360570328, timestamp : 1488426732212, ns : CHAT, payload : { chattype : CHATROOM, from : kimpeng, to : 3759501541378, contents : [ { contenttype : TEXT, text : A } ] } }, { id : 304350070763947992, timestamp : 1488426822893, ns : CHAT, payload : { chattype : CHATROOM, from : kimpeng, to : 3759501541378, contents : [ { contenttype : TEXT, text : A } ] } }, { id : 304350107678017496, timestamp : 1488426831478, ns : CHAT, payload : { chattype : CHATROOM, from : kimpeng, to : 3759501541378, contents : [ { contenttype : TEXT, text : A } ] } }, { id : 304363701610219560, timestamp : 1488429996565, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_3759501541378@conference.easemob.com, operation : JOIN, from : easemob-demo#chatdemoui_lz1@easemob.com, status : { error_code : 0, description :  } } } ], next_key : 304363701610219560, queue : 3759501541378@conference.easemob.com, timestamp : 1488429996918 } }
``` 

业务逻辑相关
---------
在3.x 中一切的操作都是用消息的形式来做的，通过发送代表特定操作的消息给服务器，然后服务器会返回这个消息发送成功，接着服务器会以新消息的形式告诉 SDK 操作成功，最后都和收发消息的日志逻辑一样；
需要注意的就是这些操作中的关键词；
首先说一点就是，这些操作都有一个特殊的属性 `ns`这个属性的指表示不同的操作:`MUC`表示群组和聊天室相关操作；`ROSTER`表示联系人相关操作

下边先用创建新群组来详细解释下：

### 创建新群组
从创建群组可以看出，创建的操作就是给服务器发送一个创建的操作，接着服务器返回这条消息发送成功，然后就像收到消息一样，服务器告诉创建者，你有新消息需要拉取，拉取到后可以发现，这条消息就是告诉自己群组创建成功，然后后边就是检查是否有新消息;

关键词：`MUC`,`SEND`,`CREATE`,`RECV`,
```log
[2017/3/2 12:7:35:249]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 2567, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488427655248, operation : CREATE, from : lz1, setting : { name : ttttt, desc : tttttt, muc_type : 2, max_users : 200, owner : lz1 }, reason : lz1邀请加入群：ttttt } } } }

[2017/3/2 12:7:35:316]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, meta_id : 2567, server_id : 304353645141428264, timestamp : 1488427655125 } }

[2017/3/2 12:7:35:317]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : NOTICE, payload : { queue : 1488427655248@conference.easemob.com } }

[2017/3/2 12:7:35:317]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { queue : 1488427655248@conference.easemob.com } }

[2017/3/2 12:7:35:372]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304353645279840296, timestamp : 1488427655132, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488427655248@conference.easemob.com, operation : CREATE, from : easemob-demo#chatdemoui_lz1@easemob.com, status : { error_code : 0, description :  } } } ], next_key : 304353645279840296, queue : 1488427655248@conference.easemob.com, timestamp : 1488427655190 } }

[2017/3/2 12:7:35:373]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { key : 304353645279840296, queue : 1488427655248@conference.easemob.com } }

[2017/3/2 12:7:35:453]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, queue : 1488427655248@conference.easemob.com, is_last : true, timestamp : 1488427655254 } }
```

### 群组其他操作
关键词：`MUC`,`operation`,`SEND`,`APPLY`,`INVITE`,`INVITE_ACCEPT`,`INVITE_DECLINE`,`APPLY_ACCEPT`,`APPLY_DECLINE``PRESENCE`,`LEAVE`,`DESTROY`,`KICK`,`ABSENCE`,`RECV`,`BLOCK`,`UNBLOCK`
```log
发送加入请求：
[2017/3/2 12:26:38:737]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 3591, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488428778313, operation : APPLY, from : lz1, tos : [ lz2 ], reason : 请求加入 } } } }

收到加入请求：
[2017/3/2 12:16:48:410]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304356020619053112, timestamp : 1488428208185, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488427655248@conference.easemob.com, operation : APPLY, from : easemob-demo#chatdemoui_lz2@easemob.com, tos : [ easemob-demo#chatdemoui_lz1@easemob.com ], reason : 请求加入, is_chatroom : false, status : { error_code : 0, description :  } } } ], next_key : 304356020619053112, queue : lz2, timestamp : 1488428208229 } }

邀请加入群组
[2017/3/2 11:39:39:774]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 2055, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1483610597352, operation : INVITE, from : lz1, tos : [ lz2 ], reason : welcome } } } }
[2017/3/2 11:39:41:64]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304346454208546772, timestamp : 1488425980847, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1483610597352@conference.easemob.com, operation : INVITE_ACCEPT, from : easemob-demo#chatdemoui_lz2@easemob.com, tos : [ easemob-demo#chatdemoui_lz1@easemob.com ], is_chatroom : false, status : { description :  } } } ], next_key : 304346454208546772, queue : 1483610597352@conference.easemob.com, timestamp : 1488425980883 } }
[2017/3/2 11:39:41:138]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304346454330181588, timestamp : 1488425980876, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1483610597352@conference.easemob.com, operation : PRESENCE, from : easemob-demo#chatdemoui_lz2@easemob.com, is_chatroom : false } } ], next_key : 304346454330181588, queue : 1483610597352@conference.easemob.com, timestamp : 1488425980928 } }

同意对方邀请
[2017/3/2 12:20:41:289]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 2823, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488427655248, operation : APPLY_ACCEPT, from : lz1, tos : [ lz2 ] } } } }
[2017/3/2 12:20:42:696]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304357026689980384, timestamp : 1488428442436, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488427655248@conference.easemob.com, operation : PRESENCE, from : easemob-demo#chatdemoui_lz2@easemob.com, is_chatroom : false } } ], next_key : 304357026689980384, queue : 1488427655248@conference.easemob.com, timestamp : 1488428442505 } }

拒绝对方邀请
[2017/3/2 12:21:34:594]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 3335, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488427655248, operation : APPLY_DECLINE, from : lz1, tos : [ lz2 ] } } } }

离开群组
[2017/3/2 11:7:24:803]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 1031, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1482203912329, operation : LEAVE, from : lz1 } } } }
[2017/3/2 11:7:25:183]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304338138476578856, timestamp : 1488424044686, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1482203912329@conference.easemob.com, operation : LEAVE, from : easemob-demo#chatdemoui_lz1@easemob.com, status : { error_code : 0, description :  } } } ], next_key : 304338138476578856, queue : 1482203912329@conference.easemob.com, timestamp : 1488424044990 } }

群组踢人
[2017/3/2 11:30:40:684]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 1799, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1483610597352, operation : KICK, from : lz1, tos : [ lz2 ] } } } }
[2017/3/2 11:30:41:122]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304344134942984200, timestamp : 1488425440851, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1483610597352@conference.easemob.com, operation : ABSENCE, from : easemob-demo#chatdemoui_lz2@easemob.com, is_chatroom : false } } ], next_key : 304344134942984200, queue : 1483610597352@conference.easemob.com, timestamp : 1488425440955 } }

服务器删除群组
[2017/3/2 13:0:44:727]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304367343482439644, timestamp : 1488430844502, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488427655248@conference.easemob.com, operation : DESTROY, from : easemob-demo#chatdemoui_系统管理员@easemob.com, tos : [ easemob-demo#chatdemoui_lz1@easemob.com ], is_chatroom : false, status : { error_code : 0, description :  } } } ], next_key : 304367343482439644, queue : 1488427655248@conference.easemob.com, timestamp : 1488430844552 } }

屏蔽群消息
[2017/3/2 13:10:23:189]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 5895, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488431342804, operation : BLOCK, from : lz1 } } } }
[2017/3/2 13:10:23:278]: log: level: 1, area: 1, ChatClient::handleSync begin
[2017/3/2 13:10:23:278]: log: level: 1, area: 1, ChatClient::handleSync complete: response
[2017/3/2 13:10:23:310]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304369828473346088, timestamp : 1488431423075, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488431342804@conference.easemob.com, operation : BLOCK, from : easemob-demo#chatdemoui_lz1@easemob.com, tos : [ easemob-demo#chatdemoui_lz1@easemob.com ], status : { error_code : 0, description :  } } } ], next_key : 304369828473346088, queue : 1488431342804@conference.easemob.com, timestamp : 1488431423128 } }

解除屏蔽群消息
[2017/3/2 15:3:29:333]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 521, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488431342804, operation : UNBLOCK, from : lz1 } } } }
[2017/3/2 15:3:32:900]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304398975526508532, timestamp : 1488438209409, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_1488431342804@conference.easemob.com, operation : UNBLOCK, from : easemob-demo#chatdemoui_lz1@easemob.com, tos : [ easemob-demo#chatdemoui_lz1@easemob.com ], status : { error_code : 0, description :  } } } ], next_key : 304398975526508532, queue : 1488431342804@conference.easemob.com, timestamp : 1488438210535 } }
```

### 聊天室
单单从日志看，这些操作其实和群组的都一样

关键词：`MUC`,`JOIN`,`PRESENCE`,`ABSENCE`,`KICK`
```log
自己加入聊天室
[2017/3/2 12:46:36:668]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 5383, to : @easemob.com, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_3759501541378@conference.easemob.com, operation : JOIN, from : lz1 } } } }

收到别人加入聊天室的回调
[2017/3/2 12:53:40:186]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304365519564179492, timestamp : 1488430419846, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_3759501541378@conference.easemob.com, operation : PRESENCE, from : easemob-demo#chatdemoui_lz2@easemob.com, is_chatroom : true } } ], next_key : 304365519564179492, queue : 3759501541378@conference.easemob.com, timestamp : 1488430420020 } }

其他人离开聊天室回调
[2017/3/2 12:56:56:655]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304366363252623336, timestamp : 1488430616269, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_3759501541378@conference.easemob.com, operation : ABSENCE, from : easemob-demo#chatdemoui_lz2@easemob.com, is_chatroom : true } } ], next_key : 304366363252623336, queue : 3759501541378@conference.easemob.com, timestamp : 1488430616481 } }

自己被后台踢出聊天室
[2017/3/2 12:58:30:329]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304366766241351692, timestamp : 1488430710093, ns : MUC, payload : { muc_id : easemob-demo#chatdemoui_3759501541378@conference.easemob.com, operation : KICK, from : easemob-demo#chatdemoui_系统管理员@easemob.com, tos : [ easemob-demo#chatdemoui_lz1@easemob.com ], is_chatroom : true, status : { error_code : 0, description :  } } } ], next_key : 304366766241351692, queue : 3759501541378@conference.easemob.com, timestamp : 1488430710153 } }
```

### 联系人
关键词：`ROSTER`,`ADD`,`REMOVE`,`ACCEPT`,`REMOTE_ACCEPT`,`DECLINE`,`REMOTE_DECLINE`,`BAN`
```log
收到添加请求
[2017/3/2 12:31:51:693]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304359900245395412, timestamp : 1488429111488, ns : ROSTER, payload : { operation : ADD, from : easemob-demo#chatdemoui_lz2@easemob.com/mobile, tos : [ easemob-demo#chatdemoui_lz1@easemob.com ], reason : 加个好友呗 } } ], next_key : 304359900245395412, queue : @easemob.com, timestamp : 1488429111530 } }

删除：
[2017/3/2 12:31:34:502]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 3847, to : @easemob.com, ns : ROSTER, payload : { operation : REMOVE, tos : [ easemob-demo#chatdemoui_lz2 ], reason : , bi_direction : 0 } } } }
[2017/3/2 12:31:34:628]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304359826958321704, timestamp : 1488429094423, ns : ROSTER, payload : { operation : REMOVE, from : easemob-demo#chatdemoui_lz2@easemob.com, tos : [ easemob-demo#chatdemoui_lz1@easemob.com/mobile ], roster_ver : DB5BEDE0B3C0828FF9C2B7E2C119D30AC8AA66E4 } } ], next_key : 304359826958321704, queue : @easemob.com, timestamp : 1488429094467 } }

同意：
[2017/3/2 12:40:33:251]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304362140305721384, timestamp : 1488429633032, ns : ROSTER, payload : { operation : ACCEPT, from : easemob-demo#chatdemoui_lz2@easemob.com, tos : [ easemob-demo#chatdemoui_lz1@easemob.com/mobile ], roster_ver : 31A3278F5D16C2726689A176C39D81FF9961647A } } ], next_key : 304362140305721384, queue : @easemob.com, timestamp : 1488429633077 } }

被同意：
[2017/3/2 12:42:59:308]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304362767614216148, timestamp : 1488429779090, ns : ROSTER, payload : { operation : REMOTE_ACCEPT, from : easemob-demo#chatdemoui_lz2@easemob.com/mobile, tos : [ easemob-demo#chatdemoui_lz1@easemob.com ], roster_ver : 13B1298E1FAF70683EB467693584B2FD74CBDC6D } } ], next_key : 304362767614216148, queue : @easemob.com, timestamp : 1488429779133 } }

拒绝：
[2017/3/2 12:39:52:931]: log: level: 1, area: 1, SEND:
{ verison : MSYNC_V1, compress_algorimth : 0, command : SYNC, payload : { meta : { id : 4103, to : @easemob.com, ns : ROSTER, payload : { operation : DECLINE, tos : [ easemob-demo#chatdemoui_lz2 ], reason : , bi_direction : 0 } } } }

被拒绝：
[2017/3/2 12:42:59:308]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304362767614216148, timestamp : 1488429779090, ns : ROSTER, payload : { operation : REMOTE_DECLINE, from : easemob-demo#chatdemoui_lz2@easemob.com/mobile, tos : [ easemob-demo#chatdemoui_lz1@easemob.com ], roster_ver : 13B1298E1FAF70683EB467693584B2FD74CBDC6D } } ], next_key : 304362767614216148, queue : @easemob.com, timestamp : 1488429779133 } }

加入黑名单
[2017/3/2 12:43:18:539]: log: level: 1, area: 1, RECV:
{ verison : MSYNC_V1, command : SYNC, payload : { status : { error_code : 0 }, metas : [ { id : 304362850200061992, timestamp : 1488429798324, ns : ROSTER, payload : { operation : BAN, status : { error_code : 0 }, from : easemob-demo#chatdemoui_lz2@easemob.com, tos : [ easemob-demo#chatdemoui_lz1@easemob.com/mobile ] } } ], next_key : 304362850200061992, queue : @easemob.com, timestamp : 1488429798362 } }
```

通话相关
-----
由于通话功能大部分都是由底层实现，这边暂时无法看懂，也就先不写了😁😝

结束语
------
没有什么能一眼看出错误的方法，只要记住正常的日志是什么样的，以后只要遇到错误的日志就知道这不正常了，所有能通过日志看出来问题的问题，都不是说一下根据哪一点日志就能知道的，一般都是需要联系下上下文的日志，然后结合场景才好分析

没什么投机取巧的方式，其实都是熟能生巧，然后大家互相交流，分享自己所知道的

最后希望这一篇分析能对大家有所帮助，

错误码参考：[环信官方 API 文档 Error](http://www.easemob.com/apidoc/android/chat3.0/classcom_1_1hyphenate_1_1_e_m_error.html)