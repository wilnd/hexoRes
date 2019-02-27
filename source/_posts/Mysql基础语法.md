---
title: Mysql基础语法
date: 2017-10-22 14:48:51
tags: Mysql 理论知识
categories: 理论知识
---
>DDL:数据定义语言 

##### 定义数据段，数据库，表，列，索引等数据库对象 关键字：create,drop.alter

 1. create database name 创建数据库
 2. drop database name 删除数据库
 3. create table name (column_name1 column_type1 constraints,column_name2 columntype2 constraint) 创建表
 4. show create table  table_name \G 查看表定义
 5. drop table tablename  删除表
 6. alter table tablename modify age varchar(20) 修改表类型
 7. alter table tablename add column  age varchar(20) 增加表字段
 8. alter table tablename drop column age 删除表字段
 9. alter table tablename change age age1(30) 修改表字段
 10. 在 add change modify后都可加first|after column_name 来更改位置

>DML：数据库操纵语句
    
##### 用于添加，更新，删除，查询数据库记录。 关键字：insert,delete,update,select
##### 插入
1. insert into tablename (column1,colum2,colum3) values (value1,value2,value3 ) 插入-指定字段名称
2. insert into tablename values(value1,value2,value3) 插入-不指定字段名称
3. insert into tablename values(valuea1,valuea2),(valueb1,valueb2); 插入-多条记录
##### 更新
4. update tablename set columnname = value where id = 1 更新单表记录
5. update tablea a , tableb b set a.columa = value1 , b.columb = value2 where clause 更新多表的记录
##### 删除
6. delete from table where cloause 删除语句
7. delete a,b from tablea a, tableb b where clause  删除多个表的数据
##### 查询
8. select * from tablename [where clause]  查询语句
9. select * from tablename [where clause] [order by column1 [desc/asc],column2[desc/asc]] asc表示升序 如果第一个排序字段一样按照第二个字段排序
10. select * from tablename order by clum1 limit a,b 表示从第a条数据开始的b跳记录
##### 聚合
11. select *，fun_name(聚合函数) from tablename [where clause][group by column(分类聚合)][with rollup(对分类聚合后的结果汇总)][having clause(对分类后的结果再过滤)] 聚合操作
##### 表连接
12. select * from tablename1 a , tablename2 b on a.deptname = b.deptname [where clause] 内连接求交集
select * from tablename1 a left join tablename2 b on a.deptname = b.deptname 外连接左表全有，右表匹配则有，不匹配补null
##### 子查询
13. select * forom tablename where column1 in (select column from tablename2) 可转换为表连接，提升效率
##### union与union all
14. union all 将结果集并在一起 union是在union all的基础上求一次distinct，所以效率会更低一点。
>DCL:数据库控制语言

##### 控制不同数据段（数据库，表，字段，用户）直接的访问级别。 关键字:grant,revoke.
15. grant select,insert on sakila.* to 'z1'@'localhost' indentified by '123' 创建一个用户z1 密码123，具有对sakila数据库的所有表有insert/select权限    
16. revoke insert on sakila.* from 'z1'@'localhost' 收回z1的insert权限，保留select权限
17. 