---
title: css布局之道——内凹圆角设计
type: categories
copyright: true
date: 2020-04-29 11:51:37
tags: [css, 布局]
categories: 前端笔记
description:
---
## 一、 效果图
先来看一下效果图：
![内凹圆角](/images/posts/内凹圆角.gif '内凹圆角')

<!--more-->

## 二、 实现
### 1. 场景
看上图，此类场景应该很是常用吧，比如账单、卡片、列表等。

### 2. 实现思路
看到效果图，能想到的实现方法则是
- planA：border-radius直接搞起来
- planB：定位

但是细想之后发现两者皆不可用，border-radius处理的话是凸角，舍弃；定位需要独立出来两个模块，复杂度高了，舍弃。
可是舍弃后，需要怎么做呢？切背景图吗？显然不合理，如果内容不固定，则模块的长度也不一样，背景图也会拉伸；思来想去，还是得从`定位`上做文章：既然元素多比较复杂，那就直接在伪类上动手。
可能大家猜到了，就是直接在`::before`、`::after`上模拟出来。

## 三、 代码
### 1. 基础布局
```html
<!-- html：随便一些div -->
<div></div>
<div></div>
<div></div>
<div></div>
<div></div>
```

```css
body{
    margin: 0;
    background: #000;
    padding: 20px 0;
}
div{
    width: 80vw;
    min-height: 15vh;
    background: #666;
    border-radius: 6px;
    margin: 0 auto;
    position: relative;
    border-top: 1px dashed #333;
}
div:first-child{
    border: none;
}
```

### 2. 伪类
基础布局出来后，怎么利用伪类处理呢？思路是这样的：
1. 利用伪类画出来一个与主题背景色一样的圆；
2. 定位，向左(右)偏移圆自身的一半，视觉效果就是半个圆在上面；
3. 继续定位，向上继续偏移小球的一半，视觉效果则是每个模块有个四分之一内凹圆角一样。

知道思路后，就继续实现吧：
```css
div::before,div::after{
    content: '';
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: #000;
    position: absolute;
    z-index: 999;
}
div::before{
    left: -10px;
    top: -10px;
}
div::after{
    right: -10px;
    top: -10px;
}
div:first-child::before,div:first-child::after{
    content: '';
    width: 0;
    height: 0;
}
```



这样就完成了~

