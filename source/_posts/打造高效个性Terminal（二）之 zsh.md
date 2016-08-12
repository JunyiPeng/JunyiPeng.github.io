---
title: 打造高效个性Terminal（二）之 zsh
category: 搬砖码农
date: 2016-08-11 21:16:00
tags:
- Tool Kit
---

# oh-my-zsh
如果说**iTerm**塑造了一个婀娜多姿颜值高的貌美姑娘，那么**zsh**就是给她塑造了一个柔情侠骨百事通的女汉子之心。

## 简介 <small>Introduction</small>

对于**zsh**的作用，摘自池建强老师的一段：
> 目前常用的 Linux 系统和 OS X 系统的默认 Shell 都是 bash，但是真正强大的 Shell 是深藏不露的 zsh， 这货绝对是马车中的跑车，跑车中的飞行车，史「终极 Shell」，但是由于配置过于复杂，所以初期无人问津，很多人跑过来看看 zsh 的配置指南，什么都不说转身就走了。直到有一天，国外有个穷极无聊的程序员开发出了一个能够让你快速上手的zsh项目，叫做「oh my zsh」，Github 网址是：[https://github.com/robbyrussell/oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)。

**oh-my-zsh** 是一个社区驱动用于管理zsh的框架，囊括了140多个主题，200多个可选插件（如rails, git, OSX, hub, brew, ant, php, python etc.）。

## 安装 <small>Installation</small>
**via curl**
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

**via wget**
```
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

## 配置 <small>Configuration</small>
zsh的配置主要集中在用户当前目录的`.zshrc`里，用vim或者你喜欢的其他编辑器打开这个文件，在里面可以修改主题、插件以及定义自己的环境变量等操作。

## 主题 <small>Themes</small>

用vim打开`~/.zshrc`中，可以看到这样配置
```
...
# Set name of the theme to load.
# Look in ~/.oh-my-zsh/themes/
# Optionally, if you set this to "random", it'll load a random theme each
# time that oh-my-zsh is loaded.
ZSH_THEME="robbyrussell"
...
```
修改`ZSH_THEME`的值就可以更换主题。

默认的主题是`robbyrussell`，这个并不是最精彩，也并不是最简单的一个，只是对于其作者Robby来说最适合的一个。

可以通过访问`~/.oh-my-zsh/themes/`目录查看哪些可选的主题

```
$ tree themes
themes
├── 3den.zsh-theme
├── Soliah.zsh-theme
├── adben.zsh-theme
├── af-magic.zsh-theme
├── afowler.zsh-theme
├── agnoster.zsh-theme
...
0 directories, 141 files
```

更多关于各个主题的详细介绍见 [zsh themes](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)，另外还有不少并不是自带的主题，见[more external themes](https://github.com/robbyrussell/oh-my-zsh/wiki/External-themes)

### Powerline Fonts
如果你查看过zsh的主题列表的话，你会发现不少主题会有这样的样式，

![混合](/image/blog/make-beauty-and-productify-terminal-part2/9A5F3A14AAB88648A5BF5397428A6F6B.jpg)

然而，当你选择了这样的主题，会发现实际是这样的

![23.pic.jpg](/image/blog/make-beauty-and-productify-terminal-part2/4CF69060B9846B43DC49B616503E6FE9.jpg)

因为，这些主题都依赖于一种字体[powerline fonts](https://github.com/powerline/fonts)，`powerline fonts`是vim增强组件[vim powerline](https://github.com/Lokaltog/vim-powerline)附属的字体，如果要使用依赖这种字体的主题，要在系统中安装该字体。

### 安装 powerline fonts
```
// 下载powerline fonts项目
git clone https://github.com/powerline/fonts
// 执行安装
./fonts/install.sh
```

### 配置iTerm
在`Preperence → Profiles → Text → Front` 中，
和`Preperence → Profiles → Text → Non-ASCII Font`中
改变字体为`Sauce Code Powerline`

![改变字体](/image/blog/make-beauty-and-productify-terminal-part2/CBB6D3E305C5BF824C676EDEC38485D2.jpg)

## 插件 <small>Plugins</small>
用vim打开`~/.zshrc`中，可以看到这样配置
```
...
# Which plugins would you like to load? (plugins can be found in ~/.oh-my-zsh/plugins/*)
# Custom plugins may be added to ~/.oh-my-zsh/custom/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(git)
...
```
修改`plugins`的值就可以修改或添加插件。

下面介绍几款我常用的插件

### [autojump](https://github.com/wting/autojump)
> A cd command that learns - easily navigate directories from the command line

一款快捷跳转目标路径的插件，支持模糊匹配，自动补全，历史记录等功能。

![autojump](/image/blog/make-beauty-and-productify-terminal-part2/5EF9E1CDFEF91CFDD426CBD029F36730.gif)

### [dirhistory](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#dirhistory)
> This plugin allows you to navigate the history of previous current-working-directories using ALT-LEFT and ALT-RIGHT. ALT-LEFT moves back to directories that the user has changed to in the past, and ALT-RIGHT undoes ALT-LEFT.

一款支持目录的上翻，下翻功能的插件。

![dirhistory](/image/blog/make-beauty-and-productify-terminal-part2/B046659B5B18FD6D356B18F4F089ADC6.gif)

### [osx](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/osx)
> This plugin provides a few utilities to make it more enjoyable on OSX.

一款增加了一些在OSX上实用的命令插件。


| Command       | Description                                    |
|:--------------|:-----------------------------------------------|
| `tab`         | Open the current directory in a new tab        |
| `ofd`         | Open the current directory in a Finder window  |
| `pfd`         | Return the path of the frontmost Finder window |
| `pfs`         | Return the current Finder selection            |
| `cdf`         | `cd` to the current Finder directory           |
| `pushdf`      | `pushd` to the current Finder directory        |
| `quick-look`  | Quick-Look a specified file                    |
| `man-preview` | Open a specified man page in Preview app       |


### [git](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugin:git)
> The git plugin provides many aliases and a few useful functions.

一款提供git别名命令以及一些方法命令的插件


### [git-extras](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#git-extras)

一款扩展了不少方便快捷git命令的插件

### More Plugins
更多的插件介绍见 [zsh plugins](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)

## vi(vim) 基础
使用命令行的时候，难免会遇到要编辑文档的时刻，尽管不是vim党，但是如果掌握vim的基本操作，对效率还是有大大提升的。

### vi与vim的关系
简单来说，vi是老师的文字处理器，不过功能已经很齐全了，但是还是有可以进步的地方。vim则可以说是程序开发者的一项很好用的工具，就连vim的官方网站([http://www.vim.org/](http://www.vim.org/))自己也说vim是一个「程序开发工具」而不是文字处理软件。

### 三种模式
基本上vi中共分为3种模式，分别是「一般模式」，「编辑模式」与「命令行模式」

* 一般模式
以vi打开一个文件，默认就是一般模式，这个模式中，可以按下`i`来进入编辑模式，按下`ESC`则退出编辑模式回到一般模式。
在这个模式中可以删除字符、删除行、复制、黏贴。

* 编辑模式
编辑模式能真正的编辑文本。

* 命令模式
在一般模式中，输入`:`,`/`,`?`中的一个可以进入命令行模式，进行诸如数据查找操作，读取、保存、离开vi等等操作。

### 常用操作
掌握下面的十一个命令，就足够你在vim中生存了。

**移动光标**

* hjkl : 你也可以使用光标键 (←↓↑→). 注: j 就像下箭头。

**行操作**

* dd : 删除当前行，并把删除的行存到剪贴板里
* yy : 复制当前行。
* p  : 黏贴剪贴板

**保存退出**

* :w : 保存已编辑的文集
* :q : 退出编辑
* :wq : 保存并退出
* :help : 显示相关命令的帮助(退出帮助需要输入:q)

**搜索**
* /word : 向下寻找一个名为word的字符串 
* ?word : 向上寻找一个名为word的字符串
* n : 重复前一个查找的操作

# 文末福利，安抚你的强迫症
感谢你能耐心的看到这里，给你安利一个强迫症患者福音的窗口控制工具：[Spectacle](https://www.spectacleapp.com/)

Spectacle能通过快捷键，很轻易的排列好窗口的大小。

![Spectacle](/image/blog/make-beauty-and-productify-terminal-part2/878986C0344538018F330B79A4C7F5FF.gif)

# 参考
http://yannesposito.com/Scratch/en/blog/Learn-Vim-Progressively
https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins
http://www.vim.org/
http://macshuo.com/?p=676
http://swiftcafe.io/2015/07/25/iterm/