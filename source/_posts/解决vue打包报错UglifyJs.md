---
title: 解决vue打包报错UglifyJs
type: categories
copyright: true
date: 2019-12-06 12:13:29
tags: [vue, uglify报错]
categories: 前端笔记
description:
---

### 报错
```
ERROR in js/xxxxxx.js from UglifyJs
Unexpected token: name «Dom7», expected: punc «;» [js/chunk-3068
```

<!--more-->

### 产生
在vue配置中，由于各插件版本兼容性差异可能会引发此问题的产生。`UglifyJs`是一款可以解析、混淆、压缩JS的工具，此处的UglifyJs是配置在脚手架/webpack中，运行在node环境中的小插件。
关于UglifyJs：
>1. uglify-js@3具有简化的API和CLI，该API和CLI与不能向后兼容uglify-js@2。
>2. UglifyJS 2.x版本的文档可以在这里找到。
>3. glify-js 仅支持JavaScript（ECMAScript 5）。
>4. 要缩小ECMAScript 2015或更高版本，请使用Babel之类的工具进行翻译。

### 解决
#### 低版本：
- 原因： UglifyJs不支持ES6语法，因此报错。
- 解决方案： 项目根目录下创建文件`.babelrc`，配置如下
```json
{
    "pressets": ["es2015"]
}
```

#### 高版本：
- 原因1：UglifyJs版本过低
- 解决方案： 升级UglifyJs版本
```
npm install uglify-js -g
```

- 原因2: babel 转译问题
- 解决方案： 找到babel的配置项`transpileDependencies`，如下操作：
```js
// 旧
transpileDependencies: [
/* string or regex */
],

// 改为如下⬇️ 

// 新  
transpileDependencies: [
    'vue-particles',
    'animejs'
],
```

### 重新打包
`npm run build`，完毕。


### 附
vue-cli@3.x `vue.config.js`配置混淆压缩代js及去除console(缺少的插件先安装上)
```js
// 去除console
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

// 压缩css、js
const CompressionWebpackPlugin = require('compression-webpack-plugin');

// 要压缩的文件
const productionGzipExtensions = ['js', 'css'];


module.exports = {
    // something else codes

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
    },
}
```


------
希望能够解决您的问题。

