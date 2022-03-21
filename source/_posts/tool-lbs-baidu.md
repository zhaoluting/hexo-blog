---
title: js引入地图API
date: 2022-03-17 17:50:18
tags: tool
categories: 工具
---

LBS（Location Based Services，围绕地理位置数据而展开的服务）现在是越来越火，用到这技术的项目也是越来越多，就稍微做了下小调研，并简单记录下。由于技术发展过快，本篇仅供参考，可能明年就不适用了，以各大官网为准。

<!--more-->

## 热门LBS
互联网发展就是快，就这十年不到的功夫，搜狗地图被腾讯收购，高德地图被阿里收购，国内谷歌地图也被封，所以下面我只简单介绍几个目前比较稳定，不容易凉凉的热门地图API。简单总结下就是，重性能用高德、重功能用百度、面向海外用谷歌、政府特殊项目用天地图。

- [高德地图](https://lbs.amap.com/)：阿里系，对开发者很友好，性能也好，更注重导航能力，在国内商业地图里是算最精细的一款。有官方vue版本。
- [百度地图](https://lbsyun.baidu.com/)：虽然在很多方面比不上高德，但POI（某个地理位置周边的信息）是强项，更注重生活，业务功能也更加丰富。无官方vue版本。
- [腾讯地图](https://lbs.qq.com/)：🤔emmmm，对比高德百度没太大优势，但也还行，腾讯背书，比较稳。无官方vue版本。
- [天地图](https://www.tianditu.gov.cn/)：国家队选手！国家测绘地理信息局的产品，主要为政府服务，国内数据资源的完整性、权威性、准确性是其他商业地图无法企及的，缺点就是性能差和颜值低。无官方vue版本。
- [谷歌地图](https://developers.google.com/maps/)：虽然非常优秀，但国内的数据比较滞后，并且很容易受国际局势影响，适合国外人用。无官方vue版本。


## vue引入百度地图

```javascript
// utils.js
export const fileTool = {
  // 异步加载百度地图
  loadBMap: function (ak) {
    return new Promise(function (resolve, reject) {
      try {
        console.log('BMap is defined:', BMapGL === undefined || BMapGL)
        resolve(BMapGL)
      } catch (err) {
        window.initBMapGL = function () {
          resolve(BMapGL)
        }
        const BMapUrl = `http://api.map.baidu.com/api?v=1.0&type=webgl&ak=${ak}&callback=initBMapGL`;
        fileTool.loadScript(BMapUrl, null, reject)
      }
    })
  },
  // 异步加载百度地图及BMapGLLib
  loadBMapGLLib: function (ak) {
    return new Promise(function (resolve, reject) {
      fileTool.loadBMap(ak).then(BMapGL => {
        let loaded = false;
        try {
          loaded = (BMapGLLib && BMapGLLib.DrawingManager);
        } catch (err) {
          loaded = false;
        }
        if (!loaded) {
          const GLLibFile = {
            js: 'https://mapopen.bj.bcebos.com/github/BMapGLLib/DrawingManager/src/DrawingManager.min.js',
            css: 'https://mapopen.bj.bcebos.com/github/BMapGLLib/DrawingManager/src/DrawingManager.min.css',
          }
          fileTool.loadCSS(GLLibFile.css);
          fileTool.loadScript(GLLibFile.js, function() {
            return resolve({BMapGL, BMapGLLib})
          }, function() {
            return resolve({BMapGL})
          });
        }
      })
    })
  },
  // 加载js文件
  loadScript: function (src, callback, reject) {
    let script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = src;
    if (script.addEventListener) {
      script.addEventListener('load', function () {
        callback && callback();
      }, false);
      script.addEventListener('error', function () {
        reject && reject();
      });
    } else if (script.attachEvent) {
      script.attachEvent('onreadystatechange', function () {
        let target = window.event.srcElement;
        if (target.readyState == 'loaded') {
          callback && callback();
        }
      });
    }
    document.getElementsByTagName('head')[0].appendChild(script);
  },
  // 动态加载外部CSS文件
  loadCSS: function (url) {
    let link = document.createElement('link');
    link.rel = 'stylesheet';
    link.type = 'text/css';
    link.url = url;
    document.getElementsByTagName('head')[0].appendChild(link);
  },
}
```


```html
<template>
  <div class="service-location">
    <div id="baiduMapContainer"></div> 
  </div>
</template>

<script>
  import { fileTool } from "@/js/utils";
  export default {
    components: {
    },
    data() {
      return {
        BMapObj: null,
      };
    },
    methods: {
      // 创建地图
      initMap() {
        fileTool.loadBMapGLLib().then(BMapGL => {
          // 创建地图实例 
          this.BMapObj = new BMapGL.Map("baiduMapContainer");
          // 初始化地图，设置中心点坐标和地图级别
          this.BMapObj.centerAndZoom(new BMapGL.Point(116.404, 39.915), 19);
          //开启鼠标滚轮缩放
          this.BMapObj.enableScrollWheelZoom(true);
          // 添加比例尺控件
          this.BMapObj.addControl(new BMapGL.ScaleControl()); 
          // 添加缩放控件
          this.BMapObj.addControl(new BMapGL.ZoomControl());
          // 添加城市列表控件
          this.BMapObj.addControl(new BMapGL.CityListControl());
        })
      },
    },
    created() {
    },
    mounted() {
      this.initMap()
    },
  };
</script>

<style lang="scss" scoped>
.service-location {
  width: 100%;
  height: 100%;
  position: relative;
  #baiduMapContainer {
    width: 100%;
    height: calc(100vh - 125px);
  }
}
</style>
```