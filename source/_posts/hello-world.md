---
title: Hello World
date: 2017/6/15
tags: hexo
categories: 其他
---
## note 
<div class="note info">博客使用了NEXT主题，特此贴上我的配置代码</div>
``` bash
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
hexo deploy -g  #生成加部署
hexo server -g  #生成加预览
hexo clean  #把public静态文件清空再重新生成部署
命令的简写
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
hexo g -d == hexo generate hexo deploy
```

### 样式
``` bash
// Custom styles.
//首页文章阴影样式
.post {
    margin-top: 60px;
    margin-bottom: 60px;
    padding: 25px;
    -webkit-box-shadow: 0 0 14px rgba(202, 203, 203, .5);
    -moz-box-shadow: 0 0 14px rgba(202, 203, 204, .5);
}
//热评文章
.ds-top-threads li a {
    padding-left: 5px;
    transition: border-width 0.2s linear 0s, color 0.2s linear 0s;
    border-bottom: none;
}
.ds-top-threads li a:hover {
    border-left: 8px solid #4d768c;
}
//首页头部样式
.header {
    background: url("../../images/background.jpg");
}
.headband {
    background: #926f5a;
}
.site-meta {
    float: none;
}
.brand {
    background: #d08e65cc;
}
.menu {
    float: none;
    background: #ffffff59;
}
.logo-line-before,
.logo-line-after {
    display: none;
}
.menu .menu-item a {
    font-size: 14px;
    color: rgb(15, 46, 65);
    border-radius: 4px;
}
.site-meta {
    margin-left: 0px;
    text-align: center;
}
.site-meta .site-title {
    font-size: 35px;
    font-family: 'Comic Sans MS', sans-serif;
    color: #fff;
    height: 50px;
    padding-top: 5px;
}
//首页尾部样式
.footer {
    background: none;
    font-size: 16px;
}
.footer-inner {
    font-family: 'Comic Sans MS', sans-serif;
    text-align: center;
    color: #a88772;
}
//侧边栏信息样式修改
.site-author-name {
    margin: 30px 0 0;
    color: #fff;
    font-family: 'Comic Sans MS', sans-serif;
    font-size: 20px;
}
// 侧栏头像（圆形以及旋转效果）
.site-author-image {
    border: 2px solid rgb(255, 255, 255);
    border-radius: 100%;
    background-color: #fff;
    transition: transform 1.0s ease-out;
}
img:hover {
    transform: rotateZ(360deg);
}
.site-description {
    color: #e4c8bf;
}
.posts-expand .post-body img:hover {
    transform: initial;
}
.links-of-blogroll {
    font-size: 14px;
    margin-bottom: 42px;
}
.links-of-author {
    margin-top: 30px;
    margin-bottom: 58px;
}
.sidebar-inner {
    color: #649ab6;
}
.sidebar {
    background: #a88773;
    box-shadow: inset 0 2px 6px #724d38;
}
.sidebar a {
    color: #fff;
    border-bottom-color: #649ab6;
    border-bottom: none;
}
.sidebar a:hover {
    color: #fff;
}
.site-state-item {
    display: inline-block;
    padding: 8px 28px;
    border-left: 1px solid #649ab6;
}
.sidebar-nav .sidebar-nav-active {
    color: #fff;
    border-bottom-color: #fff;
}
.sidebar-nav li {
    color: #e1c6b5;
}
.sidebar-nav li:hover {
    color: #fff;
}
//侧栏按钮样式
.sidebar-toggle {
    background: #a88772;
}
.page-post-detail .sidebar-toggle-line {
    background: #ffffff;
}
.back-to-top {
    background: #a88772;
}
//文章目录样式
.post-toc .nav .active>a {
    color: #fff;
}
.post-toc ol a {
    color: #e0c6b5;
}
.post-toc ol a:hover {
    color: #fff;
}
.sidebar-nav .sidebar-nav-active:hover {
    color: #fff;
}
a {
    border-bottom: none;
}
//首页阅读全文样式
.post-button {
    margin-top: 30px;
    text-align: center;
}
.post-button .btn {
    color: #fff;
    font-size: 15px;
    background: #686868;
    border-radius: 16px;
    line-height: 2;
    margin: 0 4px 8px 4px;
    padding: 0 20px;
}
.post-button a{
  border-bottom: 1px solid #666;
}
.post-button a:hover {
    color: #7784ba;
}
// 自定义页脚跳动的心样式
@keyframes heartAnimate {
    0%,100%{transform:scale(1);}
    10%,30%{transform:scale(0.9);}
    20%,40%,60%,80%{transform:scale(1.1);}
    50%,70%{transform:scale(1.1);}
}
#heart {
    animation: heartAnimate 1.33s ease-in-out infinite;
}
.with-love {
    color: #a88771;
}
```

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)
