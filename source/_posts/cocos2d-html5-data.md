---
title: Cocos2d-Html5开发之本地数据存储
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - Cocos2d-Html5
  - Data
  - LocalStroage
  - SessionStorage
id: 715
date: 2014-06-12 09:55:11
---

做游戏时经常需要的一个功能呢就是数据的保存了，比如游戏最高分、得到的金币数、物品的数量等等，`Cocos2d-Html5`使用了`Html5`，所以`Html5`的数据保存方法是对引擎可用的；

`Html5`本地数据存储是使用`js`对数据进行操作，`Html5`对数据的存储提供了两个方法：

* sessionStorage - 只对本次会话保留数据
* localStorage - 长时间保留数据

### sessionStorage
关于这个sessionStorage只在浏览器打开进行会话时可用，在游戏中没有测试，用法是和localStorage方法相同的，只是对数据保存的时间上不同；

### localStorage
其中`localStorage`方法 对保留的数据没有时间限制，除非用户手动清理数据，也是我在游戏中常用的方法；
对数据的存储最常用的就是`getItem('','');`和`setItem('','');`这两个方法；
由此可见本地存储数据的方法很简单，就是简单的设置键`key`值`value`对，以及根据键`key`获取保存的值`value`;
还有一点需要注意的就是`Html5`本地数据存储，只能保存字符串数据，无论你保存什么都会自动转换为字符串，所以如果要保存其他类型的数据的时候，要记得进行数据转换，
这里我写一个保存和读取`json`数据的例子：
```javascript
//这是一个保存娃娃数量的json数据
dollNum = {Aries: 0, Taurus: 0, Gemini: 0, Cancer: 0, Leo: 0, Virgo: 0, Libra: 0, Scorpius: 0, Sagittarius: 0, Capricornus: 0, Aquarius: 0, Pisces: 0};
/**
 * 保存Doll数量，要保存json数据的时候，需要使用JSON.stringify();方法将JSON转化为字符串
 */
function saveDollNum(){
    var tempDollNum = JSON.stringify(dollNum);
    sys.localStorage.setItem("dollNum", tempDollNum);
}

/**
 * 加载Doll数量 和 keys；然后再读取过后，需要用JSON.parse();方法将字符串转化为JSON
 */
function loadDollNum() {
    var tempDollNum = sys.localStorage.getItem("dollNum");
    if(tempDollNum == null || tempDollNum == ""){
        saveDollNum();
        cc.log("default dollNum " + dollNum);
    }else{
        tempDollNum = sys.localStorage.getItem("dollNum");
        cc.log("get dollNum " + tempDollNum);
    }
    //将字符串转化为json
    tempDollNum = JSON.parse(tempDollNum);
}
```

这样就可以一次保存多个数据，并且操作起来也方便