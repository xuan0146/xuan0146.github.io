---
title: axios进阶之路——封装篇
type: categories
copyright: true
date: 2020-05-26 17:42:59
tags: [vue, axios, 拦截器]
categories: 前端笔记
description: 本课题包含三篇：基础篇；拦截器篇；封装篇。本篇为封装篇，主题为为axios进行封装使用。
---

## 一、 封装前准备
在前两篇讲过了axios的安装、基本配置、拦截器使用，如下：
- {% post_link axios进阶之路——基础篇 axios进阶之路——基础篇 %}
- {% post_link axios进阶之路——拦截器篇 axios进阶之路——拦截器篇 %}

那么本篇的封装也是基于前两篇的基础之上进行的。

## 二、 开拔
### 1. 新建文件夹
在`src`目录下新建文件夹，用于存放所有请求列表和请求封装的js。如
```
├── src	
│  ├── service	    // ++ 新建文件夹
│  │  ├── http.js	// ++ 用于封装请求
│  │  ├── apiList.js	// ++ 存放请求路径
│  ├── page	
│  │  ├── Home.vue	// 主页组件
│  ├── router	
│  │  ├── index.js	
│  ├── App.vue	
│  ├── main.js
```

### 2. 所有接口请求的统一管理
一般情况下，一个项目的所有请求都会由一个统一的基础路径加上不同的接口路径和接口名组成，并且大部分接口都会有统一的请求或者响应处理，例如返回不同code值，要做响应处理，如果把所有接口都单独处理可谓是资源的极大浪费，因此想办法统一管理。
在上一步中，已经新建了一个名为`service`的文件夹，包含两个js文件：
- **apiList.js**：存放请求路径
- **http.js**：封装请求

先看第一个：`apiList`，就是用来将所有接口统一管理的。
例如：
```js
// 定义请求列表对象API_LIST，其下每个请求(对象)包含请求方法与请求路径两项
const API_LIST = {
    // get请求
    getData1: {
        method: 'get',
        url: '/xx/getData1'
    },
    // post请求
    getData2: {
        method: 'post',
        url: '/xx/getData2'
    },
}
// 导出
export default API_LIST
```
一个项目，特殊接口的话可以单独处理，其余的的所有请求都可以放在这个文件中进行统一管理，之后项目变动找接口修改也会更好维护。

### 3. 请求封装
在请求接口列表处理得当之后，就该在请求实例上做文章了。一般需要处理两个问题：一是走请求，二是拦截器处理。
*尽可能加上注释的提供一份参考*
```js
// http.js

// 引入axios
import axios from 'axios'
// 引入刚才封装的请求接口列表
import api_list from './api/api_list'
// 吐司框组件，配合拦截器使用，可自行处理（不要也行）
import Toast from 'xxx'
// 路由设置，配合拦截器使用，可不要
import Router from '../router/index'

// baseUrl判断
let baseURL;
// 如果是开发环境，用本地baseURL，如果是生产环境用线上baseURL
if(process.env.NODE_ENV === 'development') {
    baseURL = 'http://{本地地址}/xxx';
} else if(process.env.NODE_ENV === 'production') {
    baseURL = 'https://{线上地址}/xxx';
}

// 实例
let instance = axios.create({
    baseURL: baseURL,   // url判断
    timeout: 15000  // 超时时间设置
})

const Http = {};    // 用来包裹请求的容器

// 遍历接口列表
for(let key in api_list) {
    let api = api_list[key];    // 每一项api都包含method, url
    
    Http[key] = async function(
        params,    // 请求参数
        isFormData = true,    // 是否是form-data请求
        config = {}    // 配置参数
    ) {
        let newParams = {}; // 参数容器

        // content-type 参数转换
        // 如果需要form-data请求，则格式化为form-data，一般用于post请求
        if(params && isFormData) {
            newParams = new FormData();
            for(let key in params) {
                newParams.append(key, params[key])
            }
        } else {
            // 一般用于get请求
            newParams = params;
        }

        // 返回值容器
        let res = {};

        // 不同请求判断
        if(api.method === 'post') {
            // post请求处理
            try{
                res = await instance[api.method](api.url, newParams, config);
            } catch(err) {
                res = err;
            }
        } else if(api.method === 'get') {
            // get请求处理
            config.params = newParams;
            try{
                res = await instance[api.method](api.url, config);
            } catch(err) {
                res = err;
            }
        }

        // 返回返回值
        return res;
    }
}

// 请求拦截器
instance.interceptors.request.use(req => {
    // 吐司：loading框加载，不要也行
    Toast.loading({
        message: '加载中...',
        forbidClick: true,
        loadingType: 'spinner'
    });
    return req;
}, err => {
    // 请求错误
    Toast.clear();  // 清除吐司
    Toast.fail('请求失败，请稍后重试');
})

// 响应拦截器
instance.interceptors.response.use(res => {
    Toast.clear();  // 清除吐司
    // 不同的返回值跳相应路由即可，如201跳登陆，202跳加载失败等，根据需求自行处理
    if(res.data.code === 201) {
        Router.push('/login')
    } else if(res.data.code === 202) {
        Router.push('/loadfail')
    } else {
        // ...
    }
    
    // 返回返回值，返回值为res.data
    return res.data
}, err => {
    Toast.clear();  // 清除吐司
    Toast.fail('无响应，请稍后重试');
})

// 导出请求
export default Http
```

### 4. 使用
封装完成，盘他！
#### 4.1 挂载到vue实例上
```js
// main.js
import Vue from 'vue'
// 引入
import Http from './service/http'

// Vue 实例挂载
Vue.prototype.$Http = Http

// ...

```

#### 4.2 测试使用
在某个vue文件中：
```js
export default {
    data() {
        return {
            data: {}
        }
    },
    methods: {
        // 获取get请求数据，注意async和await配对使用
        async getData1() {
            let res = await _this.$Http.getData1({
                id: 'xxx',
                token: 'xxx'
            }, false);  // false代表不是formdata格式

            if(res.code === 200) {
                alert('get数据返回成功');
            }
        },

        // 获取post请求数据，注意async和await配对使用
        // 此处是formdata格式，第二个参数可写true,因为默认为true，因此可以不写
        async getData2() {
            let res = await this.$Http.getData2({
                id: 'xxx',
                token: 'xxx'
            });

            if(res.code === 200) {
                alert('post数据返回成功');
            }
        },
    },
    mounted() {
        this.getData1();
        this.getData2();
    },
}
```

经测试，普通格式（非文件等）请求有效，简单封装完成~

## 三、 完结撒花~
一般接口的请求封装完毕，用到了基于axios的一些知识，记录至此。在项目中灵活运用，相比于一个个接口单独处理，可谓是大幅度的提升哟~
另外，基于此，也可配合proxy（在vue.config.js中配置）处理跨域问题，就不在此处赘述了。

以上。
