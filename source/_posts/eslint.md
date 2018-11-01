---
title: 常规eslint配置
date: 2018-07-22 16:24:36
tags: eslint
categories: 规范
---
## eslint配置过程
### 安装需要依赖的npm包

``` java
"eslint": "^4.19.1", // 必须
"babel-eslint": "^8.2.4", // 支持babel
"eslint-loader": "^2.0.0", // 使webpack支持eslint
"eslint-config-google": "^0.9.1", // Google的ESLint可共享配置
"eslint-plugin-vue": "^4.5.0", // Vue.js的官方ESLint插件
"eslint-plugin-import": "^2.13.0", // 支持使用第三方的扩展
```
**项目下运行命令行**：

``` bash
$ npm install --save-dev eslint@4.19.1 babel-eslint@8.2.4 eslint-loader@2.0.0 eslint-config-google@0.9.1 eslint-plugin-vue@4.5.0 eslint-plugin-import@2.13.0
```

<!--more-->

### 在项目最外层新建并编辑.eslintrc.js文件
**加入以下代码**：
```javascript
module.exports = {
    // 默认情况下，ESLint会在所有父级组件中寻找配置文件，一直到根目录。ESLint一旦发现配置文件中有 "root": true，它就会停止在父级目录中寻找。
    "root": true,
    // 扩展流行的风格指南
    "extends": ["plugin:vue/recommended", 'plugin:vue/essential', "eslint:recommended", "google"],
    // 此插件用来识别.html 和 .vue文件中的js代码
    "plugins": ["import", "vue"],
    // 对Babel解析器的包装使其与 ESLint 兼容。
    "parserOptions": {"parser": "babel-eslint"},
    // 自己的个性化规则
    "rules": {
        // 禁用 switch 中冒号前后的空格规则
        "switch-colon-spacing": 0,
        // 禁用 jsdoc 规则
        "require-jsdoc": 0,
        // 禁用每行字数限制规则
        "max-len": 0,
        // 允许打 console.log
        "no-console": 0,
        // 允许使用debugger语句
        "no-debugger": 0,
        // 禁用代码块首尾必须要空行的规则
        "padded-blocks": 0,
        // 不检查行尾空格
        "no-trailing-spaces": 0,
        // 使用 === 替代 ==
        "eqeqeq": 2,
        "vue/no-parsing-error": [2, {
            // 关闭标签闭合检查 
            "x-invalid-end-tag": false,
        }],
    }
};
```
> “off” 或 0       => 关闭规则  
> “warn” 或 1      =>开启规则，使用警告级别的错误：warn (不会导致程序退出)  
> “error” 或 2     =>开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)

**如果在webpack.config文件中设置了resolver配置，那么还需要:**
- 安装[eslint-import-resolver-webpack](https://www.npmjs.com/package/eslint-import-resolver-webpack)依赖。
``` bash
$ npm i --save-dev eslint-import-resolver-webpack@0.10.1
```
- 默认情况下，会从项目的compile/webpack.base.config.js文件中寻找webpack的resolver配置，但是这并不一定准确，因此可以自己重新配置正确的位置。
``` javascript
module.exports = {
    "extends": ["plugin:vue/recommended", 'plugin:vue/essential', "eslint:recommended", "google"],
    "settings": {
        'import/resolver': {
            webpack: {
                config: 'compile/webpack.base.conf.js'
            }
        }
    },
};
```


### 在webpack.config.js文件中添加配置
``` javascript
module: {
    rules: [
		{
        	test: /\.(js|jsx|vue)$/,
        	loader: 'eslint-loader',
        	enforce: 'pre',
      	},
    ]
}

```
### visio studio code 配置eslint
**如果想在代码编写时 使编辑器将错误代码用红色波浪线标出 并提示错误，那么就可以做下面几步：**

1. 左侧菜单栏选择“扩展”，搜索“eslint”、“Vetur”安装并重新加载
2. 配置vscode实现对.vue .html文件中的js代码段的检测： 顶部选项Code -> 首选项 -> 设置，搜索编辑“eslint.validate”添加 "html","vue"
``` java
"eslint.validate": [
      "javascript",
      "javascriptreact",
      "html",
      "vue",
]
```
3. 可以打开项目中.vue文件，不规范的语法就都已经被红色波浪线标记出来了。

## eslint修改技巧
### eslint – fix 修复指令
- 检查并修复项目所有代码
``` bash
node_modules/.bin/eslint --fix *
```
- 检查并修复指定文件
``` bash
node_modules/.bin/eslint --fix app.js
```
- **tips: 如果直接运行  eslint – fix  会报 Cannot find module '@u51/eslint-config-web' 的错误。**  
> config文件是安装在当前项目，直接eslint --fix file调用的是全局的eslint，所以无法找到当前项目的config文件。故使用 node_modules/.bin/eslint --fix 。

### vscode 保存自动格式化eslint代码
- 进入【设置】加上以下配置：
``` java
{
    "vetur.format.defaultFormatter.html": "js-beautify-html", // 格式化.vue中html
    "vetur.format.defaultFormatter.js": "vscode-typescript", // 让vue中的js按编辑器自带的ts格式进行格式化
    "eslint.enable": true,
    "eslint.autoFixOnSave":true, // 保存时自动fix
    "eslint.run": "onType",
    "eslint.options": {"extensions": [".js",".vue"]},
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "html",
        {"language": "vue", "autoFix": true} //开启对.vue文件中错误的检查
    ]
}
```
- 重启后，右键“格式化文件”或者修改文件后点击“保存”，就可自动格式化代码了。

### 忽略ESLint检测
如果需要修改代码量太大，可以先将未修改的文件忽略掉，以后逐步修改。
- 新建.eslintignore文件，放置需要ESLint忽略的文件。例如：
```
node_modules/
src/components/input-tag.vue
```
- 将 {% label success @/* eslint-disable */ %}块注释放在文件顶部，可以在整个文件范围内禁止规则出现警告。
- {% label success @// eslint-disable-next-line %} 可关闭下一行校验。

### 命令行检测、修改文件
- 打开package.json，在script里加入:
``` javascript
// -ext 后面写上指定检测文件的后缀，紧接着写上要检测的目录，即可用命令行来检测。
"lint": "eslint --ext .js,.jsx,.vue src/",
// 加上--fix，即可在检测的同时将文件自动格式化。
"lint-fix": "eslint --fix --ext .js,.jsx,.vue src/",
```
- 终端运行 {% label default@npm run lint %} 或者 {% label default@npm run lint-fix %}

## 参考文章
[eslint规则说明](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/default.md)
[ESLint官方文档](http://eslint.cn/)