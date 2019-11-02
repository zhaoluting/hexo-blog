---
title: commit辅助工具
date: 2019-04-19 17:32:23
tags: tool
categories: 工具
---

## 说明

**工具推荐：**

- [Commitizen](https://github.com/commitizen/cz-cli)：是一个撰写合格 Commit message 的工具。
- [cz-conventional-changelog](https://github.com/commitizen/cz-conventional-changelog)：是符合AngularJS规范的Commitizen标准配置插件。
- [standard-version](https://github.com/conventional-changelog/standard-version)：根据符合规范的commit记录自动生成更改日志文件。
- [commitlint](https://github.com/conventional-changelog/commitlint)：检查 commit 信息是否符合规范。
- [gitmoji](https://github.com/carloscuesta/gitmoji)：私人项目可以用用这个比较俏皮有趣的插件。

<!--more-->

**commit格式规范：**

每条commit记录都由header、body、footer(标题行、详细内容、尾部标注)组成。header具有特殊格式，包括type、scope、subject(类型、修改范围、简短描述)，其中，Header 是必需的，Body 和 Footer 可以省略：

```html
<type>(<scope>): <subject>
// 空行
<body>
// 空行
<footer>
```

**header中的scope类型有以下几种：**

- feat: 新功能（feature）
- fix: 修补bug 
- docs: 文档改动
- style: 不影响代码运行的格式改动
- refactor: 重构，不是新增功能或修补bug的代码变动
- perf: 提高代码性能的代码变动
- test: 新增或修改测试用例
- build: 影响构建系统或外部依赖的更改（如webpack、broccoli、npm等）
- ci: 对CI配置文件和脚本的变动（如travis、circle、browserstack、saucelabs等）
- chore: 不涉及src或测试文件的代码变更 
- revert: 代码回退



## 使用

- 团队项目安装个Commitizen差不多就可以了。
- 如果需要生成CHANGELOG文件，再单独安装下standard-version即可。
- 如果想要非常非常严格的规范，可以统一安装commitlint，并配上[@commitlint/config-conventional](https://github.com/conventional-changelog/commitlint/blob/master/@commitlint/config-conventional)官方推荐配置。

### Commitizen

**推荐项目级安装：**

```bash
npm install commitizen --save-dev
commitizen init cz-conventional-changelog --save-dev --save-exact
```

**使用：**

1、执行`git add .`后使用`git cz`替代`git commit`命令。

2、按下回车后按提示输入commit信息：

```
? Select the type of change that you're committing:
  docs:     Documentation only changes

? What is the scope of this change (e.g. component or file name)? (press enter to skip)
   更改范围headerScope

? Write a short, imperative tense description of the change:
   简单描述headerSubject

? Provide a longer description of the change: (press enter to skip)
   详细描述(body)

? Are there any breaking changes?
   Yes

? Describe the breaking changes:
  当前代码与上一版本不兼容，说明对变动的描述、理由和迁移方法(footer)

? Does this change affect any open issues?
   Yes

? Add issue references (e.g. "fix #123", "re #123".):
   fix #相关issue(footer)
```

3、若提交失败，执行`git cz —retry`进行重试则不需要重新输入信息。

4、提交成功后，就能看到log记录格式为：

```
Author: zhaoluting <zhaoluting@51dojo.com>
Date:   Thu Apr 18 19:49:31 2019 +0800

    docs(更改范围headerScope): 简单描述headerSubject
    
    详细描述(body)
    
    BREAKING CHANGE: 当前代码与上一版本不兼容，说明对变动的描述、理由和迁移方法(footer)
    
    fix #相关issue(footer)
```



### standard-version

1. 推荐全局安装：`npm install standard-version -g`
2. 项目下运行`standard-version`，会自动生成CHANGELOG.md文件、变更版本号、提交commit、打tag。并出现以下提示:

```
✔ bumping version in package.json from 2.0.2 to 2.0.3
✔ bumping version in package-lock.json from 2.0.2 to 2.0.3
✔ outputting changes to CHANGELOG.md
✔ committing package-lock.json and package.json and CHANGELOG.md
✔ tagging release v2.0.3
ℹ Run `git push --follow-tags origin master && npm publish` to publish
```



**常用命令：**

- standard-version --first-release // 为第一个版本生成CHANGELOG
- standard-version --release-as 1.1.0  // 自定义版本号 => 1.1.0 
- standard-version --release-as major // 强制大版本更新 1.1.0 => 2.0.0
- standard-version --release-as minor // 强制小版本更新 1.1.0 => 1.2.0



**跳过步骤：**

通过package.json的配置可以跳过任意步骤：bump,changelog, commit, tag

```json
{
  "standard-version": {
    "skip": {
      "commit": true
    }
  }
}
```



参考链接：

[angular commit规范](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#-git-commit-guidelines)

[Commit message 和 Change log 编写指南](<http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html>)

