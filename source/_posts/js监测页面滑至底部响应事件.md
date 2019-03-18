---
title: js监测页面滑至底部响应事件
type: categories
copyright: true
date: 2019-03-18 14:16:30
tags: [js]
categories: 前端笔记
description:
---
#### 需求
&emsp;&emsp;<font color=#0099ff>页面滑动至底部时候，触发响应</font>
#### 应用场景
1. 服务协议滑动完毕可点击同意按钮;
2. 页面滑至底部加载更多(新闻、列表等);
3. 自动加载下一页等...
<!--more-->

#### 分析
&emsp;&emsp;首先，既然是监听，则首要条件便是监听页面滑动(滚动)事件；其次，由于一般页面不会一屏展示完，因此需要依赖BOM来辅助完成一些计算。最后，达到某种条件，触发方法。
#### 代码
```
 window.onscroll = function(){
	//变量scrollTop是滚动条滚动时，距离顶部的距离
	let scrollTop = document.documentElement.scrollTop||document.body.scrollTop;
	//变量windowHeight是可视区的高度
	let windowHeight = document.documentElement.clientHeight || document.body.clientHeight;
	//变量scrollHeight是滚动条的总高度
	let scrollHeight = document.documentElement.scrollHeight||document.body.scrollHeight;
	//滚动条到底部的条件
	if(scrollTop+windowHeight >= scrollHeight){
		//此处触发响应事件
		...
	}
}
```
#### 附录
```
网页可见区域宽：document.body.clientWidth
网页可见区域高：document.body.clientHeight
网页可见区域宽：document.body.offsetWidth (包括边线的宽)
网页可见区域高：document.body.offsetHeight (包括边线的高)
网页正文全文宽：document.body.scrollWidth
网页正文全文高：document.body.scrollHeight
网页被卷去的高：document.body.scrollTop
网页被卷去的左：document.body.scrollLeft
//对应的dom元素的宽高有以下几个常用的：
元素的实际高度：document.getElementById("div").offsetHeight
元素的实际宽度：document.getElementById("div").offsetWidth
元素的实际距离左边界的距离：document.getElementById("div").offsetLeft
元素的实际距离上边界的距离：document.getElementById("div").offsetTop
```


