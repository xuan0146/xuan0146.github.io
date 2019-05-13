---
title: js页面全屏
type: categories
copyright: true
date: 2019-05-13 14:20:51
tags: [vue, js, 全屏]
categories: 前端笔记
description:
---
### 需求
![页面全屏](/images/posts/页面全屏.gif '页面全屏')

<!--more-->

### 背景
&emsp;&emsp;在做前端PC端项目中，我们经常会遇到让页面全屏的需求。大多情况下，这样可以使得用户在该页面更加专注，屏蔽掉该页面之外的干扰。

### 需求分析
1. 状态判断
2. 事件操作
#### 1. 状态判断
首先，我们需要给出一个默认状态，即非全屏。然后，在此基础上我们进行DOM操作，每次用户进行操作时，我们先判断当前状态，进行相应的操作，操作过后改变当前状态即可。
#### 2. 事件操作

|操作|浏览器|代码|
|:--:|:--:|:--:|
|全屏|W3C|document.documentElement.requestFullscreen()|
|全屏|CHROME|document.documentElement.webkitRequestFullScreen()|
|全屏|FIREFOX|document.documentElement.mozRequestFullScreen()|
|全屏|IE|document.documentElement.msRequestFullscreen()|
| | | |
|还原|W3C|document.exitFullscreen|
|还原|CHROME|document.webkitCancelFullScreen|
|还原|FIREFOX|document.mozCancelFullScreen|
|还原|IE|document.msExitFullscreen|

### 代码
`注：` 本demo在vue中使用。如果使用正常.html，自行将`@click`转为正常点击事件(如`onclick`)即可。
```
// html
<div @click="handleFullScreen">
	一个按钮
</div>

// js
data() {
	return {
		fullscreen: false  // 是否全屏
	}
},
methods: {
	// 全屏事件
	handleFullScreen(){
		let element = document.documentElement;
		// 判断是否已经是全屏
		// 如果是全屏，退出
		if (this.fullscreen) {
			if (document.exitFullscreen) {
				document.exitFullscreen();
			} else if (document.webkitCancelFullScreen) {
				document.webkitCancelFullScreen();
			} else if (document.mozCancelFullScreen) {
				document.mozCancelFullScreen();
			} else if (document.msExitFullscreen) {
				document.msExitFullscreen();
			}
			console.log('已还原！');
		} else {	// 否则，进入全屏
			if (element.requestFullscreen) {
				element.requestFullscreen();
			} else if (element.webkitRequestFullScreen) {
				element.webkitRequestFullScreen();
			} else if (element.mozRequestFullScreen) {
				element.mozRequestFullScreen();
			} else if (element.msRequestFullscreen) {
				// IE11
				element.msRequestFullscreen();
			}
			console.log('已全屏！');
		}
		// 改变当前全屏状态
		this.fullscreen = !this.fullscreen;
	}
}

```
