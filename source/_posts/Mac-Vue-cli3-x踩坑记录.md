---
title: Mac Vue-cli3.x踩坑记录
type: categories
copyright: true
date: 2019-07-10 15:27:09
tags: [vue, vue-cli]
categories: 前端笔记
description:
---
### 前沿
&emsp;&emsp;随vue技术逐渐成熟，开发者们热爱的脚手架vue-cli也一步步升级优化。在体验度不断上升的过程中，也必不可少的要踩一些坑。
&emsp;&emsp;本篇主要记录下在vue-cli2.x升级到3.x以及用Mac系统的踩坑记录。

<!--more-->
### 一、安装问题
#### 1. 升级node
此过程只需要升级新版本node即可。
#### 2. 安装vue-cli 3
执行`npm install -g @vue/cli`
#### 3. 创建新项目
`sudo vue create andin`
>备注：在Mac系统执行过程中，会产生系列报错问题，一般情况下时权限不足不可创建。
#### 4. 新建文件失败
在项目内，无论新建什么文件，都会报错提示无法新建。【无此问题的可忽略此步。】解决方案是打开可读写的权限。打开终端，cd到该项目目录下，执行如下：
`sudo chown -R username /Users/******`
1. username 代表Mac的用户名
2. /Users/****** 代表项目路径

### 二、配置问题
#### 1. 安装系列依赖
- axios：交互
- qs：数据格式
- echarts：图表，数据可视化
- px2rem：自适应解决方案
- ...

#### 2. vue.config.js 配置
配置vue.config.js，解决系列问题。如基本路径、打生产包去除.map文件、代码压缩等。
```
const path = require('path');
const resolve = dir => path.resolve(__dirname, dir);

// 去除console
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

// 压缩css、js
const CompressionWebpackPlugin = require('compression-webpack-plugin');
// 要压缩的文件
const productionGzipExtensions = ['js', 'css'];


const env = process.env.NODE_ENV;


module.exports = {
    // 基本路径配置，解决打包路径问题
    publicPath: './',

    configureWebpack: (config) => {
        // 如果不是开发环境
        if (env !== 'development') {
            // 代码压缩
            config.plugins.push(new CompressionWebpackPlugin({
                algorithm: 'gzip',
                test: new RegExp(`\\.(${productionGzipExtensions.join('|')})$`),
                threshold: 10240,
                minRatio: 0.8,
            }));

            // 打包后清除断点和控制台打印
            config.plugins.push(
                new UglifyJsPlugin({
                    uglifyOptions: {
                        compress: {
                            drop_debugger: true, // 注释debugger
                            drop_console: true, // 注释console
                            pure_funcs:['console.log'] // 移除console
                        },
                    },
                    sourceMap: false,   // 去除打包后生成的.map文件
                    parallel: true,
                }),
            );
        }

        // 路径
        config.resolve = {
            extensions: ['.js', '.vue', '.json'],
            alias: {
                'vue$': 'vue/dist/vue.esm.js',
                '@': resolve('src'),
            }
        }
    },


    chainWebpack: config => {
        // 字体自适应
        config.module
            .rule('css')
            .test(/\.css$/)
            .oneOf('vue')
            .resourceQuery(/\?vue/)
            .use('px2rem')
            .loader('px2rem-loader')
            .options({
                remUnit: 37.5
            });
        // 指定入口  es6转es5
        // config.entry.app = ['babel-polyfill', './src/main.js'];

        // 添加别名
        config.resolve.alias
            .set('@', resolve('src'))
            .set('assets', resolve('src/assets'))
            .set('static', resolve('src/static'));
    },
}
```
#### 3. runtime进行时
如果是项目脚手架升级，那么其中需要格外注意的一点是runtime进行时会报错。报错信息如下：
```
You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.
```
解决方案现给出三种：
1. main.js修改
```
// 原
new Vue({
  el: '#app',
  router,
  store,
  components: { App },
  template: '<App/>'
})

// 修改为如下：
new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```

2. config配置
```
config.resolve = {
    extensions: ['.js', '.vue', '.json'],
    alias: {
        'vue$': 'vue/dist/vue.esm.js',
        '@': resolve('src'),
    }
}
```

3. 引入路径修改
`import Vue from 'vue'`修改为`import Vue from 'vue/dist/vue.esm.js'`
同理，在router配置的过程中，也改成以上路径即可。






