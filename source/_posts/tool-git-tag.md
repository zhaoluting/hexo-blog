---
title: git tag使用指南
date: 2022-02-07 16:42:06
tags: tool
categories: 工具
---

如果一个频繁迭代的项目单纯靠分支来做版本控制，一个需求一个分支，那么分支就会越来越多，分不清主次，尤其情况比较复杂的项目，各种版本分支就会交杂在一起，分支图就是一团乱麻。

我比较推荐使用git中的tag来做版本控制，这也是主流的版本控制方式，很多flow模型、GitHub的release功能等等都是基于tag。功能发布完打上tag，再删除无用分支，分支图也会更加清晰，日后的回溯和复查会更加便利。

<!--more-->

## 概述
tag顾名思义就是标签，git可以为仓库历史中的某一个commit打上tag标记，相当于一个快照，一般作为发布节点使用。tag经常和branch一起出现，tag可以被打在branch上的任意一个节点，甚至还能给tag打tag。

Git支持轻量和附注两种标签：
- 轻量标签（lightweight）：只是某个特定提交的引用，没有保存任何其他信息，不是一个对象。
- 附注标签（annotated）：存储在Git数据库中的一个完整对象，包含姓名、邮箱、日期、标签信息，可以使用GPG签名并验证。并且具有自己的hash ID，其他标签能直接指向此标签。
- 建议使用附注标签。通常附注标签意味着发布，轻量标签意味着私有或临时，因此一些git命令（如`git describe`）会默认忽略轻量标签。

tag命名一般遵循GitHub语义化版本命名规范（Semantic Versioning）：**主版本号.次版本号.修订号**
- 主版本号：当做了不兼容的 API 修改
- 次版本号：当做了向下兼容的功能性新增
- 修订号：当做了向下兼容的问题修正

可以使用以下几种方式来应用tag：
- 采用git-flow、gitlab-flow这类模型来做版本控制，它们的版本控制也是基于tag，有很多辅助工具，如扩展集[git-flow-avh](https://danielkummer.github.io/git-flow-cheatsheet/index.zh_CN.html)，可视化工具[Sourcetree](https://www.sourcetreeapp.com/)和[Git Tower](https://www.git-tower.com/)也内置git-flow，都能帮我们自动完成打tag的工作。
- 如果有自动化部署工具，就可以在合并至线上分支后时加入自动打tag的命令。
- 当然也可以选择自己来打tag，依个人喜好借助可视化工具或者直接使用命令行。


## git tag命令
### 常用命令
**查看**
```bash
# 列出已有的标签
git tag

# 列出筛选过的标签
git tag -l "v1.0*"

# 查看远程所有标签
git ls-remote --tags origin

# 查看本地所有标签，会输出与ls-remote相同格式的列表，便于与远程对比
git show-ref --tags

# 查看标签信息和与之对应的提交信息
git show v1.0.0
```

**创建**
```bash
# 创建附注标签
git tag -a v1.0.0 -m "附注信息"

# 创建轻量标签
git tag v1.0.1

# 对过去的提交打标签，在命令末尾指定提交的校验和（或部分校验和）
# git tag -a <tagName> <commitId>
git tag -a v1.1.0 9fceb02
```

**同步**
默认情况下，`git push`不会将本地标签同步到远程，需要手动推送。
```bash
# 推送标签到远程
git push origin v1.0.0

# 推送所有不在远程的标签
git push origin --tags

# 拉取远程标签
git fetch --tags
```

**切换**
需要注意的副作用：当本地代码切至某个标签后，会处于“分离头指针”状态，如果有新提交，新提交将不属于任何分支且无法访问（除非通过hash ID），标签也不会发生变化。
```bash
# 切换至某个标签所指向的文件版本
git checkout v1.0.0

# 基于某个标签创建新分支
# git checkout -b <branchName> <tagName>
git checkout -b version2 v1.0.0
```

**修改**
修改tag比较复杂，尤其涉及到已推送到远程的附注标签。如果还不能熟练使用tag，建议直接采用**删除旧标签、新建正确标签**的方式。照着网上乱七八糟的教程来反而容易出现各种奇奇怪的问题，比如破坏附注标签的签名、误建了指向另一标签的标签等等。

参考链接：[On Re-tagging](https://git-scm.com/docs/git-tag#_on_re_tagging)、[how-do-i-edit-an-existing-tag-message-in-git](https://stackoverflow.com/questions/7813194/how-do-i-edit-an-existing-tag-message-in-git)、[why-isnt-my-tag-listed-when-i-checkout-with-git-gui](https://stackoverflow.com/questions/49283734/why-isnt-my-tag-listed-when-i-checkout-with-git-gui)
```bash
# 修改本地已存在、还未推送到远程的tag注释
git tag v1.0.0 -f -m "new message"

# 重命名本地和远程的轻量标签
git tag <newTagName> <oldTagName>
git tag -d <oldTagName>
git push origin :refs/tags/<oldTagName>
git push --tags
```

**删除**
```bash
# 删除本地标签
git tag -d v1.0.1

# 删除远程标签
git push origin -d v1.0.1
```



### 常用选项
可查阅[Git-Basics-Tagging](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE)、[git-tag](https://git-scm.com/docs/git-tag)，或使用`git tag --help`命令。

- `-a`、`--annotate`：创建一个未签名、带注释的标记对象。
- `-l`、`--list`：列出标签。
- `-i`、`--ignore-case`：排序和过滤标签时不区分大小写。
- `-m <msg>`、`--message=<msg>`：设置标签信息，如果存在多个`-m`选项，它们将作为单独的段落连接起来。
- `-d`、`--delete`：删除指定标签。
- `-f`、`--force`：使用指定名称替换现有标签（而不是失败）。
- `-F <file>`、`--file=<file>`：从指定文件中获取标签消息。
- `-n<num>`：指定使用`-l`时打印多少行。
- `--sort=<key>`：根据关键字排序。
- `--contains <commit>`：只列出包含指定提交的标签（如果未指定，则为HEAD）。
- `--no-contains <commit>`：只列出不包含指定提交的标签（如果未指定，则为HEAD）。
- `-s`、`--sign`：使用默认邮箱地址密钥创建一个GPG签名标签。
- `-u <keyid>`、`--local-user=<keyid>`：使用指定密钥创建GPG签名标签。
- `-v`、`--verify`：验证指定标签的GPG签名。



