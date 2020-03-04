---
title: JS交互微信之JSAPI支付
type: categories
copyright: true
date: 2020-03-03 17:06:15
tags: [vue, 微信, js, jssdk]
categories: 前端笔记
description:
---
## 前言
本篇为**JS交互微信系列篇**的第四篇**微信JSAPI支付**，记录在微信内置浏览器内用调用微信支付过程。

## 一、 介绍
JSAPI支付是用户在微信中打开商户的H5页面，商户在H5页面通过调用微信支付提供的JSAPI接口调起微信支付模块完成支付。

<!--more-->

### 应用场景：

- 用户在微信公众账号内进入商家公众号，打开某个主页面，完成支付
- 用户的好友在朋友圈、聊天窗口等分享商家页面连接，用户点击链接打开商家页面，完成支付
- 将商户页面转换成二维码，用户扫描二维码后在微信浏览器中打开页面后完成支付

## 二、 准备

### 1. 准备内容
要拥有两个账号：
1. 微信服务号，要通过认证（企业才拥有资格）
2. 微信商户平台号(微信支付平台)

### 2. 平台配置
#### 2.1 微信支付(商户平台)中
要开通产品中心的JSAPI支付。然后`产品中心=>开发配置=>支付配置=>公众号支付配置`绑定支付授权目录，写已通过ICP备案的域名。
另外，要在ip白名单中，配置测试地址ip和线上生产地址ip，不然各种回调都会失败！
#### 2.2 微信公众平台中
由于在微信内支付需要获取用户的`openid`，要获取它则必须通过网页授权配置。在公微信公众平台中，`公众号设置=>功能设置=>网页授权域名`中按要求填写。

## 三、 开发
在支付流程方面，重点依然都在后端处理，前端方面步骤比较简单。本文只叙述前端内容。
### 1. 用户授权，获取code
在将要进入支付的前一页面，直接接入微信授权，然后跳转进要支付的那个页面。举个例子：有a、b两个页面，在b页面用到支付，b页面由a页面跳转而来。那么在a页面跳b页面的时候，别直接跳转b的url，而是跳转到：
`https://open.weixin.qq.com/connect/oauth2/authorize?appid={appId}&redirect_uri={b.html}&response_type=code&scope=snsapi_base#wechat_redirect`
我们注意到，这里有这两个需要自己写的参数：`appid`和`redirect_uri`，意义是：
- appid——服务号id
- redirect_uri——获取授权后回调的页面地址，比如b页面

另外，还有一个注意的点是，***b.html这个url我们要进行encode转码，不然地址解析可能会出现问题！***

```js
// url转码
let url = 'a.html';
url = escape(url);
```

### 2. 得到code，换取凭证获取openid
上一步执行完后，在微信浏览器中，我们会得到一个链接，类似：
`b.html?&code={code}&state=#/`
在此处，我们得到了一个code值，这就是我们获取`openid`的凭证了。
获取方法当然是把值传给后台，后台去处理啦~

### 3. 接收后端返回的我们需要的参数值
在上一步中，我们拿到code值后，就可以提交一些信息给后端了，比如商品相关属性、总价等，另外加上code值，传给后端。后端一顿操作后，返回给前端。我们需要的参数如下（后端返回下面这些参数）：
```js
// 微信支付需要参数
orderInfo: {
    package: '',    // 前端需要从后台获取该数据
    paySign: '',    // 微信签名，前端需要从后台获取该数据
    appId: '',  // 需要在微信绑定商户号，成功之后会生成有appid
    signType: '',   // 微信签名方式,默认为"MD5",也可以从后台获取
    nonceStr: '',   // 随机串，前端需要从后台获取该数据
    timeStamp: '',  // 时间戳，自1970年以来的秒数，前端需要从后台获取该数据
}
```

### 4. 交互微信
上个步骤拿到需要交互微信的参数后，就开始调用微信的支付接口了，如下：
```js

// 微信支付
WeixinJSBridge.invoke('getBrandWCPayRequest', {
    appId: _this.orderInfo.appId,   // 上一步得到的参数
    nonceStr: _this.orderInfo.nonceStr, // 上一步得到的参数
    package: _this.orderInfo.package,   // 上一步得到的参数
    signType: _this.orderInfo.signType, // 上一步得到的参数
    timeStamp: _this.orderInfo.timeStamp,   // 上一步得到的参数
    paySign: _this.orderInfo.paySign    // 上一步得到的参数
}, res => {
    // 调用后要做的事儿
    // codes...

    // 根据get_brand_wcpay_request的值判断支付状态
    if (res.err_msg === "get_brand_wcpay_request:ok") {
        // ok：支付成功
        // 支付成功要做的事儿，比如跳转支付完成后的页面等
    } else if (res.err_msg === "get_brand_wcpay_request:cancel") {
        // cancel：用户取消支付
        // 取消支付要做的事儿，比如进入重新支付步骤等
    } else if (res.err_msg === "get_brand_wcpay_request:fail") {
        // fail： 支付失败
        // 支付失败要做的事儿，比如进入支付失败步骤等
    } 
    // 注： 使用以上方式判断前端返回,微信团队郑重提示：res.err_msg将在用户支付成功后返回ok,但并不保证它绝对可靠。
})
```

### 5. 常见错误码

|名称	|描述|	原因|	解决方案|
|:--|:--|:--|:--|
|INVALID_REQUEST	|参数错误	|参数格式有误或者未按规则上传	|订单重入时，要求参数值与原请求一致，请确认参数问题|
|NOAUTH	|商户无此接口权限|	商户未开通此接口权限	|请商户前往申请此接口权限|
|NOTENOUGH|	余额不足|	用户帐号余额不足|	用户帐号余额不足，请用户充值或更换支付卡后再支付|
|ORDERPAID|商户订单已支付|	商户订单已支付，无需重复操作|	商户订单已支付，无需更多操作|
|ORDERCLOSED	|订单已关闭|	当前订单已关闭，无法支付|	当前订单已关闭，请重新下单|
|SYSTEMERROR	|系统错误	|系统超时|	系统异常，请用相同参数重新调用|
|APPID_NOT_EXIST|	APPID不存在|	参数中缺少APPID|	请检查APPID是否正确|
|MCHID_NOT_EXIST|	MCHID不存在	|参数中缺少MCHID|	请检查MCHID是否正确|
|APPID_MCHID_NOT_MATCH	|appid和mch_id不匹配|	appid和mch_id不匹配	|请确认appid和mch_id是否匹配|
|LACK_PARAMS|	缺少参数|	缺少必要的请求参数|	请检查参数是否齐全|
|OUT_TRADE_NO_USED|	商户订单号重复|	同一笔交易不能多次提交|	请核实商户订单号是否重复提交|
|SIGNERROR|	签名错误|	参数签名结果不正确|	请检查签名参数和方法是否都符合签名算法要求|
|XML_FORMAT_ERROR|	XML格式错误|	XML格式错误|	请检查XML参数格式是否正确|
|REQUIRE_POST_METHOD	|请使用post方法|	未使用post传递参数 	|请检查请求参数是否通过post方法提交|
|POST_DATA_EMPTY	|post数据为空|	post数据不能为空|	请检查post数据是否为空|
|NOT_UTF8|	编码格式错误	|未使用指定编码格式	|请使用UTF-8编码格式|


## 三、 完成
至此，调用微信JSAPI来完成在微信内的支付就完成了。
