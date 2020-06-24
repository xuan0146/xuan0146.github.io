---
title: vue构建项目入口文件index.html缓存引发的白屏问题
type: categories
copyright: true
date: 2019-09-03 15:14:27
tags: [vue, 白屏, 缓存]
categories: 前端笔记
description:
---
### 问题
&emsp;&emsp;vue项目打包后，在非首次线上替换dist文件时，某些手机/浏览器在之后首次打开页面，可能出现白屏情况。那么该问题产生原因是什么呢？我们又该如何应对呢？
### 背景及原因分析
&emsp;&emsp;在使用vue-cli脚手架构建完项目，项目完成后，需打包上线。默认打包方式则是`npm build`，然后项目根目录会生成 ***dist*** 文件夹。服务端将该文件夹替换线上即可。但是当第n（n>1）次上线后，由于在用户端会默认缓存index.html入口文件，而由于vue打包生成的css/js都是哈希值，跟上次的文件名都不同，因此会出现找不到css/js的情况，导致白屏的产生。
<!--more-->
&emsp;&emsp;经常使用vue作为开发框架的开发者都知道，build打包后，所生成的css/js的文件名中间会夹杂哈希值，以此来避免缓存问题。但是由于入口文件index.html的名字每次打包后不改变，并且也不能乱变，就导致了index.html在用户端仍然会被缓存下来。那么在服务端更新包之后，由于旧的文件被删除，而index.html所链接的路径依然是旧文件路径，因此会找不到文件，从而白屏。解决方案一般是强制刷新页面或者清除缓存重新加载。当然，网上也给出不少的“缓解方案”。为什么称之为缓解方案而不是解决方案呢？因为前端缓存问题是一个具有行业性的难题，在没有根治之前，一般是优化为主。当然，引领前端行业的大佬们自然是要挑战极限的，不在该问题之内讨论。
&emsp;&emsp;下面给出部分优化方案。

### 优化方案
#### 方案1. meta标签
```html
<!-- 在入口文件index.html中，头部添加meta标签 -->
<meta http-equiv="Expires" content="0">
<meta http-equiv="Pragma" content="no-cache">
<meta http-equiv="Cache-control" content="no-cache">
<meta http-equiv="Cache" content="no-cache">
```
#### 方案2. 时间戳区分
```js
// webpack.prod.conf.js
const Version = new Date().getTime();

output: {
    path: config.build.assetsRoot,
    filename: utils.assetsPath('js/[name].[chunkhash].'+_Version+'js'),
    chunkFilename: utils.assetsPath('js/[id].[chunkhash].'+_Version+'js')
}
```
##### 方案3. 服务端配置
```nginx
// nginx端配置
location = /index.html {
    add_header Cache-Control "no-cache, no-store";
    # add_header Cache-Control no-store;
    # add_header Pragma no-cache;
}
```
#### 方案对比
|方案|操作难度|优缺点|
|:--:|:--:|:--:|
|1|html文件简单修改|基本上没用|
|2|webpack配置简单修改|跟hash值文件名一个性质，不解决痛点|
|3|服务端加配置，简单|解决部分缓存问题，不解决全部；白屏问题依旧存在|

#### 新思路
&emsp;&emsp;在一些论坛中，在某些特定情况下（如混合开发App，原生嵌入webview还在h5)，可在原生端尝试解决：
- 加载webview前清除缓存再加载；
- 销毁webview前清除缓存；
  
&emsp;&emsp;效果如何，有待验证。


以上。