---
title: v-html指令渲染出的内容如何添加样式
type: categories
copyright: true
date: 2019-04-22 20:24:20
tags: [vue, 样式渲染, v-html]
categories: 前端笔记
description:
---
#### v-html
&emsp;&emsp;在vue使用中，指令`v-html`渲染页面经常用到，类似于jQuery的$('x').html()去渲染。通过指令`v-html`渲染出来的内容还会带有原来的标签及其样式，如果需要修改或者重设其样式，应该如何去做吗？

<!--more-->

#### 采坑
&emsp;&emsp;首先，我在style中用子级选择器去选中并修改样式，经过猛如虎的操作后，并没生效。F12打开Elements调试，发现在style里面样式根本没加载上去，没有class中也没有类名出现。除此之外，渲染非该指令元素时，所有的类名会跟有`[data-v-xxxxxx]`的东西。

#### 爬坑
##### 解决方案
现给出3中方案来解决此问题：
- 去掉style中的`scoped`;
- `watch`监测数据变化;
- 深度选择器`>>>`


##### 方案1实践
><font color=deepskyblue>去掉style中的scoped</font>

&emsp;&emsp;在vue组件中，我们写style时，为了防止页面样式冲突，在每个组件中会加上`scoped`属性。经测试，去掉该属性即可渲染样式成功。但是在组件过多或者项目中大时，经常会出现页面样式冲突，因此该方法***不建议使用***。

##### 方案2实践
><font color=deepskyblue>watch监测数据变化</font>

&emsp;&emsp;在`script > export default`中,watch属性可监听v-html所绑定值的变化。如果是后台请求的数据，那么可以在watch中监听改数据变化，当数据发生改变驱动视图后，动态绑定一个class来改变子级元素样式。此方法***有一定局限性***。

##### 方案3实践
><font color=deepskyblue>深度选择器 >>></font>

&emsp;&emsp;此时，深度选择器的应用则脱颖而出。深度选择器`>>>`，可深度改变子级样式。例：
```
<template>
	<div class="test"></div>
</template>

<script>
// ***
</script>

<style scoped>
	.test >>> *{
		width: 100%;
	}
</style>
```
如果使用`scss`，则用`/deep/`替代：
```
<style scoped type="text/scss" lang="scss">
	.test{
		/deep/ *{
			width: 100%;
		}
	}
</style>
```
