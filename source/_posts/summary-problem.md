---
title: 前端开发常见问题
date: 2020-03-20 12:33:06
tags: summary
categories: 技术小合集
---

前端开发过程中容易碰见的一些常见问题记录，方便以后查阅，会慢慢完善（flag）
<!--more-->

## web篇
**1、在页面分享或者使用其他微信api时出现错误，如分享页面地址错误等**
- **描述**：单页面应用在微信环境中，使用微信原生页面分享功能时，安卓设备分享正常，但在ios设备中，点击分享出来页面，会始终进入分享前初次进入应用时的页面，而不是分享前最后停留的页面。
- **原因**：vue单页面应用中，把第一次打开的页面叫做进入页，之后通过路由跳转的页面（通过pushState或replaceState改变的url）叫做当前页。在Android设备中，浏览器记录的是当前页的url信息；IOS设备中，浏览器记录的是进入页面信息。
- **解决办法**：在iOS环境下使用`window.location.href`跳转页面，改变iOS浏览器的页面路径记录信息。


**2、iPhone X系列安全区域适配问题**
- **描述**：iPhone X头部刘海两侧区域或者底部区域，出现刘海遮挡文字，或者呈现黑底或白底空白区域
- **解决办法**：设置安全区域，填充危险区域（指头部不规则区域，底部横条区域，左右触发区域），危险区域不做操作和内容展示。
  - 设置 viewport-fit 为 cover（视图端口被缩放以填充设备显示）
```html
<meta name="viewport" content="viewport-fit=cover">
```
  - 利用`env(safe-area-inset-*)`获取安全区域范围给iPhone X的头部底部增加适配层
```css
body {
    padding-top: env(safe-area-inset-top);
    padding-bottom: env(safe-area-inset-bottom);
    padding-right: env(safe-area-inset-right);
    padding-left: env(safe-area-inset-left);
    /* 为了兼容IOS 11，需要增加constant写法 */
    padding-top: constant(safe-area-inset-top);
    padding-bottom: constant(safe-area-inset-bottom);
    padding-right: constant(safe-area-inset-right);
    padding-left: constant(safe-area-inset-left);
}
```

### 数据转换相关
**1、iOS的Safari浏览器中**`**new Date('2020-03-18 18:33:24')**`**转换失败**
- **描述**：Safari日期对象不支持`yyyy-mm-dd hh:mm:ss`这种格式，会解析失败
- **解决办法1**：最简单的方式就是转成 `yyyy/mm/dd hh:mm:ss` 这种格式
```javascript
new Date('2020-03-18 18:33:24'.replace(/-/g, "/"))
```
- **解决办法2**：使用自己封装或第三方插件处理日期，如[__Moment.js__](http://momentjs.cn/)


**2、小数运算浮点数精度问题**
- **描述**：js采用二进制进行小数计算，但由于二进制浮点数表示法并不能精准表示十进制分数，就会产生一系列精度问题。如前端进行折扣计算：`5.6 / 10`结果却为0.5599999...。参考文章：[__JavaScript 浮点数陷阱及解法__](https://github.com/camsong/blog/issues/9) 
- **解决办法1**：`parseFloat(num.toPrecision(12))`，精度选为12基本就够用了
- **解决办法2**：使用第三方类库，如[__number-precision__](https://www.npmjs.com/package/number-precision)



### 键盘相关
**1、在iOS 12+ 和wechat 6.7.4+机型上唤起软键盘，键盘收回后页面不回落**
- **描述**：input 焦点失焦后，ios 软键盘收起，但没有触发 window resize，导致实际页面 dom 仍然被键盘顶上去，造成错位，底部会留下一片空白。
- **解决办法1**：全局监听 input 失焦事件，当触发事件后，将 body 的 scrollTop 设置为 0。
```javascript
document.addEventListener('focusout', () => {
  document.body.scrollTop = 0;
});
```
- **解决办法2**：在input的ontouchstart事件触发时，让页面滚动
```javascript
window.scrollTo(0, scroll);
```

- **解决办法3**：监听页面高度变化，强制恢复成弹出前的高度
```javascript
// 记录原有的视口高度
const originalHeight = document.body.clientHeight || document.documentElement.clientHeight;

window.onresize = function(){
  var resizeHeight = document.documentElement.clientHeight || document.body.clientHeight;
  if(resizeHeight < originalHeight ){
    // 恢复内容区域高度
    // const container = document.getElementById("container")
    // 例如 container.style.height = originalHeight;
  }
}
```

**2、唤起软键盘后会遮挡输入框**
- **描述**：当 input 或 textarea 获取焦点后，软键盘会遮挡输入框。
- **解决办法**：全局监听 window 的 resize 事件，当触发事件后，获取当前 active 的元素并检验是否为 input 或 textarea 元素，如果是则调用元素的 scrollIntoViewIfNeeded 即可。

`scrollIntoViewIfNeeded`用来将不在浏览器窗口可见区域内的元素滚动到浏览器窗口的可见区域，如果该元素已经在浏览器窗口的可见区域内则不会发生滚动，是scrollIntoView方法的专有变体，但PC端兼容性差。

```javascript
window.addEventListener('resize', () => {
  // 判断当前 active 的元素是否为 input 或 textarea
  if (
    document.activeElement!.tagName === 'INPUT' ||
    document.activeElement!.tagName === 'TEXTAREA'
  ) {
    setTimeout(() => {
      // 原生方法，滚动至需要显示的位置
      document.activeElement!.scrollIntoView();
    }, 0);
  }
});
```



**3、唤起键盘后fixed绝对定位元素被键盘顶起**
- **解决办法**：全局监听 window 的 resize 事件，当触发事件后，获取 id 名为 fixed-bottom 的元素（可提前约定好如何区分定位在窗口底部的元素），将其设置成 `display: none`。键盘收回时，则设置成 `display: block;`。

```javascript
const clientHeight = document.documentElement.clientHeight;
window.addEventListener('resize', () => {
  const bodyHeight = document.documentElement.clientHeight;
  const ele = document.getElementById('fixed-bottom');
  if (!ele) return;
  if (clientHeight > bodyHeight) {
    (ele as HTMLElement).style.display = 'none';
  } else {
    (ele as HTMLElement).style.display = 'block';
  }
});
```



