---
title: Kissy WaterFall 静态调用
category: 搬砖码农
date: 2013-03-07 12:04:24
tags:
- 前端
---

需要用到的JS

```HTML
<script src="kissy.js"></script>
<script src="base.js"></script>
<script src="loader.js"></script>
<script src="waterfall.js"></script>
```

需要设置的CSS样式

```HTML
<style>
    .ks-waterfall {
        position: absolute;
        width: 192px;
        overflow: hidden;
        padding: 15px;
        border: 2px solid #ddd;
        margin-bottom: 20px;
        text-align: center;
        left:-9999px;
        top:-9999px;
    }
</style>
```

HTML关键代码

```HTML
<div id='container' style="position: relative;">
    <!-- 重复的数据块 -->
    <div class="ks-waterfall"><img src="http://farm7.static.flickr.com/6072/6128820646_75d4f4f26d_m.jpg" width="192" height="113">
        <div class="title">untitled</div>
    </div>
</div>
```

最后调用WaterFall的内嵌脚本

```javascript
KISSY.use("waterfall", function (S, Waterfall) {
    new Waterfall({
        container: "#container",    //节点容器
        minColCount: 2,             //最小列数
        colWidth: 235               //每列的宽度
    });
});
```
