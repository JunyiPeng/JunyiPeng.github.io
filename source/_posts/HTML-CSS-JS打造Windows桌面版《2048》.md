---
title: HTML-CSS-JS打造Windows桌面版《2048》
category: 搬砖码农
date: 2014-07-19 16:10:49
tags:
- HTML
- CSS
- Javascript
- node webkit
---

![头图](/image/blog/imgFile1412168354773.jpg)

# 前言    

受益于Node的问世，Javascript终于能够挣脱浏览器这个条条框框的盒子。于是，Javascript能够开发服务器应用了。既然Javascript能够运行在操作系统中，那是否也能使用Javascript像C/C++开发那些个桌面应用呢。
在Google一番之后，果然已经有高人实现了这个想法了。

Node-WebKit，把浏览器的布局引擎WebKit和Node.js结合了在一起。WebKit负责处理HTML5 UI相关的部分，而Node.js负责本地的API接口，比如文件系统，网络，设备等等。
一个需要特别提醒的，Node-WebKit的开发维护者正是国内的王文睿大大，中国人啊，很自豪有木有！！但是项目文档好想没有中文版的，只是英文版的，这也许是要和国际接轨的原因吧。

项目地址：[https://github.com/rogerwang/node-webkit](https://github.com/rogerwang/node-webkit)

话不多说，我们撸起袖子，开始尝试一下使用前端技术开发桌面应用的乐趣吧。



# Windows桌面版《2048》

## Step 1 ：准备材料

1、node-webkit(本文使用v0.8.6-win版本):
[node-webkit-v0.8.6-win-ia32.zip](http://dl.node-webkit.org/v0.8.6/node-webkit-v0.8.6-win-ia32.zip)

2、基于jQuery实现的2048游戏：
http://huang-jerryc/（原文地址）
https://github.com/JerryC8080/My2048（下载地址）

node-webkit的其他版本，在项目地址（https://github.com/rogerwang/node-webkit）可以下载得到，不同操作系统是不同版本的。
基于jQuery实现的2048游戏，是我之前做的一个小玩意，而这一次就完美移植到Windows桌面，哇嘎嘎嘎。

node-webkit下载并解压之后，如下：
![解压后](/image/blog/imgFile1412168419938.jpg)

```
文件说明：
nw.pak           --核心Javascript库
incudt.dll       --网络库
ffmpegsumo.dll   --媒体库，为<video>、<audio>或者其他媒体功能提供支持
libEGL.dll、
libGLESv2.dll、
D3DCompiler_43.dll、
d3dx9_43.dll     --该四个dll，为WebGL、GPU、DirectX提供支持
```

基于jQuery实现的2048游戏下载并解压之后，如下：
![解压后](/image/blog/imgFile1412168441331.jpg)

## Step 2  ： 编写package.json文件

在《2048》游戏的目录，新建一个package.json文件，输入内容：
HTML 代码片段


```json
{
  "name": "MyDemo",
  "main": "index.html",
  "window": {
    "toolbar": false,
    "resizable": false,
    "width": 660,
    "height": 800
  }
}
```
![](/image/blog/imgFile1412168471850.jpg)


复制代码保存代码提示：
1. 编写代码可使用Emmet语法，即Zen coding语法 
2. 当代码框处于激活状态下按 CTRL+F11 键可全屏!


## Step 3  ：压缩源程序

我们使用压缩工具，把已经编写好的../My2048-master目录里面的内容压缩。
压缩需要注意的两点：
1. 压缩格式要为zip；
2. 文件后缀名要为.nw；

如图：
![enter image description here](/image/blog/imgFile1412168511771.jpg)

压缩完成
![enter image description here](/image/blog/imgFile1412168553899.jpg)

## Step 4  ：预览

把压缩好的 My2048-master.nw 文件复制到node-webkit目录：
![enter image description here](/image/blog/imgFile1412168592404.jpg)

运行cmd，
移动到node-webkit所在的目录： cd + node-webkit所在的路径
运行命令：nw My2048-master.nw
![enter image description here](/image/blog/imgFile1412168834596.jpg)

便能看到预览效果了
![enter image description here](/image/blog/imgFile1412168728769.jpg)

## Step 5   ：生成exe执行文件

依然在node-webkit的目录下
运行命令：
`copy /b nw.exe+My2048-master.nw My2048.exe`
![enter image description here](/image/blog/imgFile1412168755327.jpg)

OK，我们可以看到node-webkit目录下多了一个My2048.exe的执行文件，我们点解它运行看看效果：
![enter image description here](/image/blog/imgFile1412168871884.jpg)

![enter image description here](/image/blog/imgFile1412168728769.jpg)


## Step 6  ： 打包程序

如果想把这个程序发给朋友，除了.exe文件之外，还需要包括另外两个必要的文件:
- **icudt.dll**(必要网络库)
- **nw.pak**(核心javascript库)

这两个是最基本的文件，如果程序还包含了其他功能，还需要包含相对应的dll文件，各个文件功能说明文章开始已经说明了。

所以，我们得到了最基本的发布版了：
![enter image description here](/image/blog/imgFile1412168908105.jpg)
