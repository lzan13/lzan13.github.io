---
title: SublimeText的使用
comments: true
categories:
  - APPShare
tags:
  - Plugin
  - SublimeText
  - Share
  - Tool
id: 10072
date: 2016-05-11 13:37:42
---

---------------
### 前言
之前一直在使用`EditPlus3`，后来得知了`SublimeText`，好牛X的样子，所以就开始使用了


-----------------------
### 首先SublimeText的安装
先去官方下载安装包，这里下载的是[SublimeText2](http://www.sublimetext.com/2) 根据安装向导安装就好了


------------------------
### Package Control
如果需要给`SublimeText`安装插件，首先需要安装`Package Control`
- 按`Ctrl+``或者`View>Show Console`打开`Console`（SublimeText控制台）
- 粘贴下边的代码到`Console`并回车，[Package Control](https://packagecontrol.io/installation)
```js
// 2.x版本
import urllib2,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')

// 3.x版本
import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manpciual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
- 等待左下角的进度完成，然后重启`SublimeText2`就成功了


------------------------
### MarkdownEditing
#### 安装MarkdownEditing
- 按下`Ctrl+Shift+P`然后输入`Package Control: Install Package`或`pci`然后在弹出框输入`MarkdownEditing`回车，等待安装完成，然后重启`SublimeText`就可以看到`Markdown`的效果了
- 此插件可以设置一些主题，不过他的主题对`Markdown`的语言没作用，只是更改了编辑文本的主题样式
- 然后就是有一些快捷键比如：
> 插入链接：`win+ctrl+v`
> 插入图片：`win+ctrl+k`
> 其他详细可以通过打开`Ctrl+Shift+P`然后输入`MarkdownEditing`查看


------------------------
### HTML/CSS/JS Prettify
#### 安装HTML-CSS-JS Prettify
按下`Ctrl+Shift+P`然后输入`Package Control: Install Package`或`pci`然后在弹出框输入`HTML-CSS-JS Prettify`然后回车，等待安装完成重启就行了

#### 配置HTML-CSS-JS Prettify
配置的时候只需要右键`HTML/CSS/JS Prettify>Set Plugin Options`打开插件的配置文件，修改下`nodejs`的路径就行了，其中有三个平台的路径，只需要修改自己相应的平台就行了
```json
{
  // Simply using `node` without specifying a path sometimes doesn't work :(
  // https://github.com/victorporof/Sublime-HTMLPrettify#oh-noez-command-not-found
  // http://nodejs.org/#download
  "node_path": {
    "windows": "D:/develop/web/nodejs/node.exe",
    "linux": "/usr/bin/nodejs",
    "osx": "/usr/local/bin/node"
  },

  // Automatically format when a file is saved.
  "format_on_save": false,

  // Only format the selection if there's one available.
  "format_selection_only": true,

  // Log the settings passed to the prettifier from `.jsbeautifyrc`.
  "print_diagnostics": true
}
```


------------------------
### Pretty JSON
#### 安装Pretty JSON
- 按下`Ctrl+Shift+P`然后输入`Package Control: Install Package`或`pci`然后在弹出框输入`Pretty JSON`然后回车等待安装完成
- 选择`Preferences > Package Settings > Pretty JSON > Settings - Default​`进行相关设置，其实这个没有什么要设置的
- 快捷键就是`Ctrl+Alt+J`


---------------
### 一些常用设置

- Tab为4空格：菜单栏`View>Indentation>Tab Width: 4`


