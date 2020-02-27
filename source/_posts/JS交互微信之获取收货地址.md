---
title: JS交互微信之获取收货地址
type: categories
copyright: true
date: 2020-02-27 16:29:13
tags: [vue, js, 微信, jssdk]
categories: 前端笔记
description: 
---

## 前言
本篇为**JS交互微信系列篇**的第二篇，上一篇《JS交互微信之基础配置》讲述了基础配置，本篇记录获取（共享）用户收货地址。

## 一、 场景
在需要用户实际收货地址时，可以直接拉取用户微信中存在的收货地址，或者在微信中新增、编辑及删除收货地址，而后传给商户

<!--more-->

## 二、 介绍
### 1. 简介
微信收货地址共享，是指用户在微信浏览器内打开网页，填写过地址后，后续可以免填写支持快速选择，也可增加和编辑。
此地址为用户属性，可在各商户的网页中共享使用。支持原生控件填写地址，地址数据会传递到商户。

### 2. 功能支持
地址共享是基于微信JavaScript API 实现，`只能在微信内置浏览器中使用，其他浏览器调用无效`。同时，需要微信5.0 版本才能支持，建议通过user agent 来确定用户当前的版本号后再调用地址接口。
以iPhone 版本为例，可以通过useragent可获取如下微信版本示例信息："`Mozilla/5.0(iphone;CPU iphone OS 5_1_1 like Mac OS X)AppleWebKit/534.46(KHTML,like Geocko) Mobile/9B206MicroMessenger/5.0`"其中5.0 为用户安装的微信版本号，商户可以判定版本号是否高于或者等于5.0。

### 3. 地址格式
微信地址共享使用的数据字段包括：
- 收货人姓名
- 地区，省市区三级
- 详细地址
- 邮编
- 联系电话
- 国际编码

## 三、 接口说明
**openAddress**
```js
wx.openAddress({
    success: function () {
        // 用户成功拉出地址 
    },
    cancel: function () {
        // 用户取消拉出地址
    }
});
```

**返回值说明**

|返回值|说明|
|:--:|--|
|errMsg|获取编辑收货地址成功返回“openAddress:ok”|
|userName|收货人姓名|
|postalCode|邮编|
|provinceName|国标收货地址第一级地址（省）|
|cityName|国标收货地址第二级地址（市）|
|countryName|国标收货地址第三级地址（国家）|
|detailInfo|详细收货地址信息|
|nationalCode|收货地址国家码|
|telNumber|收货人手机号码|

## 四、 示例代码
### 1. 引入script
```html
// 主文件 或 入口文件 引入
<script src="https://res.wx.qq.com/open/js/jweixin-1.1.0.js"></script>
```

### 2. wx.config配置
```js
// 微信配置
// appId、timestamp、nonceStr、signature从公众号和后台获取
wx.config({
    debug: false, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    appId: '', // 必填，公众号的唯一标识
    timestamp: '', // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名
    jsApiList: [
        'checkJsApi',
        'openAddress'
    ] // 必填，需要使用的JS接口列表 这里填写需要用到的微信api openlocation为使用微信内置地图查看位置接口
});
```

### 3. 获取地址
```js
wx.ready(function () {
    // 检测接口是否可用，开发环境中测试用，生产环境没问题可不写
    wx.checkJsApi({
        jsApiList: [
            'openAddress',
        ],
        success: function (res) {
            alert(res);
        }
    });
    // 请求接口
    wx.openAddress({
        // 请求成功要做的事
        success: (res) => {
            // 这里我把获取到的用户信息存储到了vuex中的state中保存，以防后续路由跳转带来的组件销毁带来的数据没法保存
            // 根据自己需求拿自己需要的参数即可，参数名可对照上面的返回值说明表
            _this.$store.state.address.userName = res.userName;
            _this.$store.state.address.telNumber = res.telNumber;
            _this.$store.state.address.nationalCode = res.nationalCode;
            _this.$store.state.address.provinceName = res.provinceName;
            _this.$store.state.address.cityName = res.cityName;
            _this.$store.state.address.countryName = res.countryName;
            _this.$store.state.address.detailInfo = res.detailInfo;
        },
        cancel: function () {
            // 用户取消要做的事儿
            // 这里我把请求时打开的一个loading动画给关闭了
            _this.$layer.close();
        },
    });
});
```

## 五、 常见问题
在开发中，我遇到的问题如下：
1. 必须发布到线上测试，没找到好的解决办法；
2. 公众号（服务号）中要设置IP白名单，不然通不过验证！
3. 安卓可以调起而iOS无法调起：引入`script`链接时，应引入`https`而不是`http`，可能iOS对安全策略这块儿比较严格，在此处踩了个坑。
4. 在PC端无法调起！

以上。


