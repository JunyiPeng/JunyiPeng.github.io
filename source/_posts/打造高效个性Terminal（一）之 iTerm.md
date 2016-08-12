---
title: 打造高效个性Terminal（一）之 iTerm
category: 搬砖码农
date: 2016-08-11 21:16:00
tags:
- Tool Kit
---

# 前言
作为一个集效率控、颜值控、强迫症患者标签于一身的患者，对待自己工作用到的Terminal，自然是诸多要求。本文首先会讲一下怎么弄一个高颜值、个性化的Terminal，然后再说说怎么利用一些工具来提高Terminal的效率。

~~PS：文末有彩蛋。~~

# iTerm
## 简介 <small>Introduction</small>
> iTerm2 is a replacement for Terminal and the successor to iTerm. It works on Macs with OS X 10.8 or newer. iTerm2 brings the terminal into the modern age with features you never knew you always wanted.

**iTerm**是一款免费的，专为Mac OS用户打造的命令行应用。作为系统自带**Terminal**的替代，它提供了很多方便且极客的功能。诸如隔离的面板、透明窗口、强大的正则表达式搜索、自动补全等、剪贴板历史记录，快照返回等功能，更多iTerm的特性见[iTerm Feature](http://www.iterm2.com/features.html)。
下载见官网([http://www.iterm2.com/](http://www.iterm2.com/))，如果你不是Mac用户，那就跳过**iTerm**，直接看提高效率的**oh-my-zsh**

## 塑造高颜值Terminal
**iTerm**吸引我的地方，除了各种方面的特性之外，就是炫酷可定制的界面。
满足我的视觉强迫症之外还可以提供方便的功能，应当是每个MacOS程序员必备的利器。

对于使用命令行，我一般会有两个这样的使用场景
一、快速打开，执行一两条命令，然后关闭，诸如打开文件，启动服务等等。
二、长时间使用命令行，诸如调试程序，VIM编程等等。

对于第一种情况，我希望是可以通过快捷键快速的打开命令行，然后同样的快捷键快速的隐藏它，直到我需要的时候再次来到我面前。
而第二种情况，我希望是可以和正常的命令窗口界面那样，让我安安静静的调试程序。

所以，在iTerm，我会建两个Profile配置。

**第一种Profile**
![my terminal part 1.gif](/image/blog/make-beauty-and-productify-terminal-part1/5961B48A3EE1C7C5AC59C7653ABBC9CF.gif)

**第二种Profile**
![my terminal part 2.gif](/image/blog/make-beauty-and-productify-terminal-part1/1EA60589F1F6EC96B2F1BD36EF6DE854.gif)

## 一步一步往前走

### Step.1 创建第一种Profile

首先在`Preperence → Profiles`，建立一个`Profiles`， 取名`HotKey Window`

![建立Profile](/image/blog/make-beauty-and-productify-terminal-part1/C19BBDC28A01A98CD1D202CBC94A6B29.jpg)

### Step.2 背景透明与模糊设置
在`Preperence → Profiles → Window → Window Appearance` 进行设置

![配置模糊透明背景](/image/blog/make-beauty-and-productify-terminal-part1/4E35E0BAF7B7CA5EB11BE4BFF22977FD.jpg)

### Step.3 窗口风格配置
在`Preperence → Profiles → Window → Settings for New Windows` 进行设置

![窗口风格配置](/image/blog/make-beauty-and-productify-terminal-part1/46AD20F0070843F9804B1717D5CD6661.jpg)

### Step.4 设置HotKey
在`Preperence → Profiles → Keys → HotKey` 进行设置

![设置HotKey](/image/blog/make-beauty-and-productify-terminal-part1/526B5DF9BC09C70065BB863ABCDB942D.jpg)

到目前为止，第一种Profile配置完成，你可以按下你设置的`HotKey`来方便快速打开和隐藏命令行。
如果你需要打开一个会长时间使用的命令行窗口的话，按下`cmd + n`，但是你会发现窗口的样式又回到默认的了，所以下面进行第二个Profiles的配置。

### Step.5 创建第二个Profile
我们只需要在第一个窗口的基础上，进行「窗口风格」配置就可以了。

在`Preperence → Profiles`，复制`HotKey Window`， 取名`Default of Hotkey Window`

![第二个Profile](/image/blog/make-beauty-and-productify-terminal-part1/5622AB0237BACE99D299D43D98D359DF.jpg)

### Step.6 窗口风格配置

在`Preperence → Profiles → Window → Settings for New Windows` 进行设置

![设置窗口风格配置](/image/blog/make-beauty-and-productify-terminal-part1/32CFF9E358032E50DB87B07D6B6B0F9E.jpg)

### Step.7 设置默认

在`Preperence → Profiles` 中，选中 `Default of HotKey Window`, 并设置为默认

![设置默认](/image/blog/make-beauty-and-productify-terminal-part1/14C8AD735CDD1B4A0460FEF930AAB9F0.jpg)

自此，就完成了两种Profile的设置。


## 常用功能快捷键
在使用**iTerm**的过程中，下面对于控制窗口的快捷键使用频率会很高，不用记，用多了就形成肌肉记忆了。

* cmd + n : 新建窗口
* cmd + t : 新建TAB
* cmd + d : 垂直新建TAB
* cmd + shift + d : 水平新建TAB

> [《打造高效个性Terminal（二）之 zsh》](/2016/08/11/打造高效个性Terminal（二）之%20zsh/)