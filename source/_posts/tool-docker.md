---
title: docker安装
date: 2020-02-16 14:53:28
tags: tool
categories: 工具
---

现公司技术栈为[Laravel](https://learnku.com/docs/laravel/6.x) + vue，前后端未分离，开发人员都需要使用docker。公司电脑大家都一样，安装起来还是挺顺利的，但最近由于疫情开始远程办公，自己装docker是着实费了老大的劲儿，发现自己对docker真的是一窍不通，那就趁现在有空学习下吧。

<!--more-->

## 简介

参考文章：[Docker — 从入门到实践](https://github.com/yeasy/docker_practice)、[Docker 入门教程-阮一峰](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)


## 安装教程
- 进入[docker官网](https://hub.docker.com/?overlay=onboarding)下载安装 docker（[__入门教程__](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)）
- 安装前提（mac可忽略）：
  - 系统用户名必须为英文，且安装路径也不允许有中文
  - windows中Docker目前仅有win10专业版/企业版的安装包，其他版本系统需安装Docker Toolbox，建议升级系统不要折腾了（修改产品密钥即可升级）
  - 在“启用或关闭Windows功能”中开启Hyper-V
  - 在任务管理器 → 性能 → 选择CPU → 右侧“虚拟化”属性查看是否开启cpu虚拟化，若未开启，进入dios启用配置
- 安装完成后设置docker的shared dives（勾选项目所在磁盘）和 memory（调大一些）
- 克隆local-dev-env（与代码父级文件夹平级）
```text
目录样例：
- workspace
    - local-dev-env
    - my-project
        - docker-test
        - 其他项目...
```
- 增加local-dev-env\dpcker-compose.yml中的配置
```yaml
  # 每个项目都需要添加services配置项
  docker-test: # 根据项目修改容器名称
    image: registry.cn-hangzhou.aliyuncs.com/zlt/nginx-php-fpm-alpine
    environment:
      WEBROOT: '/var/www/html/public'
      ERRORS: 1
      RUN_SCRIPTS: 1
      ENABLE_XDEBUG: 1
      XDEBUG_CONFIG: "remote_host=192.168.1.138"
      XDEBUG_PORT: 9001
      PUID: 1000
      PGID: 1000
    volumes:
      - D:/workspace/my-project/docker-test:/var/www/html # 修改为本地项目地址
    ports:
      - "8001:80" # 根据本机修改端口号
```
- **拉取镜像**：
  - 第一次运行项目需要先拉取镜像，拉取镜像需要先登录docke镜像库：启动docker后，进入local-dev-env文件夹运行`docker login registry.cn-hangzhou.aliyuncs.com`，如果碰到windows版本问题（ 非TTY device）需要在命令前加上winpty
  - 登录完之后执行：`docker-compose up -d`，会先拉取镜像并启动容器，镜像只需拉取一次，此后再执行不会再拉取镜像，直接启动容器。
  - 如果拉取镜像频繁超时，将docker设置中的"registry-mirrors"字段更改为`["http://hub-mirror.c.163.com"]`(修改了docker镜像源)，重启即可生效，也可梯子翻墙，命令行默认不走代理，记得设置`http_proxy`

- **项目安装PHP依赖**：
  - 进入local-dev-env文件夹下运行`docker-compose exec XXX bash`，进入到对应项目的容器中，再安装php依赖：`composer install`
  - 如果进入容器时出现`Interactive mode is not yet supported on Windows`错误，可先运行`docker ps`获取容器名，再运行`docker exec -it <containername> sh`即可（[__参考__](https://github.com/wodby/docker4drupal/issues/41)）
  - 如果composer install拉取依赖频繁超时，可尝试以下方法之一：
    - 进入容器后执行`composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/`，将composer镜像设为[__阿里源__](https://developer.aliyun.com/composer)；
    - 梯子翻墙，命令行默认不走代理，记得设置`http_proxy`
- 进入项目目录运行`yarn`或`npm install`安装依赖，若因网络问题导致node-sass安装失败，先删除依赖，再使用梯子或国内镜像cnpm重新安装
- 复制项目中的.env.example为.env
- 之后只需先进入local-dev-env在后台启动并运行所有的容器：`docker-compose up -d`，再进入项目`npm run watch`启动项目
- 最后，感谢同事的帮助~