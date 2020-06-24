---
title: vue使用canvas签名之清空和保存
type: categories
copyright: true
date: 2020-02-03 14:11:33
tags: [js, canvas, vue]
categories: 前端笔记
description:
---

## 效果
![canvas画板清空保存](/images/posts/canvas画板清空保存.gif 'canvas画板清空保存')

<!--more-->

## 需求
&emsp;&emsp;在一些项目业务中，经常会使用到画板，让用户自己去写/画一些东西做标示，比如说在线签电子合约、签名等，在上两篇博客中，已经解决了PC端和移动端的Canvas签名，那么在签名完成之后，我们如何将画布上东西保存，或者清空呢？ ***【本篇包含PC和移动端的签名，以及清空和保存】***

## 分析
&emsp;&emsp;在前两篇中，分辨实现了`PC端canvas签名`以及`移动端canvas签名`，要是形成一个简单且完整的功能点，我们起码还缺少清空和保存两个环节。接下来分析一下。
### 1. 清空
&emsp;&emsp;就是将此前所画的所有笔画清除掉，方法则是清空存放点集合的数组重新设置画布宽高即可。另外一个方法，也可以使用`clearRect()`，这个方法是清空画布中一个矩形区域内的内容。由于我们并为保存所有点集合，所以采用此方法清空。
#### 1.1 关于clearRect
```js
// clearRect() 方法清空给定矩形内的指定像素。
context.clearRect(x,y,width,height);
```

|参数|描述|举例(全部清空)|
|:--:|:--:|:--:|
|x	|要清除的矩形左上角的 x 坐标|0|
|y	|要清除的矩形左上角的 y 坐标|0|
|width|	要清除的矩形的宽度，以像素计|context.width|
|height	|要清除的矩形的高度，以像素计|context.height|

#### 1.2 代码示例
```html
<el-button type="danger" @click="cleanCanvas">
    清空
</el-button>
```

```js
mounted () {
    let board = this.$refs.board;   // 获取DOM
    this.width = board.width = this.$refs.boardBox.offsetWidth;  // 设置画布宽
    this.height = board.height = this.$refs.boardBox.offsetHeight;    // 设置画布高
    ...     // something codes
}

// 清空画布
cleanCanvas () {
    // clearRect() 方法清空给定矩形内的指定像素。
    this.ctx.clearRect(
        0,
        0,
        this.width,
        this.height
    )
},
```

### 2. 保存
保存，需求简单明了，就是将canvas输出为一张图片。处理也简单粗暴，直接将此区域输出为一张base64的图片即可。方法是使用原生方法`toDataURL()`
#### 2.1 关于toDataURL
HTMLCanvasElement.toDataURL() 方法返回一个包含图片展示的 data URI 。可以使用 type 参数其类型，默认为 PNG 格式。图片的分辨率为96dpi。

- 如果画布的高度或宽度是0，那么会返回字符串“data:,”。
- 如果传入的类型非“image/png”，但是返回的值以“data:image/png”开头，那么该传入的类型是不支持的。
- Chrome支持“image/webp”类型。

**方法**
canvas.toDataURL(type, encoderOptions);
**参数**
- type 可选
图片格式，默认为 image/png
- encoderOptions 可选
图片质量。取值范围为 0 到 1 。如果指定图片格式为 image/jpeg 或 image/webp。如果超出取值范围，将会使用默认值 0.92。其他参数会被忽略。

**返回值**
包含 data URI 的DOMString。

#### 2.2 代码示例
```html
<el-button type="success" @click="saveCanvas">
    保存
</el-button>

<div>
    <img :src="base64" alt="">
</div>
```

```js
data() {
    return {
        base64: '',
    }
}

// 保存签名
saveCanvas () {
    this.base64 = this.$refs.board.toDataURL(); // 转为base64
}
```

### 结语
关于canvas签名的基本到这里就结束了，项目如果有遇到更复杂的再继续更新。目前更新的有
- PC端签名方法
- 移动端签名方法
- PC和移动端签名方法以及清空和保存

想了解本期源码的可以关注下公众号`流眸`回复`canvas`或者`签名`获取哦～
![微信公众号](/images/qrcode.jpg '流眸')