---
title: 前端技巧——css篇
type: categories
copyright: true
date: 2019-07-23 11:58:00
tags: [CSS, 前端技巧]
categories: 前端笔记
description: 来一波干货——CSS篇
---

### CSS篇
-----

#### 去掉type=number的箭头
```css
  input::-webkit-outer-spin-button,input::-webkit-inner-spin-button{
      -webkit-appearance: none;
  }
  input[type="number"]{
      -moz-appearance: textfield;
  }
  input{
      -webkit-user-select: text !important;
      -webkit-tap-hightlight-color: rgba(0,0,0,0);
  }
```

#### 绝对居中
```css
/* 1 */
.father{
  position: absolute;
  top: 50%;
  left: 50%;
}
.son{
  transform: translate(-50%, -50%);
}

/* 2 */
.father{
  position: absolute;
  display: flex;
  justify-content;
  align-items: center;
}
```

#### css媒体查询
```css
/*自适应尺寸*/
@media screen and (max-width: 360px) {
    html {
        font-size: 10px;
    }
}

@media (min-width: 361px) and (max-width: 420px) {
    html {
        font-size: 11px;
    }
}

@media (min-width: 421px) and (max-width: 480px) {
    html {
        font-size: 12px;
    }
}

@media (min-width: 481px) and (max-width: 540px) {
    html {
        font-size: 13px;
    }
}

@media (min-width: 541px) and (max-width: 640px) {
    html {
        font-size: 14px;
    }
}

@media (min-width: 641px) and (max-width: 750px) {
    html {
        font-size: 15px;
    }
}

@media screen and (min-width: 751px) {
    html {
        font-size: 20px;
    }
}
```

#### iOS去除点击阴影
```css
-webkit-tap-highlight-color: rgba(0,0,0,0)
```

#### css滚动条样式
```css
/*1*/
.progress1 ul{height:50px;overflow:auto;}
.progress1 ul::-webkit-scrollbar {/*滚动条整体样式*/
            width: 4px; /*高宽分别对应横竖滚动条的尺寸*/
            height:4px;
}
.progress1 ul::-webkit-scrollbar-thumb {/*滚动条里面小方块*/
    border-radius: 5px;
    -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.2);
    background:lightblue;
}
.progress1 ul::-webkit-scrollbar-track {/*滚动条里面轨道*/
   -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.2);
   border-radius: 0;
   background: rgba(0,0,0,0.1);
}

/*2*/
.modalBox .modalBody::-webkit-scrollbar {/*滚动条整体样式*/
    width: 6px; /*高宽分别对应横竖滚动条的尺寸*/
    height:15px;
}
.modalBox .modalBody::-webkit-scrollbar-thumb {/*滚动条里面小方块*/
    background:rgba(89,126,247,0.2);
    border-radius: 2px;
}
.modalBox .modalBody::-webkit-scrollbar-track {/*滚动条里面轨道*/
    border-radius: 0;
    background:#fff;
}
```


