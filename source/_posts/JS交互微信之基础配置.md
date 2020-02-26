---
title: JS交互微信之基础配置
type: categories
copyright: true
date: 2020-02-25 18:12:58
tags: [vue,微信,js,jssdk]
categories: 前端笔记
description:
---

## 前言
最近公司需求，需要在APP中分享出去的web页面中加入商品购买功能，目前需要使用微信支付。由于微信支付基础为0，因此不得不扒文档抄作业，加上度娘的支持，得以完成了一些功能，因此开一小模块记录下关于此项目中前端用到的东西。不过不得不说，复杂的东西都在后端执行，需要前端完成的真的是少之又少，都是一些基础配置以及调取微信文档中写的接口而已，不要想得过于复杂。

<!--more-->

### 篇章介绍
目前可能会更新以下几块内容，每个模块仅与基础配置篇相关，其他的相互独立（如，获取用户地址模块不参与自定义分享模块内容，但都基于基础配置篇），每篇内容尽量简洁明了。
- 基础配置（本篇）
- 获取用户收货地址
- 自定义分享
- 微信支付（微信浏览器，JSAPI支付）
- 微信支付（非微信浏览器,H5支付）

### 应用场景介绍
1. 在微信内打开网页时候，如果需要获取用户在微信填写的收获地址，可通过api直接调取。   ———— 微信地址共享
2. 商户已有H5商城网站，用户通过消息、分享、链接或扫描二维码等方式在微信内打开网页时，可以调用微信支付完成下单购买的流程。    ———— JSAPI支付
3. 用户在微信内某个页面想要分享给朋友或朋友圈时，需要对分享内容、标题及图片重新配置时使用。  ———— 自定义分享
4. H5支付是指商户在微信客户端外的移动端网页展示商品或服务，用户在前述页面确认使用微信支付时，商户发起本服务呼起微信客户端进行支付。  ———— H5支付

-----

## 一、 基础配置(JSSDK)
***本块儿内容基本上是微信开放文档中配置，可忽略***
### 概述
微信JS-SDK是微信公众平台面向网页开发者提供的基于微信内的网页开发工具包。
通过使用微信JS-SDK，网页开发者可借助微信高效地使用拍照、选图、语音、位置等手机系统的能力，同时可以直接使用微信分享、扫一扫、卡券、支付等微信特有的能力，为微信用户提供更优质的网页体验。

### 使用步骤
#### 1. 绑定域名
先登录微信公众平台进入“公众号设置”的“功能设置”里填写“JS接口安全域名”。
*备注：登录后可在“开发者中心”查看对应的接口权限*

#### 2. 引入JS
在需要调用JS接口的页面引入如下JS文件，（支持https）：
```js
<script src="https://res.wx.qq.com/open/js/jweixin-1.6.0.js"></script>
```
*备注：支持使用 AMD/CMD 标准模块加载方法加载*

#### 3. 通过config接口注入权限验证配置
所有需要使用JS-SDK的页面必须先注入配置信息，否则将无法调用（***同一个url仅需调用一次，对于变化url的SPA的web app可在每次url变化时进行调用,目前Android微信客户端不支持pushState的H5新特性，所以使用pushState来实现web app的页面会导致签名失败，此问题会在Android6.2中修复***）。
```js
wx.config({
  debug: false, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
  appId: '', // 必填，公众号的唯一标识
  timestamp: , // 必填，生成签名的时间戳
  nonceStr: '', // 必填，生成签名的随机串
  signature: '',// 必填，签名
  jsApiList: [] // 必填，需要使用的JS接口列表
});
```

#### 4. 接口验证
```js
// 成功
wx.ready(function(){
  // config信息验证后会执行ready方法，所有接口调用都必须在config接口获得结果之后，config是一个客户端的异步操作，所以如果需要在页面加载时就调用相关接口，则须把相关接口放在ready函数中调用来确保正确执行。对于用户触发时才调用的接口，则可以直接调用，不需要放在ready函数中。
});
// 失败
wx.error(function(res){
  // config信息验证失败会执行error函数，如签名过期导致验证失败，具体错误信息可以打开config的debug模式查看，也可以在返回的res参数中查看，对于SPA可以在这里更新签名。
});
```

## 二、 补充
在上面几个步骤中，有几个注意点：
- 步骤3中的参数，后端提供；
- 步骤4中，通过ready验证成功的接口，有以下通用参数：
    1. success：接口调用成功时执行的回调函数。
    2. fail：接口调用失败时执行的回调函数。
    3. complete：接口调用完成时执行的回调函数，无论成功或失败都会执行。
    4. cancel：用户点击取消时的回调函数，仅部分有用户取消操作的api才会用到。
    5. trigger: 监听Menu中的按钮点击时触发的方法，该方法仅支持Menu中的相关接口。
    6. *备注：不要尝试在trigger中使用ajax异步请求修改本次分享的内容，因为客户端分享操作是一个同步操作，这时候使用ajax的回包会还没有返回。*
- 在使用每个JS接口前，可以使用`checkJsApi`的方法判断是否支持该接口：
```js
wx.checkJsApi({
  jsApiList: ['onMenuShareTimeline'], // 需要检测的JS接口列表，此处为分享到朋友圈接口示例
  success: function(res) {
  // 以键值对的形式返回，可用的api值true，不可用为false
  // 如：{"checkResult":{"onMenuShareTimeline":true},"errMsg":"checkJsApi:ok"}
  }
});
```
- 所有接口列表

|接口名|用途|
|:--:|:--:|
|updateAppMessageShareData| 分享给朋友|
|updateTimelineShareData|分享到朋友圈|
|onMenuShareTimeline（即将废弃）|分享到朋友圈|
|onMenuShareAppMessage（即将废弃）|分享给朋友|
|onMenuShareQQ（即将废弃）|分享到QQ|
|onMenuShareWeibo|分享到微博|
|onMenuShareQZone|分享到空间|
|startRecord|开始录音|
|stopRecord|停止录音|
|onVoiceRecordEnd|录音超时|
|playVoice|播放|
|pauseVoice|暂停|
|stopVoice|停止|
|onVoicePlayEnd|播放结束时执行|
|uploadVoice|上传录音|
|downloadVoice|下载录音|
|chooseImage|选择本地照片|
|previewImage|预览|
|uploadImage|上传|
|downloadImage|下载|
|translateVoice|翻译|
|getNetworkType|网络状况|
|openLocation|打开内置地图|
|getLocation|获取用户位置|
|hideOptionMenu|隐藏菜单|
|showOptionMenu|显示菜单|
|hideMenuItems|隐藏菜单按钮|
|hideAllNonBaseMenuItem|屏蔽所有按钮|
|showAllNonBaseMenuItem|显示所有按钮|
|closeWindow|关闭页面|
|scanQRCode|扫描二维码|
|chooseWXPay|微信支付|
|openProductSpecificView|微信卡券|
|addCard|添加卡券到卡包|
|chooseCard|拉取卡券|
|openCard|添加卡券|