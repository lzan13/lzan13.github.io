---
title: Android开发中使用单例模式
categories:
  - Develop
  - Android
tags:
  - Android
  - Sington
id: 10019
date: 2013-07-05 13:58:08
---

java中单例的使用在实际开发中还是有必要的，我的理解就是单例就像是一个地铁的入口闸，每一个闸口每次只能过一个人！

在使用SQLite数据库的时候用到了这个单例模式！如果两个线程同时操作一组数据的话将会乱套！

单例模式的实现有好多中，比如懒汉式，饿汉式，静态内部类，枚举，双重校验锁等… 我这里只记录推荐使用的实现单例的方式！

首先是饿汉式：
```java
public class DBHelper {
    //在类初始化的同时实例化一个类的实例
    private static DBHelper instance = new DBHelper();
    public static DBHelper getInstance(){
    	return instance;
    }
    // 私有的构造方法
    private DBHelper() {
    }
}
```
预先声明`DBHelper`对象，这种方式的一个缺点就是：如果构造的单例很大，构造完又迟迟不使用，会导致资源浪费，一般我们的实例还是不是很大的，这种方式也是比较常用的！

下面这种是内部静态类的方式，我感觉这种用着最好：
```java
public class DBHelper {
    //创建一个内部类来实现单例！
    private static class DBHelperHolder{
    	private static final DBHelper instance = new DBHelper();
    }
    public static DBHelper getInstance(){
    	return DBHelperHolder.instance;
    }
    // 私有的构造方法
    private DBHelper() {
    }
}
```
这种方式同样利用了`classloder`的机制来保证初始化`instance`时只有一个线程，而且这种方式是`DBHelper`类被装载了，`instance`不会被初始化。因为`DBHelperHolder`类没有被主动使用，只有显示的去调用`getInstance()`方法时，才会显示的装载`DBHelperHolder`类，从而实例化`instance`

文章参考：[问征夫以前路](http://www.blogjava.net/kenzhh/archive/2013/03/15/357824.html) 和 [刘水镜](http://blog.csdn.net/liushuijinger/article/details/9069801)