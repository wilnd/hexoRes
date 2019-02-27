---
title: Mysql Date Type
date: 2017-08-29 21:11:53
tags: javaWeb
categories: javaWeb
---
## 该文章是翻译文，源自[Mysql-DateType](https://dev.mysql.com/doc/refman/5.5/en/numeric-type-overview.html)

### 数据类型综述

> #### 数字类型：下列各类型中M代表数字类型的最大值
    
![数据类型](https://ww1.sinaimg.cn/large/005Y4715gy1fj0fv7pq4mj30e3086mxd.jpg)
    
- ######  如果是由ZEROFILL修饰，表示该数字是UNSIGNED(无符号类型)，否则就是SIGNED类型
- ######  SERIAL代表 BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE.
- ######  BIT[(M)]数据类型用于存储bit值，能够存储比特长度范围为1~64。直接返回bit是不可读的，如果要变为可读的，可采用"+0"的方式或者用BIN()之类的转换函数， 转换后的值不显示高位0。
        
```
mysql> SELECT b+0, BIN(b+0), OCT(b+0), HEX(b+0) FROM t;
b+0: 255 10 5
BIN(b+0): 1111111 1010 101
OCT(b+0): 377 12 5
HEX(b+0): FF A 5    
```
- ###### TINYINT[(M)] [UNSIGNED] [ZEROFILL]  一个非常小的整形，百位级别 B 级别
- ###### BOOL, BOOLEAN 相当于TINYINT(1)相当于false，非0数字相当于true。但反过来，false是0，true是1
- ###### SMALLINT[(M)] [UNSIGNED] [ZEROFILL] 万级别 K级别
- ###### MEDIUMINT[(M)] [UNSIGNED] [ZEROFILL]  一个中等类型的整形数据，百万级别 M级别
- ###### INT[(M)] [UNSIGNED] [ZEROFILL] 一个普通类型的整形数据十亿级别 G级别
- ###### INTEGER[(M)] [UNSIGNED] [ZEROFILL]   类似于int
- ###### BIGINT[(M)] [UNSIGNED] [ZEROFILL]  最大的整形数据百亿亿级别Z级别
- ###### DECIMAL[(M[,D])] [UNSIGNED] [ZEROFILL] 用来存储精确数据，在货币存储中用的比较多。DECIMAL继承NUMERIC  M代表总长度，，M的最大长度是65. ，表示最大存储65位的数据。D代表小数点后的位数，D的默认值是0，M的默认值是10DECIMAL。实际存储的是按照M D 来的。所有+ - * / 运算都是65位的。例如DECIMAL(5,2) 可存储5位数，其中两位小数，其范围是-999.99~999.99
- ###### DEC[(M[,D])] [UNSIGNED] [ZEROFILL], NUMERIC[(M[,D])] [UNSIGNED] [ZEROFILL], FIXED[(M[,D])] [UNSIGNED] [ZEROFILL] 他们都类似于DECIMAL  FIXED可以被其他数据库兼容
- ###### FLOAT[(M,D)] [UNSIGNED] [ZEROFILL] float存储的是非精确的值，M代表数据长度，D代表小数位位数，最多精确到小数点后7位。
- ###### DOUBLE[(M,D)] [UNSIGNED] [ZEROFILL]  double类似float，最多精确到小数点后15位
> ### 日期和时间类型
- ###### DATE 用来显示日期，但不能显示时间，显示日期的区间是'1000-01-01' to '9999-12-31'，允许用String或者number转换为DATE
- ###### DATETIME 日期和时间的组合，不支持时区范围是'1000-01-01 00:00:00' to '9999-12-31 23:59:59' 允许用字符和数字转换为DATETIME
- ###### TIMESTAMP 存储从('1970-01-01 00:00:00' UTC)到指定时间的毫秒数，可存储的时间范围是'1970-01-01 00:00:01' UTC to '2038-01-19 03:14:07' UTC
- ###### 用来存储时间，范围是'-838:59:59' to '838:59:59'，一天的范围最多24小时，但是如果要计算两个时间点的距离的时候可能就需要更大的时间了。
- ###### YEAR[(2|4)] 表示年的方式有两种，一种是2位数字，一种是4位数字。其意义是一样的。能表示的范围是 1901 to 2155
> ### String类型

![](https://ww1.sinaimg.cn/large/005Y4715gy1fj0nxsqongj30en03r74a.jpg)
- ###### CHAR CHAR列的长度在创建表格的时候就是固定的了，长度区间是 0 to 255
- ###### VARCHAR varchar是可变长度用多少，拿多少 长度区间是 0 to 65,535，内容由两部分组成，实际存储的内容以及内容长度。
- ###### BINARY
- ###### VARBINARY 
- ###### BLOB 
- ###### TEXT
- ###### ENUM 
- ###### SET


    
