---
title: vue-cli自适应|flexible屏幕字体自适应布局及配置
type: categories
copyright: true
date: 2019-02-23 23:53:41
tags: [前端,vue]
categories: 前端笔记
description:
---
##### 前言：
很多前端小伙伴在写页面尤其是移动端页面的时候，要求页面布局以及字体大小随屏幕宽度变化而随之按比例自适应*【注：非响应式】*，那么，在vue-cli脚手架中应该如何去实现呢？
<!--more-->
******
##### 1.安装`flexible`库 
```
//在项目库下运行：
npm i lib-flexible --save-dev 
```

##### 2.引入
项目入口文件`main.js`中引入`lib-flexible`:
```
//main.js中：
import 'lib-flexible'
```

##### 3.配置meta
`在项目`index.html`的`head`中配置`meta`：
```
<meta name="viewport"
            content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
```

##### 4.安装`px2rem-loader`
```
//在项目库下运行
npm install px2rem-loader --save-dev
```

##### 5.配置`px2rem`编译器
在`build/utils.js`中，将`px2rem-loader`添加到`cssLoaders`中：
```
//自适应字体配置
const px2remLoader = {
    loader: 'px2rem-loader',
    options: {
        remUnit: 37.5   //设计稿的宽度 除以 10，现阶段一般设计稿的宽度都为750px。如果基于iPhone5设计则为32.0(320 / 10 = 32)
    }
}

// generate loader string to be used with extract text plugin
  function generateLoaders (loader, loaderOptions) {
    const loaders = options.usePostCSS ? [cssLoader, postcssLoader, px2remLoader] : [cssLoader, px2remLoader]
  /**
  *其余的不用改
  */
  }
```

##### 6.启动项目
```
npm run dev
```
******
##### 后语：
启动项目之后会惊讶的发现已经完美解决了，即使在PC端也是适用的哦~
想要了解更多关于`px2rem`的想伙伴可以戳下方飞机票去官方看介绍：
点击飞机票：**[px2rem](https://www.npmjs.com/package/px2rem)**
