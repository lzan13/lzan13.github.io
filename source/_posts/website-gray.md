---
title: 为雅安祈福-网站所有色调变灰色
categories:
  - Develop
  - Web
tags:
  - CSS
  - Html
  - Web
id: 284
date: 2013-04-22 00:46:08
---

雅安地震了，我们不能做些什么震惊的大事，我们从精神上支持，作为一名程序猿，我们用自己的方式为雅安祈福：网站变灰色，让站长一起行动起来！

网站变灰色代码，参考[楚狂人的博客](http://www.chukuangren.com/heibaidaima.html)
```css
/*
 * 网站变灰代码
 */
html{ 
filter: grayscale(100%);
-webkit-filter: grayscale(100%);
-moz-filter: grayscale(100%);
-ms-filter: grayscale(100%);
-o-filter: grayscale(100%);
filter: url("data:image/svg+xml;utf8,&lt;svg xmlns='http://www.w3.org/2000/svg'&gt;&lt;filter id='grayscale'&gt;&lt;fecolormatrix type='matrix' values='0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0 0 0 1 0'&gt;&lt;/fecolormatrix&gt;&lt;/filter>&lt;/svg&gt;#grayscale");  
filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);
-webkit-filter: grayscale(1);
} 
```
将此`html`代码加入到`style.css`文件中去就行！

### 站长行动起来
[![yaan](http://wp-melove.qiniudn.com/blogimg/2013/04/yaan.jpg)](http://wp-melove.qiniudn.com/blogimg/2013/04/yaan.jpg)

我是刘立正，我为雅安祈福！