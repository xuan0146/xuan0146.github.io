---
title: axios进阶之路——拦截器篇
type: categories
copyright: true
date: 2020-05-13 17:02:30
tags: [vue, axios, 拦截器]
categories: 前端笔记
description: 本课题包含三篇：基础篇；拦截器篇；封装篇。本篇为拦截器篇，主题为axios的请求拦截器、响应拦截器配置。
---

## 一、 拦截器介绍
一般在使用axios时，会用到拦截器的功能，一般分为两种：`请求拦截器`、`响应拦截器`。
- **请求拦截器**
在请求发送前进行必要操作处理，例如添加统一cookie、请求体加验证、设置请求头等，相当于是对每个接口里相同操作的一个封装；
- **响应拦截器**
同理，响应拦截器也是如此功能，只是在请求得到响应之后，对响应体的一些处理，通常是数据统一处理等，也常来判断登录失效等。


## 二、 Axios实例
### **准备工作：**
axios安装： `yarn add axios`
### **实例创建：**
新建js文件
```js
// 引入axios
import axios from 'axios'

// 实例
let instance = axios.create({
    baseURL: 'xxxxxxxxxx',
    timeout: 15000  // 毫秒
})
```
### **baseURL设置：**
```js
...

let baseURL;
if(process.env.NODE_ENV === 'development') {
    baseURL = 'xxx本地环境xxx';
} else if(process.env.NODE_ENV === 'production') {
    baseURL = 'xxx生产环境xxx';
}

// 实例
let instance = axios.create({
    baseURL: baseURL,
    ...
})
```

### **修改实例的3种配置**

1. **全局配置**
```js
// 局限性比较大
axios.defaults.timeout = 1000;
axios.defaults.baseURL = 'xxxxx';
```

2. **实例配置**
```js
let instance = axios.create({
    baseURL: 'xxxxx',
    timeout: 1000,  // 超时，401
})
// 创建完想要修改：
instance.defaults.timeout = 3000
```
3. **请求配置**
```js
instance.get('/xxx',{
    timeout: 5000
})
```
**优先级如下：**
`请求配置` > `实例配置` > `全局配置`

## 三、 拦截器配置
首先了解两个方法：
```js
// 请求拦截器
instance.interceptors.request.use(req=>{}, err=>{});
// 响应拦截器
instance.interceptors.reponse.use(req=>{}, err=>{});
```
从上可以看出，instance依然是第二步中创建的实例，然后对齐进行拦截，请求用`request`，响应用`reponse`，二者都有两个配置项，一个是成功配置，一个是error配置。
接下来简单举例说明。
### 请求拦截器
```js
// use(两个参数)
axios.interceptors.request.use(req => {
    // 在发送请求前要做的事儿
    ...
    return req
}, err => {
    // 在请求错误时要做的事儿
    ...
    // 该返回的数据则是axios.catch(err)中接收的数据
    return Promise.reject(err)
})
```

### 响应拦截器
```js
// use(两个参数)
axios.interceptors.reponse.use(res => {
    // 请求成功对响应数据做处理
    ...
    // 该返回的数据则是axios.then(res)中接收的数据
    return res
}, err => {
    // 在请求错误时要做的事儿
    ...
    // 该返回的数据则是axios.catch(err)中接收的数据
    return Promise.reject(err)
})
```

### 常见错误码处理(error)
**错误处理**，请求错误时进行的处理。
```js
axios.get().then().catch(err => {
    // 错误处理
})
```
但实际开发过程中，一般在请求/响应拦截器中统一做错误处理，有特殊接口的话做单独的catch错误处理。

#### 1. 请求错误码处理
- 404： not found
- 401： 请求超时
```js
axios.interceptors.request.use(req, err => {
    // 此处做统一处理
})
```

#### 2. 响应错误码处理
- 500： 系统错误
- 502： 系统重启
```js
axios.interceptors.reponse.use(res, err => {
    // 此处做统一处理
})
```

## ------
以上。
下一篇记录整个请求流程，以及请求的封装。

