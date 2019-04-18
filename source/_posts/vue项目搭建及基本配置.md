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
![vue运行成功](/images/posts/vue运行成功.png 'vue运行成功')

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
<font size=1 color=#aaa>本章节记录一些vue常用插件，如`axios`、`echarts`、`sass/scss`、`lib-flexible`等等。所有命令均可用`cnpm`替代。如不需要可直接跳过。</font>
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
最后，键入命令`npm run dev`，发现请求数据格式已经成为`Form Data`的格式，并且即使后端不处理跨域问题，也依然正常可用。原因是`proxy`作为中间层，先将数据请求回本地，此过程是中间层node请求后台，不存在跨域。然后请求过来的数据放在本地，前端再去请求这个数据，就可以解决跨域问题了。
##### 4.echarts可视化
&emsp;&emsp;如今相信大家不少项目需要用到数据可视化吧？各种统计图表、炫酷可视化效果等等，都需要一个不错的插件去支撑，比如`D3`、`AnyChart`、`echarts`、`HighCharts`等等。本案例用百度的`echarts`作为案例讲解。
- 安装依赖
惯例，先装依赖。命令行工具执行`npm install echarts --save-dev`。
- 全局配置
安装完成后，全局引入。引入方法：进入`项目/src/main.js`，如下操作：
```
import echarts from 'echarts'

Vue.prototype.$echarts = echarts
```
- 使用
在`***.vue`组件中即可正常使用。我在此处用`Hello.vue`写示例：
```
//template
<div id="demo"></div>

//script
let myChart = this.$echarts.init(document.getElementById('demo')); //初始化
//myChart.clear();	//根据需求配置
myChart.setOption({ // 图表配置
  xAxis: {
    type: 'category',
    data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
  },
  yAxis: {
    type: 'value'
  },
  series: [{
    data: [820, 932, 901, 934, 1290, 1330, 1320],
    type: 'line',
    smooth: true
  }]
});

//style
#demo{
  width: 500px;
  height: 500px;
}
```
- 效果图
![echarts效果图](/images/posts/echartsDemo.png 'echarts效果图')
- 按需引入
试过之后会发现echarts的包很大，会导致打包之后文件过大，因此如果不是刚需的情况下，建议按需引入来完成页面，会节省许多资源及开销。首先，将刚才`项目/src/main.js`中的两行代码注释掉。接下来，依然以`Hello.vue`举例，如下：
```
//template && style 不作变动

//script
const echarts = require('echarts/lib/echarts');	//基本模板
require('echarts/lib/chart/line');	//折线图组件
require('echarts/lib/component/title');	//标题组件

 // 初始化实例
let myChart = echarts.init(document.getElementById('demo'));
// 图表配置
myChart.setOption({
  title: { text: '引入title组件可使用标题' },
  * * * * * * * *
  * 别的配置不变 *
  * * * * * * * *
});
```
##### 5.lib-flexible | px2rem-loader
&emsp;&emsp;lib-flexible作用在于写页面的时候实现自适应效果，包括文图排本等。需要配合`px2rem-loader`使用。步骤如下：
- 安装依赖
```
npm i lib-flexible --save-dev

npm install px2rem-loader --save-dev
```
- 引入
`项目/src/main.js`中做如下配置：
```
import 'lib-flexible
```
- 主页添加视口配置
`项目/index.html`中的`head`中添加：
```
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```
- px2rem配置
`项目/build/utils.js`中如下配置，然后重启项目即可：
```
//自适应字体配置
const px2remLoader = {
  loader: 'px2rem-loader',
  options: {
    remUnit: 37.5   //设计稿的宽度除以 10，现阶段一般设计稿的宽度都为750px。如果基于iPhone5设计则为32.0(320 / 10 = 32)
  }
}

function generateLoaders (loader, loaderOptions) {
  const loaders = options.usePostCSS ? [cssLoader, postcssLoader, px2remLoader] : [cssLoader, px2remLoader]

  * * *
}
```
- 更多关于***px2rem***的介绍请移步**[官网](https://www.npmjs.com/package/px2rem)**

------
#### 四、项目打包
<font size=1 color=#aaa>本章节介绍`打包`以及去除打包后资源中的`.map`文件。</font>
开发完成后，怎么打包到线上呢？emm...this is a problem.
##### 1.打包
命令行工具`cd`到该项目下，执行`npm run build`来构建项目：
```
E:\stydy\blog>npm run build

                                                  Asset       Size  Chunks                    Chunk Names
               static/js/vendor.38756a8637fc194013f9.js     442 kB       0  [emitted]  [big]  vendor
                  static/js/app.0b5326236ed4caddb252.js     1.1 kB       1  [emitted]         app
             static/js/manifest.2ae2e69a05c33dfc65f8.js  857 bytes       2  [emitted]         manifest
    static/css/app.26e0081e2c66d97635076ad7fefbb5b6.css  164 bytes       1  [emitted]         app
static/css/app.26e0081e2c66d97635076ad7fefbb5b6.css.map  348 bytes          [emitted]
           static/js/vendor.38756a8637fc194013f9.js.map    2.38 MB       0  [emitted]         vendor
              static/js/app.0b5326236ed4caddb252.js.map    8.47 kB       1  [emitted]         app
         static/js/manifest.2ae2e69a05c33dfc65f8.js.map    4.97 kB       2  [emitted]         manifest
                                             index.html  571 bytes          [emitted]

  Build complete.
```
##### 2.位置
打包完成后，去哪儿了呢？然后会发现在项目路径下，出现了一个`dist`的文件夹，这里面就是所谓的*** 包 ***了。如下：
```
├── project	
│  ├── build	
│  ├── config	
│  ├── dist		// 包	
│  │  ├── static	// 压缩后的资源
│  │  ├── index.html	// 主页
│  ├── node_modules	
│  ├── src	
│  ├── static	
│  ├── test	
│  ├── .babelrc	
│  ├── .editorconfig	
│  ├── .gitignore	
│  ├── .postcssrc.js	
│  ├── index.html	
│  ├── package-lock.json	
│  ├── package.json	
│  ├── README.md	
```
##### 3.问题&解决
细心的你可能发现了，`dist/static/css(或js)`里面，每个css文件和js文件下有一个同名的`.map`文件，非常占空间，那它是做什么的呢？又怎么消除呢？
- 作用  
查过资料的小伙伴知道了，`.map`文件的作用就是：**项目打包后，代码都是经过压缩加密的，如果运行时报错，输出的错误信息无法准确得知是哪里的代码报错。有了map就可以像未加密的代码一样，准确的输出是哪一行哪一列有错。**
- 如何解决
那么该如何处理这些文件呢？进入`项目/config/index.js`，定位到`productionSourceMap`，将其值改为`false`即可。如下：
```
productionSourceMap: false,
```
- 重新打包验证
在命令行工具重新执行`npm run build`，发现`.map`文件已经消失，问题解决。如下：
```
E:\stydy\blog>npm run build

                                              Asset       Size  Chunks                    Chunk Names
           static/js/vendor.38756a8637fc194013f9.js     442 kB       0  [emitted]  [big]  vendor
              static/js/app.3c96ecd23cc6dc073a3e.js    1.05 kB       1  [emitted]         app
         static/js/manifest.2ae2e69a05c33dfc65f8.js  799 bytes       2  [emitted]         manifest
static/css/app.26e0081e2c66d97635076ad7fefbb5b6.css   95 bytes       1  [emitted]         app
                                         index.html  571 bytes          [emitted]

  Build complete.
```
------
#### 五、包优化
&emsp;&emsp;在项目逐渐壮大之后，虽然说上面已经讲述过了如何去除`.map`文件，但是其中许多`.js`文件的体积也不容小觑。尤其是发布到线上以后，某些css/js单个文件体积极可能超上兆的大小，如果项目服务器带宽不够，但是页面迸发量不低的话，分分钟就可能挂掉。有没有什么方法能进一步将包优化呢？
##### 1.gzip打包压缩
- 安装gzip
惯例，打开命令行工具，安装`gzip`
```
npm install compression-webpack-plugin@1.1.11 --save-dev
```
- 配置
`项目/config/index.js`中，定位到`productionGzip`，改为`true`：
```
productionGzip: true,
```
- 重新打包
`npm run build`走起，会发现打包的文件中多了同名的`.gz`文件，体积大概压缩了2/3，很棒的体验。如下：
```
E:\stydy\blog>npm run build

                                              Asset       Size  Chunks                    Chunk Names
           static/js/vendor.38756a8637fc194013f9.js     442 kB       0  [emitted]  [big]  vendor
              static/js/app.3c96ecd23cc6dc073a3e.js    1.05 kB       1  [emitted]         app
         static/js/manifest.2ae2e69a05c33dfc65f8.js  799 bytes       2  [emitted]         manifest
static/css/app.26e0081e2c66d97635076ad7fefbb5b6.css   95 bytes       1  [emitted]         app
                                         index.html  571 bytes          [emitted]
        static/js/vendor.38756a8637fc194013f9.js.gz     155 kB          [emitted]

  Build complete.
```
- nginx配置
虽然很给力的节省了许多资源，但是需要运维配合，在`gninx.conf`配置文件中做如下配置：
```
http {  
  gzip on;
  gzip_disable "msie6"; 
  gzip_vary on; 
  gzip_proxied any;
  gzip_comp_level 8;  //压缩级别
  gzip_buffers 16 8k;
  #gzip_http_version 1.1;
  gzip_min_length 100; //不压缩临界值
  gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
}
```
##### 2.采用cdn加速
&emsp;&emsp;顾名思义，即将一些比较大的插件放在第三方服务器上去加载，这样不仅快很多，也能更加节约自己的服务器资源及宽带资源。我们一般选用`unpkg`或者`BootCDN`来作为第三方。在此处我们使用`BootCDN`来作为第三方加速使用。想了解更多相关知识请点击**[unpkg](https://unpkg.com)** **[BootCDN](https://www.bootcdn.cn/)**。
- 查找包
打开**[BootCDN](https://www.bootcdn.cn/)**，在搜索栏搜索想要的第三方资源。如，我想要替换的是`echarts`、`Vue`、`axios`，则依次搜索`echarts`...，然后查找我想要的版本，然后`复制<script>标签`。
- 插入
在`项目/index.html`中将标签插入，如下：
```
<body>
<div id="app"></div>
//插入在这里
<script src="https://cdn.bootcss.com/vue/2.5.20/vue.min.js"></script>
<script src="https://cdn.bootcss.com/vue-router/3.0.1/vue-router.min.js"></script>
<script src="https://cdn.bootcss.com/echarts/4.2.1-rc1/echarts-en.common.js"></script>
<script src="https://cdn.bootcss.com/axios/0.19.0-beta.1/axios.js"></script>
</body>
```
- webpack配置
打开`项目/build/webpack.base.conf.js`中，添加`externals`:
```
module.exports = {
  context: path.resolve(__dirname, '../'),
  //..	
  externals:{
    'vue': 'Vue',
    'vue-router': 'VueRouter',
    'echarts':'echarts',
    'axios':'axios',
  }

}
```
- main配置
打开`项目/src/main.js`，将`Vue`等注释掉即可。不注释也可。
```
// import Vue from 'vue'
```
- 打包
最后，见证奇迹的时刻就要到了~
```
E:\stydy\blog>npm run build

                                              Asset       Size  Chunks             Chunk Names
           static/js/vendor.1f9d0533037be66c61cf.js    12.3 kB       0  [emitted]  vendor
              static/js/app.912fd568ff845fde7c09.js    1.36 kB       1  [emitted]  app
         static/js/manifest.2ae2e69a05c33dfc65f8.js  799 bytes       2  [emitted]  manifest
static/css/app.edc64bd802cdc531977a30fcc8a9d6e1.css   62 bytes       1  [emitted]  app
                                         index.html  876 bytes          [emitted]
        static/js/vendor.1f9d0533037be66c61cf.js.gz    4.62 kB          [emitted]

  Build complete.
```
- 搞定
上次打包，`vendor`大小为**442kB**，本次打包，疯狂缩小至**12.3kB**。神不神奇~厉不厉害~

------
#### 结语
&emsp;&emsp;到这里，vue项目从环境配置、项目搭建，到结构介绍、常用插件依赖安装使用做了比较详细的介绍，最后将项目优化也做了一定的补充。
&emsp;&emsp;<font color=deepskyblue>学而时习之，温故而知新。</font>至此，以上。


