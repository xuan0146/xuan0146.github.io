---
title: vue-cli 3.x 打包白屏、清除console及IE白屏问题解决
type: categories
copyright: true
date: 2019-05-06 09:27:27
tags: [vue-cli, vue]
categories: 前端笔记
description: 
---

### 问题
- 打包白屏
- IE白屏
- 打包后清除控制台所有console信息
- js\css压缩问题

<!--more-->

### 解决

#### 问题1. 打包白屏
***描述*** 
`npm run build`打包后，生成`dist`文件夹。从该文件夹打开`dist/index.html`页面，发现页面空白，且控制台报错。原因是路径配置有问题。
***方案***
根目录打开/新建`vue.config.js`，配置路径：
```
module.exports = {
  // 基本路径配置
  publicPath: './'
}
```
重新打包验证，问题得以解决。

#### 问题2. IE白屏
***描述***
在打包完成之后，如果是pc项目，或者移动端低版本兼容项目，则会有许多问题，其中白屏问题困扰着很多开发者，在安卓4.0、IE多个版本环境中体现的尤为别致。
***方案***
1. 下载相关插件
```
npm install @babel/core @babel/plugin-transform-runtime @babel/preset-env es6-promise babel-polyfill --save-dev-dev
```
2. 根目录新建文件`.babelrc`：
```
{
  "presets": ["@babel/preset-env"],
  "plugins": [
    "@babel/plugin-transform-runtime"
  ]
}
```
	*[注: 基于vue-cli 3.x]*
3. 修改根目录下的文件`babel.config.js`：
```
// ... some other codes

// 打包时删除console
const plugins = [];
if (['production', 'prod'].includes(process.env.NODE_ENV)) {
  plugins.push("transform-remove-console")
}

module.exports = {
  presets: [
    [
      '@vue/app',
      {
        "useBuiltIns": "entry",
        polyfills: [
          'es6.promise',
          'es6.symbol'
        ]
      }
    ]
  ],
  
  plugins: plugins
}
```
4. 创建/修改根目录下的文件`vue.config.js`：
```
module.exports = {
	// 显式转义依赖
	transpileDependencies: ['webpack-dev-server/client'],
	
	chainWebpack: config => {
            // 指定入口  es6转es5
            config.entry.app = ['babel-polyfill', './src/main.js'];
	}
}
```
5. 入口引入相关插件`src/main.js`
```
// 解决ie白屏问题
import '@babel/polyfill'
import Es6Promise from 'es6-promise'
Es6Promise.polyfill()

```
6. 重启服务/重新打包
页面正常显示，控制台无报错信息。问题解决。

#### 问题3. 打包后清除控制台所有console信息
***描述***
在开发环境中，我们调试过程中会添加些许的`console.log`或者`debugger`相关代码，来帮助我们完成开发。但是有时候此类代码太多或者马虎了，上线前没有删除干净，那么打包后生产环境中就会在控制台留下相关信息。那么为了避免这一不友好行为，我们采取插件来在打包时，清除所有打印信息。
***方案***
1. 安装插件
```
npm install uglifyjs-webpack-plugin --save-dev
```
2. 配置`vue.config.js`
```
// 去除console
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

const env = process.env.NODE_ENV;

module.exports = {
	// ... other codes

	// 去除console
    configureWebpack: (config) => {
        if (env !== 'development' || env !== 'test') {

            config.plugins.push(
                new UglifyJsPlugin({
                    uglifyOptions: {
                        compress: {
                            warnings: false,
                            drop_debugger: true, // 注释console
                            drop_console: true,
                            pure_funcs:['console.log'] // 移除console
                        },
                    },
                    sourceMap: false,
                    parallel: true,
                }),
            );
        }
    },
}
```
3. 重新打包
配置完成后，重新打包`npm run build`，打开`dist/index.html`验证，console全部清除。

#### 问题4. js\css压缩问题
***描述***
在打包成功后，为了缩小包体积，提高页面响应速度，一般会对包进行压缩，此处主要针对js\css来处理。
***方案***
1. 安装插件
```
npm install compression-webpack-plugin --save-dev
```
2. 配置`vue.config.js`中的configureWebpack
```
// 压缩css、js
const CompressionWebpackPlugin = require('compression-webpack-plugin');
// 要压缩的文件
const productionGzipExtensions = ['js', 'css'];

configureWebpack: (config) => {
	if (env !== 'development' || env !== 'test') {
		config.plugins.push(new CompressionWebpackPlugin({
			algorithm: 'gzip',
			test: new RegExp(`\\.(${productionGzipExtensions.join('|')})$`),
			threshold: 10240,
			minRatio: 0.8,
		}));
	}
}
```
3. 重启服务
完成陪之后重启服务/重新打包，处理完成，搞定收工！

-----

### 附赠
这里附上基于vue-cli3的一些简单配置文件

#### vue.config.js
```
const path = require('path');

const resolve = dir => path.resolve(__dirname, dir);

// const IS_PROD = ['production', 'prod'].includes(process.env.NODE_ENV);

// const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;





// 去除console
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
// 压缩css、js
const CompressionWebpackPlugin = require('compression-webpack-plugin');
// 要压缩的文件
const productionGzipExtensions = ['js', 'css'];
// const productionGzipExtensions = /\.(js|css|json|txt|html|ico|svg)(\?.*)?$/i;


const env = process.env.NODE_ENV;


module.exports = {
    // 基本路径配置
    publicPath: './',

    // 显式转义依赖
    transpileDependencies: ['webpack-dev-server/client'],

    // 配置less
    css: {
        loaderOptions: {
            less: {
                javascriptEnabled: true,
            }
        }
    },

    configureWebpack: (config) => {
        if (env !== 'development' || env !== 'test') {
            config.plugins.push(new CompressionWebpackPlugin({
                algorithm: 'gzip',
                test: new RegExp(`\\.(${productionGzipExtensions.join('|')})$`),
                threshold: 10240,
                minRatio: 0.8,
            }));
            config.plugins.push(
                new UglifyJsPlugin({
                    uglifyOptions: {
                        compress: {
                            warnings: false,
                            drop_debugger: true, // 注释console
                            drop_console: true,
                            pure_funcs:['console.log'] // 移除console
                        },
                    },
                    sourceMap: false,
                    parallel: true,
                }),
            );
        }
    },

    chainWebpack: config => {
        // 指定入口  es6转es5
        config.entry.app = ['babel-polyfill', './src/main.js'];

        // 修复HMR
        config.resolve.symlinks(true);

        // //修复 Lazy loading routes Error
        // config.plugin('html').tap(args => {
        //     args[0].chunksSortMode = 'none';
        //     return args;
        // });

        // 添加别名
        config.resolve.alias
            .set('@', resolve('src'))
            .set('assets', resolve('src/assets'))
            .set('static', resolve('src/static'));

        // //压缩图片
        // config.module
        //     .rule("images")
        //     .use("image-webpack-loader")
        //     .loader("image-webpack-loader")
        //     .options({
        //         mozjpeg: {progressive: true, quality: 65},
        //         optipng: {enabled: false},
        //         pngquant: {quality: "65-90", speed: 4},
        //         gifsicle: {interlaced: false},
        //         webp: {quality: 75}
        //     });

        // // 打包分析
        // if (process.env.IS_ANALYZ) {
        //     config.plugin('webpack-report')
        //         .use(BundleAnalyzerPlugin, [{
        //             analyzerMode: 'static',
        //         }]);
        // }
    },

    // 是否使用包含运行时编译器的 Vue 构建版本
    // runtimeCompiler: true,

    // 去除打包.map后缀文件
    // productionSourceMap: false,

    // devServer: {//跨域
    //     port: 8080,// 端口号
    //     open: true, //配置自动启动浏览器
    //     proxy: {// 配置跨域处理 可以设置多个
    //         '/api': {
    //             target: '',
    //             ws: true,
    //             changeOrigin: true
    //         },
    //     }
    // }
}

```
#### babel.config.js
```
// 打包时删除console
const plugins = [];
if (['production', 'prod'].includes(process.env.NODE_ENV)) {
  plugins.push("transform-remove-console")
}



module.exports = {
  presets: [
    [
      '@vue/app',
      {
        "useBuiltIns": "entry",
        polyfills: [
          'es6.promise',
          'es6.symbol'
        ]
      }
    ]
  ],
  // 配置babel-plugin-import, 用于按需加载组件代码和样式
  plugins: [
    [
      "import",
      { libraryName: "ant-design-vue", libraryDirectory: "es", style: true }
    ]
  ],

}

```
#### .babelrc
```
{
  "presets": ["@babel/preset-env"],
  "plugins": [
    "@babel/plugin-transform-runtime"
  ]
}
```
#### main.js
```
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

// 解决ie白屏问题
import '@babel/polyfill'
import Es6Promise from 'es6-promise'
Es6Promise.polyfill()

// 使用antd-vue Button组件
import { Button } from 'ant-design-vue'

// Button组件
Vue.component(Button.name, Button);


Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')

```