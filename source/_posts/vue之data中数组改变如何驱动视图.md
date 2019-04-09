---
title: vue之data中数组改变如何驱动视图
type: categories
copyright: true
date: 2019-04-09 16:06:36
tags: [vue,js,前端]
categories: 前端笔记
description:
---
#### 前言
&emsp;&emsp;熟悉vue的小伙伴们都知道vue属于`MVVM`框架，数据可以驱动视图。一般来说，vue中的data发生变化，视图中绑定的data值也会随之发生变化。然而还是有特殊情况的，例如关于数组|对象的某些操作。
&emsp;&emsp;根据官方文档定义：
> **如果在实例创建之后添加新的属性到实例上，它不会触发视图更新。**
<!--more-->

#### 问题 
&emsp;&emsp;例：data中有一对象数组`arr`，如下：
```
// html
<ul>
	<li v-for="(item, index) in arr">
		{{ index + 1 }} - {{ item }} 
		<button @click="change(index)">change</button>
	</li>
</ul>


// js
data: {
	arr: [
		{
			id: 0,
			son: [
				{
					name: 'son1'
				},
				{
					name: 'son2'
				}
			]
		},
		{
			id: 1,
			son: [
				{
					name: 'son1'
				},
				{
					name: 'son2'
				}
			]
		}
	],
}
```
&emsp;&emsp;在将数据绑定到视图中以后，点击change将当前点击的数组子项添加了一个id：
```
// ...
methods: {
	change (index) {
		this.arr[index].son.forEach((item, index) => {
			item.id = index + 1;
		});
	},
}
```
&emsp;&emsp;点击之后，data中的arr数组发生了改变，按理说即将出现的效果应是数据更新后的视图，但是惊讶的发现视图并没有改变。那么如何解决此问题呢？
#### 解决方案
&emsp;&emsp;使用<font color=red size=3>Vue.set</font>可得到解决。
> this.$set(this.data, index, obj)
// this.data: 要改变的数据
// index: 下标
// obj: 修改后的对象 

&emsp;&emsp;如刚才所举之例，解决如下：
```
change (index) {
	this.arr[index].son.forEach((item, index) => {
		item.id = index + 1;
		this.$set(this.arr[index].son, index, item);
	});
},
```
&emsp;&emsp;此时，点击`change`按钮，则视图实时改变。
&emsp;&emsp;问题得以解决。

