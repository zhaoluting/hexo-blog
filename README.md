# hexo博客构建系统
运行此项目将会自动构建个人博客，并将打包出来的静态资源部署到zhaoluting.github.io仓库中。  

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


## 建站过程
### 初始化项目
[Hexo](https://hexo.io/zh-cn/docs/)、[next-theme](https://github.com/next-theme/hexo-theme-next)

```bash
npm install -g hexo-cli # 安装hexo
hexo init hexo-blog # 初始化hexo项目
npm install hexo-theme-next --save # 安装nexT主题
npm install hexo-deployer-git --save # 安装一键部署插件
```

### 修改配置
- 修改_config.yml
- 新建_config.next.yml
```bash
cp node_modules/hexo-theme-next/_config.yml _config.next.yml 
```


