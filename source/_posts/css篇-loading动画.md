---
title: 'css篇:loading动画'
type: categories
copyright: true
date: 2019-08-26 17:00:38
tags: [css, 动画, loading]
categories: 前端笔记
description:
---
### 示例
![loading示例](/images/posts/loading.gif 'loading示例')

<!--more-->

### 一、解析
#### loading动画1
&emsp;&emsp;由图可见，动画1中有三根竖线，在进行变长变短的高度变化以及线条的颜色变化，因此分为以下几个步骤：
- 建立块级区域；
- 三根线条排版；
- 动画——颜色、线条长度渐变；
- 动画引入及执行方法；

#### loading动画2
&emsp;&emsp;动画2中则与动画1排版略有不同：在动画区域内，有四个圆点，然后重复的放大缩小以及匀速旋转。因此步骤如下：
- 建立块级区域；
- 四个原点排版（定位/flex等方法）
- 动画——缩放、旋转；
- 动画引入及执行方法；

### 二、 代码
#### 1. loading1
##### html
```html
<div class="loading">
    <div class="loading1">
        <div class="line line1"></div>
        <div class="line line2"></div>
        <div class="line line3"></div>
    </div>
</div>
```
##### css
```css
.loading{
    width: 100px;
    height: 100px;
    overflow: hidden;
    background: rgba(0,0,0,0.2);
    border-radius: 10px;
    margin: 10px;
}

/* loading1 */
.loading1{
    height: 34px;
    margin-top: 33px;
    text-align: center;
    display: flex;
    justify-content: center;
}
.line{
    margin: 0 5px;
    width: 10px;
    height: 16px;
    background: #8e6ddb;
    border-radius: 15px;
}
.line1{
    animation: loading1 .6s ease 0s infinite;
}
.line2{
    animation: loading1 .6s ease .2s infinite;
}
.line3{
    animation: loading1 .6s ease .4s infinite;
}
@keyframes loading1 {
    0% {
        height: 16px;
        background: #608cff;
    }
    50% {
        height: 34px;
        background: #8e6ddb;
    }
    100% {
        height: 16px;
        background: #608cff;
    }
}

```

#### 2. loading2
##### html
```html
<div class="loading">
    <div class="loading2">
        <div class="point point1"></div>
        <div class="point point2"></div>
        <div class="point point3"></div>
        <div class="point point4"></div>
    </div>
</div>
```
##### css
```css
.loading{
    width: 100px;
    height: 100px;
    overflow: hidden;
    background: rgba(0,0,0,0.2);
    border-radius: 10px;
    margin: 10px;
}
/* loading2 */
.loading2{
    position: relative;
    width: 46px;
    height: 46px;
    margin: 27px auto 0;
    flex-wrap: wrap;
    animation: loading2 2s linear 0s infinite;
}
.point{
    width: 16px;
    height: 16px;
    background: red;
    border-radius: 50%;
    position: absolute;
}
.point1{
    top: -8px;
    left: 50%;
    margin-left: -8px;
    background: #dbdcbf;
}
.point2{
    right: -8px;
    top: 50%;
    margin-top: -8px;
    background: #32b53f;
}
.point3{
    bottom: -8px;
    left: 50%;
    margin-left: -8px;
    background: #a77ef4;
}
.point4{
    left: -8px;
    top: 50%;
    margin-top: -8px;
    background: #c4356d;
}

@keyframes loading2 {
    0% {
        transform: rotate(0deg) scale(1);
    }
    10% {
        transform: rotate(36deg) scale(1.1);
    }
    20% {
        transform: rotate(72deg) scale(1.2);
    }
    30% {
        transform: rotate(108deg) scale(1.3);
    }
    40% {
        transform: rotate(144deg) scale(1.4);
    }
    50% {
        transform: rotate(180deg) scale(1.5);
    }
    60% {
        transform: rotate(216deg) scale(1.4);
    }
    70% {
        transform: rotate(252deg) scale(1.3);
    }
    80% {
        transform: rotate(288deg) scale(1.2);
    }
    90% {
        transform: rotate(324deg) scale(1.1);
    }
    100% {
        transform: rotate(360deg) scale(1);
    }
}
```

###
以上。
