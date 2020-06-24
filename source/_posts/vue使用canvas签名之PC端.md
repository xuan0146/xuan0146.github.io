---
title: vue使用canvas签名之PC端
type: categories
copyright: true
date: 2020-01-10 11:39:08
tags: [vue, canvas, js]
categories: 前端笔记
description:
---
## 效果
![canvas签名效果](/images/posts/canvas画板pc.gif '签名效果')

<!--more-->

## 需求
&emsp;&emsp;在一些项目业务中，经常会使用到画板，让用户自己去写/画一些东西做标示，比如说在线签电子合约、签名等，如果不用插件，那么如何使用h5的canvas画布来实现这一需求呢？ ***【本篇只讨论PC端，移动端期待下篇】***

## 分析
&emsp;&emsp;很明显，我们需要一个canvas，关于canvas的一些基本操作可以在w3school或者别的一些平台上熟悉一下，其实本例也是基础操作。本案例在vue中完成。（脱离vue也一样。）
- 首先，需要一个canvas画布
- 其次，考虑逻辑
- 把逻辑实现

### 1. canvas画布
随意布局的一个画布，此处值得注意的是**如果canvas的宽高确定，则在html>canvas中直接写宽高，如果不确定，根据别的元素变化，那么可以在js中初始化画布时写**。
#### html
```html
<div class="boardBox" ref="boardBox">
    <canvas ref="board"
            
    </canvas>
</div>
```
#### 布局
```css
.boardBox{
    margin: 100px auto 20px;
    width: 80vw;
    height: 35vh;
    background: #eee;
    canvas{
        border: 1px solid #298cff;
    }
}
```
#### 画布初始化
```js
let board = this.$refs.board;   // 获取DOM
board.width = this.$refs.boardBox.offsetWidth;  // 设置画布宽
board.height = this.$refs.boardBox.offsetHeight;    // 设置画布高
this.ctx = board.getContext('2d');   // 二维绘图
this.ctx.strokeStyle = '#ff0000';   // 颜色
this.ctx.lineWidth = 5;  // 线条宽度
```

### 2. 逻辑分析
由于本篇只讨论PC端，因此无非是在画布上监听三个鼠标事件：`mousedown`、`mousemove`、`mouseup`。
那么，在这三个事件中，分别需要做什么呢？
#### mousedown
鼠标按下，需要做：
- 获取鼠标做画布上的位置
- 存为一个点坐标（起始点）
- 以起始点建立一个路径
- 开启画布操作

#### mousemove
鼠标移动时，又要做那些准备呢？
- 判断是否开启画布操作，如果没开启我们当然不能鼠标在画布上移动就给绘制吧，因此先判断是否当前状态可绘制
- 获取鼠标做画布上的位置
- 上一个点到这一个点作连线
- 绘制出来
- 当前点存储，下一次用

可能此处有些迷，大概意思就是，鼠标不停的运动，在某一时刻鼠标的位置A（x,y），在下一时刻A则会变成上一个位置，我们的思路无非是不断的绘制出鼠标上一个时刻到当前时刻的路径而已。一会儿上代码可能就好理解一点。

#### mouseup
鼠标抬起，事件结束：
- closePath（） // 停止绘制
- 关闭画布操作的开关

**好了，其实就是这三个事件，理清楚之后去代码实现就简单得多了。附上代码一份。**

### 3. 代码
**CSS略，如初始化即可，不是重点。**
```html
<div class="boardBox" ref="boardBox">
    <canvas ref="board"
            @mousedown="pcStart"
            @mousemove="pcMove"
            @mouseup="pcEnd">
    </canvas>
</div>
```

```js
data() {
    return {
        ctx: null,
        point: {
            x: 0,
            y: 0
        },
        moving: false   // 是否正在绘制中且移动
    };
},
mounted() {
    let board = this.$refs.board;   // 获取DOM
    board.width = this.$refs.boardBox.offsetWidth;  // 设置画布宽
    board.height = this.$refs.boardBox.offsetHeight;    // 设置画布高
    this.ctx = board.getContext('2d');   // 二维绘图
    this.ctx.strokeStyle = '#ff0000';   // 颜色
    this.ctx.lineWidth = 5;  // 线条宽度
},
methods: {
    // 鼠标按下(开始)
    pcStart (e) {
        let x = e.offsetX, y = e.offsetY;   // 获取鼠标在画板（canvas）的坐标
        this.point.x = x;
        this.point.y = y;
        this.ctx.beginPath();
        this.moving = true;
    },
    // 鼠标移动（移动中...）
    pcMove (e) {
        if(this.moving) {
            let x = e.offsetX, y = e.offsetY;   // 获取鼠标在画板（canvas）的坐标
            this.ctx.moveTo(this.point.x, this.point.y);    // 把路径移动到画布中的指定点，不创建线条(起始点)
            this.ctx.lineTo(x, y);  // 添加一个新点，然后创建从该点到画布中最后指定点的线条，不创建线条
            this.ctx.stroke();  // 绘制
            this.point.x = x, this.point.y = y;   // 重置点坐标为上一个坐标
        }
    },
    // 鼠标松开（结束）
    pcEnd () {
        if(this.moving) {
            this.ctx.closePath();   // 停止绘制
            this.moving = false;    // 关闭绘制开关
        }
    },
},
```

## 思考
&emsp;&emsp;PC端出来了，而现在其实还是移动端偏多，并且这只是一个问题，是否还有延伸呢？
- 移动端是否可以如法炮制呢？
- 出错了，怎么重新绘制呢？
- 绘制完成后，怎么保存呢？





