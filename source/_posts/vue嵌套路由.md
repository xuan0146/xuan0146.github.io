---
title: vue嵌套路由
type: categories
copyright: true
date: 2019-04-20 21:26:36
tags: [vue, 前端,  路由]
categories: 前端笔记
description:
---
#### 关于嵌套路由
&emsp;&emsp;在实际项目中，多个前端页面由于需求而通常由多层嵌套的组件组合而成,尤其是多导航界面。此时，路由嵌套(vue-router)的优点则表现了出来。`嵌套路由就是在一个被路由过来的页面下，可以继续使用路由来加载新的组件。`所谓嵌套，也可以理解成父子路由。

<!--more-->
#### 应用场景
- 各样式导航栏切换；
- 标签/选项卡切换；

------

#### 案例
&emsp;&emsp;此处以常用布局`F形导航`当作分析案例：
##### 1.效果
![F形导航界面布局](/images/posts/路由嵌套.gif 'F形导航界面布局')
##### 2.解析
```
/App/about/blog                       /App/about/msg    
+------------------+                  +-----------------+
| about            |                  | about            |
| +--------------+ |                  | +-------------+ |
| | blog         | |  +------------>  | | msg         | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```
##### 3.目录拆分
观察效果图，容易得出可将图拆分为多个组件。在主要vue中挂载`首页`和`关于`组件，然后在`关于`组件中再次挂载子路由`博客`和`信息`组件。项目中src目录拆分如下：
```
├── src	
│  ├── page	
│  │  ├── index.vue	// 主页组件
│  │  ├── about.vue	// 关于组件
│  │  ├── about	
│  │  │  ├──  blog.vue	// 博客子组件
│  │  │  ├──  msg.vue	// 信息子组件
│  ├── router	
│  │  ├── index.js	
│  ├── App.vue	
│  ├── main.js
```
##### 4.通用代码
从目录中可以轻易的看出每个组件及所代表的模块。其中，index & blog & msg 三个组件均为一个带文字描述的块级元素，此处给出一个示例，不再赘述。
```
<template>
    <div>
        这是xx模块内容
    </div>
</template>
```
##### 5.路由管理
`router/index.js`进行路由管理，代码如下。此处使用懒加载模式处理。开篇已经描述，嵌套路由可以理解为父子路由，不仅如此描述，事实上我们在做路由管理也是这么处理的。***注：在children中的path不必再添加`/`，否则会出错。***
```
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

export default new VueRouter({
    routes: [
        {
            path: '/index',
            component: resolve => require(['@/page/index'],resolve),
        },
        {
            path: '/about',
            component: resolve => require(['@/page/about'],resolve),
            children: [
                {
                    path: 'blog',
                    component: resolve => require(['@/page/about/blog'],resolve),
                },
                {
                    path: 'msg',
                    component: resolve => require(['@/page/about/msg'],resolve),
                }
            ]
        },
    ]
})

```
##### 6.根组件配置
`App.vue`需要挂载`首页`组件和`关于`组件。值得一提的是，由于在点击`关于`的时候，应该直接跳往第一项子级项，也就是`博客`，因此注意路由路径。此外，切记***to:"[路径]" 中的[路径]一定要加上`/`，否则多次路由会出现路由重复而无法正确找到路由问题***。
```
<template>
    <div id="app">
       <header class="header">
           <router-link to="/index">首页</router-link>
           <router-link to="/about/blog">关于</router-link>
       </header>


        <router-view/>
    </div>
</template>

<script>
    export default {
        name: 'App',
    }
</script>

<style type="text/scss" lang="scss">
	 /*略*/
</stylle>
```
##### 7.about组件配置
所谓嵌套，或者说父子路由，在此组件表现出来，也就是将此组件当成父组件，再次挂载两个(或多个)子组件。同样，要注意路径一定要加上`/`。如下：
```
<template>
    <div class="about">
        <div class="left">
            <router-link to="/about/blog">博客</router-link>
            <router-link to="/about/msg">信息</router-link>
        </div>
        <div class="right">
            <router-view></router-view>
        </div>
    </div>
</template>

<script>
    export default {
        name: "about"
    }
</script>

<style scoped type="text/scss" lang="scss">
    .about{
        display: flex;
        .left{
            width: 100px;
            border-right: 1px solid #aaa;
            height: calc(100vh - 46px);
            display: flex;
            flex-direction: column;
            a{
                line-height: 40px;
                text-align: center;
                font-size: 12px;
                text-decoration: none;
                color: #6260ff;
            }
        }
        .right{
            flex: 1;
        }
    }
</style>

```
##### 8.完成
其实步骤就这么多，不过在实际项目中还是要多加动脑灵活运用，理解嵌套路由含义及路由配置规则，一切就会变得简单起来~

-----

#### 注
&emsp;&emsp;有几点注意事项，包括上面也有提到过：
- 子路由要写在`children`下；
- router-link 中，to中的路径要加`/`，代表根路径；
- 每一个子路由都可以嵌套多个组件；
- style中加入scoped属性，防止样式互相干扰。




