---
title: vue-cli3.x及相关配置
type: categories
copyright: true
date: 2019-05-05 17:10:33
tags: [vue-cli3, vue]
categories: 前端笔记
description:
---
### vue-cli 3.x

#### 1.安装
```
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

#### 2.环境(windows)
```
E:\stydy>node -v
v8.12.0

E:\stydy>webpack -v
4.29.6

E:\stydy>npm -v
6.4.1

E:\stydy>vue -V
3.7.0
```

#### 3.创建项目
我在此处想使用`ant-design-vue`，所以创建了一个`antd`的项目：
```
vue create antd
```

#### 4.项目配置
我选择的项目配置如下：
```
Vue CLI v3.7.0
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, CSS Pre-processors
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Less
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedicated config files
? Save this as a preset for future projects? No
? Pick the package manager to use when installing dependencies: NPM
```

#### 5.运行
依赖安装完成后，cd到该项目路径下，运行：
```
 $ cd antd	
 $ npm run serve
 
 
 DONE  Compiled successfully in 4229ms
  App running at:
  - Local:   http://localhost:8080/
  - Network: http://192.168.100.111:8080/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```
此时浏览器会访问 http://localhost:8080/ ，看到 Welcome to Your Vue.js App 的界面就算成功了。

#### 6.目录结构
```
├── README.md
├── babel.config
├── package.json
├── package-lock.json
├── postcss.config.js
├── public
│   ├── favicon.ico
│   └── index.html
├── src
│   ├── assets
│   │   └── logo.png
│   ├── components
│   │   └── HelloWorld.vue
│   ├── App.vue
│   └── main.js
└── .gitignore
```

-----

### 使用ant-design-vue
由于在实际开发工程中，加载全部样式/组件会影响前端性能，因此我们直接采取按需加载组件及样式。

#### 1.安装
```
npm install ant-design-vue --save-dev

npm install less less-loader --save-dev
```
#### 2.引入`src/main.js`
```
// 以 Button 组件为例
import { Button } from 'ant-design-vue'
Vue.component(Button.name, Button);
```
#### 3.组件使用
```
<template>
  <div id="app">
    <a-button type="danger">Danger</a-button>
  </div>
</template>
```

#### 4.babel-plugin-import
##### 4.1安装
```
npm install babel-plugin-import --save-dev
```
##### 4.2修改`babel.config.js`
```
module.exports = {
  presets: [
   '@vue/app',
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
##### 4.3报错解决方案
此时重启项目，会遇到报错情况。以下两种方案***`任选其一即可`***。两种方案：
- *配置`vue.config.js`*
在根目录下新建`vue.config.js`：
```
module.exports = {
    // 配置less
    css: {
        loaderOptions: {
            less: {
                javascriptEnabled: true,
            }
        }
    },
}
```
- *修改`babel.config.js`*
```
  // ...
  plugins: [
    [
      "import",
      { libraryName: "ant-design-vue", libraryDirectory: "es", style: 'css' }
    ]
  ]

```
##### 4.4完成
保存，重启项目：
```
npm run serve
```
可以看到，antd 组件的 js 和 css代码都会按需加载。
