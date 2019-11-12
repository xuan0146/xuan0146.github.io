---
title: vue中基于高德的多行政区域覆盖
type: categories
copyright: true
date: 2019-11-12 14:45:03
tags: [js, vue, 高德]
categories: 前端笔记
description:
---
### 前言
此系列共分为以下几篇
>- 《vue中高德地图的使用》
>- **《vue中基于高德的多行政区域覆盖》** *（本篇）*
>- 《vue中高德搭配echarts做数据迁徙流线图》

### 本篇效果
![效果展示](/images/posts/行政区域展示.png '行政区域展示')
<!--more-->

------

### 实现步骤
#### 1. 思路分析
基于上一篇的基础讲解，再来看本篇目的，行政区域展示（秒变及背景），其实就是基于在经纬度坐标系上将某个/些行政区域规划出来。如果仔细看官方文档的前端er，则更是发现在文档中提供灵了一个行政区域上色的工具方法。因此基础思路如下：
1. 加载基础坐标系；
2. 给定具体行政区域上色；
3. 多行政区域循环；

#### 2. 代码分析
==详细代码在文章底部==
##### 初始化地图
```js
// 略 见文章底部详细代码
```
##### 行政区域蒙层加载
```js
areaBG (cityName) {
    // 加载行政区划插件
    AMap.service('AMap.DistrictSearch', function() {
        let opts = {
        subdistrict: 1,   // 返回下一级行政区
        extensions: 'all',  // 返回行政区边界坐标组等具体信息
        level: 'city'  // 查询行政级别为市
        };
        // 实例化DistrictSearch
        let district = new AMap.DistrictSearch(opts);
        district.setLevel('district');
        // 行政区查询
        district.search(`${cityName}`, function(status, result){
        // 获取边界信息
        let bounds = result.districtList[0].boundaries;
        // 存放行政区划
        let polygons = [];
        if (bounds) {
            for (let i = 0, l = bounds.length; i < l; i++) {
            //生成行政区划polygon
            let polygon = new AMap.Polygon({
                map: new AMap.Map("map", {
                    center: [105.397428, 35.90923],
                    zoom: 5
                });,   // 坐标系及主题
                strokeWeight: 1,   
                path: bounds[i],
                fillOpacity: 0.2,
                fillColor: 'rgba(71,228,194,0.44)',
                strokeColor: 'rgba(83,204,79,0.65)'
            });
            polygons.push(polygon);
            }
        }
        });
    });
}
```

##### 方法调用
```js
this.areaBG('辽宁省');
```
得到如下地图示例：*(此处添加了个人的'mapStyle')*

![行政区域-辽宁省](/images/posts/行政区域-辽宁省.png '行政区域-辽宁省')

那么，如果是多个行政区域，如何循环遍历呢？

##### 多区域加载
机智的各位已经发现了,我们前面其实已经把城市名字提出来了，那么直接遍历即可。
```js
// 行政区域加载
let cities = ['北京市','郑州市','西安市','呼和浩特','辽宁省'];
for(let i = 0; i < _cities.length; i ++){
    this.areaBG(map, cities[i]);
}
```
至此，目的完成：
![效果展示](/images/posts/行政区域展示.png '行政区域展示')

### 附：代码
```html
<div class="map" id="map"></div>
```
```js
methods: {
    // 加载行政区域
    areaBG (map, cityName) {
        AMap.service('AMap.DistrictSearch', function () {
            let opts = {
                subdistrict: 1,   // 返回下一级行政区
                extensions: 'all',  // 返回行政区边界坐标组等具体信息
                level: 'city'  // 查询行政级别为市
            };
            // 实例化DistrictSearch
            let district = new AMap.DistrictSearch(opts);
            district.setLevel('district');
            // 行政区查询
            district.search(`${cityName}`, function (status, result) {
                // 获取边界信息
                let bounds = result.districtList[0].boundaries;
                let polygons = [];
                if (bounds) {
                    for (let i = 0, l = bounds.length; i < l; i++) {
                        // 生成行政区划polygon
                        let polygon = new AMap.Polygon({
                            map: map,
                            strokeWeight: 1,
                            path: bounds[i],
                            fillOpacity: 0.2,
                            fillColor: 'rgba(71,228,194,0.44)',
                            strokeColor: 'rgba(83,204,79,0.65)'
                        });
                        polygons.push(polygon);
                    }
                }
            });
        });
    },
    // 初始化地图，并加载行政区域
    mapInit () {
        let map = new AMap.Map("partnerMap", {
            center: [105.397428, 35.90923],
            mapStyle: "amap://styles/bfb1bb3feb0db7082367abca96b8d214", // 设置地图的显示样式
            zoom: 5
        });

        // 行政区域加载
        let cities = ['北京市','郑州市','西安市','呼和浩特','辽宁省'];
            for(let i = 0; i < _cities.length; i ++){
            this.areaBG(map, cities[i]);
        }
    }
}
```
```js
mounted () {
    this.mapInit();
}
```


