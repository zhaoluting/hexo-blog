---
title: git备忘录
date: 2022-02-28 10:44:02
tags:
  - tool
  - git
categories: 工具
---

[之前写了挺多篇关于git的文章](/tags/git/)，也用了好几年了，这篇就来点更实用的吧。我是比较习惯使用git命令行，再配合vscode的一些小功能，sourcetree也尝试着用过，但习惯实在是改不过来，还是更喜欢用命令行。那就记录下一些我用的没那么频繁但又必须得知道的一些指令吧，不记下来我又得忘，害！

<!--more-->

## 推荐阅读
- [Pro Git](https://git-scm.com/book/zh/v2)
- [猴子都能懂的Git入门](https://backlog.com/git-tutorial/cn/)
- [git-tips](https://github.com/git-tips/tips)
- [Bitbucket-Advanced Tips](https://www.atlassian.com/git/tutorials/advanced-overview)
- [git-recipes](https://github.com/geeeeeeeeek/git-recipes)

## 易混淆的指令
- **pull VS fetch**：一般情况下用pull就行。
  - `git fetch`：取得远端仓库的最新历史记录，不会自动merge。
  - `git pull`：可简单理解为fetch + merge，即把远端仓库的内容fetch下来后再与本地分支合并。
- **revert VS reset**：更建议使用revert。
  - `git revert`：以新增一个commit的方式还原某一个commit的修改，不会改变提交历史。
  - `git reset`：回到某个commit的状态，并删除后面的commit。有风险，不建议使用。
- **merge VS rebase VS Cherry-Pick**：慎用rebase，参考[《代码合并：Merge、Rebase 的选择》](https://github.com/geeeeeeeeek/git-recipes/blob/master/sources/5.1-%E4%BB%A3%E7%A0%81%E5%90%88%E5%B9%B6Merge%E8%BF%98%E6%98%AFRebase.md)。
  - `git merge`：将两个分支合并到一起，并生成一个新的commit记录。
  - `git cherry-pick`：“复制”一个commit并在当前分支做一次完全一样的新提交。
  - `git rebase`：变基，在当前分支重演另一分支的commit，本质上是线性化、自动化的cherry-pick。绝不要在公共的分支上使用。



## 实用技巧
### 修改最新commit描述
```bash
git commit --amend -m "new commit info"
```

### 修改历史commit描述（非最新）
```bash
# HEAD~n代表要修改前第n次提交，也可以改用<commit_id>
git rebase -i HEAD~n
# 在出现的列表中，将需要修改的commit前的pick改为reword
# 提交新的commit描述
git commit --amend -m "new commit info"
# 继续执行rebase
git rebase --continue
# 会自动跳回原分支，再将修改的提交信息强制更新至远程
git push origin -f
```

### 强制删除已提交的commit
```bash
git reset --soft HEAD~n
git push origin <branch_name> -f
```

### 撤销commit，会新增一条“反转”的commit
```bash
git revert <commit_id>
```

### 暂时不想提交本地代码，又需要在原先的代码上修改
```bash
git stash save # 暂时保存现状
git stash list # 显示暂存列表
git stash pop # 恢复暂存的操作
git stash clear # 删除所有暂存的操作
```

### 把多次提交中的某一次提交从一个分支迁移到另外一个分支
```
A-B  master
   \
    C-D-E-F-G develop
```

如果想把develop分支中的D，F两次提交移动到master分支，就可以：
```bash
git checkout master
git cherry-pick D
git cherry-pick F
```


### bug分支合入master分支时想保持整洁的commit
Squash Merge可以在merge时把分支上所有commit合并为一个commit后再merge到目标分支，不会执行commit操作。
```bash
git checkout master
git merge --squash bugfix
git commit -m "bug fixed"
```


### 找回被删除的分支
Git branch是指向commit的引用，所以删除branch操作只会删引用，不会删除commit。但如果一个commit不在任何一个branch上，那么在一定时间后，它会被Git的回收机制删除掉。
```bash
# reflog可以查看本地更新过HEAD的git命令记录，找到被删除前的commit_id
git reflog
git checkout <commit_id>
git checkout -b <branch_name>
```