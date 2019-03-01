---
title: git_分支
date: 2018-12-04 11:00:50
tags: git java工具
categories: java工具
---
#### 1. 创建分支

```
git branch develop
git branch hotfix
```
#### 2. 分支切换

```
git checkout develop
```
#### 3. 项目分叉历史

```
git log --oneline --decorate --graph --all 
```
#### 4. 分支合并
假如需要将分支合并到master

4.1 切换到master分支
```
git checkout master
```
4.2 合并需要的分支
```
git merge develop
```
4.3 合并分支出现冲突,使用图形化界面解决冲突

```
git mergetool
```
4.4 确定所有分支是否合并成功
```
git status
```
4.5 完成合并提交

```
git commit
```
##### 5. 查看分支

```
本地分支
git branch
远程分支 remotes开头的代表是远程分支
git branch -a

```
#### 5. 提交远程分支
```
git push origin hotfix:hotfix
git push origin develop:develop
```
#### 6. 强制更新线上代码

```
git reset --hard origin/develop
```





