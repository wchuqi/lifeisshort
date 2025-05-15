# PG修炼之道

## 准备篇

### 简介

官网：

https://www.postgresql.org/

中文文档：

http://www.postgres.cn/docs/13/index.html

支持丰富的数据类型，比如json，jsonb，数组类型和自定义类型。

提供丰富的接口，可以方便扩展功能。比如，在GiST框架下实现自己的索引类型；支持C语言写自定义函数、触发器；支持python、rust、java、c语言写自定义函数（PL/Perl、PL/Python、PL/Java、PL/Tcl、PL/Rust）

开源、免费，使用类BSD协议，在使用在二次开发上基本没有限制。

PG VS MySql

PG VS Oracle



### 安装和配置

#### 二进制安装包

#### 源码编译安装

Bison是自动生成语法分析器的程序。

Flex是自动生成词法分析器的程序。

pg_hba.conf文件是一个黑白名单的访问控制文件，可以控制允许哪些IP地址的机器访问数据库。

**数据库日志相关参数**

日志的收集一般是需要打开的:
`logging_collector = on`

注意，在新版本的数据库中，以上参数默认已打开。

日志的目录一般使用默认值即可：
`log_directory = 'pg_log'`

在PostgreSQL10版本中，程序日志目录是在log目录下，即上述配置如下：
`log_directory = 'log' # directory where log files are written`

日志切换和是否覆盖一般可以使用如下几种不同的方案。

方案一：每天生成一个新的日志文件。
配置方法如下：
```shell
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
log_truncate_on_rotation = off
log_rotation_age = 1d
log_rotation_size = 0
```

方案二：每当日志写满一定的大小（如10MB），则切换一个日志。
配置方法如下：
```shell
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
log_truncate_on_rotation = off
log_rotation_age = 0
log_rotation_size = 10M
```

方案三：只保留最近7天的日志，进行循环覆盖。
配置方法如下：
```shell
log_filename = 'postgresql-%a.log'
log_truncate_on_rotation = on
log_rotation_age = 1d
log_rotation_size = 0
```

PostgreSQL10以上版本默认的日志方案是方案三，不需要再进行修改。

**内存参数的设置**


`shared_buffers`：共享内存的大小，主要用于共享数据块。

`work_mem`：单个SQL执行时，以及排序、Hash Join时使用的内存，SQL运行完毕后，该内存就会被释放。

`shared_buffers`默认值为32MB，如果你的机器上有足够的内存，可以把这个参数设置得大一些，如可以设置为物理内存大小的四分之一，这样数据库就可以缓存更多数据块，当读取数据时，就可以从共享内存中进行读取，而不需要再从文件上去读。而work_mem设置大一些，会使排序操作效率更高。

**如何使用较大的数据块提高I/O性能？**

在数据仓库中使用PostgreSQL时，如果希望使用较大的数据块以提高I/O性能怎么办？

对于这类问题，只能使用从源码安装的方法，在执行`./configure`命令时指定较大的数据块，一般也需要指定较大的WAL日志块和WAL日志文件大小。

如指定32KB的数据块、32KB的WAL日志块、64MB的WAL日志文件的configure命令如下：
`./configure --prefix=/usr/local/pgsql9.2.4 --with-perl --with-python --with-blocksize=32 --with-wal-blocksize=32 --with-wal-segsize=64`

> 对于此时编译出来的PostgreSQL程序创建的PostgreSQL数据库，不能使用其他块大小的PostgreSQL程序启动。

**打开数据块的checksum功能**

对于一些数据可靠性要求很高的场景，如一些金融领域，建议打开数据块的checksum校验功能。

而在PostgreSQL12版本之前，需要在用`initdb`命令创建数据库时就把这个功能加上：
`initdb -k`

initdb命令中增加了`-k`参数，所创建的数据库的数据块就有了checksum功能。

在PostgreSQL12版本之后提供了工具pg_checksums，可以把一个没有checksum功能的数据库转换为具有该功能的数据库。

运行这个工具需要先把数据库停掉，否则会报如下错误：
> pg_checksums: error: cluster must be shut down

用`pg_checksums -c`检查当前数据库是否打开了checksum功能：
```shell
[postgres@pg01 ~]$ pg_checksums -c
pg_checksums: error: data checksums are not enabled in cluster
```

上面的提示是指没有打开checksum功能，则用下面的命令把数据库转换成具有checksum功能的数据库：
```shell
[postgres@pg01 ~]$ pg_checksums -e -P
1223/1223 MB (100%) computed
Checksum operation completed
Files scanned: 1325
Blocks scanned: 156566
pg_checksums: syncing data directory
pg_checksums: updating control file
Checksums enabled in cluster
```

在上面的命令中，“-P”参数是为了显示进度。

如果数据库比较大，使用pg_checksums把数据库转换成具有checksum功能的数据库需要比较长的时间，所以做这个操作要求有比较长的数据库停机时间。

### SQL入门

SQL是结构化查询语言（Structured Query Language）的简称，它是关系型数据库的最重要的操作语言，并且它的影响已经超出了数据库领域，比如，在Hadoop中的Hive就是一个SQL接口。

SQL是一种声明式编程语言，与命令式编程语言有较大的差异。

声明式编程语言主要是描述用户需要做什么，需要得到什么结果，不像命令式编辑语言需要描述怎么做，过程是什么。

SQL语句能智能地实现用户的需要，而不需要用户关心具体的运行过程。要完全用SQL写一个应用程序是不可能的，但非计算机专业人士也可以使用SQL语句，因为SQL语句直观易懂。

**语句的分类**

SQL命令一般分为DQL、DML、DDL三类。
- DQL：
> 数据查询语句，基本就是SELECT查询命令，用于数据查询。
- DML：
> Data Manipulation Language，即数据操纵语言，主要用于插入、更新、删除数据，所以也分为INSERT、UPDATE、DELETE三种语句。
- DDL：
> Data Definition Language，即数据定义语言，简单来说，是用于创建、删除、修改表、索引等数据库对象的语言

**建表时可以指定表的主键，主键是表中行的唯一标识，这个唯一标识是不能重复的。**

在创建表的语句中，可以在列定义后面用`primary key`主键来指定该列为主键。

```sql
CREATE TABLE student(
    no int primary key, 
    student_name varchar(40), 
    age int
);
```

使用`GROUP BY`语句时需要使用聚合函数，常用的聚合函数有count、sum等。

**多表关联查询**

```sql
-- join
SELECT student_name, class_name 
FROM student, class
WHERE student.class_no = class.no;

SELECT student_name, class_name 
FROM student a, class b
WHERE a.class_no = b.no;
```

**子查询**

当一个查询是另一个查询的条件时，称之为子查询。

主要有4种语法的子查询：
- 带有谓词IN的子查询：`expression [NOT] IN (sqlstatement)`
- 带有EXISTS谓词的子查询：`[NOT] EXISTS (sqlstatement)`
- 带有比较运算符的子查询：`comparison(>,<,=,!=)(sqlstatement)`
- 带有ANY（SOME）或ALL谓词的子查询：`comparison [ANY|ALL|SOME] (sqlstatement)`

```sql
-- in
SELECT * FROM student WHERE class_no in 
(select no FROM class where class_name = '初二(1)班');
或者
-- exists
SELECT * FROM student s WHERE EXISTS 
(SELECT 1 FROM class c WHERE s.class_no=c.no AND c.class_name = '初二(1)班');
或者
-- =
SELECT * FROM student WHERE class_no = 
(SELECT no FROM class c WHERE class_name = '初二(1)班');
或者
-- any
SELECT * FROM student WHERE class_no = 
any(SELECT no FROM class c WHERE class_name = '初二(1)班');
```

```sql
-- 如果我们要查询两个班级的学生记录，不能使用带有等于=比较符的子查询
SELECT * FROM student WHERE no = 
(SELECT no FROM class c WHERE class_name in ('初二(1)班', '初二(2)班'));
ERROR: more than one row returned by a subquery used as an expression

-- 上面的查询报错说子查询不能返回多行。
-- 这种不能返回多行的子查询也称标量子查询，标量子查询不仅能嵌套在WHERE子句中，也可以嵌套在SELECT的列表中，如我们要查询每个班级学生的最大年龄，则可以用如下SQL语句
SELECT 
no
,class_name
,(SELECT max(age) as max_age FROM student s WHERE s.no= c.no) as max_age 
FROM class c;

-- 查询两个班级的学生记录时用带有ANY（SOME）谓词的子查询就没有问题
SELECT * FROM student WHERE class_no = 
any(SELECT no FROM class c WHERE class_name in ('初二(1)班', '初二(2)班'));
```

**TRUNCATE TABLE语句**

TRUNCATE TABLE语句的用途是清空表内容。

不带WHERE条件子句的DELETE语句也表示清空表内容，从执行结果来看，两者实现了相同的功能。

但两者实现的原理是不一样的。

TRUNCATE TABLE语句是DDL语句，即数据定义语句，相当于用重新定义一个新表的方法把原表的内容直接丢弃了，所以TRUNCATE TABLE执行起来会很快；

而DELETE语句是DML语句，我们可以认为DELETE语句是把数据一条一条地删除，所以DELETE语句删除多行数据时执行起来比较慢。

如果想把student_bak表中的数据清除，可以使用如下命令：
`TRUNCATE TABLE student_bak;`

## 基础篇

### psql工具



### 数据类型

### 逻辑结构管理

### pg的核心架构

### 服务管理

实例恢复与热备份原理解密

控制文件解密

WAL文件解密

BRIN索引（块范围索引 >=9.5）

全文索引

数组

使用大页优化数据库

数据库配置最佳实践

表、索引、sql优化

逻辑复制

Standby备库

开源软件

PgBouncer

Slony-I

PL/Proxy

Postgres-XC

pgpool-II集群

Bucardo

