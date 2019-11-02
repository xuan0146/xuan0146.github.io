---
title: vue中使用高德地图开发
type: categories
copyright: true
date: 2019-11-02 10:43:31
tags: [vue, 高德地图]
categories: 前端笔记
description: 
---
## 概述
&emsp;&emsp;最近两年比较热门的一个名次叫“数据可视化”，在前端方向大致体现为`地图数据展示`以及`图表类数据展示`。那么，如何在vue中使用基于高德地图来做地图数据可视化以及加上e Charts的图表类型数据可视化呢？接下来会分三篇文章来进行概述，最终实现下图内容：

![最终效果](/images/posts/vue+amap+echarts示例.gif '最终效果')

<!--more-->
共分为以下几篇
>- **《vue中高德地图的使用》** *（本篇）*
>- 《vue中基于高德的多行政区域覆盖》
>- 《vue中高德搭配echarts做数据迁徙流线图》

接下来，开始第一篇，实现效果如下：

![实现效果](/images/posts/地图01.png '实现效果')

## vue中高德地图的使用
### 1. 准备工作
1. vue搭建
2. 高德开发key值申请（[链接：高德开放平台](链接：https://lbs.amap.com/api/yuntu/guide/create-project/permission/)），key值请放入以下需要对号入座的地方
3. 地图样式挑选，需要申请到key值后，在高德开放平台自动移地图模板处挑选配置。此处用`极夜蓝`模板来示例，也可选择其他模板。然后发布=>复制与分享=>辅助出来样式ID。如：`bb7f5165253f57515d7ba226b2******`，然后放入以下需要对号入座的地方
4. 注：第二步和第三步申请的两个值需要对应使用

### 2. 地图引入项目
#### 入口文件index.html引入标签:
```html
<script src="https://webapi.amap.com/maps?v=1.4.15&key=这里放入key值"></script>
<script src="//webapi.amap.com/ui/1.0/main.js?v=1.0.11"></script>
```
#### vue.config.js中配置:
```js
module.exports = {
    // ...

    configureWebpack(config) {
        // ...

        config.externals = {
            'AMap': 'AMap' // 高德地图配置
        }
    }
}
```

#### 使用该功能的组件中引入：
```js
import AMap from "AMap"
```

### 3. 使用
地图初始化即可：
```html
<div class="map" id="map">
```

```js
// 引入地图组件
import AMap from "AMap"
// 初始化
MapInit () {
    let _this = this;
        let map = new AMap.Map("map", {
        center: [105.397428, 35.90923],
        mapStyle: "amap://styles/这里放入样式ID", //设置地图的显示样式
        resizeEnable: true,
        zoom: 5
    });
}
```

得到初始化的地图：
![初始化地图](/images/posts/地图02.png '初始化地图')

#### 4. 展示点
其实展示点在地图中叫做`地理编码`，意思是将某个点的经纬度在地图中展现出来。与之类似的，高德也提供逆地理编码服务，此处不在我们示例范围内，有兴趣的可以自行在官方API中查看。
接下来展示数据点：
```js
// data中,location是地理编码，color 1绿2黄3红
dataList: [
    { location: "113.772,22.785", color: 1 },
    { location: "114.772,22.785", color: 3 },
    { location: "116.410778,39.897614", color: 2 },
    { location: "123.499706,41.857793", color: 2 },
    { location: "111.722363,40.868346", color: 1 },
    { location: "103.987988,30.662124", color: 3 }
]
```

```js
// 初始化
let _this = this;
let map = new AMap.Map("adMap", {
    center: [105.397428, 35.90923],
    mapStyle: "amap://styles/bb7f5165253f57515d7ba226b25ad7ec", //设置地图的显示样式
    resizeEnable: true,
    zoom: 5
});
// 根据color状态展示不同的图片
const style = [
    {
        // 1
        url: "此处放一张图的url",
        anchor: new AMap.Pixel(5, 5),   // 位移
        size: new AMap.Size(10, 10) // 图片大小
    },
    {
        // 2
        url: "此处放另外一张图的url",
        anchor: new AMap.Pixel(5, 5),
        size: new AMap.Size(10, 10)
    },
    {
        // 3
        url: "此处再放一张图的url",
        anchor: new AMap.Pixel(5, 5),
        size: new AMap.Size(10, 10)
    }
];

let locationData = []; //存放经纬度的数组
let status = null;
for (let i = 0; i < _this.dataList.length; i++) {
let locationArr = _this.dataList[i].location.split(",");
// 判断需要展示的颜色类型
if (_this.dataList[i].dataList === 1) {
    status = 0; //根据下标来相对应样式 例如：绿色对应style数组中的第0个样式
} else if (_this.dataList[i].color === 2) {
    status = 1;
} else if (_this.dataList[i].color === 3) {
    status = 2;
}

locationData.push({
    lnglat: locationArr,
    style: status // 对应的status相对应的样式 style
});
}

// 点放置
let mass = new AMap.MassMarks(locationData, {
    opacity: 0.8,
    zIndex: 111,
    cursor: "pointer",
    style: style
});

let marker = new AMap.Marker({ content: " ", map: map });
    mass.on("mouseover", function(e) {
    marker.setPosition(e.data.lnglat); //用户相对应的经纬度
    marker.setLabel({ content: e.data.name }); //用户相对应的名字
});

mass.setMap(map);
```

#### 5. 完成，启动
`npm run serve`启动，查看该示例，得到应有效果：

![实现效果](/images/posts/地图01.png '实现效果')

