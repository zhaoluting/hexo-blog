---
title: 关于git大小写不敏感的坑
date: 2019-04-19 17:30:45
tags:
  - tool
  - git
categories: 工具
---

## 问题

**场景：**

- 新建a.js 文件并使用git提交到远程仓库，再将本地 a.js 更名为 A.js，git却无法识别也无法提交。
- [两人本地存储的是同名不同大小写的文件，导致代码拉取时出现各种问题。](<http://www.chengpengfei.com/2018/01/23/>)
- 在macOS/windows上解压linux的程序时，同名但大小写不同的文件会互相覆盖。
- 一个全小写的文件名，将其引入时路径中的该文件名首字母写成了大写，本地编译时不会报错，但到线上部署时就会构建失败。

**原因：**一般开发使用的macOS/windows系统都是默认不区分文件名大小写的，而用来部署的服务器通常都是使用文件名大小写不敏感的Linux系统。git为了兼容Windows和MacOS，也是默认不区分文件名大小写的。

<!--more-->

## 解决方案

### 修改系统配置

[《在 macOS 中完美配置文件名大小写敏感》](https://zhuanlan.zhihu.com/p/35908178)这篇文章中提到了两种修改系统配置的方法：

- 将macOS 默认『Mac OS 扩展（日志式）』格式的磁盘改成『 Mac OS 扩展（区分大小写，日志式）』。
- 添加新的大小写敏感的 APFS 卷宗。

但这两种方法都不适用于团队，且会导致一些软件无法使用，所以这两种方法还是不要考虑了。



### 关于git

**git配置方法：**

- 全局设置大小写敏感：`git config --global core.ignorecase false`
- 单个项目设置大小写敏感：`git config core.ignorecase false`



**但git config core.ignorecase false也有坑**：

假设甲和乙都在有a.js文件的『分支1』下开发，而丙在更名为A.js的『分支2』下开发且设置了`git config core.ignorecase false`，当丙在『分支2』下merge『分支1』时，会提示文件将被重写。



**所以当多人协作开发时更推荐进行如下操作：**

> 使用git mv 命令时，会自动执行git add。

```bash
git mv -f [要删掉的文件名] [要留下的文件名]
git commit -m "改名" 
```

当已造成了本地一个文件、远程两个同名但大小写不同的文件时，使用此方法也可解决问题。

参考文章：[解决 Git 重命名时遇到的大小写不敏感的问题](https://blog.walterlv.com/post/case-insensitive-in-git-rename.html)



### webpack插件配置

通过Webpack插件**case-sensitive-paths-webpack-plugin**来强制区分引入文件的路径大小写，在本地编译时就能及时发现问题，杜绝第四个问题场景的出现。

npm地址：<https://www.npmjs.com/package/case-sensitive-paths-webpack-plugin>

**第一步：安装**

```bash
npm install --save-dev case-sensitive-paths-webpack-plugin
```

**第二步：配置**

```javascript
var CaseSensitivePathsPlugin = require('case-sensitive-paths-webpack-plugin');

var webpackConfig = {
    plugins: [
      	// 若想调试可用new CaseSensitivePathsPlugin({debug: true})
        new CaseSensitivePathsPlugin()
        // 其他插件 ...
    ]
    // 其他webpack配置项 ...
}
```



## 总结

为了避免大小写引来的各种问题，个人比较建议：

- 制定文件命名规范，统一为小驼峰式。
- 统一安装case-sensitive-paths-webpack-plugin插件。
- 若想修改已提交的文件名称大小写，务必使用`git mv -f [要删掉的文件名] [要留下的文件名]`。