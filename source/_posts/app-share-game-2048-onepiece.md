---
title: 个人游戏 2048动漫版介绍与下载
categories:
  - APPShare
tags:
  - 2048
  - APP
  - Android
  - Game
  - OnePiece
  - Share
id: 796
date: 2014-10-28 09:55:10
---

身为一个程序猿，没有点儿自己的东西岂不是很悲哀，岂不是很丢人！

`So`不管怎样一定要搞出来一款自己的东西，就算很挫，那也是自己做出来的O(∩_∩)O~~
这次是自己动手写的，不过也参考了大牛的一些方法！少走不少弯路；自己做游戏自己玩，很爽 哈哈！

OK 废话不多说，开始正题：
这篇文章就是介绍我的第一款正式的游戏：2048动漫版，游戏模式仿照文字版2048，界面手动PS，自己玩儿的话肯定要加入自己喜欢的元素了，然后就摸索着实现了用图片的方式合并，就加入了我喜欢的海贼造型，就是有点儿太花了，还加了一个龙猫，不过没有找到很合适的图片，就随便`PS`了几张，效果还不错，等下给大家看看效果图；

下边这段话游戏的开发介绍
>   在浏览`Cocos2d-x`的时候无意间看到了大牛`touchsnow`以及他们的官方工具`CocosEditor` [CocosEditor官方博客](http://blog.makeapp.co)老牛了，最近有点儿迷上了它，虽然`bug`不少但是能省了不少开发工作，很喜欢，很强大；是针对cocos2d-js开发的，可以进行可视化编辑游戏界面，然后方便打包成apk，就跟着兴趣学了一下，当时也用`Cocos2d-Html5`实现了`2048`这个游戏，准确的说那时就是抄的！还有那个飞翔的小鸟，`cocos2d-js`开发起来还是挺快的，再加上之前学过一些`javascript`，很快就学会了，但是博主的那个插件`bug`还是比较多的（不过现在快更新了2.0版本，期待着发布）

>   学了一段时间的`cocos2d-js`后发现有些特效实现起来还是有些僵硬的，感觉没有`cocos2d-x`强大，所以就想着学习一下`Cocos2d-x`，因为是用`C++`语言实现的，所以要从头开始，还是比较蛋疼的，还好之前稍微看了写`C++`的书，多少也有些了解，然后就边爬帖子，边`google`，边`coding`，慢慢的算是入门了吧，就想着用`Cocos2d-x`实现我之前的`2048`动漫版，而且要做完整

>   这次实现这个游戏我添加上了我所能想到的，同时我现在的能力能实现的功能和效果！同时我也会写一系列文章，把开发这款游戏的过程记录下来，方便以后自己查看，也可以帮助新入门学习的朋友！


### 游戏下载
#### 本地下载：
[2048动漫版 - 本地下载](http://lzan13.qiniudn.com/apk-site-use/DM2048.apk)
#### 各大市场下载：
[2048动漫版 - Google play （已被封）](https://play.google.com/store/apps/details?id=net.melove.game.dm2048)
[2048动漫版 - 百度手机助手](http://shouji.baidu.com/game/item?docid=7559974&from=as)
[2048动漫版 - 小米商店](http://app.mi.com/detail/76048)
[2048动漫版 - 安智市场](http://www.anzhi.com/soft_1924688.html)
[2048动漫版 - 豌豆荚](http://www.wandoujia.com/apps/net.melove.game.dm2048)


### 截图
首先是进入游戏的主界面，有几个提示信息O(∩_∩)O~~，加上可选择游戏形式，还可以分享
[![dm2048_1](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_1.png)](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_1.png)

其中一种游戏形式
[![dm2048_2](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_2.png)](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_2.png)

另外一种形式
[![dm2048_3](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_3.png)](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_3.png)

可以进行截图分享，支持分享到微博，还可以调用Android系统分享功能，分享给你其他的好友，比如QQ、微信、等其他聊天与社交软件
[![dm2048_4](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_4.png)](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_4.png)

最后是一个动画的帮助页面，
[![dm2048_5](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_5.png)](http://wp-melove.qiniudn.com/blogimg/2014/10/dm2048_5.png)

有什么bug可以告诉我哈，多谢各位的反馈 O(∩_∩)O~~