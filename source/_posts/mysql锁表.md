---
title: mysql锁表
date: 2018-9-04 11:00:50
tags: java mysql
categories: java
---
-- 这个语句记录当前锁表状态 
show OPEN TABLES where In_use > 0;
-- 查看造成死锁的sql语句
show status like '%lock%';
-- 查询进程
show processlist ;
-- 查看未提交事务
select trx_state, trx_started, trx_mysql_thread_id, trx_query from information_schema.innodb_trx
-- 查看正在锁的事务
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS; 
-- 查看等待锁的事务
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCK_WAITS;
-- 调整锁超时阈值
set session lock_wait_timeout = 1800;
set global lock_wait_timeout = 1800;
-- 杀死线程
kill 936;