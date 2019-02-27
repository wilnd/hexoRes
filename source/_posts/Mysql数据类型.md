---
title: Mysql数据类型
date: 2017-10-22 14:48:31
tags: Mysql
categories: javaWeb
---
> 数值类型
1. 整数
![](https://ww1.sinaimg.cn/large/005Y4715gy1fknf9p3cgvj30lv09aq6i.jpg)
- 超出取值范围，会发生“Out Of Range”报错
- int(5)表示数值宽度小于5时，数字前面填满宽度（如果超过这个宽度是没有影响的）
- 整数类型有一个属性AUTO_INCREMENT 一个表最多有一个，插入的数比该列最大值+1 并且为NOT NULL，还有就是为PRIMARY KEY或者UNIQUE
2. 小数 float，double，decimal （M,D）M指有M位数字，D指小数位数为D。
- 浮点数与定点数插入，有精度与标度，小数部分超出精度，小数会四舍五入，整数部分超出规定位数，会报错。
- 定点数插入，无精度控制，默认是(10.0),然后走上一步的流程。
3. bit类型 
> 日期时间类型

![](https://ww1.sinaimg.cn/large/005Y4715gy1fknitgv2l0j30mu05gdi4.jpg)
1. TIMESTAMP默认值为current_timestamp
2. show variables like 'time_zone'
3. DATA_TIME  不管插入的是以下那种形式，展示出来的是YYYY-MM-DD HH:MM:SS或YY-MM-DD HH:MM:SS
 - YYYY-MM-DD HH:MM:SS或YY-MM-DD HH:MM:SS "不严格"语法，任何标点都可以用于日期部分或时间部分的间隔符。例如
"92.12.31 11+30+45"
 - YYYYMMDDHHMMSS没有间隔符的字符串。前提是字符串对于日期类型是有意义的有意义包括两个方面1）字符串长度为12位或者14位2）翻译成日期后，日期是有效的
 - YYYYMMDDHHMMSS没有间隔的数字。数字对于日期类型是有意义的。
 - 函数：NOW() CURRENT_DATE
 
> 字符串类型
![](https://ww1.sinaimg.cn/large/005Y4715gy1fknk4i13p3j30n00bnte1.jpg)
1. char与varchar char在建表的时候声明长度，范围在0~255以内。varchar的值为可变长度
2. binary和varbinary 存储2进制的数值
3. enum 枚举类型 存储255个成员需要一个字节，存储65535个成员需要二个字节
4. set 跟enum很相似，最多存储64个成员，可以在一条纪录里面存放多个值


