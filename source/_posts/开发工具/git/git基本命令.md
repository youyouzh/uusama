---
title: Git基本命令
tags:
  - Git
url: 490.html
id: 490
categories:
  - 其他
  - 软件工具
date: 2017-12-25 20:12:41
---

Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

## Git简介
GIT不仅仅是个版本控制系统，它也是个内容管理系统(CMS),工作管理系统等。它和SVN的主要区别有：

- GIT是分布式的，而SVN，CVS不是
- GIT把内容按元数据方式存储，而SVN是按文件，隐藏在.git文件夹下
- GIT分支和SVN的分支不同：分支在SVN中一点不特别，就是版本库中的另外的一个目录
- GIT没有一个全局的版本号，而SVN有-revision
- GIT的内容完整性要优于SVN

git的安装很简单，只需要安装 git-bash 命令行环境就足够了，Windows下载地址在[这儿](http://git-scm.com/downloads)。

## 创建仓库
可以本地创建仓库，也可以从远程拉取

- git init repository : 初始化本地仓库
- git clone repositoryurl directory : 从 repository url克隆仓库到 directory目录

```bash
# 本地创建名为 repository 的仓库
git init repository

# 远程拉取仓库
git clone 
```

## 基本版本管理
常用的对当前版本的管理命令
### git add, git rm, git mv, git ls
git add 命令可将该文件添加到缓存，使用空格分割文件列表。可以直接在当前项目文件夹下添加文件，而不用使用git add命令，该命令相当于新建文件。

git rm 命令用于从缓存中移除文件

git mv 命令用于从缓存区移动文件
```bash
git add README hello.php
```

### git status
git status 命令查看上一次提交之后的修改情况。可以添加 -s 参数，显示简单输出。
```bash
$ git status -s

On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

    new file:   README
    new file:   hello.php
```

### git diff
git diff 来查看执行 git status 的结果的详细信息。git diff 命令显示已写入缓存与已修改但尚未写入缓存的改动的区别.

- 尚未缓存的改动：git diff
- 查看已缓存的改动： git diff --cached
- 查看已缓存的与未缓存的所有改动：git diff HEAD
- 显示摘要而非整个 diff：git diff --stat

### git commit
使用 git add 命令将想要快照的内容写入缓存区，git commit 将缓存区内容添加到仓库中。

Git 会记录每一次提交的用户名字与电子邮箱地址，所以第一步需要配置用户名和邮箱地址。也可以直接编辑 .git 中的 config 文件。
```bash
git config --global user.name 'uusama'
git config --global user.email uusama@uusama.com
```

### git push
将当前版本的修改提交到远程相应的远程仓库。需要有权限才可以提交。
```bash
# 将本地主分支推到远程主分支
git push origin master

# 将本地主分支推到远程(如无远程主分支则创建，用于初始化远程仓库)
git push -u origin master

# 创建远程分支
git push origin <local_branch>:<remote_branch>
```

### git fetch
抓取远程仓库更新，如果远程仓库进行了修改，在提交之前，需要使用该命令更新本地，否则会出现 update-to-date。

### git reset HEAD
git reset HEAD 命令用于取消已缓存的内容，提交的时候会排除取消缓存的内容。
```
# 取消 hello.php 文件缓存
git reset HEAD -- hello.php 
```

## 分支管理
### git branch
单独的 git branch 命令用于列出分支。还可以后面添加分支名创建分支。
```bash
# 列出你在本地的分支
git branch

# 创建名为 test 的本地分支
git branch test

# 删除 test 分支
git branch -d test

# 查看所有远程分支
git branch -r

# 查看参数等帮助
git branch -h

# 查看所有本地和远程分支
git branch -av
```

### git checkout
git checkout (branch) 切换到指定的分支。所有的更改都会基于当前分支。
```bash
# 切换到本地master分支
git checkout master

# 创建新分支并切换到新分支
git checkout -b test


# 拉取远程分支，创建本地分支并切换到新分支
git checkout -b local_branch origin/remote_branch
```

### git merge
将任何分支合并到当前分支。可以在后面添加分支名指定需要合并的分支。
```bash
# 显示当前分支
git branch
# 将test分支合并到当前分支
git merge test
```
合并的时候可能会出现冲突，要注意解决冲突之后再合并。

## 查看日志
### git log
使用 git log 命令查看提交日志。有下面的一些选项：

- --oneline 选项查看历史记录的简洁的版本
- --graph 选项查看历史中什么时候出现了分支、合并
- --reverse 逆向显示所有日志
- --author=Linus  显示特定用户的日志

## 创建标签 git tag
到达某个重要阶段时，需要创建一个 tag 来记录当前的所有更改。一般发布的时候也是使用tag版本。使用 git tag 命令打标签。
```bash
git tag -s tagname -m "message"
```