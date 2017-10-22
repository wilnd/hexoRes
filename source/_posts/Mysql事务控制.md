---
title: Mysql事务控制
date: 2017-10-22 14:50:28
tags: Mysql
categories: javaWeb
---
#### 1. LOCK TABLE 与 UNLOCK TABLE
- LOCK TABLES 锁定当前线程的表，如果表被其他线程锁定，则当前线程会等待，直到可以获取所有的锁。
- UNLOCK TABLES 可以释放当前线程可以获得的任何锁。当前线程执行另一个LOCKE TABLES或当前线程的服务器连接中断时，当前线程所有锁定的表会隐式的解锁。    

```
LOCK TABLES
    table_name [as alians] {READ [LOCAL]\[LOW PRIORITY] WRITE}
    [,table_name [as alians] {READ [LOCAL]\[LOW PRIORITY] WRITE}]...
UNLOCK TABLES
```
#### 2. 事务控制
- START TRANSACTION \BEGIN[WORK] 开始一项新的事务
- COMMIT\ROLLBACK 提交或回滚事务
- CHAIN \ RELEASE 定义事务提交或者回滚后的操作。CHAIN会启动一个新事务，并且和刚才的事务有同样的隔离级别；RELEASE则会断开与客户端的连接
- SET AUTOCOMMIT 可以修改当前连接的提交方式。如：设置SET AUTOCOMMIT=0,则设置后，所有事务都要通过明确的命令进行提交或回滚。
##### 注意：
- 如果只是对某些语句需要进行事务控制，使用START TRANSACTION语句开始一个事务比较方便，如果希望所有事务都不是自动提交的，那么通过AUTOCOMMIT控制事务比较方便。
- 对于lock方式加的表锁，不能通过rollback回滚
- 所有的DDL语言是不能回滚的，部分DDL会隐式提交
- 可以定义savepoint指定回滚事务的一个部分，不能指定提交事务的一个部分

#### 3.分布式事务
- mysql分布式事务，目前只支持InnoDB存储引擎
- 涉及一个或多个资源管理器（RM），相当于分支
- 一个事务管理器(TM)，相当于中央调度。
##### 分布式事务的原理
要执行一个分布式事务，必须知道这个分布式事务涉及哪些资源管理器，并把各资源管理器事务直行到可提交或回滚时。将各分支的事务放在一起，当做一个原子，要么都提交，要么都回滚。要考虑网络出现故障的情况。可以简单的说XA事务有两个阶段
- 第一个阶段，各分支RM向TM报告自身的状态
- 第二个阶段，TM告知RMS是否要提交或回滚，如果所有分支都准备好，所有分支被告知提交，如果有任何分支提示不能提交，则被告知要回滚。
##### 分布式事务的语法

```
 XA{START\BEGIN} xid {JOIN\RESUME}
```
启动一个带xid的XA事务 每个XA事务必须有一个唯一的xid值，用来标识一个事务。即使是同一个XA事务的xid值也可以不同，xid的格式


xid:gtrid[,bqual[,formID]

gtrid是一个分布式事务的标识符，bqual是分支限定符，formID是一个数字，用于标识由gtrid和bqual值使用的格式，默认为1

```
XA END xid[SUSPEND [FOR MIGRATE]]
XA PREPARE xid
```
使事务进入PREPARE状态，也就是第一阶段


```
XA COMMIT xid[ONE PHASH]
XA ROLLBACK xid
```
提交或回滚具体分支事务，第二阶段

```
XA RECOVER
```
返回当前数据库中，PREPARE状态的分支事务的详细信息



