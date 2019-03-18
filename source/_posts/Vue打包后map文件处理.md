---
title: Vue打包后map文件处理
type: categories
copyright: true
date: 2019-02-22 11:44:51
tags: [前端,vue,打包]
categories: 前端笔记
#description: Vue打包后JS文件夹中出现一些map文件的解决方法
---
#### 前言
最近踩坑vue，打包之后js中，会自动生成一些map文件，无形之中就增大了dist包的体积，可能会增加服务器压力。那我们怎么把它去掉呢？
<!--more-->
**********
#### 1.找到`productionSourceMap`
在项目下进入目录：`项目包/config/index.js`找到`productionSourceMap`
```
     /**
     * Source Maps
     */
    productionSourceMap: true,
    // https://webpack.js.org/configuration/devtool/#production
    devtool: '#source-map',
```

#### 2.改为`false`
将`productionSourceMap`的`true`改为`false`即可
```
    productionSourceMap: false,
    // https://webpack.js.org/configuration/devtool/#production
    devtool: '#source-map',
```

####  3.打生产包
打包:`npm run build `
```
Hash: 450e46e5a578d8ed5832
Version: webpack 3.12.0
Time: 19211ms
                                              Asset       Size  Chunks                    Chunk Names
                static/js/2.f45baf9afd59da355a2c.js    17.9 kB       2  [emitted]
                 static/img/healthIcon2.0a76940.png    21.7 kB          [emitted]
                 static/img/healthIcon1.7f9e278.png      21 kB          [emitted]
                 static/img/healthIcon3.a2530d6.png    20.3 kB          [emitted]
                static/js/0.2623d3f24b6cb900e249.js     182 kB       0  [emitted]
                static/js/1.68a7b37dd5366d3385bc.js    62.6 kB       1  [emitted]
                 static/img/healthIcon4.42cd92c.png    18.9 kB          [emitted]
                static/js/3.dd658b712540b545b1a8.js    6.57 kB       3  [emitted]
           static/js/vendor.ed30b6e3d0cfb8529feb.js     962 kB       4  [emitted]  [big]  vendor
              static/js/app.ab727f325a694968cc20.js    1.22 kB       5  [emitted]         app
         static/js/manifest.bad9ce278a16a20c8817.js    1.48 kB       6  [emitted]         manifest
static/css/app.e93cb55eadaef6e12f4bccfcc6e2e3cc.css    35.6 kB       5  [emitted]         app
                                         index.html  555 bytes          [emitted]

  Build complete.

  Tip: built files are meant to be served over an HTTP server.
  Opening index.html over file:// won't work.

```

#### 4.解决
打包成功，看`项目/dist/static/js/xxxx.js`已经没有各种```.map```文件，问题解决。

#### 【注】` .map`文件的作用：
项目打包后，代码都是经过压缩加密的，如果运行时报错，输出的错误信息无法准确得知是哪里的代码报错。**有了map就可以像未加密的代码一样，准确的输出是哪一行哪一列有错。**



