# hexo博客构建系统
本站使用[Hexo](https://hexo.io/zh-cn/docs/)搭建，配置了[nexT](https://theme-next.js.org/)主题。运行此项目将会自动构建个人博客，并将打包出来的静态资源部署到[zhaoluting.github.io](https://github.com/zhaoluting/zhaoluting.github.io)仓库中。

## 常用命令行
``` bash
hexo version # 显示 Hexo 版本
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo new draft "pageName" #草稿
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
### 初始化项目
```bash
npm install -g hexo-cli # 安装hexo
hexo init hexo-blog # 初始化hexo项目
npm install hexo-theme-next --save # 安装nexT主题
```

### 修改配置
- 安装各种插件，可查看本项目[`package.json`](https://github.com/zhaoluting/hexo-blog/blob/v2.0.0/package.json)
- 修改[`_config.yml`](https://github.com/zhaoluting/hexo-blog/blob/v2.0.0/_config.yml)，对Hexo进行配置
- 新建[`_config.next.yml`](https://github.com/zhaoluting/hexo-blog/blob/v2.0.0/_config.next.yml)，对NexT主题进行配置
- 新建[`source/_data/styles.styl`](https://github.com/zhaoluting/hexo-blog/blob/v2.0.0/source/_data/styles.styl)，覆盖NexT主题样式



