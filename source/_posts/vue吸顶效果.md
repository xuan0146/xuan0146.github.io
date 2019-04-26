---
title: vue吸顶效果
type: categories
copyright: true
date: 2019-04-26 20:28:13
tags: [vue, 吸顶]
categories: 前端笔记
description:
---
#### 关于吸顶
>吸顶效果在使得用户在滑动读取数据的时候把产品需要持续展示的控件及信息一直固定在屏幕上方，以便用户操作和交互。

<!--more-->

#### 产生背景
&emsp;&emsp;随着技术不断更新与用户审美不断提升，一些App中/浏览器中的常用交互也在对用户更加友好，在某些数据展示较多页面或者导航栏页面，为了将栏目划分，使得视图模块更加明显，吸顶效果便在此背景下孕育而生。
&emsp;&emsp;那么，我们如何在web端来做一个吸顶效果呢？

-----

#### 切入正题——吸顶

##### 简单效果展示
![简单吸顶效果](/images/posts/吸顶.gif '简单吸顶效果')

##### 需求分析
1. 有了效果图，首先我们要分析一下结构：
	- 吸顶上方数据
	- 需要吸顶的元素
	- 吸顶下方数据
2. 接着我们逻辑走一下：(我们设吸顶元素为X)
	- 在页面滑动距离 <= 吸顶元素距离顶端距离时，不吸顶
	- 否则，吸顶
3. 有了这个大前提，继续考虑，如何做到吸顶呢？
	- 设置该元素的position为fixed属性
	- 动态控制是否展示该样式
	- 扩展：加点动画效果(这里以渐变为示例)
	
##### 代码及注释
```
// html
<template>
    <div class="scrollFixed">
        <!-- 上方数据 -->
        <div class="sTop">
            <ul>
                <li v-for="(ietm, index) in 10">这是吸顶上方的第{{index + 1}}条数据</li>
            </ul>
        </div>
        <!-- 吸顶元素 -->
        <nav class="nav" id="searchBar" :class="scrollFixed === true ? 'isFixed' :''">{{ text }}</nav>
        <!-- 下方数据 -->
        <div class="sBottom">
            <ul>
                <li v-for="(ietm, index) in 30">这是吸顶下方的第{{index + 1}}条数据</li>
            </ul>
        </div>
    </div>
</template>

// js
<script>
    export default {
        data() {
            return {
                text: '需要吸顶',
                scrollFixed: false,
                offsetTop: 0
            }
        },
        methods: {
            windowScroll () {
                // 滚动条顶部 距 滚动原点的高度
                let scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop;
                /**
                 * 三目运算
                 * 两个高度比较 
                 * 如果滑动距离 > 吸顶元素到页面顶端距离  动态添加
                 */
                scrollTop >= this.offsetTop ? (this.scrollFixed = true, this.text = '已吸顶') : (this.scrollFixed = false, this.text = '需要吸顶');
            }
        },
        mounted() {
            // 需吸顶元素 距 离浏览器顶端的高度
            this.offsetTop = document.querySelector('#searchBar').offsetTop;
            // 滚动监听
            window.addEventListener('scroll', this.windowScroll);
        },
        destroyed () {
            // 关闭 销毁监听
            window.removeEventListener('scroll', this.windowScroll);
        }
    }
</script>

<style scoped type="text/scss" lang="scss">
.isFixed{
	position:fixed;
	top:0;
	left: 0;
	z-index:999;
	background: #12d168;
	transition: all 1s;
	color: #7511ff;
}
// 基本样式略
// ......
</style>
```
------

#### 备注
&emsp;&emsp;此demo在流行浏览器中运行问题不大，但是没有详测兼容性。如果要在生产上运用，务必做好各端兼容。
