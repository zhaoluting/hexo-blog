---
title: swagger文档规范
date: 2017-11-01 16:53:46
tags: tool
categories: 工具
---

## 简介
[Swagger](https://swagger.io/)是一个简单又强大的文档生成工具。在项目中集成这个工具，能够根据配置信息自动为我们生成一个api文档展示页，可以在浏览器中直接访问查看项目中的接口信息，同时也可以测试每个api接口，同时也是前后端开发人员联系的重要纽带。一份功能完整、结构清晰、描述合理的API文档能大大提高前后端的合作开发效率。

<!--more-->
<br/>

## 合格的swagger文档
在前后端分离的项目中常常会用到swagger，它既是测试工具，又是前后端沟通的桥梁，在项目中发挥着非常重要的作用。在项目初期，前后端一般都会有个磨合的过程，对swagger文档约定是不可少的。
那么，在前端眼中，怎么样算一份**合格易懂**的swagger文档。下面三张图为优秀范例：

![优秀范例](/image/swagger-sandard/swagger1.png)
![优秀范例](/image/swagger-sandard/swagger2.png)
![优秀范例](/image/swagger-sandard/swagger3.png)


**以下是我们前端人员常关注的地方：**
1. 一般对应的是 注解中的 @ApiOperation，用在方法上，说明方法的作用。
   - 合格点：用中文简单描述。
2. 一般对应的是 注解中的 @ApiModelProperty，用于描述一个model的属性。
   - 合格点：
        - 有枚举类的属性加上枚举类说明，属性多的情况下尽量多加点中文注释。  
        - response格式写完整，不能{ "code": 0, "data": {} }  了事。
3. 一般对应的是 注解中的 @ApiParam 或者 @ApiImplicitParam， 用于对每个参数的描述。
   - 合格点：
        - params格式完整。
        - 参数多的情况下尽量用中文注释。

<br/>
一般只要**以下三点达成共识**，那么对接的效率就会事半功倍，也不容易引起不愉快的争吵：
- 后端写swagger文档时在以上关注点上下点功夫（一般是从代码中提取注释即可自动生成文档，所以对后端代码可读性也是有好处的）；
- 有接口字段改动时第一时间通知前端；
- 相同意义字段统一定义；
  

  <br/>

## 参考文章
[swagger2常用注解说明](https://blog.csdn.net/u014231523/article/details/76522486)
[swagger常用注解说明](https://www.jianshu.com/p/12f4394462d5)
