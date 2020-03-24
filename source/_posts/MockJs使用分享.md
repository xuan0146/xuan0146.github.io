---
title: MockJs使用分享
type: categories
copyright: true
date: 2020-03-24 15:38:46
tags: [tools, js, mockjs]
categories: 前端笔记
description:
---

## 前言
&emsp;&emsp;在前后端分离的时代，项目开发测试中我们常常因为前后端完成模块功能时间不一致而导致开发效率的降低，尤其是需要接口对接之时。鉴于此，`MockJs`应时而生，对此痛点予以重击。
&emsp;本篇案例则属于一个简单的示范案例，在脱离后端（在没与后端联调前）怎么自己模拟数据来调试前端内容。

## 一、 效果图
先看效果图：
![Mock效果](/images/posts/MockJs.gif)

<!--more-->

## 二、 简介
![Mock效果](/images/posts/MockJs.jpg)
### 1. 地址
- [官网](http://mockjs.com/)
`http://mockjs.com/`
- [示例](https://github.com/nuysoft/Mock/wiki)
`https://github.com/nuysoft/Mock/wiki`

### 2. 资源
- 官网资源
`http://mockjs.com/dist/mock.js`
- cdn
`<script src="https://cdn.bootcss.com/Mock.js/1.0.1-beta3/mock-min.js"></script>`

### 3. 亮点

|六大优点|描述|
|:--|:--|
|前后端分离|让前端攻城师独立于后端进行开发|
|增加单元测试的真实性|通过随机数据，模拟各种场景|
|开发无侵入|不需要修改既有代码，就可以拦截 Ajax 请求，返回模拟的响应数据|
|用法简单|符合直觉的接口|
|数据类型丰富|支持生成随机的文本、数字、布尔值、日期、邮箱、链接、图片、颜色等|
|方便扩展|支持支持扩展更多数据类型，支持自定义函数和正则|

## 三、 使用
### 1. 引入
普通的`.html`文件直接标签引入即可，一些框架类可使用后者`npm`等安装使用
#### 标签引入
```html
// html
<script src="http://mockjs.com/dist/mock.js"></script>
<script>
    // some codes...
</script>
```

#### npm
通过npm安装，引入
```js
// 安装
npm i mockjs
```
```js
// 使用
import Mock from 'nockjs'

// 示例
Mock.mock('/data', {
    name: '@cname'  // 生成中文姓名
    ... // 按API写各种配置就能得到自己想要的数据
})
```

### 2. 常用接口

`Mock.mock( rurl, rtype, template | function(options) )`

参数对照：

|参数|是否必选|描述|
|:--|:--|:--|
|rurl|可选|要拦截的url。注意，如果使用get的拼参链接，可以使用正则匹配|
|rtype|可选|要拦截的请求类型，一般为`get`或`post`|
|template|可选|要返回的数据模板，也就是在此处写生成规则，返回数据都会按照这个模板返回|
|function(options)|可选|同上`template`二选一。其中`options`打印出来是一些基础参数，包括url,请求方式等，可以从中获取到请求中传的值，根据这些数据模拟更加真实的返回数据|

## 四、 实例
上面分享了一个动图，是简单地通过一个数字传输，到生成返回数据，到数据渲染，简单地展示MockJs在实际使用时的操作。接下来实现一下：
### 1. 布局
```html
<!-- 输入框和按钮 -->
<div>
    <div>
        <label for="counts">Data Length</label>
        <input type="number" id="counts" placeholder="生成数据长度" >
    </div>
    <button type="submit" id="build">Build</button>
</div>

<!-- 表格 -->
<div>
    <table>
        <thead>
            <th>ID</th>
            <th>姓名</th>
            <th>年龄</th>
            <th>性别</th>
            <th>地址</th>
            <th>文章数</th>
            <th>回答数</th>
            <th>点赞</th>
        </thead>

        <tbody id="data">
            <!-- 这里渲染返回数据 -->
        </tbody>
    </table>
</div>
```

### 2. ajax请求
```js
// 此处使用的jq，用axios等亦可
$('#build').click(() => {
    const counts = parseInt($('#counts').val());
    if(! counts) {
        alert('请输入您想要的长度哦');
    }else{
        if($('#counts').val() < 0 || $('#counts').val() > 100) {
            alert('请输入1-100的长度哦~');
        }else{
            $.ajax({
                url: 'https://www.zhuxingmin.com/lists',
                type: 'get',
                dataType: 'json',
                data: {
                    counts: counts
                },
                success: res => {
                    // 返回数据结果处理
                }
            })
        }
    }
})
```

### 3. Mock登场
```js
// 拦截
// url正则匹配
// get方式
// params即上边提到的options，可从中得到url,post,body等信息
Mock.mock(RegExp('/lists*.'), 'get', params => {
    // 拿到ajax传的数据
    const len = params.url.split('?')[1].split('&')[0].split('=')[1];
    // 返回随机数据的模板
    return Mock.mock({
        code: 200,  // 默认固定code:200
        data: { // 重头戏
            // userList|${len}： mockJs语法，len表示个数
            [`userList|${len}`]: [
                {
                    'uid|+1': 1,    // uid从1开始，依次+1
                    name: '@cname', // 随机中文名
                    'age|13-30': 13,    // 13-30的随机整数
                    'gender|1-2': 0,    // 1-2的随机整数
                    address: '@county(true)',   // 生成随机地址
                    info: {
                        'articles|3-150': 3,    // 3-150随机整数
                        'answer|9-99': 1,   // 9-99随机整数
                        'favorate|15-99999': 1, // 15-99999随机整数
                    }
                }
            ]
        },
        msg: '请求成功' // 返回默认提示
    })
})
```

### 4. 返回数据处理
刚才在第二步的时候，我们只是发送了请求，在success中没有对返回数据进行处理。现作如下处理：
```js
success: res => {
    let html = '';
    for(let i = 0; i < res.data.userList.length; i ++) {
        html += `<tr>
            <td>${res.data.userList[i].uid}</td>
            <td>${res.data.userList[i].name}</td>
            <td>${res.data.userList[i].age}</td>
            <td>${res.data.userList[i].gender === 1 ? '男' : '女'}</td>
            <td>${res.data.userList[i].address}</td>
            <td>${res.data.userList[i].info.articles}</td>
            <td>${res.data.userList[i].info.answer}</td>
            <td>${res.data.userList[i].info.favorate}</td>
        </tr>`
    }
    $('#data').html(html);
}
```
可以适当的添加样式来美化一些，不再示例。
**完成.**

----
## 结语
&emsp;&emsp;不难看出，有了Mock，前后端人员只需要定义好接口文档就可以开始并行工作，互不影响，在前端工程化中，能够高效定位代码缺陷，提前管理/修复缺陷，且有利于整个产品质量以及进度的保证。本案例只是入门级的示例，但依然能够帮助前端开发人员有一个不错的提升。更加深层次的例如原理等，本文不再探究。
**PS：**
想要完整版demo的可以扫描二维码或者微信搜索公众号"**流眸**"回复`mock`获取~
![](/images/qrcode.jpg)




