---
title: 2022，新Hexo
date: 2022-02-25 14:49:44
tags: tool
categories: 工具
---

最近升级了本地的Node版本，然后年代久远的Hexo3出问题了得升级🤦🏻‍♀️，这就唤起了我18年升级NexT的记忆，可太麻烦了，那就索性删掉重建吧！这时候发现NexT终于能变成NPM包来引入了🥲，可太欣慰了，正好见证了[NexT的三次大改版](https://theme-next.js.org/docs/getting-started/upgrade.html)。这篇就简单记录下怎么搭建的这个新版博客（虽然表面上看不出来改了版，嘻嘻）。


<!--more-->

这个博客是我在2017年6月建立的，居然都已经快5个年头了。因为懒，版面设计啥的都没更新过，但是！我却从活力四射的小青年，变成了已被社会毒打多年的秃头老社畜🥲，岁月是杀猪刀也是剃头刀啊。我也算是Hexo和NexT的忠实粉丝了，从17年开始用的Hexo 3.2.0和NexT 5，中间只在18年底升过一次NexT 6，到现在的Hexo 6.0.0和NexT 8，不得不佩服这些开源项目的作者，比我勤快太多了。好了，牢骚发完了，进入正题。


## Hexo常用命令行
``` bash
hexo version # 显示 Hexo 版本
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo new draft "pageName" #新建草稿
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
hexo deploy -g  #生成加部署
hexo server -g  #生成加预览
hexo clean  #把public静态文件清空再重新生成部署
```

命令简写
```bash
hexo n # hexo new
hexo s # hexo server
hexo g -d # hexo generate & hexo deploy
```

## 建站过程
使用[Hexo](https://hexo.io/zh-cn/docs/)搭建，配置了[nexT](https://theme-next.js.org/)主题，将打包出来的静态资源部署到[zhaoluting.github.io](https://github.com/zhaoluting/zhaoluting.github.io)仓库中。

### 初始化项目
```bash
npm install -g hexo-cli # 安装hexo脚手架
hexo init hexo-blog # 初始化hexo项目
npm install hexo-theme-next --save # 安装nexT主题
```

### 修改配置
- 安装各种插件，可查看本项目[`package.json`](https://github.com/zhaoluting/hexo-blog/blob/v2.0.0/package.json)
- 修改[`_config.yml`](https://github.com/zhaoluting/hexo-blog/blob/v2.0.0/_config.yml)，对Hexo进行配置
- 新建[`_config.next.yml`](https://github.com/zhaoluting/hexo-blog/blob/v2.0.0/_config.next.yml)，对NexT主题进行配置
- 新建[`source/_data/styles.styl`](https://github.com/zhaoluting/hexo-blog/blob/v2.0.0/source/_data/styles.styl)，覆盖NexT主题样式

