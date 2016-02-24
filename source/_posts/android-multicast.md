---
title: Android开发之获取组播信息
categories:
  - Develop
  - Android
tags:
  - Android
  - Multicast
  - MulticastLock
id: 10017
date: 2014-02-12 22:26:33
---

在`Android`开发中，默认是不接收组播信息的，因为移动设备的电量是很不耐用的！而组播又是需要时刻通讯，非常的耗电！
这几天要做到用Android设备接收组播，就查了下！可以手动获取组播锁`MulticastLock`
不过`MulticastLock`是不能直接获得的，必须通过`WIFIManager`获得
看下`google`的api：[MulticastLock API](http://developer.android.com/reference/android/net/wifi/WifiManager.MulticastLock.html)  
这里边包含有多个方法，
其中下边这两个就是我们要用到的！
```java
acquire() Locks Wifi Multicast on until release() is called.
release() Unlocks Wifi Multicast, restoring the filter of packets not addressed specifically to this device and saving power.
```
`acquire()`方法是获取组播锁，当我们接收完组播信息之后，要记得调用`release()`方法释放组播锁，节省资源

__获取组播锁的代码__很简单的一个方法！
```java
private MulticastLock mLock;
private void allowMulticast(){
	WifiManager wifiManager = (WifiManager) getSystemService(Context.WIFI_SERVICE);
	mLock = wifiManager.createMulticastLock("multicast.avcit");
	mLock.acquire();
}
```

__然后获取组播信息__
获取组播信息和java普通的获取UDP信息是一样的，只是使用的是DatagramSocket的子类MulticastSocket来获取信息，
```java
private int port = 2068;
private String group_ip;
private MulticastSocket mSocket; //组播
private DatagramPacket inPacket;
private InetAddress group;
private byte[] buffer = new byte[1024];

private void initSocket(){
	try {
		group = InetAddress.getByName(group_ip);
		mSocket = new MulticastSocket(port);
		//设置缓冲区大小
		mSocket.setReceiveBufferSize(rBuffLen);
		mSocket.joinGroup(group);	//设置加入组播组
		//设置组播组数据包回送模式，为true表示不接受自己发送的数据包
		mSocket.setLoopbackMode(true);	
		inPacket = new DatagramPacket(buffer, buffer.length);

		while(threadState){
			mSocket.receive(inPacket);
			definitionData(inPacket.getData());//这里调用自己解析数据的方法
		}
	} catch (IOException e) {
		e.printStackTrace();
	}
}
```
最后记得调用`mLock.release();`方法
OK 这里就不符上所有的代码了，主要代码就是这么多！