---
title: docker入门
date: 2023-03-03 14:08:34
tags: tool
categories: 工具
---
2013年3月，Docker创始人Solomon Hykes首次公开了Docker这一产品，瞬时风靡全球。Docker将容器技术大众化，并解决了困扰数百万开发人员的“matrix from hell”问题，使容器技术成为主流。这个划时代的开源项目也解决了一个世纪难题：在我电脑上是好的😑

<!--more-->

![](/image/tool-docker/meme_1.png)

## 了解docker
### 基本概念
![](/image/tool-docker/docker_logo.png)
Docker与虚拟机通过操作系统实现隔离不同，容器技术只隔离应用程序的运行时环境，对进程进行封装隔离，属于操作系统层面的虚拟化技术。Docker底层的核心技术包括Linux上的命名空间（Namespaces，用于隔离控制）、控制组（Control groups，用于资源分配）、联合文件系统（Union file systems，用于文件系统的镜像存储、写时复制、镜像合并）和容器格式（Container format）。

UnionFS是Docker镜像的基础，是一种分层、轻量级并且高性能的文件系统，支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。镜像包含操作系统完整的root文件系统，体积往往比较庞大，所以Docker就利用UnionFS技术，将其设计为分层存储的架构。镜像一层一层地构建，前一层是后一层的基础，每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。分层存储的特征使得镜像的复用、定制变的更为容易。同样，容器也是分层存储，每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，可以称这个为容器运行时读写而准备的存储层为容器存储层。

![](/image/tool-docker/theory_1.png)
- **镜像Images**：特殊的文件系统，提供容器运行时所需的程序、库、资源、配置等文件，还包含为运行时准备的一些配置参数，如匿名卷、环境变量、用户等。镜像不包含动态数据，其内容在构建后不会改变。
- **容器Container**：容器是独立运行、相互隔离的一个或一组应用，是镜像创建的运行实例，实质是进程，可以简单理解为`Container = 简易版操作系统 + 应用软件（含依赖组件）`。
- **客户端Client**：客户端通过命令行或者其他工具使用[Docker SDK](https://docs.docker.com/develop/sdk/)与Docker的守护进程通信。
- **主机Host**：一个物理或者虚拟的机器用于执行Docker守护进程和容器。
- **仓库Registry**：集中存储、分发镜像的服务，类似代码仓库，分为公有仓库和私有仓库。[Docker Hub](https://hub.docker.com/)是官方维护的公共仓库。[docker-registry](https://docs.docker.com/registry/)是官方提供的用于构建私有镜像仓库的工具。
- **注册服务器Registry**：管理仓库的具体服务器，每个服务器上可以有多个仓库，而每个仓库下面有多个镜像。例如仓库地址`docker.io/ubuntu`，`docker.io`是注册服务器地址，`ubuntu`是仓库名。
- **Dockerfile**：用来构建镜像的配置文件，定义了如何生成镜像，内容包含了一条条构建镜像所需的指令和说明。
- **Docker Compose**：是用于定义和运行多容器Docker应用程序的工具。通过docker-compose.yml来配置应用程序需要的所有服务，一个命令就可以从YML文件配置中创建并启动所有服务。
- **Docker Machine**：官方的一个在多种平台上快速安装Docker环境的工具，允许在虚拟宿主机上安装Docker Engine，并使用 docker-machine命令管理这些宿主机。也可以集中管理所有的docker主机，比如快速给100台服务器安装docker。

### 重要生命周期
![](/image/tool-docker/theory_2.png)
- **Created**：容器已创建，容器所需的相关资源已经准备就绪，但容器中的程序还未处于运行状态。
- **Running/Up**：容器正在运行，也就是容器中的应用正在运行。
- **Paused**：容器已暂停，容器中的所有进程都处于暂停状态。
- **Stopped**：容器已停止，占用的资源和沙盒环境都依然存在，容器内主进程被终止。
- **Deleted**：容器已删除，相关占用的资源及存储在Docker中的管理信息也都已释放和移除。

### 数据管理
容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。所以，容器存储层一定要保持无状态化，所有文件写入操作都应使用数据卷或绑定宿主机目录，直接对宿主机或网络存储发生读写，性能和稳定性更高，容器删除后数据也不会丢失。

![](/image/tool-docker/theory_3.png)

| | Volume（数据卷） |  Bind mount(挂载主机目录) | tmpfs mount |
|---|---|---|---|
| 数据存储位置 | /var/lib/docker/volumes/.../_data | 宿主机任意目录 | 宿主机内存中 |
| 对已有挂载点影响 | 原有数据复制到volume中 | 隐藏并替换为 volume | - |
|是否支持单个文件| 不支持，必须是目录 | 支持 | - |
|移植性|移植性强，无需指定host目录|移植性弱，与host path绑定|-|
|是否支持持久化 | 支持 | 支持 | 不支持 |
|受docker管理和保护| 支持 | 不支持| 不支持 |
|使用场景| 由docker管理，更安全便捷，多数情况是最优选择 | 需要Docker以外的进程访问或修改存储层 | 数据不需要持久化 |


### 网络模式
Docker默认有三种网络，“bridge”、“host”、“none”，可以通过`docker network ls`查看。
- **bridge（系统）**：默认模式，为每个容器设置network namespace、ip地址等，并将容器连接到docker0网桥（docker0是Docker进程启动时创建的默认虚拟网桥）。如果需要多个docker容器在同一个宿主机上进行通信，可直接使用默认的bridge模式。
- **host（系统）**：直接使用宿主机的IP地址与外界通信，容器内部的服务端口也可以使用宿主机的端口，不需要进行NAT。如果对网络传输效率有较高要求或需要处理大量端口，且不考虑容器的隔离性的情况下，就可以选择host模式，但是要注意端口占用的问题。
- **none（系统）**：禁用网络功能，只有lo接口(localhost本地环回接口)，不为容器创建任何网络环境。一些对安全性要求高并且不需要联网的应用可以使用none网络。
- **自定义（推荐）**：建议使用自定义网桥来更好地控制容器之间的通信，这样可以为容器提供自己的网络环境，同时保持容器之间的隔离性。
- **container**：新容器会和一个指定容器共享IP、端口范围等，这样两个容器之间可以使用localhost高效快速通信。
- **Overlay**：在多个Docker主机之间创建一个虚拟网络，适用于分布式应用程序，允许容器在不同主机上运行并相互通信。
- **Macvlan**：容器将获得与主机相同的MAC地址和网络配置，适用于需要直接连接到物理网络的应用程序。


### 常见问题
- **Docker容器后台运行，必须有一个前台进程**：以后台模式启动一个容器`docker run -d --name mycontainer nginx`，启动后`docker ps -a`会发现容器并不在运行，如果不是那些一直挂起的命令，运行结束会自动退出。所以为了让容器持续在后台运行，需要将程序以前台进程的形式运行。比如运行`docker run -d mycontainer /bin/sh -c "while true; do echo hello; sleep 2; done"`（这个命令一直在打印）。
- **定制镜像应选择Dockerfile，慎用docker commit**：`docker commit`虽然可以比较直观的帮助理解镜像分层存储的概念，但对镜像的操作都是黑箱的，这种黑箱镜像的维护工作非常痛苦，而且每一次修改都会让镜像更加臃肿一次，例如，删除前一层文件的操作，实际不是真的删除，而是仅在当前层标记为该文件已删除，该文件会一直跟随镜像。Dockerfile脚本就能完美解决`docker commit`无法重复、镜像构建透明性、体积的问题。


## 如何使用
Portainer是一款轻量级的应用，它提供了图形化界面，用于方便地管理Docker环境，包括单机环境和集群环境。虽然可视化工具用起来很简单，但还是建议系统学习下命令行。因为网上很多教程都只提供了命令行的安装方式，看懂命令行后才知道如何在工具中配置。

### 镜像常用命令
```bash
# 去下载镜像，先从本地找，没有去镜像，最后没有去 hub，标签不写默认为 lastest
$ docker pull [镜像名]:[标签Tag]

# 列出本机的所有 image 文件，-a 显示本地所有镜像（包括中间镜像），-q 只显示镜像ID，--digests 显示镜像的摘要信息
$ docker images

# 删除 image 文件, -f 强制删除镜像
$ docker rmi [镜像名][:标签Tag]

# 查询镜像名称，--no-trunc 显示完整描述，--filter=stars=30 列出star不少于指定值的镜像，--filter=is-automated=true 列出自动构建类型的镜像
$ docker search [关键字]
```

### 容器常用命令
```bash
# 列出本机正在运行的容器，-a 列出本机所有容器包括终止运行的容器，-q 静默模式只显示容器编号，-l 显示最近创建的容器
$ docker ps

# 新建并启动容器
$ docker run [option] [容器名]
  #参数说明
  --name # 为容器指定一个名称；
  -d # 容器启动后进入后台，并返回容器ID，即启动守护式容器；
  -P # 随机端口映射；
  -p 80:8080 # 将本地80端口映射到容器的8080端口；
  bash # 容器启动以后，内部第一个执行的命令；
  -it # 以交互模式运行容器，同时为容器分配伪输入终端，容器的Shell会映射到当前的Shell；
  --rm # 在容器终止运行后自动删除容器文件；
  --restart=always # 设置容器自启动；
  # 挂载映射命令
  -v 容器内路径    #匿名挂载
  -v 卷名:容器内路径    #具名挂载
  -v /宿主机路径:容器内路径   #指定路径挂载

# 启动容器
$ docker start [容器ID]/[容器Names]

# 重启容器
$ docker restart [容器ID]/[容器Names]

# 终止容器运行
$ docker kill [容器ID]  # 强行终止，相当于向容器里面的主进程发出 SIGKILL 信号，那些正在进行中的操作会全部丢失
$ docker stop [容器ID]  # 从容终止，相当于向容器里面的主进程发出 SIGTERM 信号，然后过一段时间再发出 SIGKILL 信号

# 终止运行的容器文件，依然会占据硬盘空间，-f 强制删除可以删除正在运行的容器
$ docker rm [容器ID]

# 查看容器的输出，-t加入时间戳，-f跟随最新日志打印，--tail数字显示最后多少条，如果docker run时，没有使用-it，就要用这个命令查看输出
$ docker logs -tf --tail [显示条数] [容器ID]


# 查看容器中进行的进程信息
$ docker top [容器ID]/[容器Names]

# 查看所有容器的名字跟端口号
$ docker port [容器ID]/[容器Names]

# 退出容器
$ exit

# 进入容器
$ docker attach [容器ID]      # 退出容器时会让容器停止，本机的输入直接输到容器中
$ docker exec -it [容器ID]    # 退出容器时不会让容器停止，在已运行的容器中执行命令，不创建和启动新的容器

# 设置容器在docker启动时自动启动
$ docker container update --restart=always [容器名字]

# 容器文件拷贝到本机
$ docker cp [容器ID]/[容器Names]:[要拷贝的文件目录] [本机目录]
# 本机文件拷贝到容器
$ docker cp [本机目录] [容器ID]/[容器Names]:[要拷贝的文件目录]
```


### 参考文章
- [Docker镜像部署与运维指南](http://help.websoft9.com/docker-guide)
- [Docker — 从入门到实践](https://vuepress.mirror.docker-practice.com/)
- [前端工程化](https://shanyue.tech/)
- [手摸手带你 Docker 从入门到实践](https://juejin.cn/post/6875323565479034894)
- [docker + webhook 从零实现前端自动化部署](https://mp.weixin.qq.com/s/_riSVxNdPsbyQf9qAoT4fQ)
- [前端人应该知道的Centos/Docker/Nginx/Node/Jenkins的基本操作](https://mp.weixin.qq.com/s/qXZdEIGbwtNW1CAewwAdXQ)