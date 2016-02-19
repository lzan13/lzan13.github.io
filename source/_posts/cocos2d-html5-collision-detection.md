---
title: Cocos2d-Html5开发之碰撞检测的几种方法
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - Cocos2d-Html5
  - Collision
id: 719
date: 2014-06-03 11:30:49
---

游戏中的碰撞还是比较多的，比如角色与角色的碰撞，角色与墙壁的碰撞，角色与怪物的碰撞等，都需要
进行碰撞的检测，来触发一定的事件

最近在尝试制作一个小游戏的时候需要用到碰撞检测，然后就查了下资料，并在论坛进行提问等算是找到了比较满意的碰撞检测方法，这里记录下来

现在自己知道的方法算是有了三种了，下面一一记录并分析下他们各自的优缺点
### 第一种检测
1、就是官方提供的，根据`getBoundingBox();`方法获取要检测的碰撞物体的范围，然后再根据`rectIntersectsRect()`;方法进行判断需要检测的两个精灵是否有重叠，有则发生碰撞；
优点：适合对规则的矩形物体进行检测碰撞，简单，直接
缺点：对于复杂图形不友好，对于碰撞的检测不准确，使用中有种莫名其妙的感觉
```javascript
var dollRect = sprite.getBoundingBox();
var dollHeadRect = this.catchHand.getBoundingBox();
if(cc.rectIntersectsRect(dollRect, dollHeadRect)){
    //发生碰撞事件
}
```

### 第二种检测
2、第二种是在网上找到的，我感觉有些麻烦，不过相对于第一种，对于不规则物体支持的好了一些
这里根据BoundingBox 的 上下左右的中间点来判断碰撞，使检测的更准确一些
优点：使碰撞检测更准确一点
缺点：麻烦
```javascript
var box1 = sprite1.getBoundingBox();
var bottom = cc.p(box1.x +box1.width / 2,box1.y);
var right = cc.p(box1.x +box1.width,box1.y +box1.height / 2);
var left = cc.p(box1.x,box1.y +box1.height / 2);
var top = cc.p(box1.x + box1.width / 2,box1.y + box1.height);
var box2 = sprite2.getBoundingBox();
if(cc.rectContainsPoint(box2, left)||cc.rectContainsPoint(box2, right)||cc.rectContainsPoint(box2, top)||cc.rectContainsPoint(box2, bottom)){
    //发生碰撞
}
```

### 第三种检测
3、第三种就是我现在使用的，不过这个针对大小比较规矩，即接近正方形比较好，但是对于外形可以复杂
这个碰撞检测就是要给精灵添加一个radius属性，设置精灵以中心为原点，radius为半径的碰撞区域，然后去判断两个精灵的中心点的距离是否小于radius之和，如果是则发生碰撞；
优点：更准确，简单
缺点：对于长的图片碰撞不友好
想判断距离首先要知道一个方法：pDistance();这个方法是cocos2d-html5获取两个坐标点之间的方法，使用这个方法我们就可以获取两个精灵中心的距离
```javascript
var sprite = this.dolls3[i];
var distance = cc.pDistance(this.catchHand.getPosition(), sprite.getPosition());
var radiusSum = sprite.radius + this.catchHand.radius;
cc.log("distance:" + distance + "; radius:" + radiusSum);
if(distance < radiusSum){
    //发生碰撞
}
//针对第三三种方法又加深了一下，使得对矩形类的精灵也能有好的判断，
//主要就是分别对X和Y方向设置不同的Radius，然后去进行分别判断
var distanceX = Math.abs(this.catchHand.getPositionX() - sprite.getPositionX());
var distanceY = Math.abs(this.catchHand.getPositionY() - sprite.getPositionY());
var radiusYSum = sprite.radiusY + this.catchHand.radius;
if(distanceX < sprite.radiusX && distanceY < radiusYSum){
    this.catchDollSucceed(sprite);
    return;
}
```
### 总结
综上所述，碰撞检测的方法不止一种，应该还有其他的方法是我不知道的，在适合的时候选择合适的方法才是最好的，
更多`Cocos2d-Html5`开发文章可以关注牛人 [touchsnow的博客](http://blog.makeapp.co)