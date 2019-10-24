---
title: lib-flexible适配大屏方案（附移动端适配）
type: categories
copyright: true
date: 2019-10-24 09:45:31
tags: [vue, 前端, 源码]
categories: 前端笔记
description:
---
## 前言
&emsp;&emsp;相信大多数移动端前端开发者都是用过`lib-flexible`来作为移动端适配的解决方案。lib-flexible是淘宝项目组开发出来的一个小插件，属于开源项目，可以在各类项目中引入并使用，为移动端的开发者带来了无穷的便利。
&emsp;&emsp;但是，有人提出**为什么在屏幕尺寸超出一定分辨率后便不再适配？** 今天针对该问题来尝试给出解决方案。

<!--more-->

------

## 关于lib-flexible
### 1. 解读
&emsp;&emsp;lib-flexible会自动在html的head中添加一个`meta name="viewport"`的标签，同时会自动设置html的font-size为屏幕宽度除以10，也就是1rem等于html根节点的font-size。假如设计稿的宽度是750px，此时1rem应该等于75px。假如量的某个元素的宽度是150px，那么在css里面定义这个元素的宽度就是 width: 2rem。但是当分辨率大于某个特定值时，它便不再生效。

### 2. 移动端适配步骤
&emsp;&emsp;一般而言，`lib-flexible`并不独立出现，而是搭配`px2rem-loader`一起做适配方案，目的是**自动将css中的px转换成rem**。以下为它在vue中的使用。


#### 2.1 安装 lib-flexible
```
npm install lib-flexible --save-dev
```
#### 2.2 引入 lib-flexible
在`main.js`中引入lib-flexible
```js
// px2rem 自适应
import 'lib-flexible'
```
#### 2.3 安装 px2rem-loader
```
npm install px2rem-loader --save-dev
```
#### 2.4 配置 px2rem-loader
分两种情况：
- vue-cli 2.x
- vue-cli 3.x

##### 2.4.1 第1种
如果是2.x版本。
1. 在`build/utils.js`中，找到`exports.cssLoaders`，作出如下修改：
```js
const px2remLoader = {
    loader: 'px2rem-loader',
    options: {
        remUint: 75 // 以设计稿750为例， 750 / 10 = 75
    }
}
```
2. 继续找到`generateLoaders`中的`loaders`配置，作出如下配置：
```js
// 注释掉这一行
// const loaders = options.usePostCSS ? [cssLoader, postcssLoader] : [cssLoader]
// 修改为
const loaders = [cssLoader, px2remLoader]
 
if (options.usePostCSS) {
  loaders.push(postcssLoader)
}
```
3. 重新`npm run dev`，完成。
##### 2.4.2 第2种
如果是3.x版本。由于3.x版本需要自己配置，在项目根目录新建文件`vue.config.js`，然后如下配置：
```js
module.exports = {
    css: {
        loaderOptions: {
            css: {},
            postcss: {
                plugins: [
                    require('postcss-px2rem')({
                        // 以设计稿750为例， 750 / 10 = 75
                        remUnit: 75
                    }),
                ]
            }
        }
    },
};
```
然后，重新`npm run dev`，完成。

### 3. 大屏怎么办？
&emsp;&emsp;正常情况下，如果是教程，那么到此就结束了。可本文中心是如何在大屏中正常使用`lib-flexible`。
&emsp;&emsp;例如我们屏幕尺寸要做以`3840 x 2160`为设计稿的适配，那么我们的remUnit的值则改为384。然后呢？重启项目，发现……咦？布局全部乱掉！经过排查，自己的代码基本上不会导致该问题的出现，那么只能是源码在作怪了。**重点来了。**
#### 3.1 找到源码
打开`./node_modules/lib-flexible/flexible.js`，找到如下片段源码：
```js
function refreshRem(){
    var width = docEl.getBoundingClientRect().width;
    if (width / dpr > 540) {
        width = 540 * dpr;
    }
    var rem = width / 10;
    docEl.style.fontSize = rem + 'px';
    flexible.rem = win.rem = rem;
}
```
&emsp;&emsp;从此段源码中我们不难看出，当屏幕宽度除以dpr（dpr是一个倍数，此处一般为1）大于540这个特定值的时候，那么就不再进行适配了。那么我们如何解决这个问题呢？
#### 3.2 修改源码
&emsp;&emsp;在上述源码中，进行修改。例如我要适配的大屏幕尺寸是基于3840的设计稿，而要求最小范围是1980，最大为5760，那么我们要修改的则变为：
```js
function refreshRem(){
    var width = docEl.getBoundingClientRect().width;
    if (width / dpr < 1980) {
        width = 1980 * dpr;
    } else if (width / dpr > 5760) {
        width = 5760 * dpr;
    }
    var rem = width / 10;
    docEl.style.fontSize = rem + 'px';
    flexible.rem = win.rem = rem;
}
```
#### 3.3 重启，完成
&emsp;&emsp;修改完成后，重启项目，则会适配到相应的尺寸。此外还有一个提示，当脱离掉`node_modules`重新`npm install`项目依赖时还是需要重新修改一遍的，千万别忘了！

-------

以上。







