---
title: Hello World
date: 2017/6/15
tags: tool
categories: 工具
---
<div class="note info">博客使用了NEXT主题，特此贴上我的配置代码。[More info](https://hexo.io/docs/)</div>

## 命令行
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

<!--more-->
## 需要修改的文件
### themes/next/source/css/_common/components/tags/label.styl
注释掉 white-space: nowrap;

### themes/next/source/images
该目录下创建global目录，放入
- [background.jpg](https://zhaoluting.github.io/images/global/background.jpg)
- [favicon-16x16-next.png](https://zhaoluting.github.io/images/global/favicon-16x16-next.png)
- [favicon-32x32-next.png](https://zhaoluting.github.io/images/global/favicon-32x32-next.png)

### themes/next/source/lib
```bash
cd themes/next
git clone https://github.com/theme-next/theme-next-fancybox3 source/lib/fancybox
git clone https://github.com/theme-next/theme-next-pace source/lib/pace
```

### themes/next/_config.yml
``` xml
<!-- 设置图标 -->
favicon:
  small: /images/global/favicon-16x16-next.png
  medium: /images/global/favicon-32x32-next.png

<!-- 设置页脚 -->
footer:
  since: 2017
  <!-- 设置页脚动效 -->
  icon:
    name: heart
    animated: true
    color: "#a88771"
  <!-- 隐藏自带版本信息 -->
  powered:
    enable: false
    version: false
  theme:
    enable: false
    version: false

<!-- 设置顶部菜单栏 -->
menu:
  home: / || home
  about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  schedule: /schedule/ || calendar

<!-- 右侧展开显示的联系方式 -->
social:
  GitHub: https://github.com/zhaoluting || github
  E-Mail: mailto:839327424@qq.com || envelope
  More: https://zhaoluting.github.io/about/ || vcard

<!-- 右侧展开显示的更多信息 -->
links_icon: code
links_title: 技术栈
links_layout: inline
links:
  React: https://react.docschina.org/
  TypeScript: https://www.typescriptlang.org/
  Vue: https://cn.vuejs.org/
  mpvue: http://mpvue.com/
  Node: https://nodejs.org/en/
  Pupprteer: https://pptr.dev/
  Webpack: https://webpack.js.org/
  MySQL: https://www.mysql.com/

<!-- 设置右侧展开头像 -->
avatar:
  url: /images/global/favicon-32x32-next.png
  rounded: true

<!-- 页面右下角滚动显示百分比 -->
sidebar:
  scrollpercent: true


<!-- 文章信息显示设置 -->
post_meta:
  updated_at:
    <!-- 不显示修改日期 -->
    enabled: false

<!-- 文章字数统计显示 https://github.com/theme-next/hexo-symbols-count-time -->
symbols_count_time:
  <!-- 是否单独成一行  -->
  separated_meta: false
  <!-- 文章中的显示是否显示文字（本文字数|阅读时长）  -->
  item_text_post: false
  <!-- 网页底部的显示是否显示文字（站点总字数|站点阅读时长）  -->
  item_text_total: false

<!-- 代码块具备复制功能 -->
codeblock:
  copy_button:
    enable: true
    show_result: true

<!-- 安卓浏览器样式优化 -->
android_chrome_color: "#a88773"

<!-- 打开本地搜索功能 -->
local_search:
  enable: true

<!-- 主题自带样式 note 标签配置，详见https://github.com/iissnan/hexo-theme-next/pull/1697 -->
note:
  style: simple
  icons: true

<!-- 选项卡样式配置，详见https://almostover.ru/2016-01/hexo-theme-next-test/#Tab-tag-test -->
tabs:
  transition:
    tabs: true

<!-- 开启图片预览功能 -->
fancybox: true

<!-- 显示顶部阅读进度条 -->
pace: true
pace_theme: pace-theme-flash
pace: true
```
### themes/next/source/css/_custom/custom.styl
``` css
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
    background: url("../../images/global/background.jpg");
    background-color: #A48777;
    background-repeat: no-repeat;
    background-size: 100% auto;
    background-position: center;
    @media only screen and (max-width: 767px) {
        background-size: auto 100%;
    }
}
.headband {
    background: #926f5a;
}
.brand {
    background: #d08e65cc;
}
.menu {
    float: none;
    background: #ffffff59;
    padding: 2px 0;
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
    float: none;
    .site-title {
        font-size: 35px;
        font-family: 'Comic Sans MS', sans-serif;
        color: #fff;
        height: 50px;
        padding-top: 5px;
    }
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


img:hover {
    transform: rotateZ(360deg);
}
.sidebar {
    background: #a88773;
    box-shadow: inset 0 2px 6px #724d38;
    .sidebar-inner {
        color: #e6c5b2;
        // 侧栏头像
        .site-author-image {
            border: 2px solid #fff;
            border-radius: 100%;
            background-color: #fff;
            transition: transform 1.0s ease-out;
            margin-top: 20px;
        }
        //侧边栏信息样式修改
        .site-author-name {
            margin: 30px 0 0;
            color: #fff;
            font-family: 'Comic Sans MS', sans-serif;
            font-size: 20px;
        }
        .site-description {
            color: #e6c5b2;
        }
        a {
            color: #fff;
            border-bottom-color: #e6c5b2;
            border-bottom: none;
        }
        a:hover {
            color: #fff;
        }
        .sidebar-nav {
            .sidebar-nav-active {
                color: #fff;
                border-bottom-color: #fff;
            }
            li {
                color: #e1c6b5;
            }
            li:hover {
                color: #fff;
            }
            .sidebar-nav-active:hover {
                color: #fff;
            }
        }
        .site-state {
            .site-state-item {
                display: inline-block;
                padding: 8px 28px;
                border-left: 1px solid #e6c5b2;
            }
        }
        .links-of-blogroll {
            font-size: 14px;
            margin-bottom: 42px;
            .links-of-blogroll-title {
                font-family: 'Comic Sans MS', sans-serif;
                font-size: 16px;
                color: #fff;
            }
            .links-of-blogroll-list {
                border-top: 1px dotted;
                margin: 10px 20px;
                line-height: 20px;
                padding-top: 10px;
            }
        }
        .links-of-author {
            margin-bottom: 70px;
            .links-of-author-item i {
                margin-right: 3px;
            }
        }
        //文章目录样式
        .post-toc {
            .nav .active>a {
                color: #fff;
            }
            ol a {
                color: #e0c6b5;
            }
            ol a:hover {
                color: #fff;
            }
        }
    }
}

//侧栏按钮样式
.sidebar-toggle {
    background: #a88772;
    .sidebar-toggle-line {
        background: #ffffff;
    }
}
.site-nav-toggle {
    .btn-bar {
        background: #a88772;
    }
}
.back-to-top {
    background: #a88772;
}

//首页文章块样式
.post-type-normal {
    //首页阅读全文样式
    .post-button {
        margin-top: 50px;
        text-align: center;
        .btn {
            color: #fff;
            font-size: 15px;
            background: #ae866f;
            border-radius: 16px;
            line-height: 2;
            margin: 0 4px 8px 4px;
            padding: 0 20px;
            border: 2px solid #926f5a;
        }
        a{
            border-bottom: 1px solid #926f5a;
        }
        a:hover {
            color: #fff1ea;
        }
    }
    .post-footer {
        .post-eof {
            margin: 20px auto 10px;
        }
    }
}


// 代码块复制按钮样式
.highlight-wrap .copy-btn {
    color: #8D908C;
    font-size: 12px;
}

.posts-expand {
    .post-body {
        img:hover {
            transform: initial;
        }
        a {
            border-bottom: 1px dashed #999;
        }
    }
}
```