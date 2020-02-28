---
title: JS交互微信之自定义分享
type: categories
copyright: true
date: 2020-02-28 14:12:23
tags: [vue, js, 微信, jssdk]
categories: 前端笔记
description:
---

## 前言
本篇为**JS交互微信系列篇**的第三篇**自定义微信分享**，记录在微信内置浏览器打开页面再分享时对分享的处理。

## 一、 介绍
在微信中打开的页面，如果我们不加操作直接分享出去，除了分享的不太美观（只有title\url，而不是自定义的标题、副标题、图片等），也有可能不太符合正常流程，如：用户在a页面通过登录进如了b页面，然后将b页面直接分享给别的用户，如果我们不加以处理，那么别的用户点击b页面则会是错误页（因为跨过了登录）。当面临此类情况时，搞一下自定义分享还是十分有必要的。

## 二、 注意事项
**请注意，不要有诱导分享等违规行为，对于诱导分享行为将永久回收公众号接口权限**

*请注意，原有的 `wx.onMenuShareTimeline`、`wx.onMenuShareAppMessage`、`wx.onMenuShareQQ`、`wx.onMenuShareQZone` 接口，即将废弃。请尽快迁移使用客户端6.7.2及JSSDK 1.4.0以上版本支持的 `wx.updateAppMessageShareData`、`wx.updateTimelineShareData`接口。*
引入低版本的script链接，这些接口暂时仍可用。

## 三、 示例代码
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

### 3. 自定义分享[老版本]
#### 3.1 分享到朋友圈
```js
wx.onMenuShareTimeline({
  title: '', // 分享标题
  link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
  imgUrl: '', // 分享图标
  success: function () {
  // 用户点击了分享后执行的回调函数
  }
}
```

#### 3.2 分享给朋友
```js
wx.onMenuShareAppMessage({
  title: '', // 分享标题
  desc: '', // 分享描述
  link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
  imgUrl: '', // 分享图标
  type: '', // 分享类型,music、video或link，不填默认为link
  dataUrl: '', // 如果type是music或video，则要提供数据链接，默认为空
  success: function () {
    // 用户点击了分享后执行的回调函数
  }
})
```

#### 3.3 分享到QQ
```js
wx.onMenuShareQQ({
  title: '', // 分享标题
  desc: '', // 分享描述
  link: '', // 分享链接
  imgUrl: '', // 分享图标
  success: function () {
  // 用户确认分享后执行的回调函数
  },
  cancel: function () {
  // 用户取消分享后执行的回调函数
  }
})
```

#### 3.4 分享到QQ空间
```js
wx.onMenuShareQZone({
  title: '', // 分享标题
  desc: '', // 分享描述
  link: '', // 分享链接
  imgUrl: '', // 分享图标
  success: function () {
  // 用户确认分享后执行的回调函数
  },
  cancel: function () {
  // 用户取消分享后执行的回调函数
  }
})
```

### 4. 自定义分享[新版本]
#### 4.1 分享给朋友&&分享到QQ
```js
wx.ready(function () {   //需在用户可能点击分享按钮前就先调用
  wx.updateAppMessageShareData({ 
    title: '', // 分享标题
    desc: '', // 分享描述
    link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
    imgUrl: '', // 分享图标
    success: function () {
      // 设置成功
    }
  })
})
```

#### 4.2 分享到朋友圈 && 分享到QQ空间
```js
wx.ready(function () {      //需在用户可能点击分享按钮前就先调用
  wx.updateTimelineShareData({ 
    title: '', // 分享标题
    link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
    imgUrl: '', // 分享图标
    success: function () {
      // 设置成功
    }
  })
})
```


以上。



