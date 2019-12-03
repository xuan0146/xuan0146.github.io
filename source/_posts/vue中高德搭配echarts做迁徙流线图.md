---
title: vue中高德搭配echarts做迁徙流线图
type: categories
copyright: true
date: 2019-12-03 15:36:06
tags: [js, 高德, vue, echarts, 流线, 地图, 迁徙]
categories: 前端笔记
description:
---
### 前言
此系列共分为以下几篇
>- 《vue中高德地图的使用》
>- 《vue中基于高德的多行政区域覆盖》
>- **《vue中高德搭配echarts做数据迁徙流线图》** *（本篇）*

### 本篇效果
![效果展示](/images/posts/流线迁徙.gif '流线迁徙')
<!--more-->

------

### 实现步骤
#### 1. 思路分析
第一篇的基础讲解，加上上一片的行政区域行政区域展示（描边及背景），其实可以完成很多炫酷效果了。本篇讲述系列篇的最后一篇：vue中高德搭配echarts做数据迁徙流线图。由于流线图使用的曲线是贝塞尔曲线，而在高德中无法直接简单粗暴的使用，想要完成此效果的话可能要进行一些算法操作才能完成该效果。在节约时间成本的条件下，找到了在echarts中完成该操作。简单的说，就是用echarts的功能，搭配高德的经纬度当背景板，来完成此操作。
> 步骤分析
> 1. echarts安装 引入
> 2. 高德背景板设置（经纬度、中心点等）
> 3. 挂载数据
>

#### 2. 代码分析
##### echarts安装 引入
- 安装：`npm install echarts --save-dev`
- 引入： `main.js` => 
```js
// 全局挂载echarts
import echarts from 'echarts'

Vue.prototype.$echarts = echarts;
```

##### 高德背景板设置
在此中，可以自定义的调整一些关于地图背景板的配置，如样式、3D效果、旋转、动画等，在demo中有所注释，自己根据需要配置即可。
```js
// 加载 amap 组件
amap: {
    // 高德地图中心经纬度
    center: [105.397428, 38.90923], //中心点
    // 高德地图缩放
    zoom: 4,
    // 启用resize
    resizeEnable: true,
    // 自定义地图样式主题
    mapStyle: "amap://xxxxxx", // 地图主题 自己申请即可 我的是极夜蓝主题
    // viewMode:'3D',//开启3D视图,默认为关闭
    // expandZoomRange:true,
    // zooms:[3,20],
    // pitch: 40
},
```

##### 数据
此处写了两组数据，一组为`flyLine`代表流线数组，另一组为`goals`代表目标点。其中流线数据中坐标点有两个，是起始和终点的经纬度坐标。如下所示：
```js
data() {
    return {
        flyLine: [
            {coords: [[116.496437,39.913523], [123.499706,41.857793]]},
            {coords: [[116.496437,39.913523], [86.113232,43.684254]]},
            {coords: [[116.496437,39.913523], [87.343701,35.720308]]},
            {coords: [[116.496437,39.913523], [101.406201,24.177443]]},
            {coords: [[116.496437,39.913523], [111.249951,24.976714]]},
            {coords: [[116.496437,39.913523], [107.206982,33.843608]]},
            {coords: [[116.496437,39.913523], [115.468701,43.557002]]},
            {coords: [[116.496437,39.913523], [107.031201,37.134586]]},
            {coords: [[116.496437,39.913523], [99.648388,38.522914]]},
            {coords: [[116.496437,39.913523], [124.081982,49.361353]]},
        ],
        goals: [
            [123.499706,41.857793],
            [86.113232,43.684254],
            [87.343701,35.720308],
            [101.406201,24.177443],
            [111.249951,24.976714],
            [107.206982,33.843608],
            [115.468701,43.557002],
            [107.031201,37.134586],
            [107.031201,37.134586],
            [99.648388,38.522914],
            [124.081982,49.361353]
        ]
    }
},
```
##### 数据渲染
最后一步，其实是echarts的常规操作，可以配置流线、提示、流线背景、特效、效果、等等等等，依然是配置自己需要的即可，毕竟是搭配来的地图，特效多了会影响性能～如下：
```html
<div class="map" ref="map">

</div>
```
```js
mapInit () {
    let _this = this;
    let option = {
        // 加载 amap 组件
        amap: {
            // 高德地图中心经纬度
            center: [105.397428, 38.90923], //中心点
            // 高德地图缩放
            zoom: 4,
            // 启用resize
            resizeEnable: true,
            // 自定义地图样式主题
            mapStyle: "amap://styles/bfb1bb3feb0db7082367abca96b8d214", //地图主题
            // viewMode:'3D',//开启3D视图,默认为关闭
            // expandZoomRange:true,
            // zooms:[3,20],
            // pitch: 40
        },
        tooltip: {
            trigger: 'item',
            show: false
        },
        animation: false,
        series: [
            // 流线
            {
                coordinateSystem: "amap", // 该系列使用的坐标系是高德地图的坐标系
                type: "lines", // 该类型用于地图上路线的绘制
                zlevel: 1, // 相当于z-index
                effect: { // 线特效的配置
                    show: true,  // 是否显示特效
                    period: 5, // 特效动画的时间
                    trailLength: 0.05, // 特效尾迹的长度 0-1
                    color: "#3437ff", // 特效的颜色
                    symbolSize: 5 // 特效的大小
                },
                lineStyle: { // 线的颜色
                    normal: {
                        color: "rgba(47,68,200,0.1)",
                        width: 2,
                        curveness: 0.2
                    }
                },
                data: _this.flyLine,
            },
            // 目标点
            {
                name: '目标点',
                type: "scatter",
                // 使用高德地图坐标系
                coordinateSystem: "amap",
                // 数据格式跟在 geo 坐标系上一样，每一项都是 [经度，纬度，数值大小，其它维度...]
                data:  _this.goals,
                symbolSize: 3,
                encode: {
                    value: 2
                },
                label: {
                    normal: {
                        formatter: '{b}',
                        position: 'right',
                        show: false
                    },
                    emphasis: {
                        show: true
                    }
                },
                itemStyle: {
                    normal: {
                        color: 'rgba(98,122,200,0.78)'
                    }
                }
            },

        ],

    };
    let chart = this.$echarts.init(_this.$refs.map);
    chart.setOption(option);
},
```
至此，目的完成：
![效果展示](/images/posts/流线迁徙.png '流线迁徙')
