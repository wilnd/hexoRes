---
title: Mysql触发器
date: 2017-10-22 14:50:11
tags: Mysql
categories: javaWeb
---
##### 触发器是与表有关的数据对象，在满足定义条件的时候触发，并执行触发器定义的语句集合。
#### 1. 创建触发器
##### 触发器的创建只能在永久表上

```
CREATE TRIGGER trigger_name trigger_time trigger_event
ON tab_name 
FOR EACH ROW trigger_stmt
```
- trigger_time 是触发器的触发时间，可以是before（检查约束前触发），或者after（检查约束后触发）。
- trigger_event
是触发事件，可以是insert,update,delete
- 同一个表的相同时间的相同触发事件，只能定义一个触发器

例如：

```
delimiter $$
CREATE TRIGGER ins_film AFTER INSERT
ON film 
FOR EACH ROW BEGIN 
    insert into film_text (film_id,title,description) values (new.fiml_id,new.title,new.description);
END;
$$
delimiter ;
```
插入film表后，会向film_text触发插入事件

#### 2. 删除触发器
##### 一次可以删除一个触发器，如果没有指定scheme_name默认删除当前数据库的触发器。

```
DROP TRIGGER [scheme_name] trigger_name 
```
#### 3. 查看触发器
- 方式一
```
show triggers \G
```

可以查询触发器的状态，语法等信息，但是不能查询指定触发器，所以每次都返回所有触发器，使用不是很方便
- 方式二 查询imformation_schema.triggers表

```
select * from triggers where trigger_name = 'inf_film_bef' 、\G
```
使用注意：
- mysql触发器使用顺序是before 触发器，行操作，after触发器的顺序执行的，其中任何一步发生错误，都没法继续执行下面的操作。
- 如果对事物表进行操作，出错后会整个回滚
- 如果是对非事物操作，已更新的记录无法回滚


