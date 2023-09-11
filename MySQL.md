# MYSQL8.0

## 1. 基本指令

### 1.1 创建数据库
create database name;

### 1.2 展示数据库/表
show databases;

show tables;

#### 1.2.1 展示数据库创建信息
show create database databasename;

#### 1.2.2 查看字符集编码（模糊搜索）
show variables like 'character_%';

### 1.3 创建表
1. use database;
2. create table tableNamae(id int, name varchar(15));

### 1.4 查看表中数据
select * from table;

### 1.5 添加数据

#### 1.5.1 单个添加
insert into employees values(1001, 'Tom');

#### 1.5.2 批量添加

### 1.6 乱码问题

一般是 GBK 或者 Latin字符集会导致中文乱码，在配置文件my.ini将字符集设置成utf-8-m64或者utf8别的。

## 2. SQL分类、规则和规范

### 2.1 SQL分类

#### DDL (Dataa Definition Languages) 数据定义语言
这些语句定义了不同的数据库、表、视图、索引等数据库对象，还可以创建、删除、修改数据库和数据表的结构。主要预言关键字 CREATE、ALTER、DROP、RENAME、TRUNCATE

#### DML (Data Manipulation Language) 数据操作语言
用于增删改查，主要的语言关键字有 INSERT、DELETE、UPDATE、SELECT等。

#### DCL (Data Control Language) 数据控制语言
用于定义数据库、表、字段、用户的访问权限和安全级别。主要语言关键字有 GRANT、REVOKE、COMMIT、ROLLBACK、SAVEPOINT等。

#### DQL 数据查询语言
查询语句使用的频繁度导致很多人将查询语句单独拎出来做一类。

#### TCL (Transaction Control Language) 事务控制语言
将 COMMIT、ROLLBACK单独作为控制事务的一类。

### 2.2 SQL规则和规范

#### 2.2.1 基本规则
* SQL可以写在一行或者多行。分行写必须有缩进。
* 每条命令以 ; 或 \g 或 \G 结束。
* 关键字不能被缩写也不能分行。
* 关于标点：
  * 字符串和日期时间类型数据必须用单引号括上。
  * 列的别名，尽量使用双引号，而且不建议省略 as。

#### 2.2.2 大小写规范
* Windows下不区分大小写。
* Linux下区分大小写。
  * 数据库、表名、表的别名、变量名是严格区分大小写的。
  * 关键字、函数名、列名、字段名、别名是忽略大小写的。
* 推荐统一书写方式：
  * 数据库、表名、表的别名、字段名、字段别名等要小写。
  * SQL关键字、函数名、绑定变量都大写。

### 2.2.3 注释
* 单行注释：# 开头 注释文字 或 -- 注释文字。
* 多行注释：/* 注释文字 */。

#### 2.2.4 命名规则
* 数据库、表名不得超过30个字符，变量名限制为29个。
* 只能包含大小写字母、数字和下划线等63个字符。
* 数据库名、表名、字段名等中间不要包含空格。
* 同一父级下，不能同名。
* 不要用保留字，容易冲突，一定要用着重号引起来。
* 同库中保持字段名和类型的一致性。

#### 2.2.5 导入
* source 文件全路径名; 。
* 基于图形化界面工具导入。

## 3. 基本的SELECT语句

### 3.1 最基本的SELECT语句
SELECT 字段1, 字段2, ... FROM 表名;

其中，*，表示多有的字段或列。

### 3.2 列的别名
* 重命名一个列
* 便于计算
* 紧跟列名 或者 在列名和别名中间加入关键字 as (可省略)
* 别名简短，见名知意

```SQL
SELECT employee_id emp_id FROM employees;

SELECT employee_id AS emp_id FROM employees;

-- 当别名字段中出现空格时候，可以用双引号括起，不然会报错。
SELECT employee_id "emp id" FROM employees;
```

### 3.3 去重复行 DISTINCT

清除重复数据，比如查询所有项目组中的组员名字。其中有的人可能在不同项目组身兼数职，查询以员工名字为主，因此名字只出现一次即可，但是不同项目中都有他。普通查询就会导致他的名字出现多次，因此使用关键字 DISTINCT去重。

但是数据量比较大的时候，这个关键字会影响查询时间。

DINSTINCT不要放在句中，放句首。

```SQL
SELECT DISTINCT employee_name FROM employees;
```

### 3.4 空值 NULL
* null不等同于0也不等同于''。
* 空值会参与运算，相乘的就是null。
* 解决办法：关键字 IFNULL(字段, 0)。如果这个值是空值，就用0替换。

这个就是查询的月工资和年工资：
```SQL
SELECT salary "月工资", salary * (1 + IFNULL(commission_pct, 0)) * 12 "年工资" FROM employees;
```

### 3.5 着重号 ``

对于和关键字同名的字段，需要加着重号。

```SQL
SELECT * FROM `order`;
```

### 3.6 查询常数

给所有字段匹配一个常数/常量。

```SQL
SELECT '666', id, name FROM employees;
```

### 3.7 显示表结构 DESCRIBE / DESC
```SQL
DESCRIBE employees;

DESC employees;
```

### 3.8 过滤数据

#### 3.8.1 过滤条件 WHERE
WHERE 始终紧跟在 FROM结构后。

```SQL
SELECT * 
FROM employees 
WHERE gender = 'male';
```

## 4. 运算符

### 4.1 算术运算符

* 加减乘除取余/模：+、-、*、/ (DIV)、% (MOD)。
* 数字与字符串运算还是该数字。
* 空值参与运算结果为空，包括加减。
* 当除以0的时候，不会报错，但是值会变成空值。

### 4.2 比较运算符

同类型可以比较，不同类型比较就会不等。

* = 等于运算符。当两个操作数都是NULL时，会返回NULL。
* <=> 安全等于运算符，为null而生。当两个操作数都是NULL时，它会返回 1（TRUE），而不是NULL。
* <> 或 !=，不等于运算符。
* <、> 大小于。
* IS NULL，判空。
* IS NOT NULL，判非空。
* LEAST，多个值中最小的。
* GREATEST，多个值中最大的。
* BETWEEN ... AND ...，在两值之间。
* ISNULL，判断空。
* IN，判断一个值是否在一个列表/字段/列中。
* NOT IN，判断一个值是否不在一个列表/字段/列中。
* LIKE，模糊匹配。
* REGEXP，判断值是否符合正则表达式规则。
* RLIKE，判断值是否符合正则表达式规则。

### 4.3 逻辑运算符
* NOT / !，非。
* AND / &&，与，前后都得是真。
* OR / ||，或，前后有一个是真。
* XOR，异或，左右两边不一样为真

-----------------------------------------

Mysql基础
===

Key、primary key、unique key、index的区别
---

key：普通索引

* 作用1：约束（constraint），偏重约束和规范数据库的结构结构完整性
* 作用2：索引（index），辅助查询
* primary key：主键
  * 作用1：约束（constraint)，规范一个存储主键和唯一性（UNIQUE）
  * 作用2：在这个key上建立一个主键索引
  * 主键约束：
    * 唯一标识数据中的每条记录（其实不一定是）
    * 值唯一
    * 不能为空
    * 一个表应该有主键且只能有一个主键
* unique key：唯一索引
  * 作用1：约束（constraint)，规范数据的唯一性
  * 作用2：在这个key上建立唯一索引
  * UNIQUE约束：唯一标识数据中的每条记录
    * 注，唯一约束和主键的区别，就是表可以拥有多个唯一约束，但只能有一个主键
* foreign key：外键
  * 作用1：约束（constraint)，规范数据的引用完整性
  * 作用2：在这个key上建立索引

index: 索引

* 索引只是辅助查询，它创建时会在另外的表空间（innodb表）以一个目录的结构存储。索引不会约束字段的行为（这是key的工作）

Foreign key 下的关键字
---

* Cascade
  * 作用：在父表上update / delete 记录时，子表中拥有该记录做外键的记录被同步 update / delete
* SET NULL
  * 作用：在父表上update/delete记录时，将子表上匹配记录的列设为null (要注意子表的外键列不能为not null) 
* NO ACTION
  * 作用：如果子表中有匹配的记录,则不允许对父表对应候选键进行update/delete操作
* RESTRICT：同 NO ACTION

* 外键约束使用最多的两种情况无外乎：
  * 父表更新时子表也更新，父表删除时如果子表有匹配的项，删除失败；
  * 父表更新时子表也更新，父表删除时子表匹配的项也删除。