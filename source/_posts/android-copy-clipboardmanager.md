---
title: Android开发ClipboardManager的使用复制字符串到剪切板
date: 2015-03-29 16:08:11
categories:
  - Develop
  - Android
tags:
  - Android
  - ClipboardManager
id: 10009
---

最近做的一个项目中需要复制一个字符串到系统的剪切板，以便用户发送给其他人，因为我所需要复制的内容是确定的，因此不想通过让用户长按需要复制的内容，然后选择复制，感觉那样多此一举，so选择直接通过代码来复制到剪切板，查了一下资料，发现Android是有提供这个功能的：
通过使用`ClipboardManager`来实现；[这里是ClipboardManager官方文档](http://developer.android.com/reference/android/content/ClipboardManager.html "ClipboardManager api")

`ClipboardManager`继承自`Object`类，我们主要使用的方法就是setText(String)不过这个方法在API 11后是废除的，使用setPrimaryClip(ClipData)方法替换

OK了解了这些之后，就很容易实现了：
```Java
// 获取CLipboardManager对象，并将需要的内容添加进剪切板
ClipboardManager cManager = (ClipboardManager) mActivity.getSystemService(CLIPBOARD_SERVICE);
ClipData clipData = ClipData.newPlainText("user_id", userInfo.getUserId());
cManager.setPrimaryClip(clipData);
```
在新版的api中，因为setText()方法被替换，因此添加进剪切板的内容也进行了封装`ClipData`；通过查看源码可以发现，可以添加到剪切板多种格式：比如`Uri` `html` `intent`等多种格式