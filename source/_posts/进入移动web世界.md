---
title: 进入移动web世界
type: categories
copyright: true
date: 2019-09-16 15:42:00
tags: [移动web, 解决方案]
categories: 前端笔记
description:
---


## 一、 移动端web基础

### 1. Pixel
- px: CSS pixels 逻辑像素，浏览器使用的抽象单位；
- dt,pt: 设备无关像素；
- dpr: 设备像素缩放比；
- 公式： 1px = (dpr)<sup>2</sup> * dp

<!--more-->

**默认缩放比例：**

| |ldpi|mdpi|hdpi|xhdpi|
|:--:|:--:|:--:|:--:|:--:|
|ppi|120|160|240|320|
|默认缩放比例|0.75|1.0|1.5|2.0|

### 2. Viewport
手机浏览器默认做了两件事
1. 页面渲染在了一个默认的viewport;
2. 缩放;

那么问题来了，为什么会有一个默认的viewport呢？
我们知道，pc端页面，在移动端查看的时候，由于像素不匹配，但是为了能够给用户展现一个比较完整的页面，因此会虚拟出一个viewport出来，在此viewprot上渲染页面。也就是说，最终目的，是**为了排版正确**。
但是由于一般默认情况下，给出的viewport像素宽对页面来说是不友好、不规范的，因此我们还需要解决一个规范问题。
解决方案：**在head中加一个meta标签**
格式如下：
`<meta name="viewport" content="name=value, name=value">`

### 3. meta标签
```js
<meta name="viewport" content="name=value, name=value">
// name = value 对应关系
width: device-width;
initial: xx;   // 设置初始缩放
minimum-scale: xx;  // 最小缩放
maxim-scale: xx;    // 最大缩放
user-scalable: no;  // 用户不可缩放
```

------

## 二、 高效的移动web布局
### 1. flexbox弹性布局
- flex布局混合排版
```css
/* 混合使用flex占比 */
.nav{
    display: -webkit-flex;
}
.son1{
    flex: 1;        
}
.son2{
    flex: 3;
}
.son3{
    width: 100px;
}
```
- flex水平垂直居中
```css
.parent{
    justify-content: center;
    align-items: center;
    display: -webkit-flex;
}
```
### 2. flex兼容性
- iOS可以使用最新的flex布局；
- Android4.4以下，只能兼容旧版flexbox布局；
- Android4.4及以上，可以使用最新的flex布局；

表现如下：

|新felx布局|旧flexbox布局|
|:--:|:--:|
|display: -webkit-flex;|display: -webkit-flex-box;|
|-weblit-flex: 1;|-weblit-flex-box: 1;|
|justify-content: center;|box-pack: center;|
|align-items: center;|box-align: cneter|

### 3. 响应式设计
顾名思义，一套页面，在所有端（pc、超大屏、ipad、手机等）完美运行。随页面宽高变化而改变页面样式，从而适配不同设备。
#### 媒体查询  @media
```css
@media screen and (max-width: 1024px) {
    body{
        background: #eee;
    }
}
@media screen and (max-width: 980px) {
    body{
        background: green;
    }
}
@media screen and (max-width: 720px) {
    body{
        background: yellow;
    }
}
@media screen and (max-width: 640px) {
    body{
        background: skyblue;
    }
}
@media screen and (max-width: 320px) {
    body{
        background: pink;
    }
}
@media screen and (min-width: 1025px) {
    body{
        background: darkseagreen;
    }
}
```

#### 媒体类型
- screen： 屏幕
- print： 打印机
- handhead： 手持设备
- all： 通用

#### 常用参数说明
- width: 视口宽
- height: 视口高
- device-width: 设备宽
- device-height: 设备高
- orientation: 检查设备横屏竖屏处向（landscape横，portrait竖）

#### 设计点
1. 百分比布局：使切换css不同媒体样式时更加平滑
2. 弹性图片：图片根据盒子百分比，改变盒子宽高即可
3. 重新布局，显示和隐藏：
    - 同比例缩减元素尺寸
    - 调整页面结构布局
    - 隐藏冗余的元素

#### 优劣比较
- 优点：减少重复开发，一套代码多端兼容
- 劣势：在极端情况下影响页面性能，含有较多的冗余代码

### 4. 移动web特别样式处理
#### a. 高清图片处理
>width: (w_value / dpr)px
height: (h_value / dpr)px

#### b. 1像素边框问题
**问题：** Retina屏幕下，1像素问题
**原因：** 1px使用2dp渲染
**解决：**  
```css
/* 错误案例 */
div{
    border-top: 0.5px;
}

/* 正确案例 */
div:before{
    border-top: 1px;
    -webkit-transform: scaleY(0.5);
    position: absolute;
    top: -1px;
    left: 0;
    content: '';
    height: 1px;
}
```

#### c. 相对单位
- em: 根据父节点的font-size为相对单位
- rem: 根据html的font-size为相对单位 ***（建议使用）***

那么，rem的基值设置为多少比较合适呢？
回归到开发中来，我们有一个公式：
`rem = screen.width / x`
例： 320的屏幕，可以设置为font-size=32px，而375的屏幕，设置为37.5px。我们的目的是为了方便计算。

#### d. 文本溢出
```css
/* 单行文本溢出*/
.line{
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}

/* 多行文本溢出 */
.lines{
    display: -webkit-box !important;
    overflow: hidden;
    text-overflow: ellipsis;
    word-break: break-all;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;  // 行数
}

```

## 三、 终端交互优化
### 1. 300ms延迟问题
在移动端，由于有多重手势操作替代了鼠标操作，因此，为了判断出是点击、双击、触摸移动或者别的手势，iOS系统判断中加了一个300毫秒的延迟：在第一次出发事件300毫秒内再次出发，例如点击，就会被判断为双击。那么为了统一规范，后来在Android系统中也加入了此判定。这就是著名的移动端300ms延迟问题。
那么如何解决这个问题呢？**tap**事件处理。
什么是tap事件？简而言之，就是通过touch，监听touchstart和tarchend，如果两者间隔较短，例如100ms甚至更短，且起始位置的偏移量极小，控制在几个像素之内，那么就判定为点击事件。如此操作，可以绕过系统300ms的规范，从而在用户体验上做的更优。
但我们只有，一般有利就有弊。我们解决掉300ms延迟问题，从而又产生了一个新的问题，就是穿透问题。例如在按钮上有一个蒙层，我们点击蒙层，关闭其蒙层。但是如果在蒙层下面同样有点击事件，那么我们在点击蒙层关闭后，也会触发到下面的事件。那么这种问题的一般解决方案便是关闭蒙层的时候，添加一个300ms的延时，经过300ms延时关闭后，点击不再具有穿透性，巧妙的解决的该穿透问题。但是问题又来了，300ms延时其实对用户体验来讲并不那么友好。因此还是推荐第一种解决方案，不会出现拆东墙补西墙或者说捉襟见肘的问题。当然，如果使用框架库的话，大部分强大的库默认都解决了这个问题，不用开发者再为此操心。

### 2. touch相关
***触摸是移动设备交互的核心事件***
#### a. 触摸事件
|事件|触发情况|备注|
|:--:|:--:|:--:|
|touchstart|手指触摸屏幕触发|已有手指放在屏幕上则不触发|
|touchmove|手指在屏幕上滑动|连续触发|
|touchend|手指离开屏幕时触发|/|
|touchcancel|系统取消touch时触发|不常用|

#### b. 事件属性
- touches： 跟踪触摸操作的touch对象数组
- targetTouches： 特定事件目标的touch对象数组
- changeTouches： 上次触摸改变的touch对象数组

#### c. 每个touch对象包含属性
1. clientX： 触摸目标在视口中的横坐标
2. clientY： 触摸目标在视口中的纵坐标
3. identifier： 标识触摸的唯一id
4. pageX： 触摸目标在页面中的横坐标（含滚动）
5. pageY： 触摸目标在页面中的纵坐标（含滚动）
6. screenX： 触摸目标在屏幕中的横坐标
7. screenY： 触摸目标在屏幕中的纵坐标
8. target： 触摸的DOM节点的目标

#### d. 相关bug
在Android中，某些版本只会触发一次touchstart和一次touchmove，不会触发touchend。解决方案则是在事件中（touchmove）添加阻止默认事件：`event.preventDefault()`。但与此同时，要注意随之产生的一个问题，就是组织默认事件后，页面也会随之禁止滚动，因此看情况使用。


