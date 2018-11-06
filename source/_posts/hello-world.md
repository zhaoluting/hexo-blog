---
title: Hello World
date: 2017/6/15
tags: hexo
categories: 其他
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

## 样式
themes/next/source/css/_custom/custom.styl 加入以下代码：
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
