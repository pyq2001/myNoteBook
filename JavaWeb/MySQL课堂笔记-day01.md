MySQL

1、sql、DB、DBMS分别是什么，他们之间的关系？
	DB: 
		DataBase（数据库，数据库实际上在硬盘上以文件的形式存在）

	DBMS: 
		DataBase Management System（数据库管理系统，常见的有：MySQL Oracle DB2 Sybase SqlServer...）
	
	SQL: 
		结构化查询语言，是一门标准通用的语言。标准的sql适合于所有的数据库产品。
		SQL属于高级语言。只要能看懂英语单词的，写出来的sql语句，可以读懂什么意思。
		SQL语句在执行的时候，实际上内部也会先进行编译，然后再执行sql。（sql语句的编译由DBMS完成。）
	
	DBMS负责执行sql语句，通过执行sql语句来操作DB当中的数据。
	DBMS -(执行)-> SQL -(操作)-> DB

2、什么是表？

```mysql
表：table

表：table是数据库的基本组成单元，所有的数据都以表格的形式组织，目的是可读性强。

一个表包括行和列：
	行：被称为数据/记录(data)
	列：被称为字段(column)

学号(int)	姓名(varchar)	年龄(int)
------------------------------------
110			张三				20
120			李四				21

每一个字段应该包括哪些属性？
	字段名、数据类型、相关的约束。
```

3、学习MySQL主要还是学习通用的SQL语句，那么SQL语句包括增删改查，SQL语句怎么分类呢？
	DQL（数据查询语言）: 查询语句，凡是select语句都是DQL。
	DML（数据操作语言）：insert delete update，对表当中的数据进行增删改。
	DDL（数据定义语言）：create drop alter，对表结构的增删改。
	TCL（事务控制语言）：commit提交事务，rollback回滚事务。(TCL中的T是Transaction)
	DCL（数据控制语言）: grant授权、revoke撤销权限等。

4、导入数据（后期大家练习的时候使用这个演示的数据）

```mysql
第一步：登录mysql数据库管理系统
	dos命令窗口：
		mysql -uroot -p333
		     -u用户名 -p密码
第二步：查看有哪些数据库
	show databases; (这个不是SQL语句，属于MySQL的命令。)
	+--------------------+
	| Database           |
	+--------------------+
	| information_schema |
	| mysql              |
	| performance_schema |
	| test               |
	+--------------------+
第三步：创建属于我们自己的数据库
	create database bjpowernode; (这个不是SQL语句，属于MySQL的命令。)
第四步：使用bjpowernode数据
	use bjpowernode; (这个不是SQL语句，属于MySQL的命令。)
第五步：查看当前使用的数据库中有哪些表？
	show tables; (这个不是SQL语句，属于MySQL的命令。)
第六步：初始化数据
	mysql> source D:\course\xx.sql 

```



```mysql
注意：数据初始化完成之后，有三张表：
+-----------------------+
| Tables_in_bjpowernode |
+-----------------------+
| dept                  |
| emp                   |
| salgrade              |
+-----------------------+
```

5、bjpowernode.sql，这个文件以sql结尾，这样的文件被称为“sql脚本”。什么是sql脚本呢？
	当一个文件的扩展名是.sql，并且该文件中编写了大量的sql语句，我们称这样的文件为sql脚本。
	注意：直接使用source命令可以执行sql脚本。
	sql脚本中的数据量太大的时候，无法打开，请使用source命令完成初始化。


6、删除数据库：drop database bjpowernode;

7、查看表结构：

```mysql
+-----------------------+
| Tables_in_bjpowernode |
+-----------------------+
| dept                  |   (部门表)
| ep                   |   (员工表)
| salgrade              |   (工资等级表)
+-----------------------+
```


```mysql
mysql> desc dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| DEPTNO | int(2)      | NO   | PRI | NULL    |       |		部门编号
| DNAME  | varchar(14) | YES  |     | NULL    |       |		部门名称
| LOC    | varchar(13) | YES  |     | NULL    |       |		部门位置
+--------+-------------+------+-----+---------+-------+

mysql> desc emp;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| EMPNO    | int(4)      | NO   | PRI | NULL    |       |	员工编号
| ENAME    | varchar(10) | YES  |     | NULL    |       |	员工姓名
| JOB      | varchar(9)  | YES  |     | NULL    |       |	工作岗位
| MGR      | int(4)      | YES  |     | NULL    |       |	上级领导编号
| HIREDATE | date        | YES  |     | NULL    |       |	入职日期
| SAL      | double(7,2) | YES  |     | NULL    |       |	月薪
| COMM     | double(7,2) | YES  |     | NULL    |       |	补助/津贴
| DEPTNO   | int(2)      | YES  |     | NULL    |       |	部门编号
+----------+-------------+------+-----+---------+-------+

mysql> desc salgrade;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| GRADE | int(11) | YES  |     | NULL    |       |		等级
| LOSAL | int(11) | YES  |     | NULL    |       |		最低薪资
| HISAL | int(11) | YES  |     | NULL    |       |		最高薪资
+-------+---------+------+-----+---------+-------+
```

8、表中的数据？

```mysql
mysql> select * from emp;
+-------+--------+-----------+------+------------+---------+---------+--------+
| EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL     | COMM    | DEPTNO |
+-------+--------+-----------+------+------------+---------+---------+--------+
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 |  800.00 |    NULL |     20 |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 | 1600.00 |  300.00 |     30 |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 | 1250.00 |  500.00 |     30 |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 | 2975.00 |    NULL |     20 |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 | 1250.00 | 1400.00 |     30 |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 | 2850.00 |    NULL |     30 |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 | 2450.00 |    NULL |     10 |
|  7788 | SCOTT  | ANALYST   | 7566 | 1987-04-19 | 3000.00 |    NULL |     20 |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 | 5000.00 |    NULL |     10 |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 | 1500.00 |    0.00 |     30 |
|  7876 | ADAMS  | CLERK     | 7788 | 1987-05-23 | 1100.00 |    NULL |     20 |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 |  950.00 |    NULL |     30 |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 | 3000.00 |    NULL |     20 |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 | 1300.00 |    NULL |     10 |
+-------+--------+-----------+------+------------+---------+---------+--------+

mysql> select * from dept;
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+

mysql> select * from salgrade;
+-------+-------+-------+
| GRADE | LOSAL | HISAL |
+-------+-------+-------+
|     1 |   700 |  1200 |
|     2 |  1201 |  1400 |
|     3 |  1401 |  2000 |
|     4 |  2001 |  3000 |
|     5 |  3001 |  9999 |
+-------+-------+-------+
```

9、常用命令？

```mysql
mysql> select database(); 查看当前使用的是哪个数据库
+-------------+
| database()  |
+-------------+
| bjpowernode |
+-------------+

mysql> select version(); 查看mysql的版本号。
+-----------+
| version() |
+-----------+
| 5.5.36    |
+-----------+
```

\c   命令，结束一条语句。

exit 命令，退出mysql。

10、查看创建表的语句：
	show create table emp;

11、简单的查询语句（DQL）

```mysql
语法格式：
	select 字段名1,字段名2,字段名3,.... from 表名;

提示：
	1、任何一条sql语句以“;”结尾。
	2、sql语句不区分大小写。

查询员工的年薪？（字段可以参与数学运算。）
	select ename,sal * 12 from emp;
	+--------+----------+
	| ename  | sal * 12 |
	+--------+----------+
	| SMITH  |  9600.00 |
	| ALLEN  | 19200.00 |
	| WARD   | 15000.00 |
	| JONES  | 35700.00 |
	| MARTIN | 15000.00 |
	| BLAKE  | 34200.00 |
	| CLARK  | 29400.00 |
	| SCOTT  | 36000.00 |
	| KING   | 60000.00 |
	| TURNER | 18000.00 |
	| ADAMS  | 13200.00 |
	| JAMES  | 11400.00 |
	| FORD   | 36000.00 |
	| MILLER | 15600.00 |
	+--------+----------+

给查询结果的列重命名？
	select ename,sal * 12 as yearsal from emp;

别名中有中文？
	select ename,sal * 12 as 年薪 from emp; // 错误
	select ename,sal * 12 as '年薪' from emp;
	+--------+----------+
	| ename  | 年薪        |
	+--------+----------+
	| SMITH  |  9600.00 |
	| ALLEN  | 19200.00 |
	| WARD   | 15000.00 |
	| JONES  | 35700.00 |
	| MARTIN | 15000.00 |
	| BLAKE  | 34200.00 |
	| CLARK  | 29400.00 |
	| SCOTT  | 36000.00 |
	| KING   | 60000.00 |
	| TURNER | 18000.00 |
	| ADAMS  | 13200.00 |
	| JAMES  | 11400.00 |
	| FORD   | 36000.00 |
	| MILLER | 15600.00 |
	+--------+----------+

注意：标准sql语句中要求字符串使用单引号括起来。虽然mysql支持双引号，尽量别用。

as关键字可以省略？
	mysql> select empno,ename,sal * 12 yearsal from emp;
	+-------+--------+----------+
	| empno | ename  | yearsal  |
	+-------+--------+----------+
	|  7369 | SMITH  |  9600.00 |
	|  7499 | ALLEN  | 19200.00 |
	|  7521 | WARD   | 15000.00 |
	|  7566 | JONES  | 35700.00 |
	|  7654 | MARTIN | 15000.00 |
	|  7698 | BLAKE  | 34200.00 |
	|  7782 | CLARK  | 29400.00 |
	|  7788 | SCOTT  | 36000.00 |
	|  7839 | KING   | 60000.00 |
	|  7844 | TURNER | 18000.00 |
	|  7876 | ADAMS  | 13200.00 |
	|  7900 | JAMES  | 11400.00 |
	|  7902 | FORD   | 36000.00 |
	|  7934 | MILLER | 15600.00 |
	+-------+--------+----------+

查询所有字段？
	select * from emp; // 实际开发中不建议使用*，效率较低。
```

12、条件查询。
	语法格式：
		select 
			字段,字段...
		from
			表名
		where
			条件;

```mysql
执行顺序：先from，然后where，最后select

查询工资等于5000的员工姓名？
	select ename from emp where sal = 5000;
	+-------+
	| ename |
	+-------+
	| KING  |
	+-------+
查询SMITH的工资？
	select sal from emp where ename = 'SMITH'; // 字符串使用单引号括起来。
	+--------+
	| sal    |
	+--------+
	| 800.00 |
	+--------+
找出工资高于3000的员工？
	select ename,sal from emp where sal > 3000;

	select ename,sal from emp where sal >= 3000;

	select ename,sal from emp where sal < 3000;

	select ename,sal from emp where sal <= 3000;

找出工资不等于3000的？
	select ename,sal from emp where sal <> 3000;
	select ename,sal from emp where sal != 3000;

找出工资在1100和3000之间的员工，包括1100和3000？
	select ename,sal from emp where sal >= 1100 and sal <= 3000;

	select ename,sal from emp where sal between 1100 and 3000; // between...and...是闭区间 [1100 ~ 3000]

	select ename,sal from emp where sal between 3000 and 1100; // 查询不到任何数据

	between and在使用的时候必须左小右大。

	between and除了可以使用在数字方面之外，还可以使用在字符串方面。
	select ename from emp where ename between 'A' and 'C';
	+-------+
	| ename |
	+-------+
	| ALLEN |
	| BLAKE |
	| ADAMS |
	+-------+
	select ename from emp where ename between 'A' and 'D'; // 左闭右开。

找出哪些人津贴为NULL？
	在数据库当中NULL不是一个值，代表什么也没有，为空。
	空不是一个值，不能用等号衡量。
	必须使用 is null或者is not null
	select ename,sal,comm from emp where comm is null;
	+--------+---------+------+
	| ename  | sal     | comm |
	+--------+---------+------+
	| SMITH  |  800.00 | NULL |
	| JONES  | 2975.00 | NULL |
	| BLAKE  | 2850.00 | NULL |
	| CLARK  | 2450.00 | NULL |
	| SCOTT  | 3000.00 | NULL |
	| KING   | 5000.00 | NULL |
	| ADAMS  | 1100.00 | NULL |
	| JAMES  |  950.00 | NULL |
	| FORD   | 3000.00 | NULL |
	| MILLER | 1300.00 | NULL |
	+--------+---------+------+
	select ename,sal,comm from emp where comm = null;
	Empty set (0.00 sec)

找出哪些人津贴不为NULL？
	select ename,sal,comm from emp where comm is not null;
	+--------+---------+---------+
	| ename  | sal     | comm    |
	+--------+---------+---------+
	| ALLEN  | 1600.00 |  300.00 |
	| WARD   | 1250.00 |  500.00 |
	| MARTIN | 1250.00 | 1400.00 |
	| TURNER | 1500.00 |    0.00 |
	+--------+---------+---------+
	
找出哪些人没有津贴？
	select ename,sal,comm from emp where comm is null or comm = 0;
	+--------+---------+------+
	| ename  | sal     | comm |
	+--------+---------+------+
	| SMITH  |  800.00 | NULL |
	| JONES  | 2975.00 | NULL |
	| BLAKE  | 2850.00 | NULL |
	| CLARK  | 2450.00 | NULL |
	| SCOTT  | 3000.00 | NULL |
	| KING   | 5000.00 | NULL |
	| TURNER | 1500.00 | 0.00 |
	| ADAMS  | 1100.00 | NULL |
	| JAMES  |  950.00 | NULL |
	| FORD   | 3000.00 | NULL |
	| MILLER | 1300.00 | NULL |
	+--------+---------+------+
	
找出工作岗位是MANAGER和SALESMAN的员工？
	select ename,job from emp where job = 'MANAGER' or job = 'SALESMAN';
	+--------+----------+
	| ename  | job      |
	+--------+----------+
	| ALLEN  | SALESMAN |
	| WARD   | SALESMAN |
	| JONES  | MANAGER  |
	| MARTIN | SALESMAN |
	| BLAKE  | MANAGER  |
	| CLARK  | MANAGER  |
	| TURNER | SALESMAN |
	+--------+----------+
	
and和or联合起来用：找出薪资大于1000的并且部门编号是20或30部门的员工。
	select ename,sal,deptno from emp where sal > 1000 and deptno = 20 or deptno = 30; // 错误的
	select ename,sal,deptno from emp where sal > 1000 and (deptno = 20 or deptno = 30); // 正确的。
	注意：当运算符的优先级不确定的时候加小括号。
	
in等同于or：找出工作岗位是MANAGER和SALESMAN的员工？
	select ename,job from emp where job = 'SALESMAN' or job = 'MANAGER';
	select ename,job from emp where job in('SALESMAN', 'MANAGER');

	select ename,job from emp where sal in(800, 5000); // in后面的值不是区间，是具体的值。
		+-------+-----------+
		| ename | job       |
		+-------+-----------+
		| SMITH | CLERK     |
		| KING  | PRESIDENT |
		+-------+-----------+
	
not in: 不在这几个值当中。
	select ename,job from emp where sal not in(800, 5000);
	
模糊查询like ? 
	找出名字当中含有O的？
		（在模糊查询当中，必须掌握两个特殊的符号，一个是%，一个是_）
		%代表任意多个字符，_代表任意1个字符。
		select ename from emp where ename like '%O%';
			+-------+
			| ename |
			+-------+
			| JONES |
			| SCOTT |
			| FORD  |
			+-------+
	找出名字中第二个字母是A的？
		select ename from emp where ename like '_A%';
			+--------+
			| ename  |
			+--------+
			| WARD   |
			| MARTIN |
			| JAMES  |
			+--------+
	找出名字中有下划线的？
		mysql> select * from t_user;
			+------+----------+
			| id   | name     |
			+------+----------+
			|    1 | zhangsan |
			|    2 | lisi     |
			|    3 | WANG_WU  |
			+------+----------+
		select name from t_user where name like '%_%';
			+----------+
			| name     |
			+----------+
			| zhangsan |
			| lisi     |
			| WANG_WU  |
			+----------+
		select name from t_user where name like '%\_%';
			+---------+
			| name    |
			+---------+
			| WANG_WU |
			+---------+

	找出名字中最后一个字母是T的？
		select ename from emp where ename like '%T';
			+-------+
			| ename |
			+-------+
			| SCOTT | 
			+-------+
```


13、排序（升序、降序）

按照工资升序，找出员工名和薪资？

```mysql
select 
	ename,sal 
from 
	emp 
order by
	sal;
+--------+---------+
| ename  | sal     |
+--------+---------+
| SMITH  |  800.00 |
| JAMES  |  950.00 |
| ADAMS  | 1100.00 |
| WARD   | 1250.00 |
| MARTIN | 1250.00 |
| MILLER | 1300.00 |
| TURNER | 1500.00 |
| ALLEN  | 1600.00 |
| CLARK  | 2450.00 |
| BLAKE  | 2850.00 |
| JONES  | 2975.00 |
| FORD   | 3000.00 |
| SCOTT  | 3000.00 |
| KING   | 5000.00 |
+--------+---------+
```

注意：默认是升序。怎么指定升序或者降序呢？asc表示升序，desc表示降序。
	select ename , sal from emp order by sal; // 升序
	select ename , sal from emp order by sal asc; // 升序
	select ename , sal from emp order by sal desc; // 降序。

按照工资的降序排列，当工资相同的时候再按照名字的升序排列。
	select ename,sal from emp order by sal desc;
	select ename,sal from emp order by sal desc , ename asc;
	注意：越靠前的字段越能起到主导作用。只有当前面的字段无法完成排序的时候，才会启用后面的字段。

找出工作岗位是SALESMAN的员工，并且要求按照薪资的降序排列。
	

```mysql
select 
	ename,job,sal
from
	emp
where 
	job = 'SALESMAN'
order by
	sal desc;
+--------+----------+---------+
| ename  | job      | sal     |
+--------+----------+---------+
| ALLEN  | SALESMAN | 1600.00 |
| TURNER | SALESMAN | 1500.00 |
| WARD   | SALESMAN | 1250.00 |
| MARTIN | SALESMAN | 1250.00 |
+--------+----------+---------+
```

	select 
		字段			3
	from
		表名			1
	where
		条件			2
	order by
		....		 4
	
	order by是最后执行的。

14、分组函数？
			count 计数
			sum 求和
			avg 平均值
			max 最大值
			min 最小值

	记住：所有的分组函数都是对“某一组”数据进行操作的。
	
	找出工资总和？
		select sum(sal) from emp;
	找出最高工资？
		select max(sal) from emp;
	找出最低工资？
		select min(sal) from emp;
	找出平均工资？
		select avg(sal) from emp;
	找出总人数？
		select count(*) from emp;
		select count(ename) from emp;
	
	分组函数一共5个。
	分组函数还有另一个名字：多行处理函数。
	多行处理函数的特点：输入多行，最终输出的结果是1行。
	
	分组函数自动忽略NULL。
		select count(comm) from emp;
		+-------------+
		| count(comm) |
		+-------------+
		|           4 |
		+-------------+
	
		select sum(comm) from emp;
		+-----------+
		| sum(comm) |
		+-----------+
		|   2200.00 |
		+-----------+
	
		select sum(comm) from emp where comm is not null; // 不需要额外添加这个过滤条件。sum函数自动忽略NULL。
	
	找出工资高于平均工资的员工？
		select avg(sal) from emp; // 平均工资
		+-------------+
		| avg(sal)    |
		+-------------+
		| 2073.214286 |
		+-------------+
	
		select ename,sal from emp where sal > avg(sal); //ERROR 1111 (HY000): Invalid use of group function
	思考以上的错误信息：无效的使用了分组函数？
		原因：SQL语句当中有一个语法规则，分组函数不可直接使用在where子句当中。why????
		怎么解释？
			因为group by是在where执行之后才会执行的。
				
			select		5
				..			
			from			1
				..
			where			2
				..
			group by		3
				..
			having		4
				..
			order by		6
				..
	
	count(*)和count(具体的某个字段)，他们有什么区别？
		count(*):不是统计某个字段中数据的个数，而是统计总记录条数。（和某个字段无关）
		count(comm): 表示统计comm字段中不为NULL的数据总数量。
	
	分组函数也能组合起来用：
		select count(*),sum(sal),avg(sal),max(sal),min(sal) from emp;
	
		+----------+----------+-------------+----------+----------+
		| count(*) | sum(sal) | avg(sal)    | max(sal) | min(sal) |
		+----------+----------+-------------+----------+----------+
		|       14 | 29025.00 | 2073.214286 |  5000.00 |   800.00 |
		+----------+----------+-------------+----------+----------+
	
	找出工资高于平均工资的员工？
	第一步：找出平均工资
		select avg(sal) from emp;
			+-------------+
			| avg(sal)    |
			+-------------+
			| 2073.214286 |
			+-------------+
	第二步：找出高于平均工资的员工
		select ename,sal from emp where sal > 2073.214286;
			+-------+---------+
			| ename | sal     |
			+-------+---------+
			| JONES | 2975.00 |
			| BLAKE | 2850.00 |
			| CLARK | 2450.00 |
			| SCOTT | 3000.00 |
			| KING  | 5000.00 |
			| FORD  | 3000.00 |
			+-------+---------+
	
		select ename,sal from emp where sal > (select avg(sal) from emp);

15、单行处理函数
	什么是单行处理函数？
		输入一行，输出一行。
	

```mysql
计算每个员工的年薪？
	select ename,(sal+comm)*12 as yearsal from emp;
	重点：所有数据库都是这样规定的，只要有NULL参与的运算结果一定是NULL。
	使用ifnull函数：
	select ename,(sal+ifnull(comm,0))*12 as yearsal from emp;

ifnull() 空处理函数？
	ifnull(可能为NULL的数据,被当做什么处理) ： 属于单行处理函数。
	select ename,ifnull(comm,0) as comm from emp;
	+--------+---------+
	| ename  | comm    |
	+--------+---------+
	| SMITH  |    0.00 |
	| ALLEN  |  300.00 |
	| WARD   |  500.00 |
	| JONES  |    0.00 |
	| MARTIN | 1400.00 |
	| BLAKE  |    0.00 |
	| CLARK  |    0.00 |
	| SCOTT  |    0.00 |
	| KING   |    0.00 |
	| TURNER |    0.00 |
	| ADAMS  |    0.00 |
	| JAMES  |    0.00 |
	| FORD   |    0.00 |
	| MILLER |    0.00 |
	+--------+---------+
```

16、group by 和 having
	
```mysql
group by ： 按照某个字段或者某些字段进行分组。
having : having是对分组之后的数据进行再次过滤。

案例：找出每个工作岗位的最高薪资。
select max(sal),job from emp group by job;

+----------+-----------+
| max(sal) | job       |
+----------+-----------+
|  3000.00 | ANALYST   |
|  1300.00 | CLERK     |
|  2975.00 | MANAGER   |
|  5000.00 | PRESIDENT |
|  1600.00 | SALESMAN  |
+----------+-----------+
```

```mysql
注意：分组函数一般都会和group by联合使用，这也是为什么它被称为分组函数的原因。
并且任何一个分组函数（count sum avg max min）都是在group by语句执行结束之后才会执行的。
当一条sql语句没有group by的话，整张表的数据会自成一组。

select ename,max(sal),job from emp group by job;
以上在mysql当中，查询结果是有的，但是结果没有意义，在Oracle数据库当中会报错。语法错误。
Oracle的语法规则比MySQL语法规则严谨。
记住一个规则：当一条语句中有group by的话，select后面只能跟分组函数和参与分组的字段。

每个工作岗位的平均薪资？
	select job,avg(sal) from emp group by job;
	+-----------+-------------+
	| job       | avg(sal)    |
	+-----------+-------------+
	| ANALYST   | 3000.000000 |
	| CLERK     | 1037.500000 |
	| MANAGER   | 2758.333333 |
	| PRESIDENT | 5000.000000 |
	| SALESMAN  | 1400.000000 |
	+-----------+-------------+

多个字段能不能联合起来一块分组？
案例：找出每个部门不同工作岗位的最高薪资。
	select 
		deptno,job,max(sal)
	from
		emp
	group by
		deptno,job;

找出每个部门的最高薪资，要求显示薪资大于2900的数据。

第一步：找出每个部门的最高薪资
select max(sal),deptno from emp group by deptno;
	+----------+--------+
	| max(sal) | deptno |
	+----------+--------+
	|  5000.00 |     10 |
	|  3000.00 |     20 |
	|  2850.00 |     30 |
	+----------+--------+

第二步：找出薪资大于2900
select max(sal),deptno from emp group by deptno having max(sal) > 2900; // 这种方式效率低。
	+----------+--------+
	| max(sal) | deptno |
	+----------+--------+
	|  5000.00 |     10 |
	|  3000.00 |     20 |
	+----------+--------+

select max(sal),deptno from emp where sal > 2900 group by deptno;  // 效率较高，建议能够使用where过滤的尽量使用where。
	+----------+--------+
	| max(sal) | deptno |
	+----------+--------+
	|  5000.00 |     10 |
	|  3000.00 |     20 |
	+----------+--------+
```


?	
?	
	找出每个部门的平均薪资，要求显示薪资大于2000的数据。
	
	第一步：找出每个部门的平均薪资
	select deptno,avg(sal) from emp group by deptno;
	+--------+-------------+
	| deptno | avg(sal)    |
	+--------+-------------+
	|     10 | 2916.666667 |
	|     20 | 2175.000000 |
	|     30 | 1566.666667 |
	+--------+-------------+
	
	第二步：要求显示薪资大于2000的数据
	select deptno,avg(sal) from emp group by deptno having avg(sal) > 2000;	
	+--------+-------------+
	| deptno | avg(sal)    |
	+--------+-------------+
	|     10 | 2916.666667 |
	|     20 | 2175.000000 |
	+--------+-------------+
	
	where后面不能使用分组函数：
	select deptno,avg(sal) from emp where avg(sal) > 2000 group by deptno;	// 错误了。
	这种情况只能使用having过滤。

17、总结一个完整的DQL语句怎么写？

```mysql
select		5
	..
from		1	
	..
where		2
	..
group by	3
	..
having		4
	..
order by	6
	..
```