---
title: git多人协作流程
date: 2022-05-03 18:54:16
categories: [git]
tags:
- git
- github
---

# Fork

在github上找到需要参与开发的项目，fork到自己的github仓库中

# git clone

初始化

```bash
git clone <url> #克隆远程仓库
git init #初始化本地版本库
```

克隆fork来的仓库

# git remote

```bash
git remote -v #显示所有远程仓库
git remote show <remote> #查看指定远程仓库
git remote add <remote> <url> #添加远程仓库
```

添加对应的github远程仓库

```bash
git remote add upstream <url> #添加目标项目的主仓库
git remote add origin <url> #添加fork来的的仓库
```

# git branch

```bash
git branch #查看本地所有分支
git branch -r #查看远程所有分支
git branch -a #查看本地和远程所有分支

git branch <新分支名> #基于当前分支，新建一个分支
git checkout -b <新分支名> #基于当前分支新建分支，并切换为这个分支
git checkout <分支名> #切换到本地某个分支
git checkout --orphan <新分支名> #新建一个空分支（会保留之前分支的所有文件）
git branch -d <分支名> #删除本地某个分支
git branch <新分支名称> <提交ID> #从提交历史恢复某个删掉的某个分支
git branch -m <原分支名> <新分支名> #分支更名
```

新建一个分支

```bash
git checkout -b new
```

在该分支上进行开发

# git add/git commit/git push

```bash
git add .  #提交全部文件修改到缓存区
git add <具体某个文件路径+全名>  #提交某些文件到缓存区

git diff  #查看当前代码 add后，会 add 哪些内容
git diff --staged  #查看现在 commit 提交后，会提交哪些内容
git status  #查看当前分支状态

git commit -m "<注释>"  #提交代码到本地仓库，并写提交注释
git commit -v  #提交时显示所有diff信息
git commit --amend [file1] [file2]  #重做上一次commit，并包括指定文件的新变化

git push [remote] [branch] #上传本地指定分支到远程仓库
git push [remote] --force #强行推送当前分支到远程仓库，即使有冲突
git push [remote] --all #推送所有分支到远程仓库
```

git add添加新增文件，git commit提交修改到本地仓库，git push提交到自己的远程仓库

```bash
git add test.txt
git commit -m "commit test"
git push origin new
```

# pull request

github中使用pull request将修改的内容请求合并到主仓库中

# git pull

```bash
git pull [remote] [branch] #拉取远程仓库的分支与本地当前分支合并
git merge <分支名> #合并指定分支到当前分支
git merge --abort #合并分支出现冲突时，取消合并，一切回到合并前的状态
```

在提交到主仓库之前，先拉取主仓库最新的代码

```bash
git pull upstream main
```

# git rm

```bash
git rm -r --cached . #移除所有文件
git rm --cached <文件路径> #移除指定文件
git rm --f <文件路径> #移除指定文件并删除该文件（不会放入回收站）
```

将已经提交过的文件加入git忽略文件：

```bash
git rm -r --cached . #移除所有文件
#将目标文件加入.gitignore
git add . #重新add所有文件
```

