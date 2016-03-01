---
title: Kissy WaterFall 实现手动加载数据
category: 搬砖码农
date: 2013-07-21 12:41:06
tags:
- 前端
---

## 前言
由于Kissy WaterFall默认是监听滚动事件来实现数据动态加载的，但是有一些情况要用到手动加载数据。以下是使用Kissy WaterFall实现手动加载数据的方法。

最终实现效果：点击”逛更多的商店“会动态加载数据


## 步骤：

1. 当一页数据加载完成后停止监听滚动事件

```javascript
//加载一页数据完成后触发的事件
waterfall.on('addComplete', function () {
    S.log('after add complete!');
	waterfall.pause();
});
```

2. 为按钮绑定重启监听滚动事件

```javascript
//加载更多按钮
$("#button_container_more").on('click',function(){	
	waterfall.resunme();
});
```

3. 附录

> waterfall.pause()与water.resunme()的说明（从官网上转载的）
> pause()函数和resunme()函数属于插件里的waterfall.loader对象的
> resunme() ：继续开始监控scroll事件（随时可能会动态加载）
> pause() ：停止监控scroll事件（停止动态加载）

参考网址：http://docs.kissyui.com/docs/html/api/component/waterfall/loader.html#waterfall.Waterfall.prototype.pause




出现问题：按照以上来完成的话，当点击”加载更多“按钮时，只是启动了滚动监听。意思就是，要加载数据，一要点击按钮，二要再次滚动鼠标。这样的用户体验很差。

解决办法：修改按钮动作：new一个waterfall.loader，重新赋值waterfall对象，再重新绑定addComplete事件。
代码如下：

```javascript
	$("#button_container_more").on('click',function(){	
		waterfall = new Waterfall.Loader({
			container:"#ColumnContainer",
			load:function (success, end) {
				$('#loadingPins').show();
				$('.loader').hide();
				S.ajax({
					data:{
						'method':'flickr.photos.search',
						'api_key':'5d93c2e473e39e9307e86d4a01381266',
						'tags':'rose',
						'page':nextpage,
						'per_page':20,
						'format':'json'
					},
					url:'http://api.flickr.com/services/rest/',
					dataType:"jsonp",
					jsonp:"jsoncallback",
					success:function (d) {
						// 如果数据错误, 则立即结束
						if (d.stat !== 'ok') {
							alert('load data error!');
							end();
							return;
						}
						// 如果到最后一页了, 也结束加载
						nextpage = d.photos.page + 1;
						if (nextpage > d.photos.pages) {
							end();
							return;
						}
						// 拼装每页数据
						var items = [];
						S.each(d.photos.photo, function (item) {
							/*所用到的字段：
							**price
							**height
							**collection
							**title
							**src
							*/
							item.height = Math.round(Math.random() * (300 - 180) + 180); // fake height	
							item.collection = 10;	//测试用
							item.price = 1800;		//测试用
							items.push(S.substitute(tpl,item));
						});
						success(items);
					},
					complete:function () {
						$('#loadingPins').hide();
						$('.loader').show();
					}
				});
			},
			minColCount:2,
			colWidth:175
			//align:'left' // right, center (default)
		});
		waterfall.on('addComplete', function () {
        	S.log('after add complete!');
			waterfall.pause();
    	});		
	});
```

最终整个脚本文件：

```javascript
KISSY.use("waterfall,ajax,node,button", function (S, Waterfall, io,  Node, Button) {
    var $ = Node.all;
    var tpl = $('#tpl').html(),
	nextpage = 1,
	waterfall = new Waterfall.Loader({
		container:"#ColumnContainer",
		load:function (success, end) {
			$('#loadingPins').show();
			S.ajax({
				data:{
					'method':'flickr.photos.search',
					'api_key':'5d93c2e473e39e9307e86d4a01381266',
					'tags':'rose',
					'page':nextpage,
					'per_page':20,
					'format':'json'
				},
				url:'http://api.flickr.com/services/rest/',
				dataType:"jsonp",
				jsonp:"jsoncallback",
				success:function (d) {
					// 如果数据错误, 则立即结束
					if (d.stat !== 'ok') {
						alert('load data error!');
						end();
						return;
					}
					// 如果到最后一页了, 也结束加载
					nextpage = d.photos.page + 1;
					if (nextpage > d.photos.pages) {
						end();
						return;
					}
					// 拼装每页数据
					var items = [];
					S.each(d.photos.photo, function (item) {
						/*所用到的字段：
						**price
						**height
						**collection
						**title
						**src
						*/
						item.height = Math.round(Math.random() * (300 - 180) + 180); // fake height	
						item.collection = 10;	//测试用
						item.price = 1800;		//测试用
						items.push(S.substitute(tpl,item));
					});
					success(items);
				},
				complete:function () {
					$('#loadingPins').hide();
				}
			});
		},
		minColCount:2,
		colWidth:175
		//align:'left' // right, center (default)
	});
    waterfall.on('adjustComplete', function () {
        S.log('after adjust complete!');
    });
	//加载一页数据完成后触发的事件
    waterfall.on('addComplete', function () {
        S.log('after add complete!');
		waterfall.pause();
    });
    // scrollTo
    $('#BackToTop').on('click', function (e) {
        e.halt();
        e.preventDefault();
        $(window).stop();
        $(window).animate({
            scrollTop:0
        }, 1, "easeOut");
    });
	//加载更多按钮
	$("#button_container_more").on('click',function(){	
		waterfall = new Waterfall.Loader({
			container:"#ColumnContainer",
			load:function (success, end) {
				$('#loadingPins').show();
				$('.loader').hide();
				S.ajax({
					data:{
						'method':'flickr.photos.search',
						'api_key':'5d93c2e473e39e9307e86d4a01381266',
						'tags':'rose',
						'page':nextpage,
						'per_page':20,
						'format':'json'
					},
					url:'http://api.flickr.com/services/rest/',
					dataType:"jsonp",
					jsonp:"jsoncallback",
					success:function (d) {
						// 如果数据错误, 则立即结束
						if (d.stat !== 'ok') {
							alert('load data error!');
							end();
							return;
						}
						// 如果到最后一页了, 也结束加载
						nextpage = d.photos.page + 1;
						if (nextpage > d.photos.pages) {
							end();
							return;
						}
						// 拼装每页数据
						var items = [];
						S.each(d.photos.photo, function (item) {
							/*所用到的字段：
							**price
							**height
							**collection
							**title
							**src
							*/
							item.height = Math.round(Math.random() * (300 - 180) + 180); // fake height	
							item.collection = 10;	//测试用
							item.price = 1800;		//测试用
							items.push(S.substitute(tpl,item));
						});
						success(items);
					},
					complete:function () {
						$('#loadingPins').hide();
						$('.loader').show();
					}
				});
			},
			minColCount:2,
			colWidth:175
			//align:'left' // right, center (default)
		});
		waterfall.on('addComplete', function () {
        	S.log('after add complete!');
			waterfall.pause();
    	});		
	});
	
	//收藏按钮功能
	var collect;
	$('#ColumnContainer').delegate("mouseover", ".collect", function (event) {
		var w = $(event.currentTarget).children("span");
		var text = w.text();
		if(text >= 0){
			collect = text;
		}
		
		w.replaceWith("<span class='collects'>收藏</span>");
		//w.css("text-indent","3px");
	});
	
	$('#ColumnContainer').delegate("mouseout", ".collect", function (event) {
		var w = $(event.currentTarget).children("span");
		w.replaceWith("<span class='collectionAmount'>"+collect+"</span>");
		//w.css("text-indent","13px");
	});
});
```