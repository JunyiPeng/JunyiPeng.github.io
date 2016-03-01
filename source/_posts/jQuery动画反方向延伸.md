---
title: jQuery动画反方向延伸
category: 搬砖码农
date: 2013-07-21 23:07:04
tags:
- 前端
---

Jquery的动画效果都是从左到右，从上到下延伸的。如果要实现反方向延伸呢？
以下代码实现动画从右到左延伸，读者可以举一反三实现从下往上延伸的动画。

**CSS**：
```
#bar {
    margin-left: 100px;
    height: 10px;
    width: 0;
    background: red;
}
```

**jQuery**
```
$('#bar').animate({
    marginLeft: 0,
    width: 100
});
```