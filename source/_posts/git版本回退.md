---
title: git版本回退
date: 2018-12-04 11:00:50
tags: git java工具
categories: java工具
---
1. git log

```
git会返回我们每次提交的版本信息，包括一个特别长的数字、作者、时间以及每次提交的备注
```
2. git log --pretty=oneline

```
返回的信息更加简便
```
3. git reset --hard HEAD^

```
回退到上一个版本
```
4. git reset --hard HEAD^^

```
回退到上上个版本
```

5. git reset --hard HEAD^100

```
回退到上100个版本
```
6. git reset --hard id

```
回退到某个操作
```
7. git push --force  --set-upstream origin develop

```
回退后的版本强制提交
```
