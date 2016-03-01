---
title: iframe-在IE下透明背景
category: 搬砖码农
date: 2013-08-07 13:05:02
tags:
- 前端
---

iframe 在其他主流浏览器下，默认背景是透明的。但是在IE下要通过设置iframe属性来实现背景透明化。
代码如下：

```HTML
frameborder   //边框属性
allowtransparency  //透明背景属性
scrolling  //滚动条属性

<iframe frameborder="no" allowtransparency="true" scrolling="no" src="text-test1.html"></iframe>    

```
