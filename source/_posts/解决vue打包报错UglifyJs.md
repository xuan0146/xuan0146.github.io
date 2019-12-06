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
- 原因：UglifyJs版本过低
- 解决方案： 升级UglifyJs版本
    ```
    npm install uglify-js -g
    ```

### 重新打包
`npm run build`，完毕。


------
希望能够解决您的问题。

