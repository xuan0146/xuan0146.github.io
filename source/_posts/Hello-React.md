---
title: Hello React
type: categories
copyright: true
date: 2019-08-06 15:38:41
tags: [JSX, React]
categories: 前端笔记
description:
---

## 一、 开发环境配置(Mac)
### 1. node.js 安装 （node + npm)
   ```javascript
   //官网安装包下载即可
   ```
### 2. 官方脚手架工具 create-react-app
   ```javascript
    npm install create-react-app -g
   ```

<!--more-->

### 3. 创建项目
```javascript
create-react-app hello  //创建一个叫hello的项目
```
### 4. 运行项目
```javascript
//创建完后，cd到该项目
npm start
```
### #小结
&emsp;&emsp;至此，Mac环境下react基于脚手架的开发环境已搭建完成。运行项目（`npm start`)后，浏览器会自动打开本地页面**http://localhost:3000/**。如果到此步弹出带有react的Logo的欢迎页面，则表示环境已经成功配置。


------

## 二、 Hello World
&emsp;&emsp;作为程序员最喜闻乐见的入门代码，还属 ***hello world*** 。那么第一个组件则输出一段 hello world。

### 1. 项目目录结构
基础目录结构及注释如下：
```
|——hello
   |—— node_modules              # 项目依赖
   |—— public                    # 公共文件
       |—— favicon.ico              // 网站图标
       |—— mainfest.json            // 应用基本配置信息
       └── index.html               // 主入口文件
   |—— src                       # 主程序模块
       |—— App.css                  // App css样式
       |—— App.js                   // 主组件
       |—— App.test.js              // 测试组件
       |—— index.css                // 主文件样式
       |—— index.js                 // 主文件脚本
       |—— logo.svg                 // svg logo
       └── serviceWorker.js         // 缓存配置文件
   |—— package.json              # 包配置文件
   └── README.md                 # React说明文档
```

### 2. 第一个组件
&emsp;&emsp;了解了项目结构，接下来开始写hello world组件。 **首先**，在src文件夹下新建一个文件，如HelloWorld.js。**然后**，在该文件下，三步走：
1. 引入react
2. 新建类继承react
3. 导出

示例一下：
```JavaScript
// 1. 引入
import React from 'react'

// 2. 类
class HelloWorld extends React.Component {
    render() {
        return (
            <div>
                <h1>Hello World</h1>
            </div>
        )
    }
}

// 3. 导出
export default HelloWorld
```
&emsp;&emsp;我们第一个组件就写完了，但是缺少一步：将组件挂载到根节点上：打开`src/index.js`：
```js
// ...

// 引入组件
import HelloWorld from './HelloWorld'

// 挂载
ReactDOM.render(<HelloWorld />, document.getElementById('root'));
```

### # 小结
&emsp;&emsp;写完第一个组件运行起来，仿佛打开了新世界的大门……诶，等等，刚才我写了什么？为什么在js中render函数中返回的是一段奇怪的html代码？这是什么格式？这样的代码是什么鬼？听我道来。

## 三、 JSX
### 1. 关于JSX
&emsp;&emsp;上章说到，render函数中返回的是一段奇怪的html代码，那这段代码是什么吗？在此引入**JSX**概念及语法。Facebook称其为jsx，属于JavaScript的语法扩展。虽然看似html的模板语言，实则是在JavaScript内部实现。
### 2. 语法
&emsp;&emsp;引入JSX概念，是为了编程更加简答。当然，JSX的语法也不会复杂，甚至可以说是非常简单：主要语法是一个花括号，然后在花括号内写expressions表达式即可。对expression有疑惑的可以先挪步到官方查阅文档，传送门：[expression](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Expressions_and_Operators)
#### a. 基本语法
&emsp;&emsp;那么，使用expressions表达式我们可以做什么呢？大家可以简单敲几行运行一下。简单示例下：
```js
import React from 'react'

class Test extends React.Component {
    render() {
        return (
            <div>
                <h1>Hello React</h1>
                <div>字符串： {'string'}</div>
                <div>数字运算： {12+4}</div>
                <div>数组： {[1,2,3,4]}</div>
                <div>JSX：{<mark>标识</mark>}</div>
            </div>
        )
    }
}

export default Test
```
&emsp;&emsp;如果我们需要进行一些数据操作，例如判断，遍历等等之类的，又要怎么做呢？

#### b. map
```js
import React from 'react'

class Welcome extends React.Component {
    render() {
        const learnList = ['React', 'Vue', 'Angular'];

        return (
            <div>
                <ul>
                    {
                        learnList.map((item, index) =>
                             <li>{index + 1}: {item}</li>
                        )
                    }
                </ul>

            </div>
        )
    }
}

export default Welcome
```

#### c. if三元表达式
```js
import React from 'react'

class Welcome extends React.Component {
    render() {
        const showMe = true;

        return (
            <div>
               {
                  showMe ? <mark>这是true显式的文字</mark> : <b>这是false显式的文字</b>
               }   
            </div>
        )
    }
}

export default Welcome
```

#### d. 属性
&emsp;&emsp;大部分情况，html语法在JSX中正常使用，但是也有个别例外：
1. class => className
2. for => htmlFor

例:
```js
return (
   <div className="..." htmlFor="...">
      一段文字……  
   </div>
)
```

### # 小结
&emsp;&emsp;本章了解了上章提到的，写在js中的html是JSX语法，并且介绍了JSX语法及基本功能使用，已经可以开始上手解决一部分的业务了。那么除此之外，关于组件之间如何交流，有何属性和状态？且看下回。
