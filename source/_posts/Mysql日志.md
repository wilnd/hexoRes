---
title: Mysql日志
date: 2018-9-04 11:00:50
tags: java mysql
categories: java
---
1. 查询日志是否开启

```
SHOW VARIABLES LIKE 'log_bin';
```
2. 开启日志

```
set GLOBAL general_log='ON';

2.1 Windows:
1. 找到mysql安装目录
2. 找到my.ini文件
3. 添加如下内容
# Binary Logging
server_id=1918
log_bin = mysql-bin
binlog_format = ROW
完整配置:
[mysqld]
port=3306
character-set-server=utf8mb4
basedir ="D:\mysql-5.7.20-winx64\mysql-5.7.20-winx64\"
datadir ="D:\mysqlData\"
max_allowed_packet = 32M
# Binary Logging
server_id=1918
log_bin = mysql-bin
binlog_format = ROW

2.2 linux
1. 查看mysql使用的配置文件默认路径
/usr/local/mysql/bin/mysqld --verbose --help |grep -A 1 'Default options'
2. 确定是打开状态
log-bin=mysql-bin

```
3. 查看所有binlog日志列表

```
show master logs;
```
4. 查看master状态，即最后(最新)一个binlog日志的编号名称，及其最后一个操作事件pos结束点(Position)值

```
show master status;
```
5. 查看binlog日志内容

```
5.1 使用mysqlbinlog自带查看命令法
注意:
1. binlog是二进制文件，普通文件查看器cat、more、vim等都无法打开，必须使用自带的mysqlbinlog命令查看
2. binlog日志与数据库文件在同目录中
3. 在MySQL5.5以下版本使用mysqlbinlog命令时如果报错，就加上“--no-defaults”选项

查看mysql的数据存放目录
1. ps -ef|grep mysql
2. 找到--datadir后面的目录
3. mysqlbinlog mysql-bin.000001

5.2 登录服务器并查看
show binlog events in 'mysql-bin.000001';
```
6. 恢复

```
恢复有三种
1. 完全恢复
2. 指定pos结束点恢复
3. 指定pso点区间恢复

D:/mysql-5.7.20-winx64\mysql-5.7.20-winx64\bin\mysqlbinlog --start-position=2405 --stop-position=7266 --database=xiaocaimi  D:\mysqlData\mysql-bin.000001 | D:\mysql-5.7.20-winx64\mysql-5.7.20-winx64\bin\mysql -uroot -p123456 -v xiaocaimi
```




- 查看mysql版本号

```
进入mysql 
select version();
```
