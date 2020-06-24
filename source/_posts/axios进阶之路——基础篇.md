---
title: axios进阶之路——基础篇
type: categories
copyright: true
date: 2020-05-12 09:59:21
tags: [vue, axios, 拦截器]
categories: 前端笔记
description: 本课题包含三篇：基础篇；拦截器篇；封装篇。本篇为基础篇，主题为axios基础介绍及安装使用。
---

## 一、 关于Axios
### 1. Axios是什么
- Axios是一个基于 promise 的 HTTP 库
- 可以用于浏览器和 node.js 
- 类似于 Ajax，进行前后端交互使用

### 2. Axios 特性
- 支持 Promise API 
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换JSON数据
- 客户端支持防御 XSRF

## 二、 安装使用
### 1. 安装
两种方式：（二选一）
1. npm
2. yarn

```js
// 使用npm：
$ npm install axios
```

```js
// 或者使用yarn：
$ yarn add axios
```

### 2. 使用
**基础使用示例**
```js
// 引入axios
import axios from 'axios'

axios.get('url').then(res => {
    // 请求成功要做的事
}).catch(err => {
    // 请求失败要做的事
})
```

## 三、 基础介绍及使用

### 1. axios常用请求方法
- get
- post
- put
- patch
- delete

### 2. 区别
|方法|用途|备注|
|--|--|:--:|
|**get**|获取数据|/|
|**post**|提交数据|表单提交、文件上传等|
|put|更新数据|所有数据推送到后端|
|patch|更新数据|只将修改的数据推送到后端|
|delete|删除数据|/|

其中`get`,`post`目前前后端交互中最为常用

*注：请求方法一般由后端定义，实际项目使用参照后端文档规则*

### 3. 常用示例（GET、POST）
#### GET
```js
// get
axios.get('/data.json',{
    // 注 此处参数写入params中
    params: {
        id: 'zxm'
    }
}).then(res => {
    console.log(res);
})
```

#### POST
```js
// post：参数直接跟在url后面即可
axios.post('xxxxxxxxx', {
        xxx: 'xxxx',
        xxxx: 'xxxx'
    }
}).then(res => {
    console.log(res);
})
```

### 4. 并发请求
**并发请求：**
 同时进行多个请求，并统一处理返回值。两个步骤：
1. `axios.all([]).then()`
2. `axios.spread()`

**举例：**
```js
axios.all([
    axios.get('url1'),
    axios.get('url2')
]).then(
    axios.spread((res1, res2) => {
        console.log(res1, res2);
    })
)
```

## ------
基础篇到此结束，下篇记录`axios简单实例`，`请求拦截器`，`响应拦截器`。


