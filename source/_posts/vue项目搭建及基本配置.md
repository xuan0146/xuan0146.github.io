---
title: vue项目搭建及基本配置
type: categories
copyright: false
date: 2019-03-27 17:02:16
tags: [vue, vue-cli]
categories: 前端笔记
description:
---
#### 前言
<font size=2 color=#aaa>注：适用于windows系统</font>
**本文讲述要点：**
1. vue-cli项目搭建基本环境配置；
2. 常用插件描述及分享；
3. 项目运行、调试及打包；
4. 项目配置及优化；

<!--more-->
**适用人群：**
1. vue小白；
2. 有html\css\js编程基础；
3. vue项目整体优化（打包后压缩空间）；

--------
#### 一、准备阶段
<font size=2 color=#aaa>准备阶段会将项目环境配置完毕,包含`node`、`npm`、`webpack`和`vue-cli`。已经完成的小伙伴可以跳过此步骤看第二阶段。</font>
##### 1.安装`node`及`npm`
&emsp;&emsp;从node.js中文网下载并安装[nodejs](http://nodejs.cn/download/ 'nodejs')点击下载`.msi`的32/64位安装包，然后双击本地文件一路点击`下一步`完成`nodeJs`的安装即可。<font size=2 color=#ff6666>注意安装8.10以上版本哟!</font>
安装完成后，打开命令行工具，输入`node -v`，如下：
```
E:\>node -v
v8.12.0		//版本号
```
顺便在此处继续输入`npm -v`
```
E:\>npm -v
6.4.1		//版本号
```
你会惊讶的发现`npm`已经神奇的安装完成了。这是因为`npm`在安装`nodeJs`的时候顺带已经装好了。
##### 2.安装`webpack`
<font size=2 color=blue>注：以下所有`npm`操作，由于国内速度较慢甚至出现失败，可用淘宝镜像`cnpm`替代。下列所有`npm`命令，用`cnpm`替代即可。方式如下：</font>
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

&emsp;&emsp;继续刚才的命令行工具，输入`npm install webpack -g`，`-g代表全局安装`。安装完成后继续输入`webpack -v`，出现相应版本号，代表安装成功。
我在安装时出现如下状况：
```
One CLI for webpack must be installed. These are recommended choices, delivered as separate packages:
 - webpack-cli (https://github.com/webpack/webpack-cli)
   The original webpack full-featured CLI.
We will use "npm" to install the CLI via "npm install -D".
Do you want to install 'webpack-cli' (yes/no):
```
解决方案是：输入`npm install  webpack-cli -g`。安装成功后执行`webpack -v`查看版本号，如下：
```
E:\>npm install  webpack-cli -g
*
*  安
*  装				省
*  过				略
*  程
*

E:\>webpack -v
4.29.6	//版本号
```
##### 3.安装vue脚手架`vue-cli`
&emsp;&emsp;继续上面的命令行工具。接下来就是安装配置今天的主角了，vue脚手架工具`vue-cli`。方法一样，键入命令行`npm install vue-cli -g`全局安装，然后输入`vue -V`<font size=1 color=red>注：是大写的`-V`</font>检测是否安装成功。如下：
```
E:\>vue -V
2.9.6		//版本号
```
##### 4.小结
&emsp;&emsp;至此，我们已经完成了准备阶段的工作。主要完成的是`node`、`npm`、`webpack`和`vue-cli`四个工具的环境配置。目前为止，我们在命令行工具的总结应该是如下：<font color=#aaa size=1>版本号不必一致</font>
```
E:\>node -v
v8.12.0

E:\>npm -v
6.4.1

E:\>webpack -v
4.29.6

E:\>vue -V
2.9.6
```
---------
#### 二、项目搭建
<font size=1 color=#aaa>本阶段讲述`vue-cli`项目搭建及相关配置，包含`创建项目`、`修改基础配置`、`项目运行`三个模块，可自行选择阅读或跳过。</font>
##### 1.项目创建
&emsp;&emsp;建立一个空文件夹，最好以纯英文命名。例，我项目路径为`E>stydy`。打开命令行工具，`cd`到该文件目录下，执行`vue init webpack name`，`name`可替换为你要创建的项目名。回车之后，会要求输入一系列的信息，大部分可直接回车略过，注释如下，没有注释的回车略过即可：
```
E:\stydy>vue init webpack blog

? Project name blog	//项目名
? Project description A Vue.js project	//项目简介
? Author xuan0146 <zxm0146@163.com>	//作者
? Vue build runtime
? Install vue-router? Yes	//使用项目路由
? Use ESLint to lint your code? No	//不开启ESLint语法检测 注意选No
? Set up unit tests Yes
? Pick a test runner jest
? Setup e2e tests with Nightwatch? Yes
? Should we run `npm install` for you after the project has been created? (recommended) npm

   vue-cli · Generated "blog".
```
最后一步选择`npm`或者`yarn`则会自动执行`npm install`安装项目所有依赖包。不选择的话，自己安装也是同样效果。安装步骤为：
1. `cd`到项目路径；
2. 执行`npm install`；
3. 等待完成安装即可;

##### 2.项目介绍
&emsp;&emsp;`cd`到该项目，或者打开文件夹，项目目录及备注如下：
```
├── project	//项目目录
│  ├── build	//webpack相关配置
│  │  ├── build.js	//生产环境构建
│  │  ├── check-versions.js	//版本检查
│  │  ├── utils.js	//构建相关工具
│  │  ├── vue-loader.conf.js	//处理vue文件的配置信息
│  │  ├── webpack.base.conf.js	//webpack基础配置
│  │  ├── webpack.dev.conf.js	//webpack开发环境配置
│  │  ├── webpack.prod.conf.js	//webpack生产环境配置
│  ├── config	//vue基本配置
│  │  ├── dev.env.js	//开发环境配置
│  │  ├── index.js	//主要配置
│  │  ├── prod.env.js	//生产环境配置
│  │  ├── test.env.js	//测试环境配置
│  ├── node_modules	//依赖包 忽略
│  ├── src	//项目核心文件
│  │  ├── assets	//静态资源 如公用js\css\image\媒体资源等
│  │  ├── components	//公用组件
│  │  ├── router	//项目路由
│  │  │  ├── index.js	//路由控制文件
│  │  ├── App.vue	//根组件
│  │  ├── main.js	//入口文件
│  ├── static	//静态资源
│  ├── test	//模拟测试
│  ├── .babelrc	//babel参数
│  ├── .editorconfig	//代码格式
│  ├── .gitignore	//git上传相关配置
│  ├── .postcssrc.js	//css相关工具
│  ├── index.html	//主页
│  ├── package-lock.json	//记录当前状态下实际安装的各个npm package的具体来源和版本号
│  ├── package.json	//项目信息
│  ├── README.md	//项目说明
```
##### 3.启动项目
&emsp;&emsp;命令行工具键入`npm run dev`，等待片刻，直到提示如下，则表示启动成功：
```
E:\stydy\blog>npm run dev

* * * * * * * *
* 启动过程忽略 *
* * * * * * * *

Your application is running here: http://localhost:8080
```
然后复制`http://localhost:8080`，在浏览器地址栏输入该url，进入如下页面：
![vue运行成功](/images/posts/vue运行成功.jpg 'vue运行成功')

##### 4.移动端调试
&emsp;&emsp;有的人可能要问了，如果我做移动端项目，不能用localhost去访问了吧？当然不能，只需要稍微改一下配置即可。当然，端口号8080一样可以修改，不过一般不做变动。
&emsp;&emsp;用代码工具（自己习惯的IDE）打开本项目。首先查看本机电脑ip,键入命令`ipconfig`，查看如下：
```
E:\stydy\blog>ipconfig
* *
* *
以太网适配器 以太网:

   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::da7:800d:d148:e06%10
   IPv4 地址 . . . . . . . . . . . . : 192.168.200.127
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . : fe80::48:5112:9520:564f%10
                                       192.168.200.1									   
```
&emsp;&emsp;其中，`IPv4 地址`即为我们所需要的ip。在`项目/config/index.js`中，找到`host: 'localhost'`，改成`IPv4 地址`，例:我的地址为`192.168.200.127`:
```
module.exports = {
  dev: {
    host: '192.168.200.127', 	//localhost改为ip
    port: 8080,		//端口号
  }
}
```
&emsp;&emsp;然后，我们重新启动项目。注意上次项目启动后，由于修改了配置文件，因此需要停止项目然后重新启动。停止项目的命令为`Ctrl + C`。重启项目后，如下：
```
E:\stydy\blog>npm run dev
* * * * * * * *
* 启 动 过 程 *
* * * * * * * *
Your application is running here: http://192.168.200.127:8080
```
&emsp;&emsp;<font size=2 color=#aaa>注意要在同一网络下</font>
&emsp;&emsp;最后，我们复制该地址，即可在所有端`PC`、`手机`、`pad等`查看该项目啦~

------
#### 三、常用插件
<font size=1 color=#aaa>本章节记录一些vue常用插件，如`axios`、`echarts`、`sass/scss`、`lib-flexible`、`layer`等等。所有命令均可用`cnpm`替代。如不需要可直接跳过。</font>
##### 1.sacc/scss
**用途**：可用`sass/scss`进行css书写代码。命令行工具依次键入如下命令：
```
npm install node-sass --save-dev
npm install sass-loader --save-dev
```
安装完成后，`.vue`里的`style`里添加`lang="scss"`来指定语言，即可用`sass/scss`编写css代码。如下：
```
<style lang="scss" scoped>
...
</style>
```
##### 2.axios
**用途**：平时前后端交互使用`Ajax`，在vue官方，给出`vue-resource`和`axios`的作为`ajax`的替代。在此处使用vue官方推荐首选的`axios`作为案例讲述。
- 安装
```
npm install axios
```
- 全局引入
在`项目/src/main.js`中引入如下代码，然后随意走个请求，发现……报错？并且在页面F12控制台`Newwork>Headers`里面发现数据并没有Form Data形式提交，如何处理呢？见下一步解决。
```
import axios from 'axios'
```

- 解决数据格式问题
```
//完整请求代码  post为例
this.$axios.post('url', {	//路径
  key1: 'value',		//参数
  key2: 'value'		//参数
}, 

/* *
* 添加如下代码
* * * * * * start * * * * * 
{
  transformRequest: [function (data) {
    let ret = '';
    for (let it in data) {
	  ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&';
    }
    return ret
    }
  ],
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded'
  }
}
/* * * * * * end * * * * *

).then((response) => {	//返回数据
  console.log(response)
}).catch((error) => {	//请求异常捕捉
  console.log(error)
})
```
##### 3.axios解决方案
&emsp;&emsp;另外一个axios数据格式问题的解决方案。 `重磅推荐★★★★★`此方案不仅解决数据格式问题，还顺便解决了跨域问题。第一步当然还是安装`axios`，同上一步。然后，安装`qs`，命令行工具键入`npm install qs --save-dev`。然后在`项目/src/main.js`中配置如下：
```
import axios from 'axios'
import qs from 'qs'	

Vue.prototype.$axios = axios;
Vue.prototype.$qs = qs;
```
然后在`项目/config/index.js`中，找到`proxyTable`，配置及说明如下：
```
proxyTable: {
  '/api': {
    //设置url公用部分
    target: 'http://192.168.1.100:8080/',	
    changeOrigin: true,   // 如果接口跨域，需要进行这个参数配置
    pathRewrite: {
      '^/api': ''      //这里理解成用‘/api’代替target里面的地址
    }
  }
},
```
配置完成之后，在`.vue`文件中，请求用例如下：
```
//参数
let params = this.$qs.stringify({
  key: value
});
//请求
this.$axios.post('/api/test', params).then((response) => {
  console.log(response);
}).catch((error) => {
  console.log(error);
})
```
最后，键入命令`npm run dev`，发现请求数据格式已经成为`Form Data`的格式，并且及时后端不处理跨域问题，也依然正常可用。原因是`proxy`作为中间层，先将数据请求回本地，此过程是中间层node请求后台，不存在跨域。然后请求过来的数据放在本地，前端再去请求这个数据，就可以解决跨域问题了。


<center>未完待续...</center>



















































