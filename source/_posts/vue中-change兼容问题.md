---
title: vue中@change兼容问题
type: categories
copyright: true
date: 2019-09-20 14:32:21
tags: [vue, 兼容]
categories: 前端笔记
description:
---
### @change问题
#### 1. 需求描述
- 需求：选择日期，当日期改变，则页面相应该日期的某些事件渲染
- 条件：
  1. 移动端
  2. vue框架

<!--more-->

#### 2. 问题产生及描述
**问题： 兼容性差距，由于@change触发方式不同，导致时间加载不够统一，时间触发出现问题。**

描述如下：

使用vue做移动端时，由于需求需要，需要在页面某处添加一个日期选择的控件。由于不是面向用户，内部使用，因此本着不浪费资源的原则，直接使用h5自带的input：

```html
<input type="date">
```

emm...CSS样式不再赘述。然后绑定数据,绑定change事件监听值变化：

```html
<input type="date" v-model="date" @change="selectDate">
```
```js
selecrDate () {
    // do something...
}
```

当基本流程敲定后，在pc端没问题。但注意，我们此时讨论的是移动端效果，那么用pc端打开调试工具来模拟移动端机型显然是不足以代表移动端真实场景的，因此需要在真机测试。ok，接下来，差异化出现。
由于`@change`的特性是当监听到数据发生变化时则执行，因此差异便是暴露出来：
- 安卓：选择完年月日，点击确定后，数据变化，监听事件起作用；
- iOS： 点击选择日期，选择年，监听到数据变化一次，执行一次`selectDate`，选择月，监听到数据变化，再次执行，选择日，监听到数据变化，再执行……选择不停，执行不停。

由于此不同端所存在的差异化，导致了我们必须“兼容”这个问题。那么重点问题出现，如何兼容？

#### 3. 解决方案
讨论到此，我们必须想办法先解决一个问题：如何在iOS端，阻止掉每一次选择都自动执行`selectDate`的问题。此时，经过一番探寻查找，发现`@blur`方法可以替代iOS中的`@change`，说干就干，盘他：
```html
<input type="date" v-model="date" @blur="selectDate">
```
ok,找个苹果机试一下，完美，选择年月日莫的问题，只有点击确定之后才触发该事件。但，所谓小人得志不可取，兴高采烈的时候发现在安卓机上挂掉了。原因是在安卓机上选完以后，`@blur`事件并不会触发，除非选择完成之后，点击别的区域来触发此事件。因此，我们又面临一个兼容问题，怎么同时保证在安卓和iOS系统上都能流畅运行呢？
有办法——js辨别系统是安卓还是iOS：
```js
const u = navigator.userAgent, app = navigator.appVersion;
// Android 判断
const isAndroid = u.indexOf('Android') > -1 || u.indexOf('Linux') > -1;
// iOS 判断
const isIOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); 

if (isAndroid) {
    // 这个是安卓操作系统
    // do something
}

if (isIOS) {
    // 这个是iOS操作系统
    // do something
}
```

有了此步骤，那接下来的事儿就简单多了：
- 如果是安卓系统，则用`@change`;
- 如果是iOS系统，则用`@blur`;
- 由于是移动端，所以不考虑pc～；

完成～.～！

------

### 总结
总结以上，步骤如下：
>1. 进入页面，执行js脚本，判断当前设备型号；
>2. 如果是安卓设备，执行`@change`；
>3. 如果是iOS设备，执行`@blur`。


