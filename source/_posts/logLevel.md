---
title: logLevel
date: 2019-04-19 17:22:44
tags: tool
categories: 规范
---

## 简介

- 相关链接：
  - <https://www.npmjs.com/package/loglevel>
  - <https://github.com/pimterry/loglevel>
- 推荐理由：
  - 使用本插件的项目众多，包括webpack-dev-server等。
  - 轻量、跨平台、兼容性强，基本功能都有，也比较干净。
  - 2013年创建的，一直有在维护，issue中也看出来作者比较靠谱。

## 安装

```bash
npm install loglevel --save
```



## 引入

```javascript
// ES6 module
import log from 'loglevel'; 

// CommonsJS (e.g. Node)
var log = require('loglevel'); 

// AMD (e.g. RequireJS)
define(['loglevel'], function(log) {
   log.warn("dangerously convenient");
});

// 直接引入
<script src="loglevel.min.js"></script>
```



## 使用

### level

5种常规log，级别从低到高

```javascript
log.trace(msg) // 跟踪记录信息，和debug类似，级别最低
log.debug(msg) // debug调试信息，同log.log(msg)
log.info(msg)  // 提示说明性信息
log.warn(msg)  // 警告，潜在错误信息
log.error(msg) // 错误事件信息
```

相关函数

```javascript
/*
	- 禁用所有低于给定级别的日志，默认为warn
	- 第一个参数为级别(level):
      - 可为字符串：trace、debug、info、warn、error、silent（不区分大小写）；
      - 可为数字：0到5(trace到silent)；
      - 亦可为内置变量(例如log.levels.DEBUG)
	- 第二个参数为持久化选项:
      - loglevel会尽可能使用LocalStorage和cookies来实现持久化
      - 若两者都无法使用或persist设为false，将不会使用存储。
*/
log.setLevel(level, [persist])

/* 设置默认级别，参数同setLevel */
log.setDefaultLevel(level)

/* 启用所有日志消息,等同于log.setLevel("trace") */
log.enableAll()

/* 禁用所有日志消息,等同于log.setLevel("silent") */
log.disableAll()

/* 返回当前日志级别，从0到5(trace到silent) 的数字 */
log.getLevel()
```



### 模块

```javascript
/* 创建新的logger对象，可以单独为其设置级别和方法 */
log.getLogger(loggerName)

/* 返回所有使用getLogger创建的Logger对象 */
log.getLoggers()
```



### methodFactory

```javascript
/* 
	- 每次设置级别（包括初始化）时，都会运行methodFactory方法进行重置
	- 可在此函数中，对输出日志进行格式化或异步处理
	- 最后务必调用setLevel方法以使其生效
*/
var originalFactory = log.methodFactory;
log.methodFactory = function (methodName, logLevel, loggerName) {
	var rawMethod = originalFactory(methodName, logLevel, loggerName);
	// 在每条输出的日志前加上"Newsflash: "前缀
	// 要注意传多个参数的情况 https://github.com/pimterry/loglevel/issues/86
	return function () {
		var argsArray = Array.prototype.slice.call(arguments);
		argsArray.unshift("Newsflash: ");
		rawMethod.apply(this, argsArray);
	};
};
log.setLevel(log.getLevel());
```



### 其他

```javascript
// 若与其他库冲突，可使用noConflict函数，并使用其他名字命名
var logging = log.noConflict();

// 一个对象：{ TRACE: 0, DEBUG: 1, INFO: 2, WARN: 3, ERROR: 4, SILENT: 5 }
log.levels
```



## 示例

**模块的使用**

```javascript
// 在 module-one.js 文件中:
var log = require("loglevel").getLogger("module-one");
function doSomethingAmazing() {
  	log.debug("Amazing message from module one.");
}

// 在 module-two.js 文件中:
var log = require("loglevel").getLogger("module-two");
function doSomethingSpecial() {
  	log.debug("Special message from module two.");
}

// 在 main.js 文件中:
var log = require("loglevel");
var moduleOne = require("module-one");
var moduleTwo = require("module-two");
log.getLogger("module-two").setLevel("TRACE");

moduleOne.doSomethingAmazing(); 
moduleTwo.doSomethingSpecial(); 
// 控制台中只输出了 "Special message from module two."，因为moduleOne设置的级别为默认的‘warn’
```



**在浏览器环境下，为日志添加样式及执行异步操作**

```javascript
 // ES6 module
import log from 'loglevel';

// 美化样式
function setColor(message, methodName, loggerName) {
	const mapping = { trace: '#389e0d', debug: '#f70', info: '#1890FF' };
	// 仅适用于浏览器环境
	if (mapping[methodName] && typeof window !== 'undefined') {
		return [
		`%c${methodName}-${loggerName || 'Global'}`,
		`background: ${mapping[methodName]};color: #fff;padding: 3px 5px;margin-right: 5px`,
		].concat(message);
	} else {
		return message;
	}
}
// 模拟异步请求
function sendLogMsg(message, methodName, loggerName) {
	setTimeout(() => {
		console.log('已发送', message, methodName, loggerName);
	}, 3000);
}
 
// 自定义logger函数
function initLogger() {
 	const originalFactory = log.methodFactory;
	log.methodFactory = (methodName, logLevel, loggerName) => {
		const rawMethod = originalFactory(methodName, logLevel, loggerName);
		const that = this;
		return function () {
			// 在箭头函数中call和apply会忽略第一个参数，故不用箭头函数
			let logMsg = Array.prototype.slice.call(arguments);
			// 模拟上报日志
			that.sendLogMsg(logMsg, methodName, loggerName);
			// 输出
			rawMethod.apply(this, that.setColor(logMsg, methodName, loggerName));
		};
	};
	// 应用自定义methodFactory函数
	log.setLevel(process.env.NODE_ENV === 'development' ? 0 : 'warn');
}
```



**在node环境下，为日志添加样式**

着色插件：<https://github.com/chalk/chalk>

```javascript
var log = require('loglevel'); 
var chalk = require('chalk');  

function writeLog() {
	log.setLevel(0);
	log.info(chalk.white.bgGreen.bold('Hello world!'));
}
```

