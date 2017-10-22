---
title: Mysql 引擎，数据类型，字符集，索引
date: 2017-10-22 14:49:44
tags: Mysql
categories: javaWeb
---
### 1.选择合适的Mysql 存储引擎
>MyISAM

##### 访问速度快，不支持外键，不支持事物。以select与insert为主的应用适合于这种存储类型。

>InnoDB
##### 具有提交，回滚，奔溃恢复能力的事务安全，写的处理效率非常低。
1. 自动增长。
    - 如果插入的值是空或者0，实际插入的值是自增长后的值
    - 可以通过alter table tablename auto_increment = n 语句强制设定自动增长后的初始值。
    
3. 外键约束。
    - 创建外键的时候要求主表必须有对应的索引，子表创建外键的时候也会自动创建对应索引
    - 创建索引的时候，可以指定删除父表的时候，对子表进行相应动作，包括restrict(无动作),cascade（父表更改，对应子表也更改）,set null（父表更改，子表字段置为空）,not action（无动作）
    - 如果某个表被其他表创建了外键参照，那么该表的对应索引或主键禁止被删除
    - 当导入多个表的时候，如果需要忽略表的导入顺序，可以暂时关闭外键检查。SET FOREIGN_KEY_CHECKS = 1
    
>MEMORY
##### METORY存储引擎的表访问速度非常快，因为它的数据放在内存中的，一旦服务关闭，表中的数据就会丢失。

### 2. 选择合适的数据类型
>char 与 vachar的选择
1. MyISAM引擎：建议选择固定长度的数据列（char）代替可变长度的数据列（varchar）
2. MEMORY引擎：目前都是使用固定长度的数据列（char）
3. InnoDB引擎：建议使用varchar数据类型。对于InnoDB内部存储格式没有区分固定长度与可变长度，因此在本质上使用固定长度不一定比使用可变长度性能要好，   因此主要性能是数据的存储总量，由于char的平均占用空间多于varchar，因此通过varchar可以降低存储总量和磁盘I/O。
>TEXT和BLOB

一般保存少量数据，我们会选择cahr和vachar，而保存大量的数据选择TEXT和BLOB。
1. TEXT主要保存字符数据，例如文章或者日记
2. BLOB主要保存二进制数据，例如照片
3. 删除text或者blob数据后，会留下数据空洞，可以使用 OPTIMIZE TABLE tablename来消除。
4. 不必要的时候避免检索大型的blob和text
5. 把blob或者text分离在单独的表中
>浮点数与定点数

1. 浮点数存在误差
1. 定点数的本质是字符串，所以定点数保存的数据是精确的
2. 浮点数在进行算术运算的过程中常常出现结果为近似值的情况
3. 不要将两个浮点数用==号比较，而是要用范围比较。

>日期选择

1. 根据需要，选择最小满足应用的最小存储类型。年份选YEAR类型就行了
2. 如果要记录年月日时分秒，并且记录的事件比较久远，最好使用DATATIME 而不是TIMESTAMP

### 3.字符集

mysql字符集设置：
- my.cnf中

```
[mysqld]
character-set-server=gbk
```
- 启动选项中

```
mysqld --character-sert-server=gbk
```
- 编译时指定

```
shell> cmake . -DEFAULT_CHARSET=gbk
```
### 索引的设计与使用
##### MyISAM和InnoDB存储引擎表默认创建的都是BTREE索引，MEMORY存储引擎使用的是HASH索引，支持BTREE索引
1. 创建索引
```
create index indexname on tablename
```
2. 删除索引

```
drop index indexname on tablename
```
3. 设计索引的原则
- 最适合做索引的列是出现在<font color="#FF0000">where子句中的列，或者连接子句中</font>的列。
- <font color="#FF0000">索引列的基数越大，索引的效果就越好</font>。例如用出生日期的列做索引比用性别做索引效果好。
- <font color="#FF0000">使用短索引</font>。指定一个前缀长度做索引。较小的索引涉及的磁盘IO较少。
- 不要过度使用索引，每个索引都要占用额外的空间，索引越多，效率会越低
- 对于innodb的表，如果有明确的主键，记录按主键顺序保存，如果有索引，记录按索引保存，如果既没有主键，又没有索引，表中自动生成一个内部列，按照这个列的顺序保存。按照主键或内部列的访问时最快的。<font color="#FF0000">尽量选择最常作为访问条件的列作为主键，提高查询效率。</font>
4. BTREE索引使用范围
>,<,>=,<=,between,!=,<>,like 'patten',<font color="#FF0000">patten不以通配符开始，但是可以以通配符结束</font>

### 视图
