---
title: 秒懂——惹人烦恼的this指向
type: categories
copyright: true
date: 2019-06-11 16:50:47
tags: [js, this指向]
categories: [前端笔记]
description:
---
### 关于 This
- 它是JS关键字，在JS中具有特殊意义，代表一个空间地址；
- this是执行的主体，谁执行的这个函数谁就是this。

<!--more-->

### 惹人烦恼的this
&emsp;&emsp;This是什么？什么是this指针？this指针指向哪里？何时使用this？`use or not use, that is a question.`
&emsp;&emsp;其中至关重要的`this指向`问题，不仅自身麻烦，而且各类层出不穷的相关面试题更是让人应接不暇。既然这么麻烦，难倒无数js开发er，我们到底要做怎样的挣扎才能记住呢？且听下回...哦不，且往下看。

------

### THIS
**&emsp;&emsp;首先，我们介绍下关于this。**

#### 1. 什么是this指针？this指针指向哪里？何时使用this？
>&emsp;&emsp;在JavaScript中，在创建函数时，系统默认会生成的两个隐式参数：`this`和`arguments`。函数执行时，自动生成的一个内部对象，只能在函数内部使用。this指针指向与该函数调用进行隐式关联的一个对象，该对象被称为“函数上下文”。this是js的一个关键字，随着函数使用场合不同，this的值会发生变化。
&emsp;&emsp;this永远指向其所在函数的真实调用者（谁调用的就指向谁，）如果没有所有者时，指向全局对象window。

**&emsp;&emsp;接着，我们来看一下常见函数调用方式**

#### 2. 常见函数调用方式
- 直接调用:
```
function fn (n) {
	...
}

fn(n);
```
- 作为对象方法调用:
```
var obj = {
    a: 1,
    fn: function (n) {
	    ...
    }
}

obj.fn(n);
```
- call/apply
```
function fn (n){
    ...
}

fn.call(context, n);
```

#### 3. 变幻
&emsp;&emsp;观察 **2** 中的几种常见方式，其实前几种都与最后一种密不可分。原因是前两种都是第三种方式的简写（语法糖）。当然，前两种方式也可以转变为第三种：
- `fn(n)` => `fn.call(undefined, n)`
- `obj.fn(n)` => `obj.fn.call(obj, n)`

&emsp;&emsp;因此，我们函数调用归根就是一种：`fn.call(context, n)`

#### 4. 结论
&emsp;&emsp;可是我们总结以上，跟this有什么关系呢？其实答案已经呼之欲出了！**`context`就是`this`所指向！当context为null或undefined时，也可省略不写，并且this指向window。**那么这时我们再来看看，就发现this指向很是明了了。
&emsp;&emsp;
>当转换为标准写法时，this指向context。
当context为null或undefined时，也可省略不写，并且this指向window。