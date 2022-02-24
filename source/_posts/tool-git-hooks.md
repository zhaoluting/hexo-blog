---
title: githooks
date: 2022-02-21 15:21:40
tags:
  - tool
  - git
categories: 工具
---

最近发现了[githooks](https://git-scm.com/docs/githooks)这个宝藏，前端对hook应该都不陌生，就是钩子。githooks就是在特定动作发生时触发自定义脚本。很多前端项目都会采用Husky在代码提交前进行Lint校验和commit提交校验。想起来曾经写过[《常规eslint配置》](/tool-eslint/)和[《git commit辅助工具》](/tool-git-commit)，文章提到的方案只能靠开发自觉遵守，但是有了这个githooks就能用一些相对比较强硬的手段。当然，想要更加强硬的话甚至可以把校验放在部署那块。

<!--more-->

## githooks分类
githooks可分为客户端hooks和服务端hooks。hooks太多了，就挑一些常用的介绍下，其他的看[官网](https://git-scm.com/docs/githooks)。

> `pre-`前缀代表即将发生，通常用于检查；`post-`前缀代表已结束，通常用于通知。

### 客户端Hooks
客户端Hooks可分为提交工作流、E-mail工作流和其他工作流。E-mail工作流基本用不到，所以跳过。

#### 提交工作流
关于`git commit`的有四个挂钩，触发顺序为：pre-commit、prepare-commit-msg、commit-msg、post-commit。

- **pre-commit**：在填写提交信息和生成提交对象之前执行。可以用来做初步的code review，检查代码是否符合规范、自动化测试等。
- **prepare-commit-msg**：提交信息显示之前，默认信息被创建之后调用。可以结合提交模板，为合并提交、压缩提交、修订提交等这类会自动产生默认信息的提交，动态地插入信息。
- **commit-msg**：在用户输入提交信息之后调用。可以用来检查提交信息是否符合规范。
- **post-commit**：在整个提交过程完成后调用。通常用来提示一些信息。

#### 其他工作流
- **pre-rebase**：在`git rebase`执行之前调用。可以用来规范rebase操作，建议阅读`.git/hooks/pre-rebase.sample`中的代码。
- **post-checkout**：`git checkout`执行完成后调用。可以用来清理一些不被git追踪的临时文件。
- **post-merge**：`git merge`执行完成后调用。可以用来恢复Git无法跟踪的工作区数据。
- **pre-push**：`git push`之前调用，可以在推送之前验证对引用的更新操作。

### 服务器端Hooks
- **pre-receive**：处理来自客户端的推送操作时最先被调用。可以用来阻止对引用进行非快进（non-fast-forward）的更新，或对该推送修改的所有引用和文件进行访问控制。
- **update**：在pre-receive之后调用，且与pre-receive类似，区别是它会为推送者更新的每一个分支运行一次。
- **post-receive**：在成功推送之后调用。可以用来发邮件、通知持续集成的服务器、更新issues等等。

## 简单尝试
每个git仓库下都有个很核心的`.git`文件夹，因为`.git`里的信息比较敏感，所以git不会将它上传到远程仓库，vscode默认也是隐藏的。`.git/hooks`目录下存储的就是各种hooks，文件名就对应着不同的钩子，在执行特定命令时就会调用这些脚本，其中`.sample`后缀的就是官网样例。

1. 可以在vscode的设置中修改工作区配置(`.vscode/settings.json`)，显示出`.git/hooks`文件夹。
```json
{
  "files.exclude": {
    "**/.git": false,
    "**/.git/{objects,[!hooks]*}": true,
  }
}
```

2. 项目根目录新建一个`.githooks`文件夹，此文件夹下新建`post-checkout`文件，并写入以下内容：
```sh
#!/bin/bash
curBranch=`git branch | grep "*"`
echo -e "# 当前分支：${curBranch:2} \n# 当前commit：${2}"
```

3. 如果git版本在2.9以上，可以手动配置githooks所在的目录，在项目根目录下执行：
```sh
git config core.hooksPath .githooks
```
  如果git版本在2.9以下，可以创建软链指向到`.git/hooks`：
  ```sh
  find .git/hooks -type l -exec rm {} \; && find .githooks -type f -exec ln -sf ../../{} .git/hooks/ \;
  ```

4. 最后设置hook文件的可执行权限进行脚本激活：
```sh
chmod +x .githooks/post-checkout
```

## Husky
Husky（二哈.png）可以自动配置githooks，个人项目里使用Husky会变得更加便利，团队里也可以使用Husky来做hooks共享。webpack、babel、angular、antd等大型开源项目都使用了Husky，还是比较值得信赖的。不放心可以去看下源码，代码很短也不复杂。

### 说明
不同版本Husky工作原理略有不同，其中Husky 6可以作为一个分水岭。所谓用新不用旧嘛，如果老项目里用的还是旧版，可以使用[husky-4-to-7](https://typicode.github.io/husky/#/?id=migrate-from-v4-to-v7)工具快速迁移到新版。
- **原理**：旧版是直接修改`.git/hooks`中的脚本，Husky 6则开始采用git 2.9的特性`core.hooksPath`来指定githooks所在的目录，同时也解决了一些根本上的问题。
- **使用方式**：旧版支持在`package.json`中通过`husky: {hooks: {"pre-commit": xxx} }`的方式来更新脚本，而新版仅支持修改`.husky/`目录下的脚本文件。


新版Husky中有`npx husky-init`这个关键命令，通过它就能大致了解Husky的工作原理，下面以npm为例：
- 利用npx临时下载并运行[`husky-init`](https://github.com/typicode/husky-init)这个依赖包。
  - 读取`package.json`文件，在`devDependencies`中添加`husky`包，在script中添加`"prepare": "husky install"`指令。
  - 在根目录生成`.husky`文件夹，也会生成一个`pre-commit`样例。
- 再借助`husky`依赖包的`install`完成：
  - 在根目录的`.husky`中生成`_`文件夹，其中有关键的`husky.sh`文件。
  - 执行`config core.hooksPath .husky`（需要git2.9以上），将githooks目录指向`.husky`。若git版本不够，建议升级，要是实在升不了，安装旧版husky。


### 安装和使用
由于Husky的功能与编译无关，遵循[“The only valid use of install or preinstall scripts is for compilation which must be done on the target architecture”](https://docs.npmjs.com/cli/v7/using-npm/scripts#best-practices)，Husky不再支持自动安装，所以还需要做`npm install husky`以外的事情。

完整教程请看[官方文档](https://typicode.github.io/husky/#/)，例如关于Monorepo、环境变量、Git-flow等等文档里都写的很清楚。Yarn使用husky稍微有点复杂，不支持`prepare`，私有仓库公有仓库配置也不同，用Yarn的就直接看[官网文档](https://typicode.github.io/husky/#/?id=yarn-2)吧。Npm使用起来就比较简单，所以下面就只从npm的角度介绍。

1. 首次安装直接运行以下对应的命令：
```bash
npx husky-init && npm install
```

2. 如果不想使用步骤一的自动安装，也可以选择手动安装，相当于是拆分了`husky-init`，只需分步执行以下命令：
```bash
npm install husky --save-dev
npx husky install
npm set-script prepare "husky install"
```
  其中`npm set-script`需要npm7以上，如果版本过低，建议升级或自己手动向`package.json`添加。

3. 添加hook：
```bash
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'
```

4. 如果不想执行hook，可以利用HUSKY环境变量绕过hook：
```bash
HUSKY=0 git push
```

5. 卸载Husky：
```bash
npm uninstall husky && git config --unset core.hooksPath
```


## yorkie
[yorkie](https://github.com/yyx990803/yorkie)是尤大fork自husky，并内置到了[Vue CLI](https://cli.vuejs.org/zh/guide/cli-service.html#git-hook)，能在`package.json`的`gitHooks`字段中方便地指定Git hook。
yorkie对husky并不兼容，它对monorepo进行了改良，同时也修改了配置字段。但是yorkie最后一次更新是18年🤷🏻‍♀️基本上算是停止维护了。而且它fork的是旧版husky，即原理仍是直接修改`.git/hooks`中的脚本。这时候就会面临yorkie和husky二选一，因为同时使用会产生冲突，如果yorkie够用，则不需要再安装husky。如果想要新功能或者发现了一些无法解决的问题，可以选择husky。

```json
{
  "gitHooks": {
    "pre-commit": "lint-staged"
  },
   "lint-staged": {
    "*.{js,vue}": "vue-cli-service lint"
  }
}
```

## 小技巧
- githooks支持shell、Perl、Ruby、Python等脚本语言，可以选用任意你擅长的，甚至是NodeJs。
- 建议阅读`.sample`后缀的官网样例，里面有hook基本介绍、参数说明和实用代码，非常值得借鉴。
- 大多都是使用hooks来辅助团队内的编码规范和commit规范，此外hooks还能做流程规范、自动部署（不太好用）等等。
- 如果有使用sourcetree这类工具，hooks可能不会生效，主要是环境变量问题，此时需要在脚本中添加`export PATH=/usr/local/bin:$PATH`，来临时指定PATH环境变量。


### 辅助工程化
网上教程很多，步骤也挺多，我就只写一下常见的组合吧😝

- **代码规范**：[husky](https://typicode.github.io/husky/) + [lint-staged](https://github.com/okonet/lint-staged) + [eslint](http://eslint.cn/) + [prettier](https://prettier.io/)
- **git commit规范**：[husky](https://typicode.github.io/husky/) + [commitlint](https://commitlint.js.org/#/)

### hook测试和禁用
exit命令一般用于结束脚本，执行成功会返回0，非零退出则表示脚本执行失败。
所以，要测试hook可以在脚本末尾添加`exit 1`来中止git命令。禁用也一样，例如想要禁用rebase，就可以这样创建`pre-rebase`脚本：
```sh
#!/bin/sh

# Disallow all rebasing
echo "pre-rebase: Rebasing is dangerous. Don't do it."
exit 1
```

### NodeJs脚本样例 —— Husky
创建`.husky/post-checkout.js`文件，写入js代码：
```javascript
console.log(process.argv)
```

创建post-checkout脚本，调用js文件，同时传入参数
```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"
# 支持 sourcetree
export PATH=/usr/local/bin:$PATH
node ./.husky/post-checkout.js $0 $1 $2 $3
```