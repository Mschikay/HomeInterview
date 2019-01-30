---
title: 面试知识点2--SQL
date: 2017-09-20 11:57:24
tags: 面试
---
# 数据库相关操作

## 增
## 删
## 改
## 查

***
### 一些考点
* 左连接查询
    左表为基础
* 右连接查询
    右表为基础
* 内连接查询
    全部表


* 权限管理
```sql
    -- user -> db ->table_priv
    -- 判断是否有权操作
    -- 新增用户
    grant [权限1,权限2,权限3] on *.* to user@'host' identified by 'password';
    -- 常用权限 ALL ,CREATE ,DROP ,INSERT ,DELETE ,UPDATE ,SELECT
    -- %为通配符
    GRANT ALL ON *.* TO lalala@' 10.10.4.%' identified by '111111'
    -- 收回权限
    revoke all on *.* from lalala@'10.10.4.%'
    -- 针对某个库授权
    GRANT ALL ON test1.* TO lalala@'10.10.4.%' indentified BY '111111'
    -- 授予表的权限
    GRANT INSERT ,SELECT,UPDATE ON test1.ord to lalala@'10.10.4.%'
```

* 游标
```sql
    -- cursor 游标 游标的标志
    -- 1条sql 对应N条资源 取出资源的句柄/接口 就是游标
    -- 沿着游标 可以一次取出一行
    -- declare 声明; declare 游标名; cursor for select_statement
    -- open 打开; open 游标名;
    -- fetch 取值; fetch 游标名 into var1,var2[...]
    -- close 关闭； close 游标名;
    CREATE PROCEDURE p12()
    BEGIN
     DECLARE row_gid INT ;
     DECLARE row_num INT ;
     DECLARE row_name VARCHAR(20);
     DECLARE getgoods CURSOR FOR SELECT gid,num,NAME FROM goods;
     OPEN getgoods;
     fetch getgoods INTO row_gid, row_num, row_name;
     SELECT row_num,row_name;
     CLOSE getgoods;
    END
    CREATE PROCEDURE p13()
    BEGIN
     DECLARE row_gid INT ;
     DECLARE row_num INT ;
     DECLARE row_name VARCHAR(20);
     DECLARE getgoods CURSOR FOR SELECT gid,num,NAME FROM goods;
     OPEN getgoods;
     fetch getgoods INTO row_gid, row_num, row_name;
     SELECT row_num,row_name;
      fetch getgoods INTO row_gid, row_num, row_name;
      SELECT row_num,row_name;
      fetch getgoods INTO row_gid, row_num, row_name;
      SELECT row_num,row_name;
      fetch getgoods INTO row_gid, row_num, row_name;
      SELECT row_num,row_name;
     CLOSE getgoods;
    END
    /*
    mysql> call p13()$
    +---------+----------+
    | row_num | row_name |
    +---------+----------+
    |     100 | cat      |
    +---------+----------+
    1 row in set (0.00 sec)
    +---------+----------+
    | row_num | row_name |
    +---------+----------+
    |      50 | dog      |
    +---------+----------+
    1 row in set (0.00 sec)
    +---------+----------+
    | row_num | row_name |
    +---------+----------+
    |      80 | pig      |
    +---------+----------+
    1 row in set (0.00 sec)
    ERROR 1329 (02000): No data - zero rows fetched, selected, or processed
     */
     CREATE PROCEDURE p14()
     BEGIN
      DECLARE cnt INT DEFAULT 0;
      DECLARE i INT DEFAULT 0;
      DECLARE row_gid INT ;
      DECLARE row_num INT ;
      DECLARE row_name VARCHAR(20);
    --   DECLARE getgoods CURSOR FOR select gid,num,NAME FROM goods;
      DECLARE getgoods CURSOR FOR SELECT gid,num,NAME FROM goods;
    --   declare_statement is before select_statement
      SELECT count(*) INTO cnt FROM goods;
      OPEN getgoods;
      repeat
       fetch getgoods INTO row_gid, row_num, row_name;
       SELECT row_num,row_name;
       SET i := i+1;
       until i >= cnt END repeat;
      CLOSE getgoods;
     END
    -- 游标越界
    -- 可以 declare continue handler 来操纵一个越界付
    -- declare continue hander for NOT FOUND statement;
     CREATE PROCEDURE p15()
     BEGIN
      DECLARE row_gid INT ;
      DECLARE row_num INT ;
      DECLARE row_name VARCHAR(20);
      DECLARE you INT DEFAULT 0;
      DECLARE getgoods CURSOR FOR SELECT gid,num,NAME FROM goods;
    --   continue 将会多取出一行数据
    --   这时用 declare exit handler for NOT FOUND set you := 1;
      DECLARE CONTINUE handler FOR NOT FOUND SET you := 1;
    --   declare_statement is before select_statement
      OPEN getgoods;
      repeat
       fetch getgoods INTO row_gid, row_num, row_name;
       SELECT row_num,row_name;
       until you END repeat;
      CLOSE getgoods;
     END
      CREATE PROCEDURE p16()
     BEGIN
      DECLARE row_gid INT ;
      DECLARE row_num INT ;
      DECLARE row_name VARCHAR(20);
      DECLARE you INT DEFAULT 0;
      DECLARE getgoods CURSOR FOR SELECT gid,num,NAME FROM goods;
    --   continue 将会多取出一行数据
    --   这时用 declare exit handler for NOT FOUND set you := 1;
      DECLARE exit handler FOR NOT FOUND SET you := 1;
    --   declare_statement is before select_statement
      OPEN getgoods;
      repeat
       fetch getgoods INTO row_gid, row_num, row_name;
       SELECT row_num,row_name;
       until you END repeat;
      CLOSE getgoods;
     END
    --  除了continue exit undo handler
    --  continue 触发后 后面的语句还执行
    --  exit 触发后 后面的语句不执行
    -- undo是触发后 前面的语句撤销
    CREATE PROCEDURE p17()
     BEGIN
      DECLARE row_gid INT ;
      DECLARE row_num INT ;
      DECLARE row_name VARCHAR(20);
      DECLARE you INT DEFAULT 0;
      DECLARE getgoods CURSOR FOR SELECT gid,num,NAME FROM goods;
    --   continue 将会多取出一行数据
    --   这时用 declare exit handler for NOT FOUND set you := 1;
      DECLARE undo handler FOR NOT FOUND SET you := 1;
    --   declare_statement is before select_statement
      OPEN getgoods;
      repeat
       fetch getgoods INTO row_gid, row_num, row_name;
       SELECT row_num,row_name;
       until you END repeat;
      CLOSE getgoods;
     END
    CREATE PROCEDURE p18()
     BEGIN
      DECLARE row_gid INT ;
      DECLARE row_num INT ;
      DECLARE row_name VARCHAR(20);
      DECLARE you INT DEFAULT 0;
      DECLARE getgoods CURSOR FOR SELECT gid,num,NAME FROM goods;
    --   continue 将会多取出一行数据
    --   这时用 declare exit handler for NOT FOUND set you := 1;
      DECLARE CONTINUE handler FOR NOT FOUND SET you := 1;
    --   declare_statement is before select_statement
    -- 不用exit逻辑改进
      OPEN getgoods;
       fetch getgoods INTO row_gid, row_num, row_name;
      repeat
       fetch getgoods INTO row_gid, row_num, row_name;
       SELECT row_num,row_name;
       until you END repeat;
      CLOSE getgoods;
     END

```
* 存储过程
```sql
    # 若干条语句封装起来放到数据库里
    # 存储过程
    # 语法
    CREATE PROCEDURE p1()
    BEGIN
    --  SQL 语句
    END
    # 调用
    call p1()

    存储过程一些结构

    CREATE PROCEDURE p2()
    BEGIN
    DECLARE age INT DEFAULT 18;
    DECLARE height INT DEFAULT 180;
    SELECT concat('年龄是',age,'身高是',height);
    END
    -- 运算
    -- 语法
    -- set 变量名 : = 值
    CREATE PROCEDURE p3()
    BEGIN
    DECLARE age INT DEFAULT 18;
    DECLARE height INT DEFAULT 180;
    SELECT concat('年龄是',age,'身高是',height);
    SET age := age + 20;
    SELECT concat('20年后是',age);
    END
    -- if/else 控制结构
    /*
      if condition then
      statement
      else 或者 end;
     */
    CREATE PROCEDURE p4()
    BEGIN
    DECLARE age INT DEFAULT 18;
      if age > 18 THEN
        SELECT 'man';
      else
        SELECT 'child';
      end if;
    END

    存储过程穿参

    -- 存储过程的括号里可以声明参数
    -- 语法是 [in/out/inout] 参数名 参数类型
    CREATE PROCEDURE p5(width int, height int)
    BEGIN
    SELECT concat('你的面积是',width * height);
    if width > height THEN
    SELECT '你挺胖';
    elseif width < height THEN
    SELECT '你挺瘦';
    ELSE
    SELECT '你挺方';
    end if;
    END
    -- 控制结构
    -- 循环，选择，顺序
    CREATE PROCEDURE p6()
    BEGIN
     DECLARE total int DEFAULT 0;
     DECLARE num INT DEFAULT 0;
     while num < 101 do
      set total := total + num;
      set num := num + 1;
      end while;
    SELECT total;
    END
    -- 带参数的求1-n的和
    -- in 是 input TYPE 可以接收到我们穿的参数
    -- out 是 output TYPE 是要把传入的变量的值附成out的值
    CREATE PROCEDURE p7(in n int)
    BEGIN
     DECLARE total int DEFAULT 0;
     DECLARE num INT DEFAULT 0;
     while num <= n do
      set total := total + num;
      set num := num + 1;
      end while;
    SELECT total;
    END
    CREATE PROCEDURE p8(in n int,out total int)
    BEGIN
     DECLARE num INT DEFAULT 0;
     set total := 0;
     while num <= n do
      set total := total + num;
      set num := num + 1;
      end while;
    SELECT total;
    END
    -- inout类型
    CREATE PROCEDURE p9(inout age int)
    BEGIN
      set age := age + 20;
    END

    -- case 用法
    CREATE PROCEDURE p10()
    BEGIN
     DECLARE pos INT;
     SET pos := floor(5 * rand());
     CASE pos
      WHEN 1 THEN SELECT 'This is 1';
      WHEN 2 THEN SELECT 'This is 2';
      WHEN 3 THEN SELECT 'This is 3';
      ELSE SELECT 'This is null';
      end case;
    END
    -- repeat 循环
    /*
        repeat statement
        until condition end repeat;
    */
    CREATE PROCEDURE p11()
    BEGIN
     DECLARE total INT DEFAULT 0;
     DECLARE num INT DEFAULT 0;
     repeat
     set num := num + 1;
     set total := total + num;
     until num >= 100
     end repeat;
     SELECT total;
    END

```
* 触发器编程
```sql
    -- 触发器 trigger
    --
    -- 需求：
    -- 商品表: goods
    -- 订单表: ord
    --
    --
    -- 分析：
    -- 监视：order
    -- 监视动作: insert
    -- 触发时间: after
    -- 触发事件: update
    -- 查看trigger   show triggers
    -- 删除trigger   drop trigger 名字
    --
    -- create trigger t1
    -- after
    -- insert
    -- on ord
    -- for each row
    -- begin
    -- update XXXXX
    -- end
    CREATE TABLE goods(
    gid INT ,
    NAME VARCHAR (20),
    num SMALLINT
    );
    CREATE TABLE ord(
    oid INT ,
    gid INT ,
    much SMALLINT
    );
    INSERT INTO goods VALUES
    (1,'cat',34),
    (2,'dog',50),
    (3,'pig',100);
    -- delimiter
    create trigger t1
    after
    insert
    on ord
    for each row
    begin
    update goods SET num = num-new.much WHERE gid = new.gid;
    end
    CREATE trigger t2
    after
    DELETE
    on ord
    for each row
    BEGIN
    UPDATE goods set num = num + old.much where gid = old.gid;
    END
    CREATE trigger t4
    before
    UPDATE
    ON ord
    for each row
    BEGIN
    UPDATE goods set num = num + old.much - new.much WHERE gid = old.gid;
    END

```
