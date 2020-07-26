# SQL





# 1、常用的SQL命令


## 1.1、切换数据库


```sql

-- test 是数据库名
use test;
```

## 1.2、查看数据库下的表


```sql

show tables;
```
输出显示：当前数据库下的所有表名




## 1.3、查看表信息

```sql

-- user 是表名
desc user ;
```
将显示输出：表字段的描述信息


## 1.4、查看创建表时的创建信息


```sql

-- user 是表名
show CREATE TABLE user;
```
将输出显示：创建表时的创建语句




## 1.5、修改表名



```sql

-- user 是原表名，users 是修改后的表名
alter table user RENAME users;
```


## 1.6、删除表



```sql 

-- user是要删除的表名
DROP TABLE IF EXISTS user;
```
注：删除表可以使用drop table。删除多个表时，表之间用逗号分隔。IF EXISTS用来在删表之前判断表是否存在，可避免报错。

如：
```sql
DROP TABLE [IF EXISTS] table1 [,table2]... 
```








## 1.7、增加字段


```sql

/* user 是表名，
usertype 是要增加的列名，
类型int，长度 1，非空，默认值为 0 */
ALTER TABLE user 
ADD COLUMN usertype INT(1) NOT NULL DEFAULT 0

```

## 1.8、删除字段


```sql

-- user 是表名，usertype 是要删除的字段名
ALTER TABLE user DROP COLUMN usertype 

```


## 1.9、修改字段

1、修改字段类型


```sql

/* user 是表名，
usertype 是字段名，
修改字段类型为 varchar 长度为 1 */
ALTER TABLE user MODIFY usertype VARCHAR(1);

```



2、修改字段名


```sql

/* user是表名，
usetype 是 old_column_name , 
isadmin 是 new_column_name,新的列类型是int,长度为 1 */
ALTER TABLE user CHANGE usetype isadmin INT(1);

```
注：修改字段名时一定要重新指定该字段类型




## 1.10、关联更新

~~~sql
-- 当需要更新的值是另一个表关联的值的时候使用

update user_info
set age = p.age
from user_info_param p
where user_id = p.user_id
~~~





---

 　


# 2、高级命令



## 2.1、修改时间类型的默认值


```sql

--修改CreateTime 设置默认时间 = CURRENT_TIMESTAMP 当前时间

ALTER TABLE tableName 
MODIFY COLUMN CreateTime DATETIME NULL 
DEFAULT CURRENT_TIMESTAMP 
COMMENT '创建时间' ;

```


## 2.2、修改自增序列的初始值


```sql

--改变 自增序列 从10000开始
ALTER TABLE table_name AUTO_INCREMENT = 10000 ;

```









## 2.3、同时删除两张表


```sql
DELETE s,d 
FROM secims.`studyplan` s , secims.`studyplandetail` d 
WHERE s.`planId` = d.`planId` AND s.`planId` = 18 ;
```

这里有个问题就是：母表有，子表没有对应的，则无法删除。


**// 还有一个比较好的方式就是设置外键：**

如果以它设置了外键后，
当删除它了时，则只要是以它为外键的都将被删除

~~~sql
CONSTRAINT `planIDFK` FOREIGN KEY (`planId`) 
REFERENCES `studyplan` (`planId`) 
ON DELETE CASCADE 
ON UPDATE NO ACTION
~~~

设置删除级联，更新无操作。




## 2.4、同时更新两张表

使用内联接来实现：


```sql

UPDATE test.`dept` d INNER JOIN test.`employee` e
ON d.`deptId` = e.`deptId` 
SET  d.`deptAddress` = "深圳",e.`empName` = "ma" 
WHERE  d.`deptId` = 1 AND e.`empId` = 1 ;
```


## 2.5、SQL中的三目运算符

sql中也是支持三目运算的,语法为:

**用法：CASE  WHEN 条件 THEN 如果符合 ELSE 如果不符合 END**

所以在我们这业务中的SQL语句就是:


查询：

```sql

select   
    CASE WHEN pay > 0 
    THEN pay * num 
    ELSE price * num 
    END as amount
from table_name 
order by amount desc;

```

修改/更新：

```sql
UPDATE test.`dept` d 
SET d.`deptAddress` = (  
    CASE WHEN d.`deptId` > 2 
    THEN "ch" 
    ELSE "gd" END  )  ;
```






# **3、序列**

```sql
  -- 创建序列   
  create sequence MYSQL.SEQ_GUI   
  start with 1   
  increment by 1 ;
  
  --下个序列：
  SEQ_GUI.NEXTVAL
  
  --当前序列：
  SEQ_GUI.CURRVAL
  
  --获取序列： -- oracle
  select SEQ_GUI.NEXTVAL FROM dual ; 
  
```


