---
title: js/vue输入联想功能
type: categories
copyright: true
date: 2019-03-04 14:22:13
tags: [前端,js,vue,输入框联想]
categories: 前端笔记
description: 
---

#### 实现功能如下
![模糊搜索|输入联想](/images/posts/联想.gif '模糊搜索|输入联想')
<!--more-->
#### 介绍
&emsp;&emsp;最近需求要有一个输入框模糊搜索功能，且需要匹配输入字符联想。从网上看了一些demo，总结出来一套思路，配合vue将其开发并稍做优化处理。
#### 思路
基础：
- 1.一个输入框（输入字符）、一个ul框（展示匹配数据）
- 2.将输入字符与列表中字符比对
- 3.新建空数组，将比对结果匹配项放入此数组中
- 4.将数组数据铺到ul中

优化：
- 5.鼠标滑入某一项变更背景色
- 6.点击选中数据展示到input框中
- 7.键盘上下键控制上一个下一个
- 8.回车键将选中项目展示到input框中
---
#### 代码
##### 1.HTML
```
//input框
 <input type="text"
	   @keydown.down="changeDown"	//键盘事件
	   @keydown.up="changeUp"	
	   v-model="searchVal"		//绑定输入的值
	   @keyup="searchFn"
	   @keyup.enter="enter"		//回车将选中项目展示到input框中
	   placeholder="input name"/>
	   
//选项框 判断如果新数组中有值则显示本备选框
<ul class="ulAdd" v-if="searchResult">	
	<li v-for="(item, index) in searchResult"
		:class="{bgColor: index === now}"	//渲染样式
		@click="alertFn"	//点击将选中项目展示到input框中
		@mouseenter="mouseEnter(index)"	//渲染鼠标移入/移出样式
		@mouseleave="mouseleave">{{ item.name }}</li>
</ul>
```
##### 2.数据
```
searchVal: '', //绑定name
searchResult: [],   //匹配结果
now: -1,        //索引
nameList: [	//name列表
	{name: '张三',id: '9001'},
	{name: '张四',id: '9002'},
	{name: '李三',id: '9003'}
]
```
##### 3.事件处理
```
//过滤
searchFn (e) {
	let val = e.target.value;	//输入的值
	this.searchResult = [];		//清空存结果的数组
	if (val) {
		this.nameList.forEach(function(item) {
			let pattern = new RegExp(''+val, 'g');	//正则校验
			if (pattern.test(item.name)) {
				this.searchResult.push({name: item.name,id: item.id});
			}
		}.bind(this)); // 绑定当前实例对象
	}
}

//将选中项目展示到input框中
alertFn () {
	this.searchVal = this.searchResult[this.now].agentname;
	this.searchResult = [];
},

//按键盘↓键
changeDown () {
	this.now ++;
	if(this.now == this.searchResult.length) {
		this.now = -1;
	}
},

//按键盘↑键
changeUp () {
	this.now --;
	if (this.now === -2) {
		this.now = this.searchResult.length;
	}
},

//回车将选中项目展示到input框中
enter () {
	this.searchVal = this.searchResult[this.now].agentname;
	this.searchResult = [];
},

//清空结果
deleteList () {
	this.searchResult = [];
},

//鼠标移入
mouseEnter (index) {
	this.now = index;
},

//鼠标移出
mouseleave (index) {
	this.now = -1;
}
```
##### 注
&emsp;&emsp;样式自定义即可。
*******
#### 总结
功能实现，且完成了一定的优化，但存在一定的不足：
1. 数组循环可用新语法；
2. 多个方法可合并；
3. 可以进一步优化，做出输入字母也同样联想。


