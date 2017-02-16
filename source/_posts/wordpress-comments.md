---
title: 为wordpress添加评论验证-识别机器人拦截垃圾评论
categories:
  - Develop
  - Web
tags:
  - CSS
  - Html
  - JQuery
  - Web
  - Wordpress
date: 2013-04-11 00:26:58
id: 1365611218000
comments: true
---

在互联网上混，避免不了接触垃圾，开博客，避免不了垃圾评论，之前刚开博客时还没想过垃圾评论这回事，但是当我的博客运行了两个星期左右时，擦，一天收到了几十条垃圾评论，之后装上了识别垃圾评论插件，虽然能把大部分垃圾评论识别出来并放到垃圾箱，但是说明垃圾评论还是收到了，说明这些垃圾评论还是写入到我的数据库了，浪费我的资源，今天登陆我的博客的时候又看到了两条垃圾评论，插件没有识别出来，而且是那种很烦人的，老长老长的广告评论，`fuck`最烦的就是这种评论了，还要手动去删除它！屏蔽垃圾评论的插件，毕竟也是不会思考的，也并不一定`100%`的能判断出来，所以就想给博客加上评论验证功能，但是想想输入验证码的话朋友们都很烦的吧，所以我参考网上的一些方法，使用`jquery`写了一个拖动验证的方法，不过感觉写的还不是很好，慢慢的会优化的！

下载我主题的可以直接在相应的地方添加代码，或者重新下载主题：下载主题：[主题更新下载](http://www.melove.net/?p=224)

不是使用我的主题的朋友可以参考下代码，很容易添加的！

### 效果图
[![comments](http://lzan13.qiniudn.com/blog/uploads/images/2013/04/comments.png)](http://lzan13.qiniudn.com/blog/uploads/images/2013/04/comments.png)

### 上代码
首先是在`textarea`上边添加两个div用来实现拖动的小马和定义宽度！
```html
<div class="draglock">
	<div class="dragbar">
		<div class="draghandle"> </div>
	</div><span class="dragmsg">帮我把马牵过来吧！(有点儿不好牵……)</span>
</div>
<p><textarea name="comment" id="comment" cols="70" rows="10" tabindex="4" onkeydown="if(event.ctrlKey&&event.keyCode==13){document.getElementById('submit').click();return false};"></textarea></p>
```

然后要定义一下css样式
```css
.draglock{
	position:relative;
	margin-left:10px;
	height:35px;
}
.draglock .dragbar{
	width:200px;
	height:35px;
	/*background:#ddd;*/
}
.draglock .dragmsg{
	position:absolute;
	top:0px;
	left:200px;
	margin:0px;
	height:35px;
	line-height:35px;
}
.draglock .draghandle{
	height:35px;
	width:45px;
	background:url(./images/xiaoma.gif) no-repeat;
	/*background:#789;*/
	border:none;
	overflow:hidden;
	position:absolute;
	top:0px;
	left:0px;
	z-index:10;
	cursor:pointer;
	-o-transition:none;		/* opera浏览器特有标记 */
	-webkit-transition:none;	/* safari和Chrome浏览器特有标记 */
	/*-khtml-transition:all 0.3s linear;	/* KHTML内核浏览器特有标记（KHTML是WebKit的前身） */
	-moz-transition:none;	/* Firefox浏览器特有标记 */
	-ms-transition:none;	/* iCab浏览器特有标记 */
	-icab-transition:none;	/* IE浏览器特有标记 */
}
```

是在js控制代码，这些添加在网站加载的你自己的js文件内！
```javascript
var submit_state = false;
$(function(){
	var handle=$(".draghandle");
	var dragbar=$(".dragbar");
	handle.css({"width":"45px","top":"0px","left":"0px"});
	var maxlen=parseInt(dragbar.width())-parseInt(handle.outerWidth());
	$(".draghandle").mousedown(function(e){
		var mouseX = e.pageX;
		var handleX = parseInt(handle.offset().left) - parseInt(dragbar.offset().left);
		$(document).bind("mousemove",function(e){
			var left = e.pageX - mouseX + handleX <0 ? 0 : (e.pageX - mouseX + handleX >= maxlen ? maxlen : e.pageX - mouseX + handleX);
			handle.css({left:left,top:"0px"});
			if(left == maxlen){
				$(".dragmsg").text("谢了O(∩_∩)O~~");
				submit_state = true;
				sub();
			}else{
				$(".dragmsg").text("帮我把马牵过来吧！(有点儿不好牵……)");
				submit_state = false;
				sub();
			}
		    percent=(left/maxlen*100).toFixed(0);
		    return false;
		});
		$(document).bind("mouseup",function(){
			$(this).unbind("mousemove");
		});
	});
	function sub(){
		if(submit_state){
			//设置为可用 
			document.getElementById("submit").type="submit";
			document.getElementById("submit").value="提交（Ctrl+Enter快捷）";
		}else{
			document.getElementById("submit").type="button";
			document.getElementById("submit").value="请帮我牵马！多谢！";
		}
	}
});
```