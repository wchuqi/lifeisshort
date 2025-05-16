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

psql是PostgreSQL中的一个命令行交互式客户端工具，类似Oracle中的命令行工具sqlplus，它允许用户交互地键入SQL语句或命令，然后将其发送给PostgreSQL服务器，再显示SQL语句或命令的结果。

另外，所输入内容还可以来自一个文件。此外，它还提供了一些快捷命令和多种类似Shell的特性来实现书写脚本，以及对大量任务的自动化操作。

**为什么不需要输入用户名和密码？**

安装PostgreSQL数据库时，会创建一个与初始化数据库时的操作系统用户同名的数据库用户，这个用户是数据库的超级用户，在此OS用户下登录数据库时，因为执行的是操作系统认证，所以是不需要用户名和密码的，用户也可以通过修改pg_hba.conf文件来要求用户输入密码。

有一个叫“postgres”的数据库，这是默认postgreSQL安装完成后就有的一个数据库，还有两个模板数据库,template0和template1。

当用户创建数据库时，默认是从模板数据库“template1”克隆来的，所以通常我们可以定制template1数据库中的内容，如向template1中添加一些表后函数，这样后续创建的数据库就会继承template1中的内容，也会拥有这些表和函数。

而template0是一个最简化的模板库，如果创建数据库时明确指定从此数据库克隆，将创建出一个最简化的数据库。


```sql
-- 查看数据库信息
psql -l
或者命令交互输入模式使用：
\l

-- 查看表
\d

-- 连接到testdb数据库上
\c testdb

-- psql连接数据库的常用的方法，命令格式如下：
psql -h <hostname or ip> -p <端口> [数据库名称] [用户名称]

-- 这些连接参数也可以通过环境变量指定，示例如下：
export PGDATABASE=testdb
export PGHOST=192.168.56.11
export PGPORT=5432
export PGUSER=postgres

然后运行psql，其运行结果与
psql -h 192.168.56.11 -p 5432 testdb postgres
的运行结果相同。

-- 查询SQL语句的语法
\h create user

-- \d命令的格式如下：
\d [ pattern ]
\d [ pattern ]+

该命令将显示每个匹配“pattern”（表、视图、索引、序列）的信息，包括对象中所有的列、各列的数据类型、表空间（如果不是默认的）和所有特殊属性（诸如“NOT NULL”或默认值等）等。

唯一约束相关的索引、规则、约束、触发器也同样会显示出来。

如果关系是一个视图，还会显示视图的定义（“匹配模式”将在下面定义）。

-- 如果“\d”命令后什么都不带，将列出当前数据库中的所有表
\d

-- “\d”命令后面跟一个表名，表示显示这个表的结构定义
\d tbl_1

-- “\d”命令也可以用于显示索引信息
\d t_pkey

-- “\d”命令后面的表名或索引名中也可以使用通配符，如“*”或“?”等
\d x?
\d t*

-- 使用“\d+”命令可以显示比“\d”命令的执行结果更详细的信息，除了前面介绍的信息，还会显示所有与表的列关联的注释，以及表中出现的OID。
\d+ tbl_1

匹配不同对象类型的“\d”命令如下：
- 如果只想显示匹配的表，可以使用“\dt”命令。
- 如果只想显示索引，可以使用“\di”命令。
- 如果只想显示序列，可以使用“\ds”命令。
- 如果只想显示视图，可以使用“\dv”命令。
- 如果想显示函数，可以使用“\df”命令。

-- 如果想显示执行SQL语句的时间，可以用“\timing”命令
\timing on

-- 列出所有的schema
\dn

-- 显示所有的表空间
\db

实际上，PostgreSQL中的表空间对应一个目录，放在这个表空间中的表，就是把表的数据文件放到该表空间下。

-- 列出数据库中的所有角色或用户
\dg
或者
\du
命令等价。原因是，在PostgreSQL数据库中，用户和角色是不分的。

-- 显示表的权限分配情况
\dp tbl_1
或者
\z tbl_1
```

指定客户端字符集的命令

当客户端的字符编码与服务器不一致时，可能会出现乱码，可以使用“\encoding”命令指定客户端的字符编码，

如使用`\encoding gbk;`命令设置客户端的字符编码为“gbk”；

使用`\encoding utf8;`命令设置客户端的字符编码为“utf8”。

默认情况下，psql中执行SQL语句后输出的内容是只有内边框的表格
```sql
-- \pset命令设置边框的用法如下
\pset border 0：表示输出内容无边框。
\pset border 1：表示输出内容只有内边框。
\pset border 2：表示输出内容内外都有边框。

psql中默认的输出格式是\pset border 1。

默认分隔符是“|”，我们可以用命令“\pset fieldsep”来设置分隔符

\pset fieldsep '\t'
```

实际使用时，我们需要把SQL命令输出到一个文件中，而不是屏幕上，这时可以用“\o”命令指定一个文件，然后再执行上面的SQL命令，执行结果就会输出到这个文件中
```sql
\pset format unaligned
用“\t”命令来删除列头数据
\t
\o 111.txt
select * from class;
cat 111.txt
```

使用“\x”命令可以把按行展示的数据变成按列展示
```sql
\x
```

执行存储在外部文件中的SQL命令
> 命令“\i <文件名>”用于执行存储在外部文件中的SQL语句或命令。

```sql
\x
\i getrunsql
```

当然也可以在psql命令行中加上“-f <filename>”来执行SQL脚本文件中的命令
`psql -x -f getrunsql`

其中命令行参数“-x”的作用相当于在psql交互模式下运行“\x”命令

编辑命令“\e”可以用于编辑文件，也可用于编辑系统中已存在的函数或视图定义

输入“\e”命令后会调用一个编辑器，在Linux下通常是Vi，当“\e”命令不带任何参数时则是生成一个临时文件，前面执行的最后一条命令会出现在临时文件中，当编辑完成后退出编辑器并回到psql中时会立即执行该命令。

“\e”后面也可以指定一个文件名，但要求这个文件必须存在，否则会报错。
`\e 1.sql`

可以用“\ef”命令编辑一个函数的定义，如果“\ef”后面不跟任何参数，则会出现一个编辑函数的模板：
```sql
\ef
CREATE FUNCTION ( )
RETURNS
LANGUAGE
 -- common options: IMMUTABLE STABLE STRICT SECURITY
DEFINER
AS $function$
$function$
```

如果“\ef”后面跟一个函数名，则函数定义的内容会出现在Vi编辑器中，当编辑完成后按“wq:”保存并退出，再输入“;”就会执行所创建函数的SQL语句。

同样输入“\ev”且后面不跟任何参数时，在Vi中会出现一个创建视图的模板
```sql
CREATE VIEW AS
SELECT
-- something...
```
然后用户就可以在Vi中编辑这个创建视图的SQL语句，编辑完成后，保存并退出，再输入分号“;”，就会执行所创建视图的SQL语句。

也可以编辑已存在的视图的定义，只需在“\ev”命令后面跟视图的名称即可。

“\ef”和“\ev”命令可以用于查看函数或视图的定义，当然用户需要注意，退出Vi后，要在psql中输入“\reset”来清除psql的命令缓冲区，防止误执行创建函数和视图的SQL语句.

```sql
\ev vm_class
在这里进入Vi后，在Vi中用":q"退出
:q

\reset 
在这里不要忘了输入"\reset"命令清除psql缓冲区
Query buffer reset (cleared).
```

“\echo”命令用于输出一行信息
`\echo hello word`

此命令通常用于在使用.sql脚本的文件中输出提示信息。
```sql
文件“a.sql”有如下内容：
\echo =========================================
select * from x1;
\echo =========================================
运行a.sql脚本：
osdba=# \i a.sql
=========================================
id | name
----+--------
1 | aaaaaa
2 | bbbbbb
3 | cccccc
(3 rows)
=========================================
```

更多其他的命令可以用“\?”命令来显示
`\?`


可以使用上下方向键把以前使用过的命令或SQL语句调出来

连续单击两次Tab键表示把命令补全或给出输入提示

**需要特别注意的是，在psql中事务是自动提交的**

如果不想让事务自动提交，方法有两种。

方法一：运行“begin;”命令，然后执行DML语句，最后再执行commit或rollback语句
```sql
begin;
update x1 set name='xxxxx' where id=1;
rollback;
```

方法二：直接使用psql中的命令关闭自动提交功能。
`\set AUTOCOMMIT off`

这个命令中的“AUTOCOMMIT”是大写的，不能使用小写，如果使用小写，虽不会报错，但会导致关闭自动提交的操作无效。

**如何得到psql中快捷命令执行的实际SQL**

在启动psql的命令行中加上“-E”参数，就可以把psql中各种以“\”开头的命令执行的实际SQL语句打印出来。
```sql
psql -E postgres
\d
\d testtable*

-- 如果在已运行的psql中显示了某个命令实际执行的SQL语句后又想关闭此功能，该怎么办？
-- 这时可以使用“\set ECHO_HIDDEN on|off”命令

psql postgres
\dn
\set ECHO_HIDDEN on
\dn
```


### 数据类型

为了提高SQL的兼容性，部分数据类型还有很多别名。

integer类型，可以用int、int4表示，int与integer和int4是等效的。

smallint也可以用int2表示

bigint与int8是等效的。

char varying(n)可以用varchar(n)表示

numeric(m,n)也可以用decimal(m,n)表示

```sql
-- 对于复杂的数据类型，可以按照“类型名”加上单引号括起来的类型值格式来输入
select bit '11110011';
select int '1' + int '2';

-- 类型转换函数CAST来进行数据类型转换
select CAST('5' as int), CAST('2014-07-17' as date);
-- 还有一种更简捷的类型转换方式，即双冒号方式
select '5'::int, '2014-07-17'::date;
```

boolean的值要么是true（真），要么是false（假），如果是unknown（未知）状态，用NULL表示。

boolean在SQL中可以用不带引号的TRUE或FALSE表示，也可以用其他表示“真”和“假”的带引号字符表示，如'true'、'false'、'yes'、'no'，等等。

常用的逻辑操作符有：AND、OR、NOT。

SQL使用三值的布尔逻辑：TRUE、FALSE和NULL，其中NULL代表“未知”。

```sql
布尔类型可以使用“IS”这个比较运算符，具体如下：
expression IS TRUE。
expression IS NOT TRUE。
expression IS FALSE。
expression IS NOT FALSE。
expression IS UNKNOWN。
expression IS NOT UNKNOWN。
```

常用的数据类型是int（或integer），因为它提供了在范围、存储空间、性能之间的最佳平衡。

一般只有在磁盘空间紧张的时候才使用smallint类型。

通常，只有integer类型的取值范围不够时才使用bigint类型，因为前者的执行速度绝对快得多。

SQL只声明了整数类型integer（或int）和smallint。

精确的小数类型可用numeric、numeric(m,n)、numeric(m)表示。

其中，numeric类型和decimal类型是等效的，这两种类型都是SQL标准，可以存储最多1000位精度的数字，并且可准确地进行计算。

它们特别适用于货币金额和其他要求精确计算的场合。

不过，基于numeric类型的算术运算相比于基于整数类型或者下面介绍的浮点数类型的算术运算，其速度要慢很多。

```sql
声明一个字段的类型为numeric
NUMERIC(precision, scale)

其中，精度precision必须为正数，标度scale可以为0或者正数。

NUMERIC(precision)表示标度为0，与NUMERIC(precision,0)的含义是相同的。
```

如果不带任何精度与标度地声明NUMERIC，则表示创建一个可以存储任意精度和标度的数值（当然不能超过系统可以实现的精度和标度）。

这种类型的字段不会把输入数值转化成任何特定的标度，而带有标度声明的NUMERIC字段会把输入值转化为指定标度。

在标准SQL和MySQL中，语法DECIMAL等价于DECIMAL(M,0)，M在MySQL中默认为10，PostgreSQL中因作用不大而把它改成了一个任意精度和标度的数值。如果你关心可移植性，建议总是明确声明精度和标度。

**浮点数类型**

数据类型real和double precision是不精确的、变精度的数字类型。

对于浮点数，需要注意如下几个方面：

如果要求精确地计算（比如计算货币金额），应使用numeric类型。

如果想用这些类型做任何重要的复杂计算，尤其是那些对范围情况（无穷/下溢）严重依赖的复杂计算，那么应该仔细评诂你的实现。

对两个浮点数值进行相等性比较时，有可能不会像你所想象的那样运转。

除了普通的数字值之外，浮点类型还有以下几个特殊值：
- Infinity。
- -Infinity。
- NaN

这些值分别表示特殊值“正无穷大”“负无穷大”“不是一个数字”。

在不遵循IEEE 754浮点算术的机器上，这些值的含义可能不是预期的。

如果在SQL命令里把这些数值当作常量来写，必须在它们周围放上单引号，如
`UPDATE table SET x='Infinity'`。输入时，这些值与大小写无关。

**序列类型**

在序列类型中，serial和bigserial与MySQL中的自增字段含义相同。

PostgreSQL实际上是通过序列（sequence）实现的。

PostgreSQL数据库与Oracle一样有序列，而MySQL中没有序列。

```sql
CREATE TABLE t (
id SERIAL
);

上面的语句等价于声明下面几个语句：

CREATE SEQUENCE t_id_seq;
CREATE TABLE t (
id integer NOT NULL DEFAULT nextval('t_id_seq')
);
ALTER SEQUENCE t_id_seq OWNED BY t.id;
```

**货币类型**

货币类型可以存储固定小数的货币数目，与浮点数不同，它是完全保证精度的。

其输出格式与参数lc_monetary的设置有关，不同的国家其货币输出格式也不相同

```sql
SELECT '12.34'::money;
money
---------
￥12.34
(1 row)

show lc_monetary;
lc_monetary
-------------
zh_CN.UTF-8
(1 row)

set lc_monetary = 'en_US.UTF-8';
SET
SELECT '12.34'::money;
money
--------
$12.34
(1 row)
```

money类型占用8字节空间来存储数据，表示的范围为-92233720368547758.08到+92233720368547758.07。

**PostgreSQL支持丰富的数学操作符**

**字符串类型**

字符串类型是最常用的几种数据类型之一，分为变长和定长等类型。

虽然在某些其他的数据库系统里，定长的character(n)有一定的性能优势，但在PostgreSQL中，定长的character(n)与varchar(n)没有差别。

故在大多数情况下，建议使用text或varchar。

**二进制数据类型**

二进制类型是可以存放任意数据的一种类型

PostgreSQL中只有一种二进制类型：bytea类型。

此数据类型允许存储二进制字符串，对应MySQL和Oracle中的blob类型。Oracle中的raw类型也可以用该类型取代。

二进制字符串是一个字节序列。

二进制字符串和普通字符串的区别有两个：

第一，二进制字符串完全可以存储字节零值，以及其他“不可打印”的字节（定义在32到126范围之外的字节）。
普通字符串不允许字节零值，而且也不允许存储那些不符合选定的字符集编码的非法字节值或者字节序列。

第二，对二进制字符串的处理实际上就是对字节的处理，而对字符串的处理，则取决于区域设置。
简单说，二进制字符串适于存储那些程序员认为是“原始字节”的数据，比如图片内容，而字符串则适合存储文本。

**位串类型**

位串类型是可以存放一系列二进制位的类型，相对于二进制类型来说，此类型在做一些位操作更方便、更直观。

位串就是一串由1和0组成的字符串。

PostgreSQL中可以直观地显式操作二进制位。

下面是两种SQL位类型：
- bit(n)。
- bit varying(n)。

其中n是一个正整数。

bit(n)类型的数据必须准确匹配长度n，试图存储短一些或者长一些的数据都是错误的。

bit varying(n)类型的数据是最长为n的变长类型，更长的串会被拒绝。

写一个没有长度的bit等效于bit(1)，没有长度的bit varying表示没有长度限制。

如果明确地把一个位串值转换成bit(n)，那么它的右边将被截断，或者在右边补齐0到刚好为n位，而不会抛出任何错误。

类似地，如果明确地把一个位串数值转换成bit varying(n)，而其超过n位，那么它的右边将被截断。

**日期/时间类型**

日期/时间类型是最常用的几种数据类型之一，除包括不同日期/时间范围和精度的类型外，还包括了时间间隔类型。

为方便起见，PostgreSQL中用了一些特殊的字符串输入值表示特定的意义

日期、时间和inteval类型数值之间可以进行加减乘除运算

PostgreSQL提供了许多用于返回当前日期和时间的函数。下面的函数都
是按照当前事务开始的时间返回结果的：
```sql
CURRENT_DATE。
CURRENT_TIME。
CURRENT_TIMESTAMP。
CURRENT_TIME(precision)。
CURRENT_TIMESTAMP(precision)。
LOCALTIME。
LOCALTIMESTAMP。
LOCALTIME(precision)。
LOCALTIMESTAMP(precision)。
now()。
transaction_timestamp()。
```
其中，CURRENT_TIME和CURRENT_TIMESTAMP返回带时区的值；

LOCALTIME和LOCALTIMESTAMP返回不带时区的值。

CURRENT_TIME、CURRENT_TIMESTAMP、LOCALTIME、LOCALTIMESTAMP可以选择性地给予一个精度参数，该精度会导致结果的秒数域被四舍五入到指定的小数位。如果没有精度参数，将给予所能得到的全部精度。
这些函数全部是按照当前事务开始的时间返回结果的，所以它们的值在整个事务运行期间都不会改变。PostgreSQL这样做是为了允许一个事务在“当前时间”上有连贯的概念，这样同一个事务里的多个修改就可以保持同样的时间戳了。

PostgreSQL同样也提供了返回实时时间值的函数，它们的返回值会在事务中随时间的推移而不断变化。

这些函数列表如下：
- statement_timestamp()
- clock_timestamp()
- timeofday()

now()函数、CURRENT_TIMESTAMP函数和transaction_timestamp()函数是等效的。

不过，transaction_timestamp()的命名更准确地表明了其含义。

statement_timestamp()返回当前语句开始时刻的时间戳。

statement_timestamp()和transaction_timestamp()在一个事务的第一条命令里的返回值相同，但是在随后的命令中返回结果却不一定相同。

clock_timestamp()返回实时时钟的当前时间戳，因此它的值甚至在同一条SQL命令中都会变化。

timeofday()相当于clock_timestamp()，也返回实时时钟的当前时间戳，但它返回的是一个text字符串，而不是timestamp with time zone值。

所有日期/时间类型还接受特殊的文本值“now”，用于声明当前的日期和时间（重申：乃当前事务开始的时间）。

因此，下面3个语句都返回相同的结果：
- `SELECT CURRENT_TIMESTAMP`
- `SELECT now()`
- `SELECT TIMESTAMP with time zone 'now'`

**枚举类型**

枚举类型是包含一系列有序的静态值集合的一个数据类型，等于某些编程语言中的enum类型。

枚举类型的定义
`select * from pg_enum;`

**几何类型**

PostgreSQL数据库提供了点、线、矩形、多边形等几何类型，其他数据库大都没有这些类型。

**网络地址类型**

PostgreSQL为IPv4、IPv6以及以太网MAC地址都提供了特有的类型，使用这些类型存储IP地址、MAC地址相对于用字符串存储这些类型来说，不容易产生歧义，同时提供了相应的函数让IP地址的运算更方便。

**复合类型**

PostgreSQL中可以如C语言中的结构体一样定义一个复合类型。

**xml类型**
xml类型可用于存储XML数据。

使用字符串类型（如text类型）也可以存储XML数据，但text类型不能保证其中存储的是合法的XML数据，通常需要由应用程序来负责保证所输入数据的正确性，这会增加应用程序的开发难度，而使用xml类型就不存在此类问题，数据库会对输入的数据进行检查，使不符合XML标准的数据不能存放到数据库中，同时还提供了函数对其类型进行安全性检查。

注意，要使用xml数据类型，在编译PostgreSQL源码时必须使用以下参数：
`configure --with-libxml`

xml中存储的XML数据有以下两种：
- 由XML标准定义的documents。
- 由XML标准定义的content片段。

content片段可以有多个顶级元素或character节点。但documents只能有一个顶级元素。

可以使用“xmlvalue IS DOCUMENT”来判断一个特定的XML值是一个documents还是content片段。

PostgreSQL的xmloptions参数用来指定输入的数据是documents还是content片段，默认情况下此值为content片段，所以输入的xml可以有多个顶级元素，但如果我们把此参数设置成“document”，将不能输入有多个顶级元素的内容。

```sql
show xmloption;
xmloption
-----------
content
(1 row)

select xml '<a>a</a><b>b</b>';
xml
------------------
<a>a</a><b>b</b>
(1 row)

SET xmloption TO document;
SET

select xml '<a>a</a><b>b</b>';
ERROR: invalid XML document
LINE 1: select xml '<a>a</a><b>b</b>';
^
DETAIL: line 1: Extra content at the end of the document
<a>a</a><b>b</b>
```

也可以通过函数xmlparse由字符串数据来产生xml类型的数据，使用xmlparse函数是SQL标准中将字串转换成xml的唯一方法。

函数xmlparse的语法如下：
`XMLPARSE ( { DOCUMENT | CONTENT } value)`

```sql
SELECT xmlparse (document '<?xml version="1.0"?
><person><name>John</name><sex>F</sex></person>');
xmlparse
------------------------------------------------
<person><name>John</name><sex>F</sex></person>
(1 row)

SELECT xmlparse (content '<person><name>John</
name><sex>F</sex></person>');
xmlparse
------------------------------------------------
<person><name>John</name><sex>F</sex></person>
(1 row)
```

**字符集的问题**

PostgreSQL数据库在客户端与服务器之间传递数据时，会自动进行字符集的转换。

如果客户端的字符集与服务端不一致，PostgreSQL会自动进行字符集转换。但也正是因为这一特性，用户在传递XML数据时需要格外注意。

我们知道，对于XML文件来说，可以通过类似“encoding="XXX"”的方式指定自己文件的字符集，但当这些数据在PostgreSQL之间传递时，PostgreSQL会把其原始内容的字符集变成数据库服务端的字符集，这会导致一系列问题，因为这意味着XML数据中的字符集编码声明在客户端和服务器之间传递时，可能变得无效。

为了应对该问题，提交输入到xml类型的字符串中的编码声明将会被忽略，同时内容的字符集会被认为是当前数据库服务器的字符集。

正确处理XML字符集的方式是，将XML数据的字符串在当前客户端中编码成当前客户端的字符集，在发送到服务端后，再转换成服务端的字符集进行存储。

当查询xml类型的值时，此数据又会被转换成客户端的字符集，所以客户端收到的XML数据的字符集就是客户端的字符集。

所以通常来说，如果XML数据的字符集编码、客户端字符集编码以及服务器字符集编码完全一致，那么用PostgreSQL来处理XML数据将会大大减少字符集问题，并且处理效率也会很高。

通常XML数据都是以UTF-8编码格式进行处理的，因此把PostgreSQL数据库服务器端编码也设置成UTF-8将是一种不错的选择。

**JSON类型**

JSON数据类型可以用来存储JSON（JavaScript Object Notation）数据，而JSON数据格式是在RFC 4627中定义的。

当然也可以使用text、varchar等类型来存储JSON数据，但使用这些通用的字符串格式将无法自动检测字符串是否为合法的JSON数据。而且，JSON数据类型还可以使用丰富的函数。

JSON数据类型是从PostgreSQL 9.3版本开始提供的，在9.3版本中只有一种类型JSON，在PostgreSQL 9.4版本中又提供了一种更高效的类型JSONB，这两种类型在使用上几乎完全一致，两者主要区别是，JSON类型是把输入的数据原封不动地存放到数据库中（当然在存储前会做JSON的语法检查），使用时需要重新解析数据，而JSONB类型是在存储时就把JSON解析成二进制格式，使用时就无须再次解析，所以JSONB在使用时性能会更高。

另外，JSONB支持在其上建索引，而JSON则不支持，这是JSONB类型一个很大的优点。

因为JSON类型是把输入的整个字符串原封不改动地保存到数据库中，因此JSON串中key之间多余的空格也会被保留下来。

而且，如果JSON串中有重复的key，这些重复的key也会保留下来（默认处理时以最后一个为准），同时也会保留输入时JSON串中各个key的顺序。

而JSONB类型则恰恰相反，既不会保留多余的空格，也不会保留key的顺序和重复的key。

PostgreSQL中每个数据库只允许用一种服务器编码，如果数据库的编码不是UTF-8，PostgreSQL中的JSON类型是无法严格遵循JSON规范中对字符集的要求的。

如果输入中包含无法在服务器编码中表示的字符数据，将无法导入数据库中。

但是，能在服务器编码中表示的非UTF-8字符则是被允许的。

可以使用\uXXXX形式的转义，从而忽视数据库的字符集编码。

当把一个JSON类型的字符串转换成JSONB类型时，JSON字符串内的数据类型实际上被转换成了PostgreSQL数据库中的类型，需要注意的是，如果是在JSONB中，不能输入超出PostgreSQL的numeric数据类型范围的值。

JSON类型的索引

因为JSON类型没有提供相关的比较函数，所以无法在JSON类型的列上直接建索引，但可以在JSON类型的列上建函数索引。

JSONB类型的列上可以直接建索引。除了可以建BTree索引以外，JSONB还支持建GIN索引。

GIN索引可以高效地从JSONB内部的key/value对中搜索数据。

通常情况下，在JSONB类型上都会考虑建GIN索引，而不是BTree索引，因为该索引的效率可能不高，原因是BTree索引不关心JSONB内部的数据结构，只是简单地按照比较整个JSONB大小的方式进行索引。

分析表：
`ANALYZE jtest01;`

查询没有走索引的执行计划
`EXPLAIN ANALYZE VERBOSE SELECT * FROM jtest01 WHERE jdoc->>'name'='lnBtcJLR85';`

查看索引大小
`select pg_indexes_size('jtest02');`

**Range类型**

PostgreSQL数据库中特有的数据类型：Range类型，此类型可以进行范围快速搜索，因此在一些场景中非常有用。

Range类型是从PostgreSQL 9.2版本开始提供的一种特有的类型，用于表示范围，如一个整数的范围、一个时间的范围，而范围底下的基本类型（如整数、时间）被称为Range类型的subtype。

**数组类型**

PostgreSQL支持表的字段使用定长或可变长度的一维或多维数组，数组的类型可以是任何数据库内建的类型、用户自定义的类型、枚举类型及组合类型。但目前还不支持domain类型。

默认情况下，PostgreSQL数据库中数组的下标是从1开始的，但也可以指定下标的开始值。

指定数组上下标的格式如下：
`'［下标：上标］=[元素值1，元素值2，元素值3，....]'`


**伪类型**

伪类型（Pseudo-Types）是PostgreSQL中不能作为字段的一种数据类型，但是它可以用于声明函数的参数或者结果类型。

所包含的类型有以下几种。
```sql
any：用于指示函数的输入参数可以是任意数据类型。
anyelement：表示一个函数接受任何数据类型。
anyarray：表示一个函数接受任何数组类型。
anynonarray：表示一个函数接受任何非数组类型。
anyenum：表示一个函数接受任何枚举类型。
anyrange：表示一个函数接受任何范围类型。
cstring：表示一个函数接受或返回一个以空字符（\0）结尾的C语言字符串。
internal：表示一个函数接受或者返回一种服务器内部的数据类型。
language_handler：声明一个函数的返回值类型是PL语言的handler函数。
fdw_handler：声明一个函数的返回值类型是FOREIGN-DATA WRAPPER的handler函数。
record：标识一个函数返回一个未详细定义各列的row类型。
trigger：一个触发器函数要声明为返回trigger类型。
void：表示一个函数没有返回值。
opaque：已经过时的类型，早期的PostgreSQL版本中用于上面这些用途。
```

**UUID类型**

UUID（Universally Unique Identifier）用于存储一个UUID。

UUID定义在RFC 4122和ISO/IEC 9834-8:2005中。它是一个128bit的数字。

虽然PostgreSQL核心源代码中没有提供产生UUID的函数，但contrib下的uuid-ossp模块提供了产生UUID的函数。

具体可参见官方手册：

http://www.postgresql.org/docs/9.4/static/uuid-ossp.html。

**pg_lsn类型**

pg_lsn类型是PostgreSQL9.4以上版本提供的一种表示LSN（Log Sequence Number）的数据类型。

LSN表示WAL日志的位置。

一些记录WAL日志信息的系统表中某些字段的类型就是pg_lsn类型

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

