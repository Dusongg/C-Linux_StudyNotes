[TOC]

![mysql](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/mysql.png)

# ==[MySQL参考手册](https://dev.mysql.com/doc/refman/5.7/en/preface.html)==

# 1 预备知识

## 1.1 安装

- 获取yum源`rz` :   http://repo.mysql.com/

- 安装yum源：`rpm -ivh mysql57-community-release-e17.rpm`

- 安装mysql：`yum install -y mysql-community-server`

  - 报错：

    ![image-20240111194015333](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240111194015333.png)

    `rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022`

安装完后：

![image-20240111194716623](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240111194716623.png)

- 启动mysql： `start mysqld`

![image-20240111195028586](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240111195028586.png)

- 查服务端口号： `netstat -nltp`

![image-20240111195239301](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240111195239301.png)

## 1.2 [Ubuntu下安装MySQL](https://ubuntu.com/server/docs/databases-mysql)



## 1.3 登录 & 退出

`mysql -u root -p`

`quit`

- 初始化root密码

![image-20240417214538696](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240417214538696.png)

## 1.4 配置文件

`sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf`



```cnf
//免密码登录
--skip-grant-tables  
```



# 2 基础知识

## 2.1 链接服务器

```bash
mysql -h 127.0.0.1 -P 3306 -u root -p
```

- `-h`：指明登录部署了mysql服务的主机
- `-P`：指明要访问的端口号
- `-u`：指明登录用户
- `-p`：指明需要输入密码

## 2.2 什么是数据库

- mysql 是数据库服务的客户端，mysqld是数据库服务的服务器端
- mysql本质是基于CS模式的一种网络服务
- 数据库一般指的是再磁盘或内存存储的特定结构的数据

## 2.3 基本使用

### 2.3.1创建表

- 查看数据库文件:

  - ```bash
    cd /var/lib/mysql
    ```

![image-20240112141923131](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112141923131.png)

### 2.3.2 插入数据

![image-20240112142444837](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112142444837.png)

## 2.4 服务器、数据库、表的关系

![image-20240112143620855](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112143620855.png)

 

## 2.5 SQL分类

- DDL（data definition language）数据定义语言，用来维护存储数据的结构
  代表指令: `create, drop, alter`
- DML（data manipulation language）数据操纵语言，用来对数据进行操作
  代表指令： `insert，delete，update`
  - DML中又单独分了一个DQL，数据查询语言，代表指令： select

- DCL（data control language）数据控制语言，主要负责权限管理和事务
  代表指令： `grant，revoke，commit`

## 2.6 存储引擎

- 什么是存储引擎：数据库管理系统如何存储数据，如何为存储的数据建立索引和如何更新、查询数据等技术的实现方法

- `MyISAM` 、 `BDB` 、 `Memory` 、 `innoDB`（最常用）、 `Archive` 、 `NDB`

- ```mysql
  show engines \G；   #查看存储引擎
  ```

![image-20240112145711744](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112145711744.png)

# 3 Mysql数据库的操作

## 3.1 创建和删除

- 创建 —— 本质是在 `/var/lib/mysql`创建一个目录

  - ```mysql
    create [if not exists] database db_name;    #[]里面的是可选项	
    ```

- 删除 —— 本质是在 `/var/lib/mysql`删除一个目录

  - ```mysql
    drop [if exists] database db_name;
    ```



## 3.2 字符集和校验规则

查看编码集（写）：

```mysql
show variables like 'character_set_database';
```

![image-20240112152034459](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112152034459.png)

查看校验集（读）：

```mysql
show variables like 'collation_%';
```

![image-20240112152151872](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112152151872.png)

- 创建一个使用`utf-8`字符集、并带校对规则的数据库（如果没有写，默认为`utf-8`）

```mysql
create database dp_name charset=utf8 collate=utf8_general_ci;
create database dp_name character set utf8 collate utf8_general_ci;   #与  charset=utf8  一样
```



## 3.3 查看数据库

- 查看当前在哪一个数据库中

```mysql
select database();
```

- 查看所有数据库

```mysql
show databases;
```



## 3.4 修改数据库

```mysql
alter database db_name charset=utf8   #对编码格式修改
```



## 3.5 备份和恢复

- 备份

```bash
mysqldump -P3306 -u root -B [数据库名1] [数据库名2] > [数据库备份存储的文件路径]  
```

```mysql
mysqldump -P3306 -u root -B [数据库名] [表名1] [表名2] > [数据库备份存储的文件路径]    #备份同一个数据库中的不同表
```



- 恢复

```mysql
source [数据库备份存储的文件路径]
```

## 3.6 数据库的链接情况

```mysql
show processlist
```



# 4 表的操作 — `create\alter\drop`

##  4.1 创建表

```mysql
CREATE TABLE [if not exists] table_name (
	field1 datatype comment [文字说明],
	field2 datatype,
	field3 datatype  
)character set [字符集] collate [校验规则] engine [存储引擎];
```

![image-20240112163627454](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112163627454.png)

![image-20240112164028571](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112164028571.png)

### 4.1.1 复制表

```mysql
create table [new_table] like [old_table]; 
```



## 4.2 查看表

```mysql
desc [表名]
```

```mysql
show create table [表名] \G
```

![image-20240112164545462](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112164545462.png)

## 4.3 修改表

![image-20240112165753877](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112165753877.png)[^1]

[^1]: 下述以该表为例

### 4.3.1重命名

- 改表名

```mysql
alter table [表名] rename to [新表名] ;   #或者下面一种
rename table [] to [];  
```

- 改表中某一列的名字

```mysql
alter table [表名] change [old_name] [new_name] [属性];
```

![image-20240112174008991](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112174008991.png)

### 4.3.2 插入数据

```mysql
insert into user values (1, 'yy', '1234', '2003-8-26');
```

### 4.3.3 向表中新增一列

```mysql
alter table [表名] add [新增列名] [列属性] comment [说明] after [列名];
```

![image-20240112170300472](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112170300472.png)

### 4.3.4 修改某一列的属性

```mysql
alter table [表名] modify [列名] [列属性] comment [说明]; 
```

![image-20240112172518036](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112172518036.png)

### 4.3.5 删除某一列

```mysql
alter table [表名] drop [要删除的列名];   
```

![image-20240112172357233](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240112172357233.png)

## 4.4 删除表

```mysql
drop table [表名]   
```



## 4.5 条件更改

- 将来自美国、澳大利亚、英国供应商的税率设置为0.08，其他供应商税率设置为0.00。

```mysql
update Lineitem set tax = case
    when Lineitem.suppkey in (
    select suppkey from Lineitem, Supplier, Nation
    where Lineitem.suppkey = Supplier.suppkey and Supplier.nationkey = Nation.nationkey and Nation.name in ('美国', '澳大利亚', '英国')
    ) then 0.08
    else 0.00
    end;
```





# 5 数据类型

## 5.1 数值类型

`tinyint、 smallint、 mediumint、 int、 bigint` 分别占1、2、3、4、8个字节， 默认为**有符号**整数（无符号：`tinyint unsigned`、 `smallint unsigned`...）

- 和C/C++不同的是，Mysql中，值超出某一类型的数据范围，会直接报错（mysql中，数据类型是一种**约束**）

**![image-20240113115726261](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113115726261.png)**



## 5.2 bit类型

```mysql
[type_name] bit(M)    # M表示位数，范围[1,64], 默认为1
```

## 5.3 浮点数类型

### 5.3.1 `float` /`double`

```mysql
[type_name] float(m, d)   
# m表示数字总个数（与是有符号或无符号无关），d表示小数位数
# 默认有符号 (m - d 表示整数的位数)
# 
```

![image-20240113121523659](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113121523659.png)[^2]

![image-20240113121824043](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113121824043.png)[^3]

[^2]:四舍五入之后超出范围
[^3]:四舍五入之后在合法范围之内



### 5.3.2 `decimal`

- 基本用法与float类似，**精度比`float`高**

![image-20240113125439515](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113125439515.png)

 

## 5.4 字符类型

### 5.4.1 `char`

```mysql
type_name char(L)  
# L为字符的固定长度（一个汉字/字母视为一个字符）
# 最大长度为255
```

### 5.4.2 `varchar`

```mysql
type_name varchar(L) 
# L表示字符长度
# 最大长度为65535字节（65535/3个字符， utf-8一个字符占3个字节）
```

- varchar中用1-3个字节表示内容总长度（“用多少给多少”），所以实际用于数据存储的字节最大有65532 

| 实际存储 | char(4) | varchar(4) | char占用字节 | varchar占用字节 |
| -------- | ------- | ---------- | ------------ | --------------- |
| A        | A       | A          | 4 * 3 = 12   | 1 * 3 + 1 = 4   |



## 5.5 日期类型 

 ![image-20240113133713185](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113133713185.png)

![image-20240113141351919](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113141351919.png)

- 当表被更新时，`timestamp`自动更新成当前时间 

## 5.6 enum 和 set 类型

- enum： 多选一，插入时只能写枚举的常量或者下标（从1开始）
- set ： 多选多，用n个位表示是否选取，1表示选，全0表示空串

![image-20240113144413136](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113144413136.png)建表

![](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113144437866.png)3表示二进制的011，即插入basketball和football

![image-20240113144500659](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113144500659.png)

![image-20240113144507499](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113144507499.png)

### 5.6.1 enum类型和set类型的选取 —— `find_in_set`

![image-20240113145429969](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113145429969.png)

上述语句选取的是只包含’football‘的，而不是含有’football‘的

- `find_in_set`

```mysql
select find_in_set('a', 'a,b,c');    # 0为false 非0为true（下标）
```

![image-20240113145451901](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113145451901.png)[^4]

[^4]: 查找hobby中包含football的数据

- 若要查询包含多项，可用`and`链接`find_in_set`



# 6 表的约束

- 通过约束，使插入数据库表中的数据如何符合预期

 

## 6.1 空属性约束

```mysql
[type_name] [type] not null
```

- `null`（默认）或 `not null`
- 约束 not null 表示当插入数据时，该列不能为空

![image-20240113172930753](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113172930753.png)

![image-20240113173138486](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113173138486.png)[^5]

[^5]:address没有默认值

![image-20240113173401965](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113173401965.png)[^6]

[^6]:address不能为null

### 6.1.1 null的存储

如果列属性允许为null，那么会默认有一个字节的空间表示一列是否为空，一列用一个bit表示（超过八列加一个字节，以此类推）

![img1](https://cdn.xiaolincoding.com/gh/xiaolincoder/mysql/row_format/null%E5%80%BC%E5%88%97%E8%A1%A85.png)



## 6.2 default约束

- 如果同时约束了 `not null` 和 `default` ，在插入数据时可以忽略该列，即填入默认值 
- 没有写默认值，那么默认值默认为`NULL`（没有`not null` 约束时）

```mysql
[field_name] [type] default [defualt_value]   # 设置默认值
```

![image-20240113174236444](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113174236444.png)



## 6.3 comment约束

- `comment`没有实际含义，用来描述字段，类似于注释

```mysql
[type_name] [type] comment [描述]
```



## 6.4 zerofill

- 将数字剩余的宽度填充0

![image-20240113181542927](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113181542927.png)[^7]

[^7]: `int(10) unsigned`中的10，表示因为该无符号整数最大值2^32总共有10位数字（有符号11位，有一位表示符号）



## 6.5 主键 —— `primary key`

- `primary key` 用来约束该字段里面的**数据不能重复**且**不能为空**， 一张表中最多**只能有一个主键**（不代表只能有一列为主键），主键所在的列通常是整数类型

![image-20240113225918430](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113225918430.png)![image-20240113230255081](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113230255081.png)

- 去除主键

```mysql
alter table [table_name] drop primary key; 
```

-  添加主键

```mysql
alter table [table_name] add primary key([某一字段名])
```

- 复合主键（当多个字段值**均相同**时才报错）

![image-20240113232251799](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240113232251799.png)

## 6.6 auto_increment

- 自增长，每次插入后加一
- 一张表最多只能有一个自增长，必须配合主键使用

```mysql
create table [table_name] (
	  id int primary key auto_increment,
    #...
) AUTO_INCREMENT=1000;  #默认为1
```

- 获取上一次auto_increment的值

```mysql
select last_insert_id();
```



## 6.7 唯一键 —— `unique key` 

```mysql
create table [table_name] (
    id int unique key,
    #...
);
```

### 6.7.1 与主键的区别

- 唯一键可以为NULL，且可以多个为空 
- 主键更多是标识唯一性；唯一键更多是保证不重复(配合primary key使用，保证主键之外的唯一性)

## 6.8 外键 —— `references`

- 外键约束主要定义在从表上，主表必须有主键或唯一键约束，定义外键后，要求外键列数据必须在主表的主键列存在或者为NULL

- 约束主表(master_table)与从表(slave_table)的关系
  - 例如主表为班级，从表为学生，通过外键将从表的某一列与主表建立联系

```mysql
 foreign key([从表的某一列]) references [主表名]([主表某一列])
```

![image-20240116173610607](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240116173610607.png)



# 7 Mysql基本查询

## 7.1 `insert` —— 增

### 7.1.1 一次插入多行

```mysql
insert into [table_name] values(/*...*/), (/*...*/), (/*...*/);
```

 

### 7.1.2 主键/唯一键冲突后修正

```mysql
#若存在冲突，则更新
#若没有冲突，则插入values括号内的值
insert into [table_name] values() on duplicate key update []=[], []=[];
```

![ ](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240116184949280.png)      

3. `replace`

```mysql
#如果有冲突，删除之前的，新增一条
replace into [table_name] () values ();
```

## 7.2 `select` —— 查

### 7.2.1基本查询

```mysql
#全列查询
select * from [table_name];
#选列查询
select [/*可以写表达式， 通过as重命名列*/] from [table_name];   
```

![image-20240116201554077](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240116201554077.png)![image-20240116201642756](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240116201642756.png)

 

### 7.2.2 `distinct` 查询去重 

```mysql
select distinct [列名] from [table_name]
```

### 7.2.3 `where`条件

- 比较运算符

| 运算符              | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| >, >=, <, <=        | 大于，大于等于，小于，小于等于                               |
| =                   | 等于，NULL 不安全，例如 NULL = NULL 的结果是 NULL            |
| <=>                 | 等于，NULL 安全，例如 NULL <=> NULL 的结果是 TRUE(1)         |
| !=, <>              | 不等于                                                       |
| `BETWEEN a0 AND a1` | 范围匹配，[a0, a1]，如果 a0 <= value <= a1，返回 TRUE(1)     |
| IN (option, ...)    | 如果是 option 中的任意一个，返回 TRUE(1)                     |
| IS NULL             | 是 NULL                                                      |
| IS NOT NULL         | 不是 NULL                                                    |
| LIKE / not like     | 模糊匹配。% 表示任意多个（包括 0 个）任意字符；_ 表示任意一个字符 |

![image-20240116222650952](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240116222650952.png)[^8]

- 逻辑运算符
  - 可以用括号将多个逻辑条件分组

| 运算符 | 说明                                       |
| ------ | ------------------------------------------ |
| AND    | 多个条件必须都为 TRUE(1)，结果才是 TRUE(1) |
| OR     | 任意一个条件为 TRUE(1), 结果为 TRUE(1)     |
| NOT    | 条件为 TRUE(1)，结果为 FALSE(0)            |



### 7.2.4 `order by`子句

- NULL表示最小值
- 默认升序
- 执行顺序是先筛选数据，最后排序

 ```mysql
 select [] from [table_name] order by [column_name1] [asc/desc], [column_name2] [asc/desc], ...
 #多个排序，当第一个相同时，再通过第二个排...
 ```

> ==例题：[586. 订单最多的客户](https://leetcode.cn/problems/customer-placing-the-largest-number-of-orders/)==
>
> ```mysql
> select customer_number from Orders group by customer_number order by count(*) desc limit 1;
> ```



### 7.2.5 筛选分页结果 —— `limit` 

```mysql
#筛选前n条
select [] from [table_name] where ... limit n;   #[0,n)  ,表的每一行数据下标从0开始
#筛选第s到n条
select [] from [table_name] where ... limit s, n;     	  #[s, n)
select [] from [table_name] where ... limit n offset s;   #[s, n)
```

![image-20240117133040344](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240117133040344.png)

- 考虑表中数据不足limit数量的情况

## 7.3 `update` —— 改

- 对查询到的结果进行列值更新

```mysql
update [table_name] set [column_name]=[expr] [where ...] ...;
```

![image-20240117132721923](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240117132721923.png)

## 7.4 `delete` —— 删

```mysql
delete from [table_name];  #删除表内所有数据
delete from [table_name] where [column_name]=[expr]...;   #删除指定数据

delete from [table_name] order by [column] asc limit n;   #删除表内排序后的前n行数据

```



### 7.4.1 截断表

- 清空后会重置`auto_increment`项
- 速度比delete快，因为它不走事务，不记录日志

```mysql
truncate table_name;     #与delete from [table_name]类似，都是清空表的数据
```



## 7.5 插入查询操作

```mysql
insert into [table_name] select [column] from [table_name] ...;
```



![](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240117171357303.png)



# 7.7 分组查询&聚合函数

## 7.7.1 `group by` 分组查询 

- 分组之后便于聚合统计
- group by 按照后一列的值进行分组，分成不同行
- 可以将多列联合分组

<img src="https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240117175209396.png" alt="image-20240117175209396" style="zoom: 50%;" />

## 7.7.2 `having`

- 对分组聚合统计之后的数据进行筛选（类似于`where`）

```mysql
#显示平均工资低于2000的部门及它的平均工资 
select department_no, avg(salary) department_avg_salary from employee group by department_no having department_avg_salary<2000; 
```

- having于where的区别？执行顺序？ 
  - where对任意列进行筛选，having对聚合之后的结果进行筛选
  - where先，having在group by之后

## 7.7.3 group by，where，having执行的先后顺序以及用法

在 MySQL 中，`GROUP BY`，`WHERE`，和 `HAVING` 子句的执行顺序如下：

1. `WHERE` 子句：`WHERE` 子句用于筛选行，仅选择符合条件的行。这是在聚合之前执行的，因此它可以用来过滤数据。
2. `GROUP BY` 子句：`GROUP BY` 子句将行分组为汇总行，并为每个组计算汇总值（如 `COUNT`, `SUM`, `AVG` 等）。`GROUP BY` 子句在 `WHERE` 子句之后执行。
3. `HAVING` 子句：`HAVING` 子句用于过滤分组后的结果。它在 `GROUP BY` 子句之后执行，并且只保留满足 `HAVING` 子句条件的分组。

这里有一个简单的示例来说明它们的用法：

```mysql
sqlCopy codeSELECT department, COUNT(*) as total
FROM employees
WHERE salary > 50000
GROUP BY department
HAVING COUNT(*) > 1;
```

这个查询首先筛选出薪资大于 50000 的员工，然后按部门分组。最后，它仅保留至少有两名员工的部门，并计算每个部门的员工总数。

### `count`

### `sum`

### `avg` 

保留小数点后2位`round(avg(xxx), 2)`

- 

### `max` / `min`

- [511. 游戏玩法分析 I](https://leetcode.cn/problems/game-play-analysis-i/)

```mysql
select player_id, min(event_date) as first_login from Activity group by player_id ;
```

- [619. 只出现一次的最大数字](https://leetcode.cn/problems/biggest-single-number/)

```mysql
select max(t.num) as num from (select num from MyNumbers group by num having count(*) = 1) as t;
```



# 8 复合查询

## 8.1 多表查询

以下两张表为例

![image-20240119165147722](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240119165147722.png)

### 8.1.1 合并两张表（内连接）

```mysql
#通过deptno相同合并两张表
select * from emp, dept where emp.deptno=dept.deptno;
```

## 8.2 自链接

 ```mysql
 #默认自链接方式
 select * from emp as t1, emp as t1;
 ```



```mysql
# 查找员工FORM的领导的名字和编号
# 1. 通过子查询
select ename, empno from emp where empno=(select mgr from emp where enmae='FORM');

# 2. 通过自链接表
select e2.ename, e2.empno from emp e1, emp e2 where e1.ename='FORM' and e1.mgr=e2.empno;
```



## 8.3 子查询

```mysql
select [] from [] where ()(select [] from [] where ...);
```



### 8.3.1 单行子查询

```mysql
# 显示SMITH同一部门的员工
select * from emp where deptno=(select deptno from emp where ename='SMITH');
```

### 8.3.2 多行子查询

**三个关键字：**

1. `in`/`not in`：筛选在一个集合中的列

```mysql
# 查询与10号部门的工作岗位相同的员工的名字、岗位、工资、部门号，但是不包含10好部门自己的
select enmae, job, sal, deptno from emp where job in (select distinct job from emp where deptno=10) and deptno<>10;
```

2. `all`：所有

```mysql
# 显示工资比30号部门的所有员工的工资高的员工的名字、工资、部门号
select ename, sal, deptno from emp where sal > all (select distinct sal from emp where deptno=30);
```

3. `any`：任意

```mysql
# 显示工资比30号部门的任意一个员工的工资高的员工的名字、工资、部门号
select ename, sal, deptno from emp where sal > any (select distinct sal from emp where deptno=30);
```



### 8.3.3 多列子查询

```mysql
# 与员工SMITH的部门和工作相同的其他员工的数据
select * from emp where (deptno, job)=(select deptno, job from emp where enmae='SMITH');
```



### 8.3.4 在`from`子句中使用子查询

**上述子查询在where语句之后，充当判断条件；以下子查询出现在from子句中，将查询结果作为一张新的表**

```mysql
# 显示每个高于自己部门平均工资的员工的信息
select * from emp, (select deptno, avg(sal) avg_sal  from emp group by deptno) as tmp where emp.deptno=tmp.deptno and emp.sal>tmp.avg_sal;
 显示每个部门的信息和人员数量
select * from dept t1, (select deptno, count(*) dept_num from emp group by deptno) t2 where t1.deptno=t2.deptno;
```



## 8.4 合并查询

-  `union`：求两个表的并集
- `union all`：直接拼接两个表



# 9 函数

## 9.1 日期函数

![image-20240119150423284](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240119150423284.png)

```mysql
 #查看当前时间
select current_time();   

#想表中插入时间数据 (birthday 类型为date)
insert into [] (birthday) value (date(current_date()));   

#统计两分钟以内插入的数据
select content msg_time from [] where msg_time > date_sub(now(), interval 2 minute);

```

## 9.2 字符串函数

| `charset(str)`                          | 查看字符串字符集                                   |
| --------------------------------------- | -------------------------------------------------- |
| `concat(str, [])`                       | 链接                                               |
| `instr(str, substr)`                    | 查找返回下标，没有则返回0                          |
| `ucase()` , `lcase`                     | 转大写，转小写                                     |
| `left(str, length)`                     | 取`str`左边起长度为length的子串                    |
| `length(str)`                           | 求`str`的长度， 字节长度， 一个汉字utf-8占三个字节 |
| `replace(str, search_str, replace_str)` | 将`str`中的`search_str`替换成`repalce_str`         |
| `strcmp(str1, str2)`                    | 比较两字符串                                       |
| `substring(str, position, length)`      | 取`str[positon, posiron + length]`                 |
| `ltrim(str)`, `rtrim(str)`，`trim(str)` | 去除字符串左侧/右侧/两侧空格                       |

```mysql
#用concat链接多列信息
select concat('姓名: ', name, '总分：', chinese + english, '语文成绩： '， chinese, '英语成绩', english) msg from exam_score;
```

 ## 9.3 数学函数

| `abs()`                            |                              |
| ---------------------------------- | ---------------------------- |
| `bin(), hex()`                     | 十进制转二进制/十六进制      |
| `conv(number, from_base, to_base)` | 将任意数进行进制转换         |
| `ceiling(), floor()`               | 上取整/下取整                |
| `rand()`                           | 返回随机浮点数[0.0, 1.0]     |
| `mod(number, denominator)`         | 取模求余                     |
| `format(number, n)`                | 对浮点数保留n位小数，用0占位 |
| `round(number, n)`                 | 对浮点数保留n位小数，不占位  |



##  9.4 其他函数

| `user()`       | 查询当前用户                                |
| -------------- | ------------------------------------------- |
| `md5()`        | 将一个字符串进行MD5摘要，得到一个32位字符串 |
| `database()`   | 查询当前使用的数据库                        |
| `password()`   | mysql内置的加密函数                         |
| `ifnull(x, y)` | `if x ? x : y`                              |



## [9.5 窗口函数](https://dev.mysql.com/doc/refman/8.0/en/window-functions.html)

- [178. 分数排名](https://leetcode.cn/problems/rank-scores/)

```mysql
SELECT
  S.score,
  DENSE_RANK() OVER (
    ORDER BY
      S.score DESC
  ) AS 'rank'
FROM
  Scores S;
```



![image-20240419171446101](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419171446101.png)

# 10 表的内外链接

## 10.1 内连接

```mysql
select 字段 from table1 inner join table2 on 连接条件 and 其他条件;
```



```mysql
# 显示SMITH的名字和部门名称
# 之前合并表的写法
select ename, dname from emp, dept where emp.deptno=dept.deptno and ename='SMITH';

#标准的内连接写法
select ename, dname from emp inner join dept on emp.deptno=dept.deptno and ename='SIMTH';
```



## 10.2 外连接

### 10.2.1 左外连接

- 链接两张表，左侧表完全显示称为左外连接

```mysql
select 字段 from table1 left join table2 on 链接条件; 
```

![image-20240121172034992](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240121172034992.png)[^9]

> ==例题：[577. 员工奖金](https://leetcode.cn/problems/employee-bonus/)==
>
> ```mysql
> #注意判断空不能用   = null
> #需要用          is null
> select name, bonus from Employee left join Bonus on Employee.empid = Bonus.empid where bonus is null or bonus < 1000;
> ```



### 10.2.2 右外连接

```mysql
select 字段 from table1 right join table2 on 连接条件;
```



# 11 索引

- ## 索引的设计原则

![image-20240418135919013](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418135919013.png)

##  11.1 存储

- 在linux中，表结构在磁盘中就是文件 ，找到文件就是定位到对应的扇区（磁头->柱面->扇区）
- Mysql进行IO的基本单位为16KB（page）
-  B+树：叶子节点保留数据，非叶子节点不要数 据，只保存目录项，叶子节点全部用双向链表连起来

 ![image-20240418142614627](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418142614627.png)

## 11.2 聚簇索引和非聚簇索引

- `innoDB`和`MyISAM`都采用B+树
- `MyISAM`最大的特点是将索引page和数据page分离，也就是叶子节点没有数据，而存储数据地址   ，称为非聚簇索引

### 11.2.1 聚簇索引与二级索引

- `innoDB`叶子节点存放数据，称为聚簇索引， 二级索引叶子节点存放对应的主键
- 如果该表没有主键索引或者唯一索引，该表会在每一列生成一个隐藏的`row_id`作为聚集索引
- 一张表可能对应多个B+树

## 11.3 索引操作

- 查看索引结构

```mysql
show index from [tablename]\G
```

### 11.3.1主键索引 —— primary key

1. 创建主键索引

```mysql
#其他创建主键索引的方法写在表的约束中
alter table [] add primary key([]);
```

2. 删除主键索引

```mysql
alter table [] drop paimary key;
```

### 11.3.2 唯一索引 —— unique

1. 其他索引的删除 

```mysql
alter table [] drop index []
drop index [索引名] on [表名]
```

2. 其他操作在表的约束中



### 11.3.3 普通索引

1. 创建

```mysql
#1.在创建表时添加
#2.alter，以某一列为普通索引
alter table [] add index([]);
#3.创建索引并给索引起名
create index index_name on 表名(列名);
```

2. 删除与唯一键索引相同



### 11.3.4 复合索引(联合索引)

- 尽量使用联合索引，而非单列索引

![image-20240203002842879](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240203002842879.png)

![image-20240203002915143](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240203002915143.png)

 

### 11.3.5 全文索引

- 当文章字段或有大量文字的字段进行检索时，使用全文索引

```mysql
create table articles (
	id int unsigned auto_increment not null primary key,
    title varchar(200),
    body text,
    fulltext(title, body)
)engine=MyISAM;


```



### 11.3.6 前缀索引

```mysql
create index idx_name on table_name(cloumn(n));
```

- 查询选择性：字符串前n个不重复的比值，越大性能越高

```mysql
select count(distinct email)/count(*) from tb_user;
select count(distinct substring(email, 1, 5)) / count(*) from tb_user;
```

- 查询步骤
  - 通过前缀索引找到id值
  - 通过id值找到一行的内容
  - 将改行该列的内容与where后面的条件比对，如果不相同，则通过叶子节点的链表往后找，相同则返回

![image-20240418134853351](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418134853351.png)

## 11.4 性能分析

1. ### 查看SQL执行频率

   ```mysql
   show [global|session] status like 'Com_______';
   ```

2. ### 慢查询日志 —— 定位耗时查询

 开启后，当一条SQL超过了设置的`long_query_time`他就会记录到慢查询日志中![image-20240416152025109](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416152025109.png)

```mysql
#查看是否开启
show variables like 'slow_query_log'
```

- 在配置文件`/etc/my.cnf`中配置相关信息:

  ```
  #开启MySQL慢查询日志
  slow_query_log = 1
  
  #设置慢查询日志时间
  long_query_time = 2
  ```

  

- 查看慢日志文件中记录的信息

  ```bash
  cat /var/lib/mysql/localhost-slow.log
  ```

3. ### profile —— 查看时间耗费在哪里

```mysql
#是否支持
select @@have_profiling;

#是否开启
select @@profiling;

#开启
set [session|global] profiling = 1;

#查看每一条SQL的耗时情况
show profiles;

#查看指定query_id的SQL语句各个阶段的耗时情况
show profile for query query_id

#查看指定query_id的SQL语句CPU的使用情况
show profile cpu for query query_id
```

![image-20240416154537403](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416154537403.png)                                                                                

4. ### explain

```mysql
explain select ...;
```

![image-20240416155044523](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416155044523.png)

- `id`：`select`查询的序列号，表示查询中执行select子句或者是操作表的顺序(id相同，执行顺序从上到下; id不同，值越大，越先执行)。

![image-20240416160104014](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416160104014.png)

- `select_type`

  表示SELECT的类型，常见的取值有SIMPLE〈简单表，即不使用表连接或者子查询)、PRIMARY（主查询，即外层的查询)、UNION(UNION中的第二个或者后面的查询语句）、SUBQUERY (SELECT/WHERE之后包含了子查询)）等

- `type`

  表示连接类型，性能由好到差的连接类型为NULL、system、const(通过主键或者唯一索引)、eq_ref、ref(非唯一索引)、range、index、all 。

- `prossible_key`

  显示可能应用在这张表上的索引，一个或者多个

- `key`

  实际用到的索引，没有则是NULL

- `key_len`

  表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好。

- `rows`

  MySQL认为必须要执行查询的行数，在innodb引擎的表中，是一个估计值，可能并不总是准确的。

- `filtered`

  表示返回结果的行数占需读取行数的百分比,filtered的值越大越好。

## 11.5 索引失效

1. ### 联合索引最左前缀法则

如果索引了多列(联合索引)，要遵守最左前缀法则。最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。如果跳跃某一列，索引将部分失效(后面的字段索引失效)。**<u>与位置无关</u>**

2. ### 联合索引的范围查询

   ![image-20240416220658495](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416220658495.png)

3. ### 对索引进行函数操作

![image-20240416221606468](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416221606468.png)

4. ### 隐式转换

![image-20240416221929847](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416221929847.png)

5. ### 左模糊匹配

6. ### or链接条件

用or分割开的条件，如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到。

7. 数据分布影响

如果MySQL评估使用索引比全表更慢，则不使用索引

![image-20240416222744109](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416222744109.png) 

## 11.6 SQL提示

![image-20240416223934095](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416223934095.png)

##  11.7 覆盖索引

尽量使用覆盖索引（查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到)，减少select *。

- extra信息：

  ![image-20240416224928613](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240416224928613.png)

![image-20240418133402283](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418133402283.png)

# 12 事务 

## 12.1 什么是事务？

- mysql是网络服务，多个客户端请求访问数据，mysql内部采用多线程 -> 事务锁？
- 事务是一组DML语句，这些语句在逻辑上具有相关性，要么全部成功，要么全部失败
- 一个完整的事务满足以下四个属性
  - 原子性(Atomicity)：对于所有操作保证原子性，一次性全部完成
  - 一致性(Consistency)：写入数据必须完全符合所有的预设规则，保证数据的精确度，可预期 —— redo log + undo log
  - 隔离性(Isolation)：未提交、读提交、可重复读、串行化？ —— undo log + readView + 隐藏字段  + 锁
  - 持久性(Durability)：保证数据的修改是永久的 —— redo log

### 哪些存储引擎支持事务？

**![image-20240204232901951](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240204232901951.png)**



- 设置隔离级别为：读未提交

```mysql
set global transaction isolation level READ UNCOMMITTED;
```

- 查看链接mysql服务的用户

```mysql
show processlist;
```



## 12.2 事务的提交方式

事 物的提交方式有两种：自动提交、手动提交

```mysql
#查看提交方式
show variables like 'autocommit';
#修改提交方式
set autocommit=0;
```



## 12.3 事务的基本操作

```mysql
create table if not exists account (
	id int primary key,
    name varchar(20) not null default'',
    balance decimal(10,2) not null default 0.0
)engine=InnoDB default charset=utf8;

#1. 启动事务
start transaction;  
#或
begin;

#2. 设置保存点s1
savepoint s1;

#3. 插入
insert into account values(1, 'xx', 1234);

#4. 回滚，事务提交之后无法回滚
rollback to s1;

#5. 结束
commit;

```



## 12.4 事务异常

- 只要输入`begin`或者`start transaction`，事务便需要通过`commit`提交之后才会持久化，与是否开启自动提交无关
- 当操作异常时，mysql会自动回滚
- 单独一句sql会被打包成事务，当自动提交打开时，mysql异常退出后，该sql语句会自动提交，关掉之后，需要手动`commit`





## 12.5 事务的隔离级别

### 12.5.1 理解 隔离性与隔离级别

- **隔离性**：数据库中，为了保证事务执行过程中尽量不受干扰，区分事务谁先执行，谁在执行，一个执行的事务时原子的，相互隔离

- **隔离级别**：数据库中，允许事务受不同程度的干扰
  
  - 读未提交(read uncommitted)：两个事务，当一个事务对数据进行修改但未提交，第二个事务立马能看见修改（隔离级别最低）
  
    - **dirty read**：一个事务在执行中读到另一个执行中事务的更新（或其他操作）但是未`commit`的数据的现象
  
    ![图片](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/10b513008ea35ee880c592a88adcb12f.png)
  
  - 读提交(read committed )：一端提交之后，另一端才能看见修改
  
    - 不可重复读（non repeatable read)：用一个事务内，在不同时间段独到的数据不同
  
    ![图片](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/f5b4f8f0c0adcf044b34c1f300a95abf.png)
  
    - 幻读：当同一个查询在不同的时间产生不同的结果集时，事务中就会出现所谓的幻象问题。例如，如果 SELECT 执行了两次，但第二次返回了第一次没有返回的行，则该行是“幻像”行。
  
      ![图片](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/d19a1019dc35dfe8cfe7fbff8cd97e31.png)
  
    
  
  - 可重复读(repeatable read)：一端提交之后，另一端需要也提交或者结束才能看见修改（默认隔离级别）
  
  - 串行化(serializable)：在每个读的数据行上加上共享锁，强制事务排序，使之不可能互相冲突（隔离级别最高）



## 12.6 查看与设置隔离级别

1. 查看隔离级别

```mysql
select @@global.tx_isolation;  #全局

select @@session.tx_isolation;  #仅该会话（局部）

```

2. 设置

```mysql
#[]内可不写， {}内四选一
set [session|global] transaction isolation level {read uncommitted | read committed | repeatable read | serializable};

#设置完全局之后，重新登录之后，局部和默认的隔离级别通过全局初始化
```

### RC与RR的本质区别

- RC级别下，事务中每次快照读都会新生成一个Read View
- RR级别下，同一个事务下的第一次快照才会创建Read View

# 13 视图/存储过程/触发器

## 13.1 视图

视图是一个虚拟表，其内容由表查询定义。 

视图的作用：操作简单，安全， 数据独立（屏蔽了基表的变化）

```mysql
#语法
create view [view_name] as [基表];
create [or replace] view 视图名称[(列明列表)] as select语句 [with [cascaded | local] check option];


#E.G.
create view ename_dname as select ename, dname from emp inner join dept on emp.deptno=dept.deptno;

```

![image-20240418184659435](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418184659435.png)

- ### 在视图中插入/删除数据

### 13.1.1 视图的检查选项

#### cascaded

- 如果当前视图加了`with cascaded check option`，那么插入时该视图及之上的视图都会检查

![image-20240418214353378](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418214353378.png)

#### local

- 如果当前视图加了`with local check option`，那么插入时只检查当前视图有没有检查选项，对于上层有则检查，没有则不检查（MySQl8.0）

  ![image-20240418221309421](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418221309421.png)

```mysql
create or replace view stu_v_4 as select id , name from student where id <= 15 ;
insert into stu_v_4 values(5 , 'Tom');		#ok
insert into stu_v_4 values(16, 'Tom');		#error

create or replace view stu_v_5 as select id, name from stu_v_4 where id >= 10 with local check option ;
insert into stu_v_5 values (13, ' Tom ' );		#ok
insert into stu_v_5 values(17,' Tom ' );   		#ok ： 不检查id <= 15
```

### 13.1.2 视图的更新

![image-20240418221640943](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418221640943.png)

## 13.2 存储过程

- 特点：封装，复用；可以接受参数，也可以返回数据；减少网络交互，提升效率

```mysql
delimiter $$

#创建
create procedure 存储过程名称([参数列表])
begin
	...    #在命令行中遇到分号就结束，所以这里需要用delimiter设置结束符
end;

delimiter ;

#调用
call 名称([参数]);

#查看
#查看xxx数据库的存储过程
select * from information_schema.ROUTINES where ROUTINE_SCHEMA = 'xxx';	
#查询存储过程的内容
show create procedure 名称;	

#删除
drop procedure [if exists] 名称;


```

### 13.2.1 变量

####  系统变量 

```mysql
#查看系统变量
SHOW [SESSION | GLOBAL] VARIABLES;
SHOW [SESSION | GLOBAL] VARIABLES LIKE 'xxx%';
SELECT @@[SESSION. |GLOBAL.]系统变量名;

#设置系统变量
SET [SESSION | GLOBAL] 系统变量名 = 值;
SET @@[SESSION | GLOBAL] 系统变量名 = 值;
```

- 如果没有指定SESSION/GLOBAL，默认是SESSION，会话变量。
- mysql服务重新启动之后，所设置的全局参数会失效，要想不失效，可以在`/etc/tany.cnf` 中配置。

#### 用户自定义变量

```mysql
#赋值
赋值
SET @var_name1 = expr [@var_name2 = expr ] ... ;
SET @var_name1 := expr [@var_name2 := expr] ... ;

SELECT @var_name1 := expr [@var_name2 := expr] ...;
SELECT 字段名 INTO @var_name FROM 表名;

#使用
SELECT @var_name;
```

- 用户定义的变量无需对其进行声明或初始化，初始值为NULL



#### 局部变量 —— `declare`

![image-20240419004116678](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419004116678.png)

```mysql
create procedure p2()
begin
	declare stu_count into default 0;
	select count(*) into stu_count from student;
	select stu_count;
end;

call p2();
```

#### 参数

![image-20240419004846818](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419004846818.png)

```mysql
#eg.1
#判断成绩
create procedure p4(in score int, out result varchar(10))
begin
	if score >= 85 then
		set result := '优秀';
	elseif score >= 60 then
		set result := '及格';
	else 
		set result := '不及格';
	end if;
end;

call p4(100, @result);
select @result;

#eg.2 
#将两百分值换算成百分制
create procedure p5(inout score double)
begin
	set score := score * 0.5;
end;

set @score = 150;
call p5(score);
select score;
```



### 13.2.2 流程控制语句

#### `if`

```mysql
if 条件1 then
	...
elseif 条件2 then
	...
else
	...
end if;
```

#### `case`

![image-20240419005905948](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419005905948.png)

- [610. 判断三角形](https://leetcode.cn/problems/triangle-judgement/)

  ```mysql
  SELECT 
      x,
      y,
      z,
      CASE
          WHEN x + y > z AND x + z > y AND y + z > x THEN 'Yes'
          ELSE 'No'
      END AS 'triangle'
  FROM
      triangle
  ;
  ```

#### `while`

```mysql
#eg.1
#计算1-n的和
create procedure p7(in n int) 
begin
	declare total int default 0;
	while n>0 do
		set total := total+n;
		set n := n - 1;
	end while;
end;
```

#### `repeat`

- 满足条件退出循环

```mysql
#语法
repeat
	...
	until 条件
end repeat;
```

#### `loop`

![image-20240419011352880](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419011352880.png)

```mysql
#eg.1
# 计算1-n中偶数的总和
create procedure p9(in n int) 
begin
	declare total int default 0;
	
	sum: loop
		if n <= 0 then 
			leave sum;
		end if;
		if n % 2 = 1 then 
			n := n - 1;
			iterate sum; 
		end if;
		set total := total + n;
		n := n - 1;
	end loop sum;
	
end;
```

### 13.2.3 游标(光标）—— `cursor`

![image-20240419012232080](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419012232080.png)

```mysql
#eg.1
#将年龄小于uage的行插入到一个新表当中
create procedure p11(in uage int)
begin
	#需要先声明变量再声明游标
	declare uname varchar(100);
	declare upro varchar(100);
	declare u_cursor cursor for select name , profession from tb_user where age <=uage;

	
	drop table if exists tb_user_pro;
	create table if not exists tb_user_pro(
        id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
   	);
   	
   	open u_cursor;
   	while frue do
   		fetch u_cursor into uname , upro;     #error:考虑判断游标里没有数据
   		insert into tb_user_pro values (null, uname,upro);
   	end while;
   	close u_cursor;
   
end;
```

### 13.2.4 [条件处理程序](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html)

![image-20240419014225459](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419014225459.png)

```mysql
#eg.1
#将年龄小于uage的行插入到一个新表当中
#解决前一个例子的报错问题
create procedure p11(in uage int)
begin
	#需要先声明变量再声明游标
	declare uname varchar(100);
	declare upro varchar(100);
	declare u_cursor cursor for select name , profession from tb_user where age <=uage;
	#用于退出循环
	declare exit handler for SQLSTATE '02000' close u_cursor;  
	#declare exit handler for not found close u_cursor;  
	
	drop table if exists tb_user_pro;
	create table if not exists tb_user_pro(
        id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
   	);
   	
   	open u_cursor;
   	while true do
   		fetch u_cursor into uname ,upro;     #当游标没有数据时报错，被条件处理程序捕获。退出游标
   		insert into tb_user_pro values (null, uname,upro);
   	end while;
   	close u_cursor;
   
end;
```

### 13.2.5 存储函数

- 有返回值的存储过程，参数只能是IN类型（默认也是IN）

![image-20240419015021889](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419015021889.png)

```mysql
#求1-n的总和
create function func(n int) returns int deterministic
begin 
	declare total int default 0;
	while n > 0 do
		set total := total + n;
		set n := n - 1;
	end while;
	
	return total;
end;

#调用
select func(10);
```

## 13.3 触发器

- 行级触发：影响多少行，触发多少次
- 语句级触发：一次sql之触发一次

![image-20240419170108399](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419170108399.png)

```mysql
#创建
CREATE TRIGGER trigger_name
BEFORE/AFTER INSERT/UPDATE/DELETE ON tbl_name FOR EACH ROW #行级触发器
BEGIN
	...
END;

#查看
SHOW TRIGGERS ;

# 删除
DROP TRIGGER [schema_name.]trigger_name; #如果没有指定schema_name，默认为当前数据库。
```



# 14 用户管理

- root初始化密码

![image-20240211222326692](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240211222326692.png)

## 14.1 用户操作

```mysql
use musql;
#查看主机、用户、密码
select host, user, authentication_string from user;

#创建一个在本地登录的用户，不能远程登陆，Dusong 密码：123456
create user 'Dusong'@'localhost' identified by '123456';   

#刷新
flush privileges;

#删除用户
drop user '用户名'@'主机名';

#创建可以远端登录的用户
create user 'Dusong'@'%' identified by '123456';

#自己该自己的密码
set password=password('xxxx');

#root用户改别人的密码 
#1.
set password for '用户名'@'主机名'=password('xxxx');
#2.
update user set authentication_string=password('xxx') where user='用户名';
```

## 14.2 用户权限

```mysql
#给某用户权限
grant 权限列表 on 库.表名 to '用户名'@'主机名'

# 给某用户所有权限
grant all [privileges] on ...;

#查看某个用户的权限
show grant for ''@'';

#去掉用户的某个权限
revoke [] on 库.表名 from ''@''      
#eg. revoke delete,insert on rootDB.user from 'Dusong'@'%';
```



# 15 C/C++引入MySQL客户端库

```bash
#mysql动静态库
ls /usr/lib64/mysql/
#mysql头文件
ls /usr/include/mysql/
```

```cpp
//查看客户端版本
mysql_get_client_info()
```

## 15.1 C API

- 初始化

```cpp
#include <mysql/mysql.h>
#include <iostream>

int main() {
    //初始化
	MYSQL *myfd = mysql_init(nullptr);
    
    //链接, 返回nullptr则链接失败
    mysql_real_connect(myfd, "127.0.0.1", "Dusong", "123456", "test_db", 3306, nullptr, 0); 
    
    //设置字符集
    mysql_set_character_set(myfd, "utf8"); 
    
    //sql
    mysql_query(myfd, "select * from user");  //查询user表
    
    //获取结果
    MYSQL_RES *res = mysql_store_result(myfd);
        
    //关闭连接 
    mysql_close(myfd);
}
```

- 链接数据库

![image-20240208143738984](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240208143738984.png)



- 设置字符集，默认时`latin1`，而`mysqld`使用的`utf8`

```cpp
mysql_set_character_set(myfd, "utf8");
```



- 下发mysql命令

 ![image-20240208152527463](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240208152527463.png)

- 获取查询结果集 （select）

  ![image-20240208154044350](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240208154044350.png)

  - 获取行数：`my_ulonglong mysql_num_rows(MYSQL_RES *res)`
  - 获取列数：`my_ulonglong mysql_num_fields(MYSQL_RES *res)`

- 打印结果

```cpp
my_ulonglong row = mysql_num_rows(res);
my_ulonglong col = mysql_num_fields(res);

//属性
MYSQL_FIELD *fields = mysql_fetch_fields(res);
for (int i = 0; i < col; i++) {
    cout << fields[i].name << '\t';
}
cout << '\n';


//内容
for (int i = 0; i < row; i++) {
    MYSQL_RES row_res = mysql_fetch_row(res);
    for (int j = 0; j < col; j++) {
        cout << row_res[j] << '\t';
    }
    cout << '\n';
}
```



- 释放结果集的空间

![image-20240210221818901](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240210221818901.png)



# 16 SQL优化

## 16.1 `insert`优化

1. ### 批量插入

```mysql
insert into tb_test values(1,'Tom'),(2,'Cat'),(3,"Jerry');
```

2. ### 手动事务提交

```mysql
start transaction;
insert into tb_test values(1, 'Tom'),(2,'Cat'),(3,'Jerry');
insert into tb_test values(4,'Tom'),(5,'Cat'),(6,'Jerry');
insert into tb_test values(7,'Tom'),(8,'Cat'),(9,'Jerry');
commit;
```

3. ### 主键顺序插入(主键优化)

> 主键乱序插入:8 1  9 21 88 24 15 89 5 7 3
>
> 主键顺序插入:1  2 3 4 5 7 8 9 15 21 88 89

4. ### 大批量插入数据

![image-20240418141859451](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418141859451.png)

```mysql
mysql --local-infile -u root -p
 
select @@local_infile

set global local_infile = 1;

load data local infile '/root/xx.log' into table 'xxx' fields terminated by ',' lines terminated by '\n';
```



## 16.2 主键优化

### 16.2.1 页分裂——主键乱序插入

![image-20240418142944414](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418142944414.png)

此时插入id为50的行，发生页分裂，开辟一个新的数据页3#，将1#号数据页的50%移动到3#，最后链接数据页

![image-20240418143207618](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418143207618.png)

### 16.2.2 页合并——删除数据

- `MERGE_THRESHOLD`：合并页的阈值，在创建表或者索引时可指定

![image-20240418143342522](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418143342522.png)

![image-20240418143357909](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418143357909.png)



### 16.2.3 主键的设计原则

1. 满足业务需求的情况下，尽量降低主键的长度。
2. 插入数据时，尽量选择顺序插入，选择使用`AUTO_INCREMENT`自增主键。
3. 尽量不要使用UUID做主键或者是其他自然主键，如身份证号。
4. 业务操作时，避免对主键的修改。



## 16.3 `order by`优化

> **<u>Using filesort</u>**：通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort。buffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫FileSort排序。
>
> **<u>Using index</u>**：通过有序索引顺序扫描直接返回有序数据，这种情况即为using index，不需要额外排序，操作效率高。



1. 对于一个默认升序的联合索引，如果在order by时一个升序一个降序，将会走using index 和 using filesort

![image-20240418150550797](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418150550797.png)

- `show index from xxx`中的`Collation`列为A(asc)表示升序，D(desc)表示倒序，可以在建立索引时指定：

  ```mysql
  create index idx_user_age_pho_ad on tb_user(age asc, phone desc);
  ```

2. 尽量使用覆盖索引，不然都会使用using filesort

3. 如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小 `sort_buffer_size`(默认256k)。

   ```mysql
   show variables like 'sort_buffer_size';
   ```

   

## 16.4 `group by` 优化

- 通过索引进行优化，分组时索引的使用也满足最左前缀法则



## 16.5 `limit` 优化 

- 通过覆盖索引 + 子查询优化

![image-20240418173924025](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418173924025.png)

```mysql
explain select * from tb_sku t, 
(select id from tb_sku order by id limit 2000000, 10) a
where t.id = a.id;
```

## 16.6 `count`优化

### `count(常量)`、`count(字段名/主键)`、`count(*)`的区别

- `count(常量)`、`count(*)`表示查询符合条件的行数
- `count(字段名)`查询符合条件的列的值不为NULL的行数

![image-20240418182821200](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418182821200.png)

## 16.7 `update`优化

事务中，根据索引进行update使用的是行锁，反之则用的是表级锁，行锁升级为表锁会降低并发性

![image-20240418183308449](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240418183308449.png)



# 17 锁

## 17.1 全局锁

- 备份表，不加锁的情况：

![image-20240419220227928](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419220227928.png)

- 加锁的情况

![](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419220414399.png)

```mysql
flush tables with read lock;	#加锁


unlock tables;		#解锁
```

```shell
#备份
mysqldump -uroot -p123123 itcase > itcast.sql
```

- 在InnoDB引擎中，我们可以在备份时加上参数`--single-transaction` 参数来完成不加锁的一致性数据备份。(底层使用快照读实现)

```shell
mysqldump --single-transaction -uroot -p123123 itcase > itcast.sql
```

## 17.2 表级锁

### 17.2.1 表锁

1. 表共享读锁（read lock）：共享读，不能写
2. 表独占写锁（write lock）：只有自己能读写

```mysql
#加锁
lock tables xxx read;
lock tables xxx write;

#解锁
unlock tables;  #或者客户端断开连接
```

![image-20240419222447147](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419222447147.png)

### 17.2.2 元数据锁(meta data lock, MDL)

![image-20240419223003894](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419223003894.png)

- 共享锁与排他锁互斥的情况

![1211](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419223711258.png)

```mysql
#查看元数据锁
select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.metadata_locks;
```

### 17.2.3 意向锁

1. 意向共享锁（IS)∶
   1. 由语句`select ... lock in share mode`添加。
   2. 与表锁共享锁( read）兼容，与表锁排它锁（ write)互斥。
2. 意向排他锁（IX)︰
   1. 由`insert、update、delete、select ... for update`添加。
   2. 与表锁共享锁(read）及排它锁(write）都互斥。意向锁之间不会互斥。

为了避免DML在执行时，**加的行锁与表锁的冲突**，在InnoDB中引入了意向锁，使得表锁不用检查每行数据是否加锁，使用意向锁来减少表锁的检查。

- 线程A在update时加行锁并且加上意向锁，此时线程B加表锁会被阻塞

![image-20240419225055119](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240419225055119.png)

- 查看意向锁及行锁的加锁情况

  ```mysql
  select object_schema,object_name,index_name, lock_type,lock_mode,lock_data from performance_schema.data_locks;
  ```


## 17.3 行级锁

![image-20240420141104363](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420141104363.png)

### 17.3.1 行锁

默认情况下，InnoDB在 REPEATABLEREAD事务隔离级别运行，InnoDB使用**<u>next-key锁</u>**进行搜索和索引扫描，以防止幻读。

1. 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。
2. InnoDB的行锁是针对于索引加的锁，<u>**不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时就会升级为表锁**</u>

![image-20240420141233237](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420141233237.png)![image-20240420141325582](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420141325582.png)



### 17.3.2 间隙锁&临键锁

1. 索引上的等值查询(唯一索引)，给不存在的记录加锁时,优化为间隙锁。

![image-20240420142953689](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420142953689.png)

2. 索引上的等值查询(普通索引)，向右遍历时最后一个值不满足查询需求时，**next-key lock退化为间隙锁**

- 因为普通索引不唯一，防止其他事务在间隙中插入相同的索引值，需要加上间隙锁

![image-20240420143632829](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420143632829.png)

![image-20240420144043565](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420144043565.png)

3. 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

```mysql
begin;

select * from stu where id >= 10 lock in share mod;

end;
```



- 间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁。







# 18 InnoDB存储引擎

![image-20240420150022874](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420150022874.png)

## 18.1 内存结构

1. Buffer Pool

![image-20240420150311906](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420150311906.png)

2. Change Buffer

![image-20240420150558680](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420150558680.png)

3. 自适应哈希

![image-20240420150850182](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420150850182.png)

4. Log Buffer

![image-20240420151031587](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420151031587.png)



## 18.2 磁盘结构

![image-20240420151412994](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420151412994.png)

![image-20240420151712507](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420151712507.png)

![image-20240420151858718](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420151858718.png)



## 18.3 后台线程

![image-20240420152156565](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420152156565.png)



## 18.4 事务原理

![image-20240420155358682](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420155358682.png)

### 18.4.1 redo log —— 一致性

![image-20240420160121214](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420160121214.png)

### 18.4.2 undo log —— 原子性&MVCC

![image-20240420160730528](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420160730528.png)

## 18.5 MVCC

### 基本概念

![image-20240420161702220](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420161702220.png)

### 隐藏字段

![image-20240420161859851](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420161859851.png)

- 查看idb文件信息

  ```bash
  idb2sdi xxx.idb
  ```

  

### undo log

回滚日志，在insert、update、delete的时候产生的便于数据回滚的日志。

- `insert`的时候，产生的undo log日志只在回滚时需要，在事务提交后，可被立即删除。

- `update`、`delete`的时候，产生的undo log日志不仅在回滚时需要，在快照读时也需要，不会立即被删除。

![image-20240420163430922](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420163430922.png)

### read view

![image-20240420163603169](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420163603169.png)

![image-20240420163911405](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420163911405.png)

- read view 生成的时机：

  - RC：每一次执行快照读都会生成

  ![image-20240420164924369](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420164924369.png)

  - RR：只在第一次快照读的时候生成，后面快照读复用

    ![image-20240420165149924](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420165149924.png)



# 19 MySQL管理

## 19.1 系统数据库

 ![image-20240420212450987](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420212450987.png)

## 19.2 常用工具

1. `mysql`

![image-20240420212629812](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420212629812.png)

2. `mysqladmin`

![image-20240420212806164](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420212806164.png)

3. `mysqlbinlog`

![image-20240420213406836](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420213406836.png)

4. `mysqlshow`

![image-20240420213432780](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420213432780.png)

5. `mysqldump`

![image-20240420221353746](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420221353746.png)

6. `mysqllimport` / `source`

![image-20240420222334209](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240420222334209.png)





# 20 其他

![image-20240426211336081](https://typora-dusong.oss-cn-chengdu.aliyuncs.com/image-20240426211336081.png)





[^8]: 注意左模糊匹配造成索引失效
[^9]: 注意外连接与内连接的区别