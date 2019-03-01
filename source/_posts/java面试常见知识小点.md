---
title: java面试常见知识小点
date: 2018-2-14 11:00:50
tags: java 
categories: java
---
1. hashMap的结构
```

```
2. countLatch await方法
```
1. 新建一个countLatch对象
1. 多个线程共用这个对象, 并在finally里面将计数器的值count down
2. countLatch.await
```
3. 线程池 java.uitl.concurrent.ThreadPoolExecutor


```
减少处理器单元的闲置时间，增加处理器单元的吞吐能力
线程池不仅调整T1,T3产生的时间段，而且它还显著减少了创建线程的数目
一个线程池包括以下四个基本组成部分：
1、线程池管理器（ThreadPool）：用于创建并管理线程池，包括 创建线程池，销毁线程池，添加新任务；
2、工作线程（PoolWorker）：线程池中线程，在没有任务时处于等待状态，可以循环的执行任务；
3、任务接口（Task）：每个任务必须实现的接口，以供工作线程调度任务的执行，它主要规定了任务的入口，任务执行完后的收尾工作，任务的执行状态等；
4、任务队列（taskQueue）：用于存放没有处理的任务。提供一种缓冲机制。
```

- corePoolSize
```
 默认情况下，线程池中并没有任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()或者prestartCoreThread()方法
 默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中
```
- maximumPoolSize
```
线程池最大线程数, 它表示在线程池中最多能创建多少个线程
```
- keepAliveTime
```
表示线程没有任务执行时最多保持多久时间会终止
只有当线程池中的线程数大于corePoolSize时，keepAliveTime才会起作用，直到线程池中的线程数不大于corePoolSize
即当线程池中的线程数大于corePoolSize时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize
```
- unit
```
参数keepAliveTime的时间单位
```
- workQueue
```
ArrayBlockingQueue; 基于数组的先进先出队列，此队列创建时必须指定大小；
LinkedBlockingQueue; 基于链表的先进先出队列，如果创建时没有指定此队列大小，则默认为Integer.MAX_VALUE；
SynchronousQueue; 这个队列比较特殊，它不会保存提交的任务，而是将直接新建一个线程来执行新来的任务
```

4. ThreadLocal

5. Thread 常用关键字

- join

```
join（）方法的作用是调用线程等待该线程完成后，才能继续用下运行
```
- yield

```
类似sleep, 只是不能控制时间
```

- sleep
 
```
在同步块中:
    继续持有锁, 不论优先级高低
在非同步块中:
    高优先级线程sleep后, 低优先级线程就有机会
```

- start run

```
start 启动一个线程 这时此线程是处于就绪状态， 并没有运行
真正运行时是执行的run方法
```
- Thread Runnable

```
java不支持多继承, 所以如果要继承其他类,只能用runnable
```
- wait()、notify()、notifyAll()

```
这三个方法用于协调多个线程对共享数据的存取，所以必须在synchronized语句块内使用
wait()方法使当前线程暂停执行并释放对象锁标示，让其他线程可以进入synchronized数据块,当前线程被放入对象等待池中
当调用notify()方法后，将从对象的等待池中移走一个任意的线程并放到锁标志等待池中，只有锁标志等待池中线程能够获取锁标志；如果锁标志等待池中没有线程，则notify()不起作用
notifyAll()则从对象等待池中移走所有等待那个对象的线程并放到锁标志等待池中。准备争夺锁的拥有权
```
[wait_notify>>>](http://www.importnew.com/10173.html)

[死锁>>>](https://blog.csdn.net/tayanxunhua/article/details/20998449)
- 终止线程

```
1. 
2.
3.stop
```


6. 索引 [索引详解>>>](http://www.cnblogs.com/linhaifeng/articles/7274563.html)

```
不断地缩小想要获取数据的范围来筛选出最终想要的结果
索引字段要尽量的小
索引的最左匹配特性
```
- 常用索引
```
普通索引INDEX：加速查找
唯一索引：
    -主键索引PRIMARY KEY：加速查找+约束（不为空、不能重复）
    -唯一索引UNIQUE:加速查找+约束（不能重复）
联合索引：
    -PRIMARY KEY(id,name):联合主键索引
    -UNIQUE(id,name):联合唯一索引
    -INDEX(id,name):联合普通索引
```
- 索引类型

```
hash类型的索引：查询单条快，范围查询慢
btree类型的索引：b+树，层数越多，数据量指数级增长（我们就用它，因为innodb默认支持它）
InnoDB 支持事务，支持行级别锁定，支持 B-tree、Full-text 等索引，不支持 Hash 索引；
MyISAM 不支持事务，支持表级别锁定，支持 B-tree、Full-text 等索引，不支持 Hash 索引；
Memory 不支持事务，支持表级别锁定，支持 B-tree、Hash 等索引，不支持 Full-text 索引；
NDB 支持事务，支持行级别锁定，支持 Hash 索引，不支持 B-tree、Full-text 等索引；
Archive 不支持事务，支持表级别锁定，不支持 B-tree、Hash、Full-text 等索引；
```

- 正确使用索引

```
1. 范围问题
条件不明确，条件中出现这些符号或关键字：>、>=、<、<=、!= 、between...and...、like 范围越明确, 越能利用到索引. 百分号加在开头可以使用到索引
2. 区分度问题
尽量选择区分度高的列作为索引,区分度的公式是count(distinct col)/count(*)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就是0.
那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条记录
3. 顺序问题
=和in可以乱序，比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式
4. 干净问题
索引列不能参与计算，保持列“干净”，比如from_unixtime(create_time) = ’2014-05-29’就不能使用到索引.
原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本太大。所以语句应该写成create_time = unix_timestamp(’2014-05-29’)
5. and/or
    and的工作原理
    条件：
        a = 10 and b = 'xxx' and c > 3 and d =4
    索引：
        制作联合索引(d,a,b,c)
    工作原理:
        对于连续多个and：mysql会按照联合索引，从左到右的顺序找一个区分度高的索引字段(这样便可以快速锁定很小的范围)，加速查询，即按照d—>a->b->c的顺序
    or的工作原理
    条件：
        a = 10 or b = 'xxx' or c > 3 or d =4
    索引：
        制作联合索引(d,a,b,c)
        
    工作原理:
        对于连续多个or：mysql会按照条件的顺序，从左到右依次判断，即a->b->c->d
6.最左前缀匹配原则 
非常重要的原则，对于组合索引mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配(指的是范围大了，有索引速度也慢)，
比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，
如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。
7.其他情况
- 避免使用select *
- count(1)或count(列) 代替 count(*)
- 创建表时尽量时 char 代替 varchar
- 表的字段顺序固定长度的字段优先
- 组合索引代替多个单列索引（经常使用多个条件查询时）
- 尽量使用短索引
- 使用连接（JOIN）来代替子查询(Sub-Queries)
- 连表时注意条件类型需一致
- 索引散列值（重复少）不适合建索引，例：性别不适合
8.explain rows是核心指标，绝大部分rows小的语句执行一定很快（有例外，下面会讲到）。所以优化语句基本上都是在优化rows。
9. 慢查询优化的基本步骤
0.先运行看看是否真的很慢，注意设置SQL_NO_CACHE
1.where条件单表查，锁定最小返回记录表。这句话的意思是把查询语句的where都应用到表中返回的记录数最小的表开始查起，单表每个字段分别查询，看哪个字段的区分度最高
2.explain查看执行计划，是否与1预期一致（从锁定记录较少的表开始查询）
3.order by limit 形式的sql语句让排序的表优先查
4.了解业务方使用场景
5.加索引时参照建索引的几大原则
6.观察结果，不符合预期继续从0分析
```
7. get post区别
```
入门:
GET在浏览器回退时是无害的，而POST会再次提交请求。
GET请求会被浏览器主动cache，而POST不会，除非手动设置。
GET请求只能进行url编码，而POST支持多种编码方式。
GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
GET请求在URL中传送的参数是有长度限制的，而POST么有。
对参数的数据类型，GET只接受ASCII字符，而POST没有限制。
GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
GET参数通过URL传递，POST放在Request body中
询问
GET和POST是什么？HTTP协议中的两种发送请求的方法
HTTP是什么？HTTP是基于TCP/IP的关于数据如何在万维网中如何通信的协议。
HTTP的底层是TCP/IP。所以GET和POST的底层也是TCP/IP，也就是说，GET/POST都是TCP链接。GET和POST能做的事情是一样一样的。你要给GET加上request body，给POST带上url参数，技术上是完全行的通的。
get post 主要是为了区分,本质都是http请求
HTTP的底层是TCP/IP。所以GET和POST的底层也是TCP/IP，也就是说，GET/POST都是TCP链接。GET和POST能做的事情是一样一样的。你要给GET加上request body，给POST带上url参数，技术上是完全行的通的。
GET产生一个TCP数据包；POST产生两个TCP数据包。
```
8. tcp三次握手 [tcp>>>](https://www.cnblogs.com/zmlctt/p/3690998.html)

```
第一次: client -> server data:同步报文 + client序号
第二次: server -> client data:同步报文 + client序号++ + server序号  
第三次: client -> server data:同步报文 + server序号++ + 新client序号
```
9. mysql日志
- 重做日志redo log
```
作用: 确保事务的持久性,防止在发生故障的时间点，尚有脏页未写入磁盘，在重启mysql服务的时候，根据redo log进行重做，从而达到事务的持久性这一特性
产生: 事务开始之后就产生redo log，redo log的落盘并不是随着事务的提交才写入的，而是在事务的执行过程中，便开始写入redo log文件中。
释放: 当对应事务的脏页写入到磁盘之后，redo log的使命也就完成了，重做日志占用的空间就可以重用（被覆盖）。
```
- 回滚日志undo log

```
作用: 保存了事务发生之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读（MVCC），也即非锁定读
内容: 在执行undo的时候，仅仅是将数据从逻辑上恢复至事务之前的状态，而不是从物理页面上操作实现的，这一点是不同于redo log的
产生：事务开始之前，将当前是的版本生成undo log
释放: 当事务提交之后，undo log并不能立马被删除，而是放入待清理的链表，由purge线程判断是否由其他事务在使用undo段中表的上一个事务之前的版本信息
```
- 二进制日志binlog

```
作用: 用于复制，在主从复制中，从库利用主库上的binlog进行重播，实现主从同步;用于数据库的基于时间点的还原。
内容: 逻辑格式的日志，可以简单认为就是执行过的事务中的sql语句。但又不完全是sql语句这么简单，而是执行的sql语句（增删改）反向的信息.
产生: 事务提交的时候，一次性将事务中的sql语句（一个事物可能对应多个sql语句）按照一定的格式记录到binlog中
释放: binlog的默认是保持时间由参数expire_logs_days配置，也就是说对于非活动的日志文件，在生成时间超过expire_logs_days配置的天数之后，会被自动删除
```
- 错误日志errorlog
- 慢查询日志slow query log
- 一般查询日志general log
- 中继日志relay log