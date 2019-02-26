---
title: vue中axios跨域问题
type: categories
copyright: true
date: 2019-02-24 19:08:57
tags: [前端,vue]
categories: 前端笔记
description: 
---
#### 前言
兴冲冲的开始用vue封装组件、自定义指令之后，开始实战。诶，等等，貌似少了……交互？
大致研究了一下axios，但是存在以下两个问题：
<!--more-->
- 跨域问题
- 数据格式问题
跨域信息示例：
```
Failed to load http://localhost:8080/login:
Response to preflight request doesn't pass access control check:
No 'Access-Control-Allow-Origin' header is present on the requested resource. 
Origin 'http://localhost:8080' is therefore not allowed access.
```
---

所以针对该问题给出一套解决方案（以post请求为例）：
#### 1.跨域处理
`cinfig/index.js`中对`proxyTable`做如下配置：
```
 proxyTable: {
      '/api': {
        target: 'https://0.0.0.0:8080/login',      //设置url公用部分，记得加http/https和端口号
        changeOrigin: true,
        pathRewrite: {
          '^/api': '/'      //这里理解成用‘/api’代替target里面的地址，后面组件中我们掉接口时直接用api代替 比如我要调用'http://40.00.100.100:3002/user/add'，直接写‘/api/user/add’即可
        }
      }
    },
```
`main.js`中引入axios并作为Vue属性使用
```
import axios from 'axios'

Vue.prototype.HOST = '/api'
```
在调取数据组件中，axios配置如下（可以自行封装）：
```
 getData () {
            this.$axios({
              method: 'post',     //请求方式   一般为get\post  此处以post为例
              url: this.HOST + '/login',   //url
              data: {       //数据
                loginId: '123456',
                password: '123456'
              },
            }).then(function (res) {
              console.log(res);
              debugger;
            }.bind(this)).catch(function (err) {      //bind  绑定当前作用域
              console.log(err);
              debugger;
            })
          }
```
ok，保存，然后
`npm run dev`
咦？发生了什么？没跨域啊？数据发过去了啊？为什么服务器给我抛异常==、
```
{
      code:100,
      data: {},
      msg: "服务器异常"
 }
```
检查发现错误原因：数据发送时没有走formdata，接下来处理该问题。
![错误示例.jpg](https://upload-images.jianshu.io/upload_images/2502265-6ade903d14e80280.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 '错误数据格式')


---
#### 2.数据格式修正
formdata形式提交数据
处理方法则是在刚才配置的`getData()`中的`this.$axios()`加上一段格式化代码配置即可，代码如下：
```
//formdata
 transformRequest: [function (data) {
     let ret = '';
     for (let it in data) {
         ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&';
      }
      return ret
  }],
//头信息
  headers: {
     'Content-Type': 'application/x-www-form-urlencoded'
  }
```
### 注：说明
步骤2 `formdata`形式提交数据，可直接解决跨域问题，不用执行步骤1
完整`axios`的`post`配置如下：
```
getData () {
            this.$axios({
              method: 'post',     //请求方式   一般为get\post  此处以post为例
              url: 'http://localhost:8080/login',
              data: {       //数据
                loginId: '123456',
                password: '123456'
              },
              transformRequest: [function (data) {
                let ret = '';
                for (let it in data) {
                  ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&';
                }
                return ret
              }],
              headers: {
                'Content-Type': 'application/x-www-form-urlencoded'
              }
            }).then(function (res) {
              console.log(res);
              debugger;
            }.bind(this)).catch(function (err) {
              console.log(err);
              debugger;
            })
}
```
继续测试：保存之后`npm run dev`![格式化成功.jpg](https://upload-images.jianshu.io/upload_images/2502265-ad7bfd5a0a0c5411.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'FormData数据格式')
完美~~，接下来看返回数据：
![成功状态.png](https://upload-images.jianshu.io/upload_images/2502265-3147d7c476c75f1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 '成功')

---
ok，解决方案到此结束，希望对大家有所帮助。继续快乐的采坑吧各位~








