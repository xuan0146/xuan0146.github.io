---
title: vue项目打包后资源路径与背景图路径问题
type: categories
copyright: true
date: 2019-02-28 21:55:51
tags: [前端, vue, 打包]
categories: 前端笔记
description:
---
### 前言
本文主要解决基于`vue/vuex`打包后经常出现的两个问题：
- 静态资源路径错位问题
- css背景图路径错位问题
<!--more-->
------
### 问题的出现
&emsp;&emsp;一般通过vue-cli脚手架开发的时候，开发模式使用`npm run dev`进行开发，一切配置正常。但是当打包后会出现各种问题，其中两个最为常见且致命的问题莫过于上述的两个资源路径错位问题，但是当我们分析后，发现依旧是配置问题。尤其是当好多猿把` assetsPublicPath: '/'`的`'/'`改为`'./'`后，一般的资源路径包括css、js、图片音视频等都能校正，然而唯独css设置的背景图却全部失效。接下来请看此bug图。
* **bug图：**
**1. 资源路径404**
![资源路径404.png](https://upload-images.jianshu.io/upload_images/2502265-9327d2c5c190797a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 '资源路径404')
**2. 背景图路径404**
![背景图404.jpg](https://upload-images.jianshu.io/upload_images/2502265-d9a32ca040d852d4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 '背景图路径404')
可以看到背景图的路径错且重复。
---------
### 解决方案

**1. 静态资源路径错位问题解决方案：**
在`项目/config/index.js`中找到`module.exports`中的`build`，其中的`assetsPublicPath`项改为：
```
assetsPublicPath: './', //解决静态资源路径错位问题
```
**2. css背景图路径错位问题解决方案：**
在`项目/build/utils.js`中找到`ExtractTextPlugin.extract`，追加`publicPath: '../../'`：
代码片段为：
```
return ExtractTextPlugin.extract({
            use: loaders,
            fallback: 'vue-style-loader',
            publicPath: '../../'  //解决css背景图路径错位问题
      })
```
------
* **测试结果：**
ok，配置完毕，打包测试下~
`npm run build` 倒数10个数：10,9,8,7,6...
控制台空空如也~完美！
![完美解决.png](https://upload-images.jianshu.io/upload_images/2502265-7bd97c7e5bec7d8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 '解决')






