---
title: JavaScript 开发者所需要知道的 V8（一）：V8 In NodeJS
date: 2016-11-08 10:36:00
tags:
- Javascript
- v8
- NodeJS
category: 搬砖码农
---


# Motivation
JavaScript 是一款拥有「自动垃圾回收」功能的编程语言。
市面上具有这样功能的语言，一般都是拥有相对应的虚拟机的，像 Java的JVM ，C#的CLR ，PHP的Zend。
虚拟机一般实现了代码解析，内存的管理、布局、垃圾回收等功能。
不像C/C++这种没有虚拟机的语言，它们需要手动管理内存。
C/C++语言编译后的文件，是可以直接运行的。

我认为学习一门开发语言，除了知道一些语法上的使用，各种API的调用以外。学习相应的虚拟机也是很有必要的。而 JavaScript 由于其特殊的历史原因，并不是只有 V8 一个引擎。但是目前 V8 它是业界最优秀的 JavaScript 引擎，也就成为了一个学习样本。

如今的 JavaScript 不仅仅是用在浏览器端了，也因为 NodeJS 的关系得以在服务器端运行。和浏览器端不同的地方在于服务器端对资源的敏感性是很高的。当业务规模大了，并发量上来了，一些很细小的问题会放大。这时候一些小小的内存泄漏，都会酿造灾难。

所以作为一个 JavaScript 开发者，搞清楚从敲入 `console.log('hello   world')`，直到后面交由CPU执行的中间过程是很重要的。
这也对如何用 JavaScript 这门松散的语言编写出高质量的代码是具有指导作用的。

想真正做到 JavaScript 全栈，路漫漫其修远兮。

# V8 概述
V8 作为一个 JavaScript 引擎，最初是服役于 Google Chrome 浏览器的。它随着 Chrome 的第一版发布而发布以及开源。现在它除了 Chrome 浏览器，已经有很多其他的使用者了。诸如 NodeJS、MongoDB、CouchDB 等。

JavaScript 作为 Prototype-Based Language , 基于它使用 Prototype 继承的特征，V8 使用了直译的方式，即把 JavaScript 代码直接编译成机器码( Machine Code, 有些地方也叫 Native Code )，然后直接交由硬件执行。
与传统的「编译-解析-执行」的流程不同，V8 处理 JavaScript，并没有二进制码或其他的中间码。

** 简单来说，V8主要工作就是：「把 JavaScript 直译成机器码，然后运行」**
但这中间，往往是一个复杂的过程，它需要处理很多的难题，诸如：
1. 编译优化
2. 内存管理
3. 垃圾回收

我写的这一系列文章，也是从这三个大点来出发，解读V8针对这些内容的处理。

# V8 In NodeJS

## NodeJS源码小览

NodeJS，是怎么引入V8的？
我们关注[Node的源码](https://github.com/nodejs/node)目录：

```
.
├── ...
├── deps
│   ├── ...
│   ├── v8
│   ├── ...
├── ...
├── lib
│   ├── ...
│   ├── buffer.js
│   ├── child_process.js
│   ├── console.js
│   ├── ...
├── node -> out/Release/node
├── ...
├── out
│   ├── ...
│   ├── Release
|         ├── node
|         ├── node.d
|         ├── obj
|             └── gen
|                 ├── ...
|                 ├── node_natives.h
|                 ├── ...
│   ├── ...
├── src
│   ├── ...
│   ├── debug-agent.cc
│   ├── debug-agent.h
│   ├── env-inl.h
│   ├── env.cc
│   ├── ...
├── 
...
```

需要关注的几个目录和文件：

**`/deps/v8`**：这里是V8源码所在文件夹，你会发现里面的目录结构跟[V8源码](https://github.com/v8/v8)十分相似。

**`/src`**：由C/C++编写的核心模块所在文件夹，由C/C++编写的这部分模块被称为「Builtin Module」

**`/lib`**：由JavaScript编写的核心模块所在文件夹，这部分被称为「Native Code」，在编译Node源码的时候，会采用V8附带的`js2c.py`工具，把所有内置的JavaScript代码转换成C++里面的数组，生成`out/Release/obj/gen/node_natives.h`文件。有些 Native Module 需要借助于 Builtin Module 实现背后的功能。

**`/out`**：该目录是Node源码编译(命令行运行`make`)后生成的目录，里面包含了Node的可执行文件。当在命令行中键入`node xxx.js`，实际就是运行了`out/Release/node`文件。

来张图说明一下V8在Node运行时的整体过程。

![v8 in nodejs.png](/image/blog/the-v8-what-javascripter-should-konw/85B39636DBC008CDB299B1BB6E45883B.png)

Node在启动的时候，就已经把 Native Module，Builtin Module 加载到内存里面了。后来的 JavaScript 代码，就需要通过 V8 进行动态编译解析运行。

## 查看V8版本号

NodeJS的进步与V8息息相关，关注每个NodeJS版本所对应的V8版本，可以加强该版本新功能的理解和由来。

在NodeJS中，通过`process.versions`可以查看NodeJS依赖模块的版本号，V8就包含其中。

例如，我运行的 `v7.0.0`的NodeJS：
```
$ node
> process.versions
{ http_parser: '2.7.0',
  node: '8.0.0-pre',
  v8: '5.4.500.36',
  uv: '1.10.0',
  zlib: '1.2.8',
  ares: '1.10.1-DEV',
  modules: '51',
  openssl: '1.0.2j',
  icu: '58.1',
  unicode: '9.0',
  cldr: '30.0.2',
  tz: '2016g' }
```

# NodeJS与V8的缠绵

- 2008年9月，V8 的第一个版本随着 Chrome 的第一版本发布。
- 2009年5月，NodeJS 的第一个版本由 Ryan Dahl 在 GitHub 上发布。
- 2010年12月，[官方公布](https://blog.chromium.org/2010/12/new-crankshaft-for-v8.html) V8 的名为 Crankshaft 的优化编译器，与原来的 Full Compiler 一起工作，声称较2008年版本提高50%性能。
- 2014年12月， io.js 从久久不更新的 NodeJS 分出来支，并且引入最新的 V8 ，这时候 NodeJS 处于`0.12.17`版本。
- 2015年2月，NodeJS基金宣布NodeJS(`v0.12`)和io.js(`v3.3`)合并，合并版本在未来发布。
- 2015年7月7日，[官方公布](https://blog.chromium.org/2015/07/revving-up-javascript-performance-with.html)又一个新的名为TurBoFan的优化编译器，主要提供ES6的新语法，以及提高性能。并表明该编译器最终目标是全部替代Crankshaft编译器
- 2015年7月17日，[官方发布](http://v8project.blogspot.com/2015/07/v8-45-release.html)集成TurboFan的V8版本(`v4.5`)
- 2015年9月08日，NodeJS紧跟着[发布](https://nodejs.org/en/blog/release/v4.0.0/)了与io.js的合并版本(`V4.0`)，引入最新的V8，给开发者们带来了最新的ES6语法，以及性能上的提高。
- 2015年8月28日，V8[发布](http://v8project.blogspot.com/2015/08/v8-release-46.html)`v4.6`版本
- 2015年10月29日，NodeJS[发布](https://nodejs.org/en/blog/release/v5.0.0/)`V5.0.0`版本
- 2016年3月15日，V8[发布](http://v8project.blogspot.com/2016/03/v8-release-50.html)`v5.0`版本
- 2016年4月26日，NodeJS[发布](https://nodejs.org/en/blog/release/v6.0.0/)`V6.0.0`版本
- 2016年7月18日，V8[发布](http://v8project.blogspot.com/2016/07/v8-release-53.html)`v5.3`版本，新增名为Ignition的解析器(Interpreter)，跟原有的优化编译器(Crankshaft and TurboFan)进行串联工作，提供了更加优化的内存使用方案，主要针对于低内存的Android设备，并称在未来会普及到全平台。
- 2016年10月25日，NodeJS[发布](https://nodejs.org/en/blog/release/v7.0.0/)`v7.0.0`版本
- 截止到今天(2016年11月)，NodeJS版本`v7.0.0`，搭配了`v5.4`的v8，而官方发布的最新v8版本是`v5.5`。

回顾整个历程，由于NodeJS是搭建在V8之上的，所以NodeJS很多「新增语言特性」和「提高性能」等更新都需要依赖V8的发布日程。

就像NodeJS和io.js宣布合并，和真正发布`V4.0`版本的中间，还隔了V8的生命历程一个重大的更新。这个更新直接提供了相当一部分的ES6语法，以及性能上的提高。

# 总结
本篇主要描述了下面几点：
1. 我的写作动机，理解NodeJS底层，给写出高质量JavaScript代码提供指导。
2. 简单描述V8的角色，以及主要职责：编译优化、内存管理、垃圾回收。
3. 通过NodeJS源码目录，以及NodeJS代码加载过程，来认识V8在这之中的位置。
4. 罗列NodeJS与V8的历代迭代版本以及联系，强调V8在NodeJS中的地位。