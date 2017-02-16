---
title: 为Editplus添加格式化js、css、html代码工具
categories:
  - APPShare
tags:
  - Editplus3
  - Share
  - Tool
date: 2013-05-01 14:33:41
id: 1367390021000
comments: true
---

身为一个程序员，经常编码是必须的了，刚开始学习编程的时候，老师推荐我们使用记事本，不让我们用开发工具，算是为了锻炼我们的记忆代码能力吧！这不是重点！！！后来，老师给我们推荐了一个编辑器，就是`EditPlus3`使用过这个编辑器之后，非常喜欢，以至于我经常向别人推荐她，特别是在查看别人的代码，以及一些简单的代码时，用这个是非常方便的，但是最近我发现`她`不能`格式化代码`！所以百度了下，发现可以为`EditPlus`添加工具！我这里搜集了`Editplus`添加格式化`js`、`css`、`html`的工具；

简单的说下为EditPlus添加工具组建方法！

先将我提供的软件下载下来，里边包含Editplus 3 主程序 注册码 edtool格式化工具代码文件！

安装及注册EditPlus就不说了，就说下添加工具！

将edtool解压到一个文件夹内，我是放到了Editplus 3的安装目录！路径随意！

打开Editplus 3 点击菜单栏 `工具`->`配置用户工具`，会弹出对话框：

[![editplus](http://lzan13.qiniudn.com/blog/uploads/images/2013/05/editplus3.png)](http://lzan13.qiniudn.com/blog/uploads/images/2013/05/editplus3.png)

首先是选择一个群组，然后点击右边的`群组名称`更改工具组名，自己喜欢就行；

然后点击`添加工具`->`程序`之后会新建一个菜单工具项！

然后在`菜单文本`>即4那里输入`菜单工具名`

在命令行输入：（这里只说添加html代码的格式化工具，其它的方法同样！）
```javascript
cscript /nologo "C:Program FilesEditPlus 3edToolshtmlFormatter.js"
```

往下就选择`作为文本过滤器运行`点击确定就OK了，一个格式化html代码的工具添加成功！

我这里整理的Editplus 3 以及格式化工具的下载地址：[Editplus 3 含注册 & edtool](http://pan.baidu.com/share/link?shareid=408890&uk=2987718070)