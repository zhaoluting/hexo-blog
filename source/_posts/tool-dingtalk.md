---
title: 钉钉应用开发
date: 2022-06-08 15:54:22
tags: tool
categories: 工具
---

钉钉微应用开发起来难度不大，具体使用方法可以看[官方文档](https://open.dingtalk.com/document/orgapp-client/read-before-development)。里面的控件、事件监听等等小功能，调试起来没什么大问题，文档写的也还可以，再配合使用官方的各种[调试工具](https://open.dingtalk.com/document/resourcedownload/jsapi-explorer)，我用下来还是蛮顺利的。主要麻烦的地方是鉴权部分，如果所以本文也主要放鉴权这一块的内容。还可以自己创建个企业玩玩，也能熟悉下[钉钉开放平台](https://open-dev.dingtalk.com/fe/app#/corp/app)对应用的配置。此外，钉钉浏览器内核以Chrome为主，但一定要注意样式的兼容性。

<!--more-->

## 安装
通常是使用npm安装，按需引入。
```shell
npm install dingtalk-jsapi --save 

import * as dd from 'dingtalk-jsapi';
```

## 鉴权

### 免登授权码
```javascript
DingLogin(callback) {
	if (dd.env.platform == "notInDingTalk") {
		let code = urlTool.getQuery('code', window.location.href);
		if (code) {
			handelTool.loginXHR({code: code}, callback)
		} else {
			Message({ type: 'warning',  message: '请在钉钉内打开' })
		}
	} else {
		dd.ready(() => {
			dd.runtime.permission.requestAuthCode({
				corpId: 'ding******',
				onSuccess: (result) => {
					handelTool.loginXHR(result, callback)
				},
				onFail(err) {}
			});
		});
	}
},
```

### 扫码登录
```javascript
<template>
	<div id="ding-container" class="qr-code"></div>
</template>

<script>
import * as dd from 'dingtalk-jsapi'
export default {
	data() {
		return {
			appid: 'ding*********',
			// 企业id
			corpId: 'ding*********',
			isDing: false, 
		};
	},
	props: {},
	methods: {
		handleLogin(params) {
			this.$emit('login', params);
		},
		// 处理钉钉登录
		initDingLogin() {
			let _this = this;
			const code = _getQuery('code');
			if (code) {
				this.handleLogin({
					authType: 'scanCode',
					code: code
				})
			}
			_loadScript("https://g.alicdn.com/dingding/dinglogin/0.0.5/ddLogin.js", function () {
				_this.initDingQR();
			})
		},
		// 加载钉钉二维码
		// https://open.dingtalk.com/document/orgapp-server/scan-qr-code-to-log-on-to-third-party-websites
		initDingQR() {
			let params = `?appid=${this.appid}&response_type=code&scope=snsapi_login&state=STATE&redirect_uri=${window.$config.url}/dingding/`
			let goto = encodeURIComponent('https://oapi.dingtalk.com/connect/qrconnect' + params);
			let dingQR = window.DDLogin({
				id: 'ding-container',
				goto: goto,
				style: 'border:none;background-color:unset;',
				width: '391',
				height: '300'
			})
			let handleMessage = (event) =>{
				let origin = event.origin;
				if( origin == "https://login.dingtalk.com" ) {
					let loginTmpCode = event.data; 
					window.location.href = 'https://oapi.dingtalk.com/connect/oauth2/sns_authorize' + params  + `&loginTmpCode=${loginTmpCode}`
				}
			};
			if (typeof window.addEventListener != 'undefined') {
					window.addEventListener('message', handleMessage, false);
			} else if (typeof window.attachEvent != 'undefined') {
					window.attachEvent('onmessage', handleMessage);
			}
		},
		handleFromDing() {
			const access_token = _getQuery('access_token');
			this.isDing = (dd.env.platform !== 'notInDingTalk');
			this.$emit('setDingShow', !this.isDing && !access_token)
			// 从钉钉应用打开
			if (access_token) {
				this.handleLogin({
					authType: 'dingding',
					authCode: access_token
				})
			} else if (this.isDing) {
				dd.ready(function () {
          dd.runtime.permission.requestAuthCode({
            corpId: this.corpId, // 企业id
            onSuccess: function (info) {
              window.open(
                `${window.location.origin}${window.location.pathname}#/login?access_token=${info.code}`
              )
            },
						onFail: function(err) {
							window.open(
                `${window.location.origin}${window.location.pathname}#/login`
              )
						}
          })
        })
			} else {
				// 在浏览器中
				this.initDingLogin();
			}
		},
    //  组件加载js文件
    _loadScript(src, callback) {
      let script = document.createElement('script');
      let head = document.getElementsByTagName('head')[0];
      script.type = 'text/javascript';
      script.charset = 'UTF-8';
      script.src = src;
      if (script.addEventListener) {
        script.addEventListener('load', function () {
          callback();
        }, false);
      } else if (script.attachEvent) {
        script.attachEvent('onreadystatechange', function () {
          let target = window.event.srcElement;
          if (target.readyState == 'loaded') {
            callback();
          }
        });
      }
      head.appendChild(script);
    },
    // 获取单个URL参数
    _getQuery(name, urlString) {
      // 检验参数是否为空
      if (name) {
        let url = urlString || window.location.search || window.location.hash;
        let search = url.substring(url.indexOf('?') + 1);
        let reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
        let result = unescape(search).match(reg);
        return (result !== null) ? result[2] : null;
      }
      return null
    },
	},

	mounted() {
    this.handleFromDing();
	},
};
</script>
```
