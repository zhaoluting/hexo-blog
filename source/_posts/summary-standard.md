---
title: summary-standard
date: 2020-03-17 10:50:21
tags: summary
categories: 技术小合集
---

规范的重要性不言而喻，决定了团队的代码质量和风格走向。规范有很多方面，比如流程规范、commit规范、readme规范、安全规范等等。团队越大，越能凸显出规范的作用。这篇就简单总结下各种规范，还不完整，以后慢慢补充。（立起了一个flag）

<!--more-->

## 流程规范
1. 需求确认后，从线上主分支拉出新任务分支，同时向团队告知“正在开发”
2. 开发完毕后将代码merge到测试分支，指派给测试人员，向团队告知“已提测”
3. 完成code review，交由测试、设计、产品验收；
4. 约定发布时间，并捋清需要发布的服务及顺序；
5. 创建由任务分支到预发分支的Merge Requests，交由负责人审核确认并发布；
6. 预发环境验收后，创建由任务分支到线上主分支的MR，交由负责人审核确认并发布；
7. 通知业务方做好准备，上线后及时进行回归，再次由测试、设计、产品验收；
8. 观察半小时左右，确保线上无异常。

## git规范
### 命名 -TODO
- 仓库名：
- 分支名：
权限管理
分支管理

### 注意事项
- 千万**不要使用gitlab在线解决冲突**，会污染任务代码。

### commit
参考[commit辅助工具](https://zhaoluting.github.io/2019/04/19/tool-commit/)，项目不强制使用Commitizen等工具，但提交时需要选择性加上以下一种前缀：
- **feat**: 新功能（feature）
- **fix**: 修补bug
- **docs**: 文档改动
- **style**: 不影响代码运行的格式改动
- **refactor**: 重构，不是新增功能或修补bug的代码变动
- **perf**: 提高代码性能的代码变动
- **test**: 新增或修改测试用例
- **build**: 影响构建系统或外部依赖的更改（如webpack、broccoli、npm等）
- **ci**: 对CI配置文件和脚本的变动（如travis、circle、browserstack、saucelabs等）
- **chore**: 不涉及src或测试文件的代码变更
- **revert**: 代码回退
- **misc**: 一些未归类或不知道将它归类到什么方面的提交

### README规范
每个git项目必须在根目录创建README文档，项目简介、成员为必填信息，其他信息视具体项目情况而定，可参考[README](https://github.com/zhaoluting/hexo-blog/blob/master/README-template.md)

## vue编码规范
以下规范参考[风格指南 — Vue.js](https://cn.vuejs.org/v2/style-guide/)，进行了提炼及小部分删减。

### 必要规范
- **组件名应该始终是多个单词**，根组件 App 以及 <transition>、<component> 之类的 Vue 内置组件除外。这样做可以避免跟现有的以及未来的 HTML 元素[相冲突](https://w3c.github.io/webcomponents/spec/custom/#valid-custom-element-name)。
- **组件的 data 必须是一个函数**。当在组件中使用data属性的时候 (除了 new Vue 外的任何地方)，它的值必须是返回一个对象的函数。因为对象data会在组件的所有实例之间共享，而利用函数可以使每个实例变成一个独立的数据对象。
- **prop 的定义应该尽量详细**，至少需要指定其类型。
- **必须为 v-for 设置始终唯一的键值 key**，以便维护内部组件及其子树的状态。
- **避免把 v-if 和 v-for 同时用在同一个元素上**，一般有两种常见情况：
  - 为了过滤一个列表中的项目 (比如 `v-for="user in users" v-if="user.isActive"`)。在这种情形下，请将 users 替换为一个计算属性 (比如 activeUsers)，让其返回过滤后的列表。
  - 为了避免渲染本应该被隐藏的列表 (比如 `v-for="user in users" v-if="shouldShowUsers"`)。这种情形下，请将 v-if 移动至容器元素上 (比如 ul, ol)。
- **除 App 和布局组件以外的其它所有组件样式都应该设置作用域**，并且对于组件库应该更倾向于选用基于 class 的策略而不是 scoped attribute，使用唯一的 class 名同样可以避免样式冲突。
- **使用模块作用域保持不允许外部访问的函数的私有性。**如果无法做到这点，就始终为插件、混入等不考虑作为对外公共 API 的自定义私有属性使用 `$_` 前缀，并附带一个命名空间以回避和其它作者的冲突 (比如 `$_yourPluginName_`)。详见[私有属性名](https://cn.vuejs.org/v2/style-guide/#%E7%A7%81%E6%9C%89%E5%B1%9E%E6%80%A7%E5%90%8D-%E5%BF%85%E8%A6%81 "私有属性名 必要")。

### 推荐遵守规范
- **尽可能的将文件拆解成组件**，封装高质量、可复用组件。
- **单文件组件的文件名应始终是单词大写开头 (PascalCase)或横线连接 (kebab-case)。**单词大写开头对于代码编辑器的自动补全最为友好，因为这使得我们在 JS(X) 和模板中引用组件的方式尽可能的一致。（涉及到[__大小写不敏感的文件系统的问题__](https://zhaoluting.github.io/2019/04/19/tool-caseSensitive/)，待讨论）
- **特定样式和约定的基础组件应全部以特定的前缀开头**，比如 Base、App，便于统一管理。
- **只拥有单个活跃实例的组件应该以 The 前缀命名**，以示其唯一性。此单例组件应该每个页面只使用一次，不接受任何 prop。
- **和父组件紧密耦合的子组件应该以父组件名作为前缀命名，**这样可以把相关联文件排在一起。
- **组件名应以高级别** (一般化描述) **的单词开头，以描述性的修饰词结尾**，便于编辑器排序后组件之间的关系一目了然。
- **在单文件组件、字符串模板和 JSX 中没有内容的组件应该是自闭合的，**如`<MyComponent/>`，但在 DOM 模板里不要这样做。
- **在单文件组件、字符串模板、JS/**[**JSX**](https://cn.vuejs.org/v2/guide/render-function.html#JSX)** 中组件名应使用 PascalCase ，在 DOM 模板、只通过 Vue.component 定义全局组件的应用中必须使用 kebab-case。**
- **组件名应该倾向于完整单词而不是缩写，**不常用的缩写尤其应该避免。
- **声明 prop 时应使用 camelCase命名，在模板和 **[**JSX**](https://cn.vuejs.org/v2/guide/render-function.html#JSX)** 中应使用 kebab-case。**因为在 JavaScript 中更自然的是 camelCase，而在 HTML 中则是 kebab-case。
- **多个 attribute 的元素应分多行撰写**，每个 attribute 一行**。**
- **组件模板应该只包含简单的表达式**，复杂的表达式则应该重构为计算属性或方法，并且**把复杂计算属性尽可能多的分割为更简单的属性。**
- **非空 HTML attribute 值应带引号**，建议双引号**。**
- **指令缩写 **(用`:`表示`v-bind:` 、`@` 表示 `v-on:`、 `#`表示`v-slot:`) 要么都用要么都不用。

### 非强制规范
**组件/实例的选项的顺序**
1. **副作用** (触发组件外的影响)：`el`
2. **全局感知** (要求组件以外的知识)：`name`、`parent`
3. **组件类型** (更改组件的类型)：`functional`
4. **模板修改器** (改变模板的编译方式)：`delimiters`、`comments`
5. **模板依赖** (模板内使用的资源)：`components`、`directives`、`filters`
6. **组合** (向选项里合并属性)：`extends`、`mixins`
7. **接口** (组件的接口)：`inheritAttrs`、`model`、`props`/`propsData`
8. **本地状态** (本地的响应式属性)：`data`、`computed`
9. **事件** (通过响应式事件触发的回调)：`watch`、生命周期钩子 (按照被调用的顺序`beforeCreate`、`created`、`beforeMount`、`mounted`、`beforeUpdate`、`updated`、`activated`、`deactivated`、`beforeDestroy`、`destroyed`）
10. **非响应式的属性** (不依赖响应系统的实例属性)：`methods`
11. **渲染** (组件输出的声明式描述)：`template`/`render`、`renderError`

**元素 attribute 的顺序**
1. **定义** (提供组件的选项)：`is`
2. **列表渲染** (创建多个变化的相同元素)：`v-for`
3. **条件渲染** (元素是否渲染/显示)：`v-if`、`v-else-if`、`v-else`、`v-show`、`v-cloak`
4. **渲染方式** (改变元素的渲染方式)：`v-pre`、`v-once`
5. **全局感知** (需要超越组件的知识)：`id`
6. **唯一的 attribute** (需要唯一值的 attribute)：`ref`、`key`
7. **双向绑定** (把绑定和事件结合起来)：`v-model`
8. **其它 attribute** (所有普通的绑定或未绑定的 attribute)
9. **事件** (组件事件监听器)：`v-on`
10. **内容** (覆写元素的内容)：`v-html`、`v-text`

**单文件组件的顶级元素的顺序**
`<style>`放在最后，所有组件顺序都保持一致即可

```html
<!-- ComponentA.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```


### 小建议
- **如果一组 `v-if` + `v-else`的元素类型相同，最好使用**`key` (比如两个 `<div>` 元素)。因为vue在相同类型的元素之间切换时，会修补已存在元素，而不是将旧的元素移除然后在同一位置添加新元素。如果本不相同的元素被识别为相同，可能会出现[意料之外的结果](https://jsfiddle.net/chrisvfritz/bh8fLeds/)。
- **元素选择器应该避免在`scoped`中出现**，大量的元素和 attribute 组合的选择器 (比如 `button[data-v-f3f3eg9]`) 会比类和 attribute 组合的选择器 慢，所以应该尽可能选用类选择器。
- **优先通过 prop 和事件进行父子组件之间的通信**，而不是 `this.$parent` 或改变 prop。 遵循prop 向下传递，事件向上传递的约定。
- **优先通过[Vuex](https://github.com/vuejs/vuex)管理全局状态**，而不是通过 `this.$root` 或一个全局事件总线。


## CSS规范
业界出名的规范是[BEM](http://getbem.com/introduction/)，但太复杂，个人建议使用kebab-case这种简单规范，参考了[编码规范 by @mdo](https://codeguide.bootcss.com/)，详见：
### 语法
- 对于属性值或颜色参数，值在 -1 和 1 之间时去掉小数点前的 “0”，如果属性值为数字 0，不加任何单位；
- 十六进制值应该全部小写，且尽量使用简写形式的十六进制值，例如，用 `#fff` 代替 `#ffffff`
- 属性选择器或属性值使用双引号 "" 括起来
- 不要在 `rgb()`、`rgba()`、`hsl()`、`hsla()` 或 `rect()` 值的内部的逗号后面插入空格
- 在每个声明块的左花括号前、每条声明语句的冒号后、圆括号中的数学计算表达式的数值、变量和操作符之间都应该插入一个空格
- 每条声明都应该独占一行，声明块的右花括号应当单独成行
- 块级内容都应缩进，用两个空格来代替制表符（tab）
- 所有声明语句都应当以分号结尾
- 将媒体查询放在尽可能相关规则的附近，不要将打包放在单一样式文件中或文档底部
- 对于只包含一条声明的样式，为了易读性和便于快速编辑，建议将语句放在同一行。对于带有多条声明的样式，还是应当将声明分为多行。

```css
.selector,
.selector-secondary,
.selector[type="text"] {
  /* Positioning */
  z-index: 100;

  /* Box-model */
  padding: 15px;
  margin-bottom: 15px;

  /* Typography */
  font: normal 13px "Helvetica Neue", sans-serif;

  /* Visual */
  background-color: rgba(0,0,0,.5);
  -webkit-box-shadow: 0 1px 2px rgba(0,0,0,.15);
          box-shadow: 0 1px 2px rgba(0,0,0,.15);
}

.icon           { background-position: 0 0; }
.icon-home      { background-position: 0 -20px; }
.icon-account   { background-position: 0 -40px; }
```

### 注释规范
在适当的位置给予代码正确的注释，让他人更容易理解。
```css
/* Wrapping element for .modal-title and .modal-close */
.modal-header {
}
```
如果是对整个文件做注释，最好放在文本头部，简要描述一下文中元信息以及作用。
```css
/**
 * 这里描述元信息
 */
html, body {
  height:100%;
}
```

### 样式顺序规范
相关的属性说明放在一组，提高代码的可读性。
1. Positioning，布局方式、位置，如position, left, right, top, bottom, z-index等
2. Box model，盒模型，如display, float, width, height, margin, padding等
3. Typographic文本排版，如font, color, line-height, text-align
4. Visual视觉外观，如background, border, border-radius
5. Misc杂项，如opacity, transform, animation

**完整排序请参考**：[__https://github.com/twbs/stylelint-config-twbs-bootstrap/blob/master/css/index.js__](https://github.com/twbs/stylelint-config-twbs-bootstrap/blob/master/css/index.js)

### 命名规范
#### 选择器命名规范
- class 名称中只能出现小写字符和破折号（dashe）（不是下划线，也不是驼峰命名法）。破折号应当用于相关 class 的命名（类似于命名空间）（例如，`.btn` 和 `.btn-danger`）。
- 避免过度任意的简写。`.btn` 代表 button，但是 `.s` 不能表达任何意思。
- class 名称应当尽可能短，并且意义明确。
- 使用有意义的名称。使用有组织的或目的明确的名称，不要使用表现形式（presentational）的名称。
- 基于最近的父 class 或基本（base） class 作为新 class 的前缀。
- 使用 `.js-*` class 来标识行为（与样式相对），并且不要将这些 class 包含到 CSS 文件中。

#### 文件命名规范
1. index.css: 一般用于首页建立样式
2. base.css: 共用样式
3. style.css: 独立页面所使用的样式文件
4. global.css: 页面样式基础，全局公用样式，页面中必须包含
5. layout.css: 布局、版面样式，公用类型较多时使用，一般用在首页级页面和产品类页面中
6. themes.css: 主体样式

#### 页面结构命名
1. page: 代表整个页面，用于最外层。
2. wrap: 所有元素包在一起的一个外围包，用于最外层
3. wrapper: 页面外围控制整体布局宽度，用于最外层
4. container: 一个整体容器，用于最外层
5. head, header: 页头区域，用于头部
6. nav: 导航条
7. content: 内容，网站中最重要的内容区域，用于网页中部主体
8. main: 网站中的主要区域（表示最重要的一块位置），用于中部主体内容
9. column: 栏目
10. sidebar: 侧栏
11. foot, footer: 页尾、页脚。网站一些附加信息放置区域，或命名为 copyright用于底部


#### 功能命名
1. breadcrumb: 页面所处位置导航提示
2. logo: 标记网站logo标志
3. banner: 标语、广告条、顶部广告条
4. homepage: 首页
5. subpage: 二级页面子页面
6. current: 当前的
7. login: 登陆，（例如登录表单：form-login）
8. register: 注册
9. tool, toolbar: 工具条
10. searchbar: 搜索条
11. searchlnput: 搜索输入框
12. icon: 小图标
13. label: 商标
14. hot: 热门热点
15. list: 文章列表，（例如：新闻列表：list-news）
16. scroll: 滚动
17. tab: 标签
18. msg 或 message: 提示信息
19. status: 状态
20. btn: 按钮，（例如：搜索按钮可写成：btn-search）
21. tips: 小技巧
22. note: 注释
23. arr, arrow: 标记箭头
24. download: 下载
25. vote: 投票

#### 导航命名
1. nav, navbar, navigation, nav-wrapper: 导航条或导航包，代表横向导航
2. topnav: 顶部导航
3. mainbav: 主导航
4. subnav: 子导航
5. sidebar: 边导航
6. leftsidebar 或 sidebar_a: 左导航
7. rightsidebar 或 sidebar_b: 右导航
8. title: 标题
9. summary: 摘要
10. menu: 菜单，区域包含一般的链接和菜单
11. submenu: 子菜单
12. drop: 下拉
13. dorpmenu: 下拉菜单
14. links: 链接菜单

#### 页尾命名
1. sitemap: 网站地图
2. joinus: 加入
3. siteinfo: 网站信息
4. partner: 合作伙伴
5. link, friendlink: 友情链接
6. copyright: 版权信息
7. siteinfoCredits: 信誉
8. siteinfoLegal: 法律信息


