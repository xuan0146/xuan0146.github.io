---
title: vue/cli3项目运行报错sockjs-node/info解决方案
type: categories
copyright: true
date: 2019-08-13 17:07:31
tags: [vue-cli3, webpack, vue]
categories: 前端笔记
description:
---
### bug复现
&emsp;&emsp;继上次将vue项目脚手架工具从vue-cli2.x升级到vue-cli3.x后，除了环境、配置问题外，在运行时出现了一些个新的问题。
&emsp;&emsp;启动项目后，在内网非本机运行项目时，控制台报错如下：
```
get http://localhost:8080/sockjs-node/info?t=1462183700002 net::ERR_CONNECTION_REFUSED
[WDS] Disconnected!
get http://localhost:8080/sockjs-node/info?t=1462183700002 net::ERR_CONNECTION_REFUSED
[WDS] Disconnected!
get http://localhost:8080/sockjs-node/info?t=1462183700002 net::ERR_CONNECTION_REFUSED
[WDS] Disconnected!
...
```

<!--more-->

### webpack.config.js的目前配置如下：
```
const path = require('path');

const resolve = dir => path.resolve(__dirname, dir);

const env = process.env.NODE_ENV;


module.exports = {
    // 基本路径配置，解决打包路径问题
    publicPath: './',


    // 配置less
    css: {
        loaderOptions: {
            css: {},
            postcss: {
                plugins: [
                    require('postcss-px2rem')({
                        remUnit: 37.5
                    })
                ]
            }
        }
    },

    configureWebpack: (config) => {
        // 如果不是开发环境
        if (env !== 'development') {

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
        // 添加别名
        config.resolve.alias
            .set('@', resolve('src'))
            .set('assets', resolve('src/assets'))
            .set('static', resolve('src/static'));
    },
}
```

### 报错分析
&emsp;&emsp;首先，sockjs-node是何方神圣？不难查出，sockjs-node是一个JavaScript库，提供跨浏览器JavaScript的API，创建了一个低延迟、全双工的浏览器和web服务器之间通信通道。那么在各端表现为：
- 服务端：sockjs-node（https://github.com/sockjs/sockjs-node）
- 客户端：sockjs-clien（https://github.com/sockjs/sockjs-client）

&emsp;&emsp;vue-cli3.x的启动方式是`npm run serve`，我们没有用到该sockjs-node功能，但启动时会默认连续发请求调用本地接口。因此我们要做的就是移除掉它。那么如何移除？

### 解决方案
&emsp;&emsp;分享两种方案
#### 1. 注释法
&emsp;&emsp;顾名思义，找到依赖包中的源码，将其注释：
1. 进入路径`/node_modules/sockjs-client/dist/sockjs.js`
2. 代码1605行注释掉：
    ```js
    try {
        // self.xhr.send(payload);  //本行注释
    } catch (e) {
        self.emit('finish', 0, '');
        self._cleanup(false);
    }
    ```
3. 重启项目

#### 2. 配置vue.config
&emsp;&emsp;vue.config.js中的module.xports中添加如下，然后重启：
```js
devServer: {
    proxy: 'http://localhost:8080',
    public: '192.168.xxx.xxx:8080'  // 本地ip
}
```