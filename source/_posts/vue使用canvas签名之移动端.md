---
title: vue使用canvas签名之移动端
type: categories
copyright: true
date: 2020-01-14 15:04:37
tags: [js, canvas, vue]
categories:
description:
---
## 效果
![canvas移动端签名](/images/posts/canvas画板m.gif 'canvas移动端签名')

<!--more-->

## 需求
&emsp;&emsp;在一些项目业务中，经常会使用到画板，让用户自己去写/画一些东西做标示，比如说在线签电子合约、签名等，如果不用插件，那么如何使用h5的canvas画布来实现这一需求呢？ ***【本篇只讨论移动端，PC端请看上篇】***

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
    margin: 30px auto;
    width: 90vw;
    height: 25vh;
    background: #f9f9f9;
    canvas{
        border: 1px solid #b3b3b3;
    }
}
```
#### 画布初始化
```js
let board = this.$refs.board;   // 获取DOM
board.width = this.$refs.boardBox.offsetWidth;  // 设置画布宽
board.height = this.$refs.boardBox.offsetHeight;    // 设置画布高
this.ctx = board.getContext('2d');   // 二维绘图
this.ctx.strokeStyle = '#000';   // 颜色
this.ctx.lineWidth = 3;  // 线条宽度
```

### 2. 逻辑分析
由于本篇只讨论移动端端，因此无非是在画布上监听三个触摸事件：`touchstart`、`touchmove`、`touchend`。
那么，在这三个事件中，分别需要做什么呢？
#### touchstart
开始滑动按下，需要做：
- 获取触摸点做画布上的位置
- 存为一个点坐标（起始点）
- 以起始点建立一个路径
- 开启画布操作

#### touchmove
触摸滑动时，又要做那些准备呢？
- 判断是否开启画布操作，如果没开启就禁止绘制，因此先判断是否当前状态可绘制
- 获取触摸点做画布上的位置
- 上一个点到这一个点作连线
- 绘制出来
- 当前点存储，下一次用

#### touchend
滑动结束，事件结束：
- closePath（） // 停止绘制
- 关闭画布操作的开关

**好了，其实就是这三个事件，理清楚之后去代码实现就简单得多了。附上代码一份。**

### 3. 代码
**CSS略，如初始化即可，不是重点。**
```html
<div class="boardBox" ref="boardBox">
    <canvas ref="board"
            @touchstart="mStart"
            @touchmove="mMove"
            @touchend="mEnd">
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
    this.ctx.strokeStyle = '#000';   // 颜色
    this.ctx.lineWidth = 3;  // 线条宽度
},
methods: {
    // 触摸(开始)
    mStart (e) {
        console.log(e);
        let x = e.touches[0].clientX - e.target.offsetLeft,
            y = e.touches[0].clientY - e.target.offsetTop;   // 获取触摸点在画板（canvas）的坐标
        this.point.x = x;
        this.point.y = y;
        this.ctx.beginPath();
        this.moving = true;
    },
    // 滑动中...
    mMove (e) {
        if(this.moving) {
            let x = e.touches[0].clientX - e.target.offsetLeft,
                y = e.touches[0].clientY - e.target.offsetTop;   // 获取触摸点在画板（canvas）的坐标
            this.ctx.moveTo(this.point.x, this.point.y);    // 把路径移动到画布中的指定点，不创建线条(起始点)
            this.ctx.lineTo(x, y);  // 添加一个新点，然后创建从该点到画布中最后指定点的线条，不创建线条
            this.ctx.stroke();  // 绘制
            this.point.x = x, this.point.y = y;   // 重置点坐标为上一个坐标
        }
    },
    // 滑动结束
    mEnd () {
        if(this.moving) {
            this.ctx.closePath();   // 停止绘制
            this.moving = false;    // 关闭绘制开关
        }
    },
},
```

## 思考
- 上一篇，在PC端完成绘制，本篇如法炮制，在移动端也顺利完成，相比pc端只是稍微的修改了一下获取坐标点的算法而已。那么PC端和移动端如何并存呢？
- 出错了，怎么重新绘制呢？
- 绘制完成后，怎么保存呢？
