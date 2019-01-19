---
title: puppeteer实战-微信文章爬取调研
date: 2018-10-20 16:18:00
tags: puppeteer
categories: 前端
---
## puppeteer
Puppeteer是一个Nodejs的库，支持调用Chrome的API来操纵Web，可以很方便的用它来抓取文章。微信公众号文章又是大家阅读量非常大的一个平台，但是想要完美得抓取出公众号文章是相当难的。
<!--more-->

## 编辑工具
微信公众号的文章一般是通过如下的富文本编辑器进行编辑，就算是使用了其他第三方的编辑器（如秀米），也是编辑完再复制到后台的，所以只研究后台的这个编辑器就OK了。
![编辑工具](/image/puppeteer/1-1-1.png)
  
  

## 典型文章  
1. 带视频的文章： https://mp.weixin.qq.com/s/Uw6b7_W_NkCZDIce4Obn6g  
2. 带音频的文章：https://mp.weixin.qq.com/s/-5oj6Q4qYS0gf5hptNYfjw  
3. 带投票的文章：https://mp.weixin.qq.com/s/ROlm_YXgmQMC1of7CEojRw  
4. 带小程序二维码的文章：https://mp.weixin.qq.com/s/A9UvCji6XJmfusNpSWigFQ  
5. 特殊视频类文章：https://mp.weixin.qq.com/s/XPrdTJppD-jVCwuGzdd0Ew  
   推送出来是个视频，点进去是篇带视频的文章：
   ![特殊视频类文章](/image/puppeteer/1-2-1.png)
6. 分享类文章：  
    - https://mp.weixin.qq.com/s/fnVRpI1rvMHDnTZIiMzL-A 
    - https://mp.weixin.qq.com/s/nlR6AzRi1IPnWh0m9M9dcA
7. 争议类文章：
    - https://mp.weixin.qq.com/safe/dispute?action=info&__biz=MzIxMjgxMDAwOA==&mid=2247485963&idx=1&sn=661d71f440962cebc08594abb124bd20#wechat_redirect


## 元素分析
### 普通元素
- 这些看起来很复杂的元素，都是富文本，可以简单理解为带css样式的图片和文字
![普通元素](/image/puppeteer/1-3-1.png)

### 特殊图片元素
- 右上角带着类似出口标志的都是带超链接的图片
- 特征：class="js_jump_icon h5_image_link"
![特殊图片元素](/image/puppeteer/1-3-2.png)

### 视频元素
- 特征：class="js_tx_video_container" 嵌iframe的方式
- 上传视频要求：视频不能超过20M，超过20M的视频可至腾讯视频上传后添加，也可通过添加视频详情页链接以及公众号文章链接插入视频，视频时长不少于1秒，不多于10小时，支持大部分主流视频格式。
- 小于20MB的视频好像也会转到腾讯视频（如https://mp.weixin.qq.com/s/VOLKVAIcOqiX-XAv-s3b2w 待验证）

### 音频元素
- 特征：qqmusic标签，class="db qqmusic_area
- 上传音频要求：格式支持mp3、wma、wav、amr，文件大小不超过30M，语音时长不超过30分钟

### 投票元素
- 特征：class="vote_iframe js_editor_vote_card" 
- 嵌iframe的方式加进文章

### 小程序元素
- 特征：mp-miniprogram标签，class="weapp_display_element js_weapp_display_element"

### 文章链接卡片
- 公众号可分享原创声明文章：[微信公众号如何分享原创声明文章？](https://jingyan.baidu.com/article/454316ab126339f7a7c03aae.html)，分享后网页样式会有所不同。
- 分享者只能编辑引导语，且只能编辑文字，并且限制在140字以内，无法插入图片，文字样式等也不可更改。
- 例如：https://mp.weixin.qq.com/s/fnVRpI1rvMHDnTZIiMzL-A
- 特征：id="js_share_content"

### 底部广告与留言元素
- pc端会隐藏掉 点赞小手 及 以下的所有内容，所以不用去管底部的广告与留言。
![底部广告与留言元素](/image/puppeteer/1-3-3.png)

### 打赏元素
- [打赏功能](https://mp.weixin.qq.com/s/8VLIsbkuQcAFI5qM_B4g3w)，由于微信和苹果吵了一架，iOS就没有了赞赏功能，但是PC上打开是没有的，所以也不用管了。
![打赏元素](/image/puppeteer/1-3-4.png)


## 非文章内容
### 微信自带广告
- 位于 点赞小手 下方
- 在PC端打开不会出现不用管。

### 评论留言及打赏
- 位于 点赞小手 下方
- 在PC端打开不会出现不用管。

### 编辑者放的广告
- 普通文本：如 “点击关注...”、“点击阅读原文”；
- 带超链接的广告图片；
- 不带超链接的普通广告图片；
- 小程序卡片；  

由于那些内容是编辑者自己放的，有些情况下其本身是文章中不可删除的内容部分，若一概删除会有内容丢失的风险。

### 文章推荐列表
- 当前公众号内的往期文章
- "你可能喜欢的文章"
- 其他合作公众号的推荐文章  

由于这些列表都是编辑者自己放的，有些情况下其本身是文章中不可删除的内容部分，若一概删除会有内容丢失的风险。

### 二维码
- 公众号的二维码
- 私人号的二维码
- 打赏用的二维码
- H5页面的二维码

由于这些二维码都是编辑者自己放的，有些情况下其本身是文章中不可删除的内容部分，若一概删除会有内容丢失的风险。

## 源码分析 
### <head\>代码
- document.title 获取<title\></title\>
- 无网站描述

### <body\>代码
- document.getElementById("activity-name") 获取 id="activity-name" 为标题 （分享类文章没有标题）
- document.getElementById("js_content") 获取 id="js_content"为正文部分
- 一些全局变量（特殊视频类文章除外）
```
// 浏览器控制台输入 console.log(_ori_article_type,nickname,msg_title,msg_desc)
var _ori_article_type = "文章内容类型";
var nickname = "公众号名称";
var msg_title = "文章标题";
var msg_desc = "文章描述";
```

### 脚本重要代码段
```
var article = new Object();
// 设置文章来源
article.sourcename='微信公众号';

try {
  // 设置文章标题
  article.title = msg_title;
  // 获取文章描述
  article.summary= msg_desc;
} catch (e){
  console.log(e);
}

// 当文章为分享类，点击 阅读原文 按钮 跳转到源文章地址
var shareContent = document.getElementById("js_share_content")
var sourceBut = document.getElementById("js_share_source");
if (shareContent && sourceBut) {
  sourceBut.click();
}

// 删除小程序卡片
var deleteElements = document.getElementsByClassName("weapp_display_element js_weapp_display_element")
for(var i=0;i<deleteElements.length;i++) {
   deleteElements[i].remove();
}

// 获取文章内容  
var contentElement = document.getElementById('js_content');
```