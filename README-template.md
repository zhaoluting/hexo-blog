# 项目名称

一句话介绍下项目，一句话讲下技术栈

## 项目简介
项目简介，可以从产品文档里抄。

### 相关人员
- 业务方负责人：
- 产品经理：
- UI设计：
- 后端开发：
- 前端开发：
- 测试：
- 运维：

### 相关链接
- 进度管理：[teambition](https://www.teambition.com/)
- 设计稿：[蓝湖](https://lanhuapp.com/)、[墨刀](https://orgnext.modao.cc/)
- 接口文档：[gitlab-wiki](http://lqbyun.com/)
- 图片管理：[七牛云](https://portal.qiniu.com/kodo/bucket)（前缀：）、[iconfont](https://www.iconfont.cn/manage/index)
- 管理后台地址：线上、测试、预发
- 地址：线上、测试、预发
- 监控：sentry
- 框架文档：

### 第三方标识
**谷歌分析**
- 唯一标识：
- 服务号渠道：

**微信**
- 关联公众号：
- 关联小程序：

## 开发
### 如何运行
1、 下载项目
```bash
git clone git@github.com:zhaoluting/hexo-blog.git
```
2、安装依赖
```bash
npm install
```
3、本地启动项目，开启3000端口，支持热更新
```bash
npm run dev
```

**其他命令：**
```bash
# 打包输出代码和资源文件
npm run build

# 生产构建打包并查看分析报告
npm run build --report

# 使用eslint检测src下代码的规范情况
npm run lint
```

### 发布CheckList
- [ ] 测试环境下，完成code review，测试、设计、产品验收通过；
- [ ] 约定发布时间，并捋清需要发布的服务及顺序；
- [ ] 创建Merge Requests，由任务分支合并到预发分支，交由对应负责人审核确认并发布；
- [ ] 预发环境下，测试、设计、产品验收通过；
- [ ] 创建Merge Requests，由任务分支合并到线上主分支，交由对应负责人审核确认；
- [ ] 通知业务方做好准备，上线后及时进行回归，再次由测试、设计、产品验收；
- [ ] 观察半小时左右，确保线上无异常。


## 其他
### 备注
一些其他方面的注意点，视具体项目而定

### changelog
> 仅需记录大版本迭代及重要时间节点即可。

| 日期       | 需求     | teambition |
| ---------- | -------- | ---------- |
| 2020-03-13 | 初版开发 | 无         |
|            |          |            |
|            |          |            |
