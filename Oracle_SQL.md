# SQL基础
## DDL
create,alter,drop(truncate)

1. 视图
create or replace view 视图名 as select …… with read only;

2. 序列
create sequence 索引名称test……
select test.nextval from dual;

3. 键、检查、索引等
create index 索引名称 on emp(列1,列2);

## DML
select(DQL),
insert into values,
update set where,
delete from where

## DCL
grant,revoke,commit,rollback,savepoint(TPL,事务控制)

# PL/SQL
Oracle数据库中对SQL进行扩展而成的编程语言，换句话说就是集成SQL语言的编程语言
## PL/SQL的基本语法
  1. 声明+执行+异常
    
  2. 声明语句、数据类型
    default, :=, select into
    %ROWTYPE,%TYPE
    VARCHAR2(),NUMBER(),DATE,BOOLEAN等常用类型
    
  3. 流程控制（条件、循环、顺序）
    if / case
    while / for
    null / goto
    
  4. 动态执行语句（实现DDL语句）
  execute immediate 'select ……'
    into ……
    using ……
    returning into ……；
    
  5. 游标
    声明
      declare cursor 游标名称 is select ^;
    打开
      open 游标名称
    使用
      fetch 游标名称 into 变量
    关闭
      close 游标名称
    游标属性：游标名称%FOUND %NOTFOUND %ISOPEN %ROWCOUNT
    
    具体使用形式：
    (1)loop循环
    open ^;
    loop 
    fetch ^ into ^;
    ^;
    exit when ^;
    end loop;
    close ^;
    
    (2)for循环
    
    for i in 游标名称 loop
      ……
    end loop;
    
    (3)while循环
    
    open ^;
    fetch ^ into ^;
    while ^ loop
    ^;
    fetch ^ into ^;
    end loop;
    close ^;

    
## 存储过程
create or replace procedure 名称(i in int,a out int)
is | as
  声明;
begin
  执行;
end 名称;
## 函数
## 触发器
create or replace trigger 名称
before | after insert|delete|update on 表名称
fori each row
begin
  ^
end;


