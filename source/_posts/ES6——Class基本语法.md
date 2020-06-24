---
title: ES6——Class基本语法
type: categories
copyright: true
date: 2019-07-29 16:43:11
tags: [JS, ES6, Class]
categories: 前端笔记
description:
---

### 一、 简介
&emsp;&emsp;JavaScript语言传统方法是通过构造函数定义并生成新对象。例：
```javascript
function Add (a, b) {
    this.a = a;
    this.b = b;
}

Add.prototype.toString = function () {
    return this.a + this.b;
}

var add = new Add(5, 8)
```

<!--more-->

&emsp;&emsp;而在ES6中，引入了**Class**这个概念来作为对象的模板。而class只是一个语法糖写法，还是基于ES5封装而来的。上面的例子用class来改写如下：
```javascript
// 定义类
class Add {
    constructor (a, b) {
        this.a = a;
        this.b = b;
    }

    toString () {
        return this.a + this.b;
    }
}
```
>注：方法之间不需要加逗号，否则会报错。

&emsp;&emsp;从中可见一个**constructor方法**，这**便是构造方法**。另外，**关键字this则代表实例对象**。

-----

### 二、基本准则
- ES6的类完全可以看作是构造函数的另一种写法
- 类的数据类型就是函数，类本身就只想构造函数
- 构造函数的prototype属性在ES6的类上继续存在
- 类的所有方法都定义在类的prototype上
- 在类的实例上调用方法，其实就是调用原型上的方法
- Object.assign可以一次向类添加多个方法
- 类的内部定义的所有方法都是不可枚举的
- 类的属性名可以采用表达式

#### 1. 一次向类添加多个方法
```javascript
// Object.assign(类原型,{方法列表})
Object.assign(Add.prototype, {
    toString () {},
    toValue () {}
})
```

#### 2. 不可枚举
```javascript
class Add {
    constructor (a, b) {
        // ...
    }

    toString () {
        // ...
    }
}

Object.keys(Add.prototype); // []
Object.getOwnPropertyNames(Add.prototype);  // ["constructor", "toString"]
```

#### 3. 属性名表达式
```javascript
const methodName = 'getValue';

class Add {
    constructor (a, b) {
        // ...
    }

    [methodName] () {
        // ...
    }
}
```
-----
### 三、 拓展
#### 1. 严格模式
&emsp;&emsp;类和模版的内部默认使用严格模式，所以无需使用*use strict*指定运行模式。只要将代码卸载类或者模块之中，就只有严格模式可用。ES6已经把整个语言都升级到了严格模式下。
#### 2. constructor方法
&emsp;&emsp;constructor方法是类的默认方法，通过new命令生成对象实例时自动调用该方法。如果类中没有显式定义，则会默认添加一个空的constructor方法。例：
```javascript
class Add {

}

// 等同于
class Add {
    constructor () {}
}
```
&emsp;&emsp;上面代码中定义了一个空的类Add，JavaScript引擎会自动给它添加一个空的constructor方法。除此之外，还需注意的是：
- constructor方法默认返回this，不过也可以指定返回另一个对象
- 类必须使用new来调用，否则会报错

#### 3. 类的实例对象
&emsp;&emsp;实例的属性除非是显式定义在this对象上，否则都是定义在了Class上。
```javascript
class Add {
    constructor (a, b) {
        this.a = a;
        this.b = b;
    }

    toString () {
        return this.a + this.b;
    }
}

var add = new Add(5, 8);
add.toString(); // 13

add.hasOwnProperty('a');    // true
add.hasOwnProperty('b');    // true
add.hasOwnProperty('toString');     // false
add.__proto__.hasOwnProperty('toString');   // true
```
&emsp;&emsp;从上面代码中，a和b都是实例对象Add自身的属性，因为定义在this变量上，所以执行hasOwnProperty方法返回true；而toString是原型对象的属性，因为定义在Add上，所以hasOwnProperty方法返回的事false。另外，类的所有实例共享一个原型对象；可以通过实例的__proto__属性为类来添加方法。

#### 4. 其他
&emsp;&emsp;除上述所说之外，类同样有很多有意思的属性或者方法，此处放上一些，以供参考。
>- Class表达式
>- 不存在变量提升
>- 私有方法
>- 私有属性
>- this的指向
>- name属性
>- Class的存取值函数（setter/getter）
>- Class的Generator方法
>- Class的静态方法
>- 静态属性和实例属性
>- new.target属性

参考：[ECMAScript 6 入门 - 阮一峰](http://es6.ruanyifeng.com/)
