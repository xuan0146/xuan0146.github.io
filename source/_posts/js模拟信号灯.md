---
title: js模拟信号灯
type: categories
copyright: true
date: 2019-04-29 10:21:28
tags: [js, Promise, 信号灯]
categories: 前端笔记
description:
---

### 效果图
![信号灯](/images/posts/信号灯.gif '信号灯')

<!--more-->

### 需求
给出一个div元素块，模拟一个如下条件的交通信号灯：
- 绿灯亮x毫秒，转黄灯
- 黄灯亮y毫秒，转红灯
- 红灯亮z毫秒，转绿灯
- 无限循环执行

### 需求分析
&emsp;&emsp;首先，div元素块设置css变圆；其次，每隔一定时间，转换元素块背景色；接着，控制显示时长；然后，将上两步组合；最后，无限循环执行。

### 应用

#### 技术栈
1. css
	- border-radius
	- background
2. js
	- setTimeout
	- Promise
	- async/await
	- while

#### 技术栈分析
|类别|技术|用途|
|:--:|:--:|:--:|
|样式|border-radius|元素形状|
|样式|background|元素背景色|
|脚本|setTimeout|定时器|
|脚本|Promise|处理回调|
|脚本|async/await|配合使用|
|脚本|while|循环|

#### 代码
```
// html
<div id="signal-lamp"></div>
<button class="btn" onclick="run()">执行</button>
<span id="showText"></span>


// css
#signal-lamp {
	width: 100px;
	height: 100px;
	border-radius: 50%;
	border: 1px solid #ffcecc;
}


// js

// 定义函数sleep
function sleep(ms){
	// 返回一个Promise实例
	return new Promise(function(resolve){
		setTimeout(resolve, ms);
	})
}

// 改变颜色 等待参数时间 （时间及颜色均为参数）
async function ChangeColor(ms, color){
	// 改变颜色
	document.getElementById("signal-lamp").style.background = color;
	// 打印出显示时间
	document.getElementById("showText").innerText = `${color}:显示${ms}ms`;
	// 睡眠ms毫秒
	await sleep(ms);
}

// 循环依次执行
async function run(){
	// while 循环
	while (true) {
		// 绿灯 1.8s
		await ChangeColor(1800,"green");
		// 黄灯 0.8s
		await ChangeColor(800, "yellow");
		// 红灯 1.2s
		await ChangeColor(1200, "red");
	}
}

```