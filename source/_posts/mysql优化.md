---
title: mysql优化
date: 2018-2-14 11:00:50
tags: java mysql
categories: java
---
案例:
访问量比较大的时候一秒两次,0.8秒的查询已达到瓶颈,加了一次索引查询速度降到了0.4,基本上满足了需求.

1. 索引的缺点

```
1.1 创建索引需要时间,数据量越大时间越大
1.2 索引需要占据磁盘空间
1.3 对数据表中的数据进行增删改的时候,索引页需要动态维护
```
2 创建索引的原则

```
2.1 更新频繁的列不应设置索引
2.2 数据量小的表不用设置索引
2.3 重复数据多的字段不设置索引
2.4 首先考虑对where和order by涉及的字段建立索引
```
3. 确认索引是否使用 explain 语句

```
select_type  simple表示简单查询 还有其他如primary，union，subquery
table  表名
partitions  匹配的分区
type  引擎在表中找到所需行的方式 由差到好为：all（全表扫描），index（只遍历索引树），range（索引范围扫描，常见于between，>，< 等查询中），ref（非唯一性索引扫描），eq_ref（唯一性索引扫描），const / system（当MySQL对查询某部分进行优化，并转换为一个常量时，使用这些类型访问），null（MySQL在优化过程中分解语句，执行时甚至不用访问表或索引）
possible_keys  可供选择的索引
key  使用的索引
key_len  索引字节数的长度，数值越小，运行速度越快
ref  连接匹配条件，即哪些列或常量被用于查找索引列上的值
rows  返回的数据行数
filtered  被表条件过滤的行数的百分比
extra  额外信息  类型： using index（表示select操作中使用了覆盖索引），using where（mysql服务器在存储引擎受到记录后进行“后过滤“），using temporary（表示mysql需要使用临时表来存储结果集，常见于排序和分组查询）， using filesort（mysql中无法使用索引完成的排序操作，成为“文件排序”）
```
4. 优化mysql查询语句

```
1. 不要在where条件语句 '=' 的左边进行函数，运算符或表达式的计算，如 select name from tb_user where age/2=20，因为索引不会生效（引擎会放弃使用索引，进行全表扫描）
2. 不要使用 <>，！=，not in ，因为索引不会生效
3. 避免对字段进行null的判断，因为索引不会生效（可以用一个值代替null，如-999）
4. 使用like模糊查询时，like '%xx%'会导致索引不生效，like 'xx%' 索引能够被使用，所以避免使用第一种
5. 避免使用or，可以用union替代（要想使用or，又让索引生效，or条件中的每个列都必须加上索引）
6. 使用exist代替in（表中数据越多，exist的效率就比in要越大）
7. 数据类型隐形转换，索引不会生效：如 select name from user where phone=13155667788;（phone字段在数据库中为varchar类型，应改成 phone='13155667788'）
8. 联合索引必须要按照顺序才会生效：如创建的索引顺序为a,b，where a="xx" and b="xx" 生效，但 b="xx" and a="xx" 则不会生效，补充：a="xx" 没有后面的，索引也会生效
9. 尽量避免使用游标（游标效率低）
10. 不要使用 select * 
11. 索引列的选择性

```
5. 在已经存在的表上创建索
```
create index index_name  on table_name (column_name[length], ...) [asc|desc]
alter table table_name add [unique|fulltext|spatial] [index|key] index_name(column_name[length, ...]) [asc|desc]
```
6. 删除索引

```
alter table table_name drop index index_name
drop index index_name on table_name
```


