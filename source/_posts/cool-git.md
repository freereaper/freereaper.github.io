---
title: cool git
date: 2015-08-10
layout: post
comments: true
tags: [git, shell]
---

![img](/assets/BlogImg/git.png)
很早之前就开始使用并接触Git，这种分布式的SCM给我的*coding*带来了不少便捷.

## Git学习

可以在线学习[ProGit](http://git-scm.com/book/en/v2)对Git有比较深入的了解。

## Git Tricks

### 如何将只有两个commit历史的库合并为一个commit

```bash
git reset --soft HEAD^1
git commit --amend -sv
```

+ git reset --soft HEAD^1 可以将HEAD指向第一个commit，且
工作区和暂存区的文件都保持不变。
+ git commit --amend -v
    * --amend 新提交覆盖上一次提交
    * -v verbose，列出跟上一提交之间的改变

<!-- more -->

### git diff

1. git diff
> 工作区和暂存区进行比较

2. git diff --cached
> 暂存区和HEAD比较

3. git diff HEAD
> 工作区和HEAD比较

4. git diff master origin/branch
> 本地master分支和远程branch分支进行比较

### git add

1. ***git add -A***   #Stage All(new, modified, deleted)files

2. ***git add .***    #Stage New and Modified files only

3. ***git add -u***   #Stage Modified and Deleted files only
