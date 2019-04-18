---
title: axios全局拦截器配置
type: categories
copyright: true
date: 2019-04-17 17:03:37
tags:
categories:
description:
---
#### 关于请求拦截器
&emsp;&emsp;项目中，我们经常遇到请求后台接口时要做后天返回的`code`码验证判断。code码通常是一个特定的数值，比如一般返回`200`作为正常请求，返回其他作为数据异常或者请求异常等。如果接口量足够大，那么每一个接口都需要做正常/异常处理。为满足统一管理，那么在全局请求拦截器便应劫而出。

<!--more-->

#### 作用
- 统一管理
- 路由请求拦截 => request
- 路由响应拦截 => response

#### 实例
##### 1.安装axios及qs
```
npm install axios --save-dev    //安装axios

npm install qs --save-dev     //安装qs
```
##### 2.新建一个js文件
```
├── project	
│  ├── build	
│  ├── config
│  ├── node_modules	
│  ├── src	
│  │  ├── assets		
│  │  │  ├── request.js	// 我在这里新建了一个js文件来配置拦截器
│  │  ├── components	
│  │  ├── router	
│  │  ├── App.vue	
│  │  ├── main.js	
│  ├── static	
│  ├── test	
│  ├── .babelrc	
│  ├── .editorconfig	
│  ├── .gitignore	
│  ├── .postcssrc.js	
│  ├── index.html	
│  ├── package-lock.json	
│  ├── package.json	
│  ├── README.md	
```
##### 3.js拦截器配置如下：
```
import axios from 'axios'	//引入axios
import qs from 'qs'	//引入qs qs可将数据转换为FormData格式

// axios默认配置
axios.defaults.timeout = 10000;   // 超时时间
axios.defaults.baseURL = 'https://www.demo.com';  // 公用地址

// 路由请求拦截
// http request 拦截器
axios.interceptors.request.use((config) => {
    // 数据格式化
    config.data = qs.stringify(config.data);
    // 给请求头加键值对
    config.headers['Content-Type'] = 'application/x-www-form-urlencoded';
    return config;
}, (error ) => {	// 错误信息
    return Promise.reject(error.response);
});

// 路由响应拦截
// http response 拦截器
axios.interceptors.response.use(
    (response) => {
        if (response.data.code !== 200) {
            // 异常请求要做的事
        } else {
            // 正常请求要做的事
        }
    },(error) => {
	// 返回接口返回的错误信息
        return Promise.reject(error.response)   
    }
);


export default axios;

```
##### 4.main.js配置
```
// 引入该配置文件
import axios from './assets/axiosRequest'

// 注册到全局
Vue.prototype.$axios = axios;
```
##### 5.使用
```
// 要发送的数据
let params = {
	name: 'AAA',
	number: '10100110',
	age: '18'
};

// post请求为例  url不用写公共部分，直接写后面即可
this.$axios.post('/health/detail/basic',params
).then((res) => {
	debugger;
}).catch((error) => {
	debugger;
})
```

#### 总结
&emsp;&emsp;http全局拦截器配置完毕，不仅可在response中统一管理返回状态，而且可在request中动态添加request headers，在实际项目中有很大的运用空间。

