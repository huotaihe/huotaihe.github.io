---
title: git 常用命令手册
date: 2018-12-18 11:33:58
tags:
---
收集工作中常用到的git 命令；
只收集不易记忆的，常用的不在罗列都在脑子里。


## 分支管理

### 本地仓库绑定远程仓库
```bash
git remote add origin <remote repositorie>
```
### 修改远程仓库地址
```bash
## 方式一 通过修改远程地址
git remote set-url origin <remote repositorie> 
## 方式二 先删除后添加
git remote rm origin
git remote add origin <remote repositorie> 

## 方式三修改confige文件
vim .git/confiG

```

### 查看当前的本地分支与远程分支的关联关系

```bash
git branch -vv
```
### 查看当前使用的分支号
```bash
git symbolic-ref --short -q HEAD
```
<!-- more -->
### 把本地分支与远程origin的分支进行关联处理

```bash
#<branch name> 代表变量，运行时替换为你远程的分支名
git branch --set-upstream-to=origin/<branch name>
```

### 把当前本地分支推送到指定的远程分支
```bash
#<branch name> 代表变量，运行时替换为你远程的分支名
git push origin <branch name>
```
---
## 编辑管理
### 回滚未提交的代码 
```bash
#本地所有修改的。没有的提交的，都返回到原来的状态
git checkout . 
```
### 回滚到某个版本节点
```bash
##<hash> 代表你要回滚到的记录点的hash id
#返回到某个节点，不保留修改。
git reset --hard <hash>
#返回到某个节点。保留修改 
git reset --soft <hash> 
```