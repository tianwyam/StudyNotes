

# MySQL单表备份&还原





[TOC]

<br/>



**场景：**上线操作之前，都会对原始实际表进行备份，然后进行业务数据库表操作，若是不想影响到线上的数据库表数据，则需要进行 备份，然后出现问题时进行还原等操作



<br/>





## 1. 备份



<br/>

<br/>



方式一：

~~~sql

-- 删除备份表 （防止已经备份、可重复执行脚本）
DROP TABLE IF EXISTS back_student_20210903 ;

-- 创建备份表，数据来源与 student
CREATE TABLE back_student_20210903 SELECT * FROM student ;

~~~



<br/>



方式二：

~~~sql

-- 删除备份表 （防止已经备份、可重复执行脚本）
DROP TABLE IF EXISTS back_student_20210903 ;

-- 创建表、表结构来源于 student
CREATE TABLE back_student_20210903 LIKE student ;

-- 迁移数据 到备份表中
INSERT INTO back_student_20210903 SELECT * FROM student ;


~~~



<br/>

<br/>







## 2. 还原



<br/>





~~~sql

-- 删除表
DROP TABLE IF EXISTS student ;

-- 还原备份表
RENAME TABLE back_student_20210903 TO student ;
~~~



<br/>

