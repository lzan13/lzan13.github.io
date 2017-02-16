---
title: 使用CocosEditor时要注意的事项以及遇到的一些错误和解决办法
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - Cocos2d-Html5
  - CocosEditor
date: 2014-05-24 20:18:57
id: 1400933937000
comments: true
---

最近研究`CocosEditor`此插件是牛人`touchsnow`开发 [CocosEditor官方博客](http://blog.makeapp.co)编写的，不过这个插件暂时还有`bug`，所以在开发中难免会遇到令人蛋疼的错误，所以这里整理我遇到的错误以及解决办法！错误是经常遇到的，遇到新的就更新这篇文章！

如果有遇到错误的朋友也可以在这里回复，我会添加上去，这样对大家都有好处

1、刚才群里还有人遇到，这个也是我刚研究时遇到的最蛋疼的，就是提示：

    main.js:25:Error: wrong number of arguments；main.js 25行错误

但是`main.js`根本就没动他，25行也没有明显错误，这是因为在编写布局文件时，cocoseditor的bug 导致 在拖拽或者删除控件时会保存布局文件错误，可以在text模式下手动编写ccbx文件修改错误！

2、在使用`CocosEditor`的打包图片功能时，如果关闭了`plist`文件，是无法双击打开的，想再次编辑需要手动拖动到编辑窗口打开编辑，而且也无法预览`plist`文件，貌似只能通过点击左边的`Image`标签预览图片；

3、在使用`plist`打包图片时，如果有多个`plist`，但是`UI`并没有使用`plist`里的图片时，游戏不会加载`plist`缓存，此时要手动加载`plist`文件缓存 
```javascript
MainLayer.prototype.initPlistCache = function () {
    var cache = cc.SpriteFrameCache.getInstance();
    cache.addSpriteFrames("res/lm_packer.plist");
    cache.addSpriteFrames("res/op_packer.plist");
}
```

4、在调用方法与获取实例时经常会忘记在方法名后边加上括号`/`以及忘记`.create()`，导致提示错误 `XXXX is not a function`；

5、在使用`ccbxUI`设计界面设计的控件时要记得给控件加上`var`和`target`属性，不然是找不到控件的，有可能会提示`not a function`错误