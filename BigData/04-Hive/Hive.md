# Hive

## Apache Hive 概述

**Apache Hive是大数据数仓管理工具**

> 数仓的最基本功能:
>
> 存储数据
>
> 数据的写入
>
> 数据的读取
>
> 数据的计算
>
> 
>
> **Hive不能存储数据**(包括读写),其存储功能依托于HDFS
>
> **Hive也不能计算数据**, 其计算功能依托于MapReduce+Yarn
>
> 
>
> **Hive可以将结构化的数据映射为表**
>
> **Hive可以将简单的SQL语句规划为结构逻辑复杂的MapReduce计算任务**
>
> 
>
> Hive极大的降低了大数据开发的准入门槛,提高了大数据开发的工作效率

```
1, 小明, 13
2, 小芳, 15
```

| id   | name | age  |
| ---- | ---- | ---- |
| 1    | 小明 | 13   |
| 2    | 小芳 | 15   |

### 什么是元数据

> 描述数据的数据
>
> 举例: 
>
> 在mysql中存储一个学员的信息,  1, 小明, 男,18   如果查询数据我们想获取的就是这一串内容
>
> 但是,我们怎样查询和存储这个数据呢? 要知道如下内容
>
> - 该数据在哪个数据库中
> - 在哪个表中
> - 每个字段的含义
> - 每个字段的数据类型
> - 这条记录的创建修改时间
> - ........
>
> 上方的举例中1, 小明, 男,18是数据
>
> 其余均为元数据
>
> 
>
> **可以理解为,元数据通过各种表述,创建了一张空表. 数据可以将表的内容填写完整.**

> 元数据管理 : 建议在关系型数据库中管理元数据,因为存取效率高,且可以随意删改,企业中大多使用mysql
>
> sql解析器 : 将sql语句转换为mr任务,并进行优化,提交,运行以及结果输出.

### Hive基础架构

![image-20230822181731850](../image/Hive/image-20230822181731850.png)

> - 三种客户端模式:
>   - 命令行模式  hive
>   - 第三方服务(要遵循jdbc协议) : java database connection  datagrip  beeline
>   - web页面: 几乎不用
>
> - metastore元数据服务
>   - 帮助我们查询元数据
>
> - HiveDriver hive驱动  
>   - 解析sql语句
>   - 制定执行任务
>   - 优化执行方案
>   - 提交mr任务
>
> - 执行计算任务
>   - 向yarn申请资源
>   - 从hdfs中提取数据
>
> 注意: hivedriver支持多种计算引擎, 例如  MapReduce  Tez  Spark

### 远程模式

> 1、元数据使用外置的RDBMS，常见使用最多的是MySQL。
>
> 2、metastore服务单独配置  单独手动启动  全局唯一。
>
> 这样的话各个客户端只能通过这一个metastore服务访问Hive.企业生产环境中使用的模式，支持多客户端远程并发操作访问Hive.
>

![image-20230822192205224](../image/Hive/image-20230822192205224.png)

### Hive客户端

**metastore和hiveserver2的启动**

```shell
# 前台启动
# 前台启动 (当终端任务结束后,立即结束服务)
# 前台启动,可以比较方便的查看日志信息
hive --service metastore
hive --service hiveserver2

# 后台启动
# 后台启动 (不影响中端使用,在那个目录下启动该服务,就会在该目录下,生成一个nohup.out文件,记录日志信息.)
nohup hive --service metastore &
nohup hive --service hiveserver2 & 
```

**第一代客户端: hive**

第一代客户端直接使用hive指令连接,链接的就是metastore服务,并且只要连接成功就可以操作hive数据库

```shell
# 使用第一代客户端
hive
```

> 第一代客户端存在的问题:
>
> 1. metastore既负责元数据的管理,又负责客户端的连接保持(峰值性能不强)
> 2. 没有身份识别,只要能够连接metastore就可以操作hive安全性不强
> 3. 无法第三方服务协议进行连接(JDBC)

**第二代客户端: beeline**

> 创建了一个单独的hiveserver2服务
>
> 1. 专门处理客户端连接请求,以及保持客户端连接,
> 2. 并且hiveserver2会对于客户端身份进行校验,如果不满足将无法使用hive服务,
> 3. 使用jdfc协议连接,可以无缝连接第三方服务

```shell
# 第二代客户端启动
beeline

# 第二代客户端连接
!connect jdbc:hive2://node1:10000
# 输入账号: root 密码 :
```

**注意:** hiveserver2服务启动后1-2分钟后才可用

如果不放心可以使用`lsof -i:10000` 去查询,如果有进程使用了该端口号则hiveserver2服务就可用了

metastore占用的端口号是: 9083

### 数仓的分层架构

> ODS层(operation data Store): 数据操作层,源数据层,主要就是将各个数据源中的数据集中采集到指定平台中,几乎不对数据做任何处理,只是临时存放等待后续处理
>
> 
>
> DW层(Data Warehouse) : 数据仓库层, 对于数据进行数据清洗,数据处理,数据转换等,使其满足数据分析的要求
>
> 
>
> DA层APP层(application) : 数据应用层, 根据业务部门的需求,进行数据分析处理,获得的数据可以直接用于前端展示或数据报表等.

每一个公司的数仓分层策略都不同,只需要记住三层架构即可,在公司中,通常会对于dw层和da层进行细化.

**分层好处**

- 解耦合(相互依赖),方便协同开发
- 可以追根溯源,更快对于问题进行解决和修复
- 过程中创建的中间表可以重复利用
- 空间换时间 提高最终应用层使用数据的效率

![image-20230822194820056](../image/Hive/image-20230822194820056.png)

## 数据库操作

```sql
-- 1. 创建一个数据库
-- 注意: 在hive中 database 和 schema 是完全相同的
create database test_db;
create schema test_db2;

-- 创建数据库时,如果数据库已存在将会报错,我们可以使用if not exists进行异常规避
create database if not exists test_db;

-- 2. 使用数据库
-- hive中没有使用数据库依旧可以创建成功默认使用default数据库
create table student(
    id int,
    name varchar(255)
);

-- 使用数据库后则可以将表创建到指定数据库中
use test_db;

create table student(
    id int,
    name varchar(255)
);

-- 3. 查看当前使用的数据库
select current_database();

-- 4. 查看数据库详细信息
desc database test_db;

-- 5. 创建数据库时指定数据存放位置
create database if not exists test_db3 location 'hdfs://node1:8020/tmp/db_1';

create table test_db3.student(
    id int,
    name varchar(255)
);

-- 6. 创建数据库时指定注释信息
create database if not exists test_db4 comment 'this db is a test';

desc database test_db4;

-- 7. 删除数据库
-- 空数据库可以直接删除
drop database test_db4;

-- Database test_db3 is not empty. One or more tables exist.
-- 空数据库可以直接删除,但是如果内部创建了表,则不能随意删除
-- drop database test_db3;

-- 如果需要强行删除非空数据库,需要使用cascade 进行修饰
drop database test_db3 cascade;
```

### 表操作语法和数据类型

![image-20230822195107969](../image/Hive/image-20230822195107969.png)

- 内部表和外部表(分隔符+存放位置)
- 分区
- 分桶
- 序列化

#### 数据类型

> hive中不仅支持sql中的数据类型,还支持 java中的部分数据类型

在hive中支持的数据类型比较多,我们常用的数据类型如下:

> 整型: int
>
> 浮点型: double
>
> 字符型: string
>
> 时间型: date timestamp
>
> 复杂数据类型: map array

**注意: 在mysql中 没有string类型数据,而hive中有, 在mysql中有datetime类型而hive中不存在.在mysql中不支持复杂数据类型,hive中支持**

### 内部表操作

使用`desc formatted 表名;`查看后table type是manged_table则该表为内部表;

```shell
-- 1. 创建一个数据表
create table test_db.student1
(
    id   int comment 'stu_id',
    name string comment 'stu_name'
) comment 'student_table';

-- 2. 查询表的信息
desc test_db.student1;

-- 3. 查询表的元数据信息
desc formatted test_db.student1;

-- 4. 查看数据库中所有的表名
show tables;
show tables in test_db;

-- 5. 向表中插入数据信息
insert into test_db.student1
values (1, '小红'),
       (2, '小明'),
       (3, '小刚');

-- 6. 查询表中的数据
select *
from test_db.student1;

-- 我们的hive不会使用常见字符作为默认分隔符,使用的是`\001`

select '1\001小明';

```

在数据表中插入数据,系统会在字段之间添加一个默认的分隔符,帮助系统判别不同的字段,但是这个字符不能是常见的字符,

> `\001`是一个不可见字符,代表标题开始.编程软件和编程语言很容易就可以输出,但是任何一款文本编辑器都不能轻易输出该字符,所以在使用过程中,不会和数据库中的数据产生歧义

### 自定义字段分隔符的方式

```sql
-- 创建一个内部表, 并且指定分隔符为 \t
create table test_db.student2(
    id int,
    name string
)
row format delimited -- 默认的序列化格式
fields terminated by '\t'; -- 使用 \t作为字段间的分隔符

-- 上传数据后,查询数据内容
select * from student2;
```

### 创建内部表的另外两种方式

```shell
-- 1. create table as ...   将查询结果创建一个表保存起来
create table test_db.student_copy as select * from test_db.student2;

-- 查看表结构
desc test_db.student_copy;

-- 查看表中的数据内容
select * from test_db.student_copy;

-- 2. create table ... like ... 创建一个内部表,表的结构复制指定的表
create table test_db.student_copy1 like test_db.student2;

-- 查看表结构
desc test_db.student_copy1;

-- 查看表中的数据内容
-- 查看该表时,发现没有数据,所以like 方法只能复制表结构,不能复制内部的数据内容.所以仅仅copy了他的元数据信息,速度非常快不走mr任务.
select * from test_db.student_copy1;
```

### 删除内部表

```sql
-- 删除内部表
-- 数据表中无论是否有数据都可以直接删除,并且删除时没有询问或提示,所以要慎重.
drop table test_db.student2;

-- 内部表在删除时,会将文件和目录一起删除掉
drop table test_db.student1;

-- 内部表被删除时,会将元数据同时删除
show tables in test_db;

-- 结论: 在删除内部表时,会将元数据和数据同时删除. (所有的数据完全受到hive控制)
```

### 修改表操作

```sql
-- 创建一个内部表
create table test_db.person
(
    id   int,
    age  int,
    name string
)
    row format delimited
        fields terminated by '\t';

-- 查看表的详细信息
desc formatted test_db.person;

-- 1. 增加一个字段
alter table test_db.person
    add columns (gender string, height double);

desc formatted test_db.person;

-- 2. 修改字段
alter table test_db.person
    change gender sex string;

desc formatted test_db.person;

-- 删除字段
-- alter table test_db.person drop sex;
-- hive中不支持删除字段的方法, 如果字段多余,可以重建表

-- 3. 修改表名
-- 自定义位置的表名不会修改
alter table test_db.person
    rename to test_db.person_tb;

show tables in test_db;

-- 4. 修改表数据的存储位置(不常用)
-- 在person_db目录中添加一行数据
insert into test_db.person_tb
values (1, 12, '小明', '男', 1.75);
select *
from test_db.person_tb;
-- 修改表绑定的数据位置
alter table test_db.person_tb
    set location '/tmp/person_test';

desc formatted test_db.person_tb;

select *
from test_db.person_tb;
-- 修改表绑定的数据位置后,原有数据不会移动,但是数据查找的位置发生了改变

-- 在新位置插入一条数据
insert into test_db.person_tb
values (2, 16, '小芳', '女', 1.96);

-- 自定义路径下的表,修改表名后,目录名称不会发生改变
alter table test_db.person_tb
    rename to test_db.person_tb1;

-- 5. 修改表的属性
-- 如果修改的属性为EXTERNAL就会修改表的类型  值为true就修改为外部表  值为false就修改为内部表
-- 强调: EXTERNAL必须为大写, 而flase和true可以是大写或小写
-- 如果属性名存在就是修改属性名
alter table test_db.person_tb1 set tblproperties ('EXTERNAL'='false');
-- 如果属性名不存在,就是添加新的属性键值对
alter table test_db.person_tb1 set tblproperties ('chuanzhi'='bangbangda');

desc formatted test_db.person_tb1;

-- 6. 清空表数据
-- Attempt to do update or delete using transaction manager that does not support these operations.
-- hive数据仓库中,不支持更新和删除操作
-- delete from test_db.person_tb1;

-- Cannot truncate non-managed table test_db.person_tb1.
-- 外部表不支持清空操作, 内部表可以清空
truncate table test_db.person_tb1;

-- 7. 表的删除操作
drop table test_db.person_tb1;
```

### 外部表操作

```shell
-- 1. 创建一个外部表
-- 在创建表时,使用external进行修饰,就是外部表
drop table test_db.ext_person;
create external table test_db.ext_person (
    id int,
    name string
)
row format delimited
fields terminated by '\t';

-- 2. 查询外部表的详细信息
desc formatted test_db.ext_person;

-- 3. 向外部表中插入数据
insert into test_db.ext_person values (1, '小明'), (2, '小红');

-- 4. 从外部表中查询数据内容
select * from test_db.ext_person;

-- 5. 清空外部表中的数据
-- Cannot truncate non-managed table test_db.ext_person.
-- 外部表中的数据不可以被清空
-- truncate table test_db.ext_person;

-- 6. 删除外部表
drop table test_db.ext_person;

-- 数据表,在元数据中已经被删除,无法查询和使用
select * from ext_person;  -- Table not found 'ext_person'
show tables in test_db;

-- 经查询,hdfs中该表所涉及的目录和数据文件依然存在,没有被删除

-- 结论: 删除外部表时,只能删除元数据, 不能删除其数据内容 (该表只有元数据收到hive控制,数据内容不受hive控制)
```

**使用外部表的场景:**

> 1. 数据极其重要或不易获得,在使用过程中为了防止误删,必须使用外部表
> 2. 数据集被多个表引用,或被多个用户引用,此时如果删除会影响其他人操作,我们就使用外部表
>
> 有一些表会做成外部表, 公司组织架构表, 日期表....

### 数据加载

```sql
-- 数据准备
CREATE TABLE test_db.test_load
(
    dt      string comment '时间（时分秒）',
    user_id string comment '用户ID',
    word    string comment '搜索词',
    url     string comment '用户访问网址'
) comment '搜索引擎日志表' ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';

-- 1. 从linux中,将数据上传到hdfs的指定目录下,映射数据
-- 步骤1: 将数据文件先存放在 /root/hive_data目录中
-- 步骤2: 使用shell指令将数据文件上传至 hdfs目录中  /user/hive/warehouse/test_db.db/test_load
hadoop fs -put /root/hive_data/search_log.txt /user/hive/warehouse/test_db.db/test_load
-- 步骤3: 查看表中是否映射成功该文件内容
select * from test_db.test_load;

-- 注意 使用该方法映射文件要注意,分隔符的指定一定要正确,否则无法映射成功

-- 2. load data sql语句,可以直接从本地文件中加载数据文件
-- 步骤1: 复制一个test_load表结构
create table test_db.test_load1 like test_db.test_load;
-- 步骤2: 使用load data语法将数据从本地加载到表中
-- 加载格式: load data local inpath '文件路径' into table 表名;
load data local inpath '/root/hive_data/search_log.txt' into table test_db.test_load1;
-- 步骤3: 查看表中的数据是否映射成功
select * from test_db.test_load1;

-- 结论: load data 的操作方式其实和put基本一致, 上传文件后数据原文件依然存在

-- 3. load data sql语句, 可以从hdfs上将数据加载到表目录中
-- 步骤1: 复制一个test_load表结构
create table test_db.test_load2 like test_db.test_load;
-- 步骤2: 将数据上传到/tmp/test_data/
-- 步骤3: 将hdfs上的/tmp/test_data/search_log.txt 文件加载到表中
-- 加载格式: load data inpath 'hdfs中的文件路径' into table 表名;
load data inpath 'hdfs://node1:8020/tmp/test_data/search_log.txt' into table test_db.test_load2;
-- 步骤4: 查看数据是否映射成功
select * from test_db.test_load2;

-- 结论: load data 的操作方式,如果将hdfs上的数据进行上传,则为移动该文件到表目录中, 原位置文件消失.

-- 4. load data ... overwrite ... 覆盖加载数据
-- 可以对同一个表加载多次数据,数据会累加显示在虚拟表中.但是我们有时为了防止其重复加载,会使用overwrite进行覆盖加载
-- 刚才对于test_load1进行了多次加载,此时我们要使用覆盖加载,将原有数据清空,插入新数据
load data local inpath '/root/hive_data/search_log.txt' overwrite into table test_db.test_load1;

-- 查看文件是否加载成功, 此时之前的数据被清空,仅保留本次插入的数据内容.
select * from test_db.test_load1;
```



**数据加载的其他方式**

```sql
-- 1. insert into values
-- 步骤1: 复制test_load的表结构
create table test_db.test_load3 like test_db.test_load;
-- 步骤2: 使用 insert into values 加载2条数据
insert into test_db.test_load3
values ('00:00:01', '1233215666', '传智播客', 'http://www.itcast.cn'),
       ('00:50:00', '2233216666', '黑马程序员', 'http://www.itcast.cn');
-- 步骤3: 查看数据内容是否加载完成  建表时指定的分隔符是什么,此时加载数据就使用什么分隔符
select *
from test_db.test_load3;

-- 结论: 该操作方式,加载数据过于缓慢,我们一般不用

-- 2. insert into ... select
-- 将我们查询或清洗后的数据结果加载到目标表中
-- 步骤1: 复制test_load的表结构
create table test_db.test_load4 like test_db.test_load;
-- 步骤2: 将test_load中的数据文件全部读取出来,并且存放到test_load4当中
insert into test_db.test_load4
select *
from test_db.test_load
where user_id > '1233333333';
-- 步骤3: 查询表中的数据是否加载成功
select *
from test_db.test_load4;

-- 结论: 这种方式用的比较多,因为我们不希望在插入数据这种机械操作中浪费时间,但是我们清洗后将结果插入浪费的时间有价值.

-- 3. insert overwrite ... select
-- 将我们查询或者清洗后的数据结果覆盖加载到目标表中
-- 步骤1: 复将test_load中的数据读取出来,并且筛选user_id < '1233333333' 覆盖加载到test_load4表中
insert overwrite table test_db.test_load4
select *
from test_db.test_load
where user_id < '1233333333';

-- 步骤2: 查询加载后的文件是否准确覆盖
select * from test_db.test_load4;

-- 结论: 覆盖方法其实使用的也不是特别多,因为在开发中我们需要保留历史数据.
```

### 数据导出

```sql
-- 1. insert overwrite local directory  '' select * from....
-- 将查询到的数据结果,保存到指定的本地目录中
-- 有local 就是保存或者加载本地数据
-- 步骤1: 将test_load中的数据导出到node1 的/root/aaa 目录中
-- 指定的导出位置只能是目录,不能是文件
insert overwrite local directory '/root/aaa'
select *
from test_db.test_load;
-- 步骤2: 观察导出文件分隔符为\001  导出文件后,目标目录被覆盖,所有文件被清除,仅保留导出结果使用默认文件名.
-- 步骤3: 导出文件时增加分隔符
insert overwrite local directory '/root/aaa' row format delimited fields terminated by '\t'
select *
from test_db.test_load;

-- 2. insert overwrite  directory  '' select * from....
-- 将查询到的数据结果,保存到hdfs上的目录中
insert overwrite directory '/tmp/small' select *from test_db.test_load;

-- 3. 导出文件没有追加方法
-- insert into  directory '/tmp/small' select *from test_db.test_load;

-- 注意: 使用insert overwrite directory 进行数据导出,一定要注意谨慎操作,因为会将该目录中原有的数据全部清空,且无法恢复.
```

> 注意: 
>
> 1. 我们导出数据时导出到指定的目录下,指定文件名称无用,最终会形成一个默认名称的文件
>
> 2. 导出数据只能覆盖,不能追加

### 使用hive的shell指令运行hsql

> 思考: hive中的shell客户端有几个???
>
> hive : 在hive中只有第一代客户端是shell客户端
>
> beeline : 第二代客户端是使用jdbc协议远程连接的,不是shell客户端.

```sql
-- 方式1: 使用终端运行sql语句
hive -e 'select * from db_test.test_load;'
-- 方式2: 使用脚本文件运行sql语句
-- 先创建一个hive.hql文件,在文件内部书写`select * from hive_day02.test_load;`
hive -f hive.hql
```

> -e 就是执行命令
>
> -f  就是执行脚本文件

### 分区表

**单分区表**

```sql
-- 1. 创建分区表
/*
分区表格式
create table 表名(
字段1 字段类型,
字段2 字段类型....
)
partitioned by (分区字段 字段类型)
 */
-- Column repeated in partitioning columns
-- 分区表中,分区字段也会出现在虚拟表中, 此时该字段的字段名称,不能与其他实体字段冲突
create table test_db.score_part(
    s_name string,
    s_course string,
    score int
)
partitioned by (month string)
row format delimited
fields terminated by '\t';

-- 2. 查看表结构
desc formatted test_db.score_part;

-- 3. 给分区表加载数据
load data local inpath '/root/hive_data/score.txt' into table test_db.score_part;

-- 4. 查看表中加载的数据内容
-- 经过观察发现,如果不书写分区名称,则加载到默认分区中
select * from test_db.score_part;

-- 5. 使用put将数据上传至 表目录中.
hadoop fs -put /root/hive_data/score.txt /user/hive/warehouse/test_db.db/score_part
select * from test_db.score_part;
-- 经过查询,我们发现没有映射出新的数据,在表目录中添加数据文件,不能映射成功

-- 6. 指定分区名称加载数据内容
-- 我们在加载数据时指定分区名称,则在该表目录下会创建一个分区目录,将数据加载进去
load data local inpath '/root/hive_data/score.txt' into table test_db.score_part partition (month='2022-12');
load data local inpath '/root/hive_data/score.txt' into table test_db.score_part partition (month='2022-10');

-- 7. 查看分区内数据是否可以映射成功
-- 经观察发现,只有写入到分区目录中的文件才会被映射成功,否则映射失败.
select * from test_db.score_part;
```

**多级分区表**

```sql
-- 1. 创建一个多级分区表
-- 多级分区表其实就是指定多个分区字段
create table test_db.score_parts
(
    s_name   string,
    s_course string,
    score    int
)
    partitioned by (year string, month string, day string)
    row format delimited fields terminated by '\t';

-- 2. 给多级分区表加载数据
-- 和普通分区表相比,多级分区表就是在普通分区表基础上增加几个键=值即可
load data local inpath '/root/hive_data/score.txt' into table test_db.score_parts partition (year = '2022', month = '03', day = '05');
load data local inpath '/root/hive_data/score.txt' into table test_db.score_parts partition (year = '2022', month = '06', day = '05');
load data local inpath '/root/hive_data/score.txt' into table test_db.score_parts partition (year = '2022', month = '06', day = '15');

-- 3. 查询使用该方法插入的数据打印结果是什么
select *
from test_db.score_parts;

-- 4. 在表目录下,添加一个分区格式目录,并插入数据后,数据映射并不会成功,因为没有源数 存储,没有办法进行该操作
hadoop fs -mkdir /user/hive/warehouse/test_db.db/score_parts/year=2022/month=01/day=01
hadoop fs -put /root/hive_data/score.txt /user/hive/warehouse/test_db.db/score_parts/year=2022/month=01/day=01

-- 4.1 可以添加分区 (添加分区时,不能遗漏任何一个分区字段)
alter table test_db.score_parts
    add partition (year = '2022', month = '01', day = '01');

-- 增加分区结束后,数据就添加完成了
select *
from test_db.score_parts;

-- 4.2 可以一次性添加多个分区
alter table test_db.score_parts
    add partition (year = '2022', month = '02', day = '16') partition (year = '2022', month = '02', day = '01');

-- 5. 查看分区
show partitions test_db.score_parts;

-- 6. 删除分区
alter table test_db.score_parts
    drop partition (year = '2022', month = '02', day = '16');

-- 7. 修改分区名称
alter table test_db.score_parts
    partition (year = '2022', month = '02', day = '01') rename to partition (year = '2022', month = '04', day = '12');

```

> 多级分区表必须多个层级间有从属关系,例如年月日, 省市区   
>
> 多级分区表的层级一般不超过三级,因为数据层级过多,就会产生大量的小文件.