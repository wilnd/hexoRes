---
title: Mysql存储过程
date: 2017-10-22 14:50:00
tags: Mysql
categories: javaWeb
---
### 存储过程
##### 存储过程和函数是事先经过编译并存储在数据库的一段sql语句集合。
#### 1.创建/修改 存储过程

```
create procedure sp_name ([proc_parameter[...]])
    [characteristics ...] routing body

create function sp_name([func_parameter[...]])
    returns type
    [characteristics ...] routing body
    proc_parameter:
    [IN/OUT/INOUT] param_type
    
    func_parameter:
    parameter_name type

type:
    any valid mysql datatype
    
characteristic:
    language sql
    |[NOT] DETERMINISTIC
    |{CONTAINS SQL\NO SQL\READS SQL DATA\MODIFIES SQL DATA}
    |SQL SECURITY {DEFINER\INVOKER}
    |COMMENT 'string'

routing_body:
    Valid SQL procedure statement or statements
    
ALTER {PROCEDURE \ FUNCTION }sp_name [characteristics]

characteristics:
    {CONSTANS SQL\NO SQL \READS SQL DATA \MODEFIES SQL DATA}
    |SQL SECURITY {DEFINER\INVOKER}
    |COMMENT 'string'
```
#### 实例：
```
create procedure film_in_stock(IN p_film_id INT,IN p_store_id INT,OUT p_film_count INT)
READS SQL DATA
BEGEIN 
    SELECT inventory_id
    from inventory
    where film_id = p_fim_id
    and store_id = p_store_id
    and inventory_in_stock(inventroy_id);
    
    select FOUND_ROWS() INTO p_film_count;
END $$

```
该存储过程做了两件事
1. 找到film_id跟store_id满足要求的inventory表的记录
2. 将记录返回
3. 事先将delimiter置为$$。默认delimier是“；”这意为着只要有输入为“；”的地方，视为句子已经结束了，会执行这个句子，置为$$后，在出现$$后前面的句子才会执行

可以看出存储过程的执行跟sql执行的效果是一样的，但是存储过程的好处在于将处理逻辑都放在数据库里面，调用者不需要了解村粗逻辑。如果逻辑发生变化，只需要修改存储过程即可，对调用者的程序没有影响。

##### characteristic参数说明
- LANGUAGE SQL：说明以下过程body是使用SQL语言编写的，这条是系统默认的
- [NOT]DETERMINSTICS:DETERMINSTIC确定的，即每次输入一样输出也一样的程序。NOT DETERMINSTICS非确定的，默认是非确定的
- {CONSTANS SQL（表示子程序不包含读或写数据的语句）\NO SQL（子程序不包含sql语句） \READS SQL DATA （子程序包含读数据的语句）\MODEFIES SQL DATA（子程序包含写数据的语句）} 
- SQL SECURITY{DEFINER|INVOKER} 指定子程序该由创建子程序者的许可来执行，还是使用调用者的许可来执行，默认是DEFINER
- COMMENT('String') 存储过程函数的备注信息

#### 2. 删除存储过程或者函数
- 一次只能删除一个存储过程或函数
- 删除存储过程需要ALTER ROUTINE权限

#### 3. 查看存储过程或函数
##### 通过查看存储过程，函数状态，定义等信息便于了解存储过程或者函数的基本情况。
- 查看存储过程/函数的状态

```
SHOW {PROCEDURE|FUNCTION} STATUS [LIKE 'pattern']
```
- 查看存储过程或函数的定义

```
SHOW CREATE {PROCEDURE|FUNCTION} sp_name
```
- 通过查看  information_schema.Routines了解存储过程的函数信息，包括：名称，类型，语法，创建人等

```
获取存储过程film_in_stock的相关信息
select * from routines where ROUTINE_NAME ='film_in_stocke' \G
```
#### 4. 变量的使用
1. 变量的定义
##### 通过DECLARE可以定义一个局部变量，该变量的范围在BEGIN 到 END块中
- 变量的定义必须在任何符合语句的开头，任何其他语句的前面
- 可以一次声明多个相同类型的变量
- 语法如下

```
DECLARE var_name [,...] type[DEFAULT VALUE]
```
- 例如

```
DELCARE last_month_start DATE
```
2. 变量的赋值
- 直接赋值

```
SET var_name = expr[,var_name =expr]...
SET last_month_start = DATE_SUB(CURRENT_DATE(),INTERVAL 1 MONTH)
```

- 通过查询赋值

```
select col_name [,...] into var_name [,...] table expr
```
#### 5. 定义条件和处理
##### 条件的定义和处理可以定义处理过程中遇到问题时相应的处理步骤。
- 条件的定义

```
DECLARE condition_name CONDITION FOR condition_value

condition_value :
    SQLSTATE[VALUE] sqlstate_value
    |mysql_error_code
```

- 条件的处理

```
DECLARE handler_type HANDLER FOR condition_value [,...] sp_statement

hander_type:
    CONTINUE
    |EXID
    |UNDO
    
condition_value：
    SQLSTATE [VALUE]:sqlstate_value
    |condition_name 
    |SQLWARNING 01开头的SQLSTATE的速记
    |NOT FOUND 02开头的SQLSTATE的速记
    |SQL EXCEPTION 非01 02开头的速记
    |mysql_error_code
    
```
意思就是是，当执行一个执行计划的时候，如果有对某个异常有处理声明，那么发生这个异常的时候，执行计划不会退出，而是根据该异常的处理情况，会自动处理这个异常。如果是声明的continue类型，exit表示执行终止，undo表示暂时还不执行

#### 6. 光标的使用
##### 可以使用光标对结果集进行循环处理。包括光标的声明，OPEN,FHETCH和CLOSE
- 声明光标

```
DECLARE cursor_name CURSOR FOR select_statement
```
- OPEN光标

```
OPEN cursor_name
```
- FETCH 光标

```
FETCH cursor_name INTO var_name [,var_name]
```
- CLOSE光标

```
CLOSE cursor_name
```
注意：
变量和条件必须在最前面声明，然后爱是光标的声明，最后才是处理程序的声明。

#### 7. 流程控制
- IF 语句
IF实现条件判断。

```
IF search_conditon THEN statement_list
    [ELSEIF serchcondition THEN statement_list]
    [ELSE statmet_LIST]
END IF
```
- CASE 语句

```
CASE case_value
    WHEN when_value THEN statment_list
    [WHEN when_value THEN statment_list]
    [ELSE statement_list]
END CASE
或者：
CASE 
    WHEN search_condition THEN statement_list
    [WHEN search_condition THEN statement_list]
    [ELSE  statment_condition]
END CASE
```
- LOOP语句
##### 实现简单循环，退出循环使用LEAVE语句
```
[begin lable:]LOOP
    statement_list
END LOOP [end_lable]
```
如果不在statement_list里面增加退出循环的语句，可以使用LOOP实现死循环
- LEAVE语句
##### 用来从标注的流程构造中退出，通常更BEGIN...END 或者LOOP使用

#### ITERATOR语句
##### ITERATOR语句必须在循环中使用，作用是跳过当前循环剩下的语句，直接进入下一轮循环。

#### REPEAT语句
##### 有条件的循环控制语句，当满足条件的时候退出循环

```
[begin_lable:]REPEAT
    statement_list
UNTIL search_condition
END REPEAT[end_lable]
```

```
REPEAT  
    FETCH cur_payment INTO i_staff_id,d_amout;
        if i_staff_id =2 then
            set @x1 = @x1 + d_amout;
        else 
            set @x2 = @x2 + d_amount;
        end if;
UNTIL 0 END REPEAT;
```

#### WHILE 语句
##### 也是有条件的循环控制语句，当满足条件是执行循环内容，REPEAT是当满足条件的时候退出循环，REPEAT至少执行一次。


```
CREATE PROCEDURE loop_demo()
BEGIN
    SET @X1 = 1, @X2 = 2;
    REPEAT
        set @x1 = @x1 +1 ;
    UNTIL @X1>0 END REPEAT;
    
    WHILE @X2<0 do
        set @x2 = @x2+1;
    END WHILE;
END ;
$$
```
#### 事件调度器，可以理解为时间触发器

```
CREATE EVENT myevent
    ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 HOUR
    DO UPSTATE myscheme.mytable SET mycol=mycol+1
```
解释：
- 事件名称在create event 关键字后指定
- ON SCHEDULE 指定执行的时间以及频次
- DO 指定具体执行的操作或事件

例如：

```
create event test_event_1
on schedule 
    every 5 second
do
    insert into test.test(id1,create_time) values('test',now());
```

- 查看调度状态：

```
how event \G;
```

- 调度器默认是关闭的，打开调度器：

```
set global event_scheduler = 1;
```
- 禁用或删除调度器

```
alter event test_event_1 disable;

drop evvent test_event_1 ;
```
##### 事件调度器可以用于定期收集统计信息，定期清理历史数据，定期数据库检查。