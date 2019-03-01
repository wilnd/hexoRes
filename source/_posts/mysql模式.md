---
title: mysql锁表
date: 2018-9-04 11:00:50
tags: java mysql
categories: java
---
1. 查看sql_model参数命令
```
SELECT @@GLOBAL.sql_mode;
```
2. 设置sql_model参数
```
set @@GLOBAL.sql_mode='';
```

