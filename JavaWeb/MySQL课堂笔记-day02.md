MySQL day02

1、关于查询结果集的去重？

```mysql
mysql> select distinct job from emp; // distinct关键字去除重复记录。
+-----------+
| job       |
+-----------+
| CLERK     |
| SALESMAN  |
| MANAGER   |
| ANALYST   |
| PRESIDENT |
+-----------+	

mysql> select ename,distinct job from emp;
以上的sql语句是错误的。
记住：distinct只能出现在所有字段的最前面。

mysql> select distinct deptno,job from emp;
+--------+-----------+
| deptno | job       |
+--------+-----------+
|     20 | CLERK     |
|     30 | SALESMAN  |
|     20 | MANAGER   |
|     30 | MANAGER   |
|     10 | MANAGER   |
|     20 | ANALYST   |
|     10 | PRESIDENT |
|     30 | CLERK     |
|     10 | CLERK     |
+--------+-----------+
案例：统计岗位的数量？
select count(distinct job) from emp;
+---------------------+
| count(distinct job) |
+---------------------+
|                   5 |
+---------------------+

```

2、连接查询

2.1、什么是连接查询？
	在实际开发中，大部分的情况下都不是从单表中查询数据，一般都是多张表联合查询取出最终的结果。
	在实际开发中，一般一个业务都会对应多张表，比如：学生和班级，起码两张表。
		stuno		stuname			classno		classname
		-----------------------------------------------------------------------------------
		1			zs					1				北京大兴区亦庄经济技术开发区第二中学高三1班
		2			ls					1				北京大兴区亦庄经济技术开发区第二中学高三1班
		...
		学生和班级信息存储到一张表中，结果就像上面一样，数据会存在大量的重复，导致数据的冗余。

2.2、连接查询的分类？
	根据语法出现的年代来划分的话，包括：
		SQL92（一些老的DBA可能还在使用这种语法。DBA：DataBase Administrator，数据库管理员）
		SQL99（比较新的语法）
	
	根据表的连接方式来划分，包括：
		内连接：
			等值连接
			非等值连接
			自连接
		外连接：
			左外连接（左连接）
			右外连接（右连接）
		全连接（这个不讲，很少用！）

2.3、在表的连接查询方面有一种现象被称为：笛卡尔积现象。（笛卡尔乘积现象）

案例：找出每一个员工的部门名称，要求显示员工名和部门名。

```mysql
EMP表
+--------+--------+
| ename  | deptno |
+--------+--------+
| SMITH  |     20 |
| ALLEN  |     30 |
| WARD   |     30 |
| JONES  |     20 |
| MARTIN |     30 |
| BLAKE  |     30 |
| CLARK  |     10 |
| SCOTT  |     20 |
| KING   |     10 |
| TURNER |     30 |
| ADAMS  |     20 |
| JAMES  |     30 |
| FORD   |     20 |
| MILLER |     10 |
+--------+--------+
DEPT表
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+

select ename,dname from emp,dept;
+--------+------------+
| ename  | dname      |
+--------+------------+
| SMITH  | ACCOUNTING |
| SMITH  | RESEARCH   |
| SMITH  | SALES      |
| SMITH  | OPERATIONS |
| ALLEN  | ACCOUNTING |
| ALLEN  | RESEARCH   |
| ALLEN  | SALES      |
| ALLEN  | OPERATIONS |
............
56 rows in set (0.00 sec)
```

笛卡尔积现象：当两张表进行连接查询的时候，没有任何条件进行限制，最终的查询结果条数是两张表记录条数的乘积。

关于表的别名：
	select e.ename,d.dname from emp e,dept d;
	表的别名有什么好处？
		第一：执行效率高。
		第二：可读性好。

2.4、怎么避免笛卡尔积现象？当然是加条件进行过滤。
思考：避免了笛卡尔积现象，会减少记录的匹配次数吗？
	不会，次数还是56次。只不过显示的是有效记录。

案例：找出每一个员工的部门名称，要求显示员工名和部门名。
	select	
		e.ename,d.dname
	from
		emp e , dept d
	where
		e.deptno = d.deptno; //SQL92，以后不用。

	+--------+------------+
	| ename  | dname      |
	+--------+------------+
	| CLARK  | ACCOUNTING |
	| KING   | ACCOUNTING |
	| MILLER | ACCOUNTING |
	| SMITH  | RESEARCH   |
	| JONES  | RESEARCH   |
	| SCOTT  | RESEARCH   |
	| ADAMS  | RESEARCH   |
	| FORD   | RESEARCH   |
	| ALLEN  | SALES      |
	| WARD   | SALES      |
	| MARTIN | SALES      |
	| BLAKE  | SALES      |
	| TURNER | SALES      |
	| JAMES  | SALES      |
	+--------+------------+

2.5、内连接之等值连接：最大特点是：条件是等量关系。

案例：查询每个员工的部门名称，要求显示员工名和部门名。

SQL92:（太老，不用了）
	select 
		e.ename,d.dname
	from
		emp e, dept d
	where
		e.deptno = d.deptno;

SQL99：（常用的）
	select 
		e.ename,d.dname
	from
		emp e
	join
		dept d
	on
		e.deptno = d.deptno;

```mysql
// inner可以省略的，带着inner目的是可读性好一些。
select 
	e.ename,d.dname
from
	emp e
inner join
	dept d
on
	e.deptno = d.deptno;

语法：
	...
		A
	join
		B
	on
		连接条件
	where
		...

SQL99语法结构更清晰一些：表的连接条件和后来的where条件分离了。

+--------+------------+
| ename  | dname      |
+--------+------------+
| CLARK  | ACCOUNTING |
| KING   | ACCOUNTING |
| MILLER | ACCOUNTING |
| SMITH  | RESEARCH   |
| JONES  | RESEARCH   |
| SCOTT  | RESEARCH   |
| ADAMS  | RESEARCH   |
| FORD   | RESEARCH   |
| ALLEN  | SALES      |
| WARD   | SALES      |
| MARTIN | SALES      |
| BLAKE  | SALES      |
| TURNER | SALES      |
| JAMES  | SALES      |
+--------+------------+
```

2.6、内连接之非等值连接：最大的特点是：连接条件中的关系是非等量关系。

案例：找出每个员工的工资等级，要求显示员工名、工资、工资等级。

```mysql
mysql> select ename,sal from emp; e
+--------+---------+
| ename  | sal     |
+--------+---------+
| SMITH  |  800.00 |
| ALLEN  | 1600.00 |
| WARD   | 1250.00 |
| JONES  | 2975.00 |
| MARTIN | 1250.00 |
| BLAKE  | 2850.00 |
| CLARK  | 2450.00 |
| SCOTT  | 3000.00 |
| KING   | 5000.00 |
| TURNER | 1500.00 |
| ADAMS  | 1100.00 |
| JAMES  |  950.00 |
| FORD   | 3000.00 |
| MILLER | 1300.00 |
+--------+---------+

mysql> select * from salgrade; s
+-------+-------+-------+ 
| GRADE | LOSAL | HISAL |
+-------+-------+-------+
|     1 |   700 |  1200 |
|     2 |  1201 |  1400 |
|     3 |  1401 |  2000 |
|     4 |  2001 |  3000 |
|     5 |  3001 |  9999 |
+-------+-------+-------+

select 
	e.ename,e.sal,s.grade
from
	emp e
join
	salgrade s
on
	e.sal between s.losal and s.hisal;

// inner可以省略
select 
	e.ename,e.sal,s.grade
from
	emp e
inner join
	salgrade s
on
	e.sal between s.losal and s.hisal;

+--------+---------+-------+
| ename  | sal     | grade |
+--------+---------+-------+
| SMITH  |  800.00 |     1 |
| ALLEN  | 1600.00 |     3 |
| WARD   | 1250.00 |     2 |
| JONES  | 2975.00 |     4 |
| MARTIN | 1250.00 |     2 |
| BLAKE  | 2850.00 |     4 |
| CLARK  | 2450.00 |     4 |
| SCOTT  | 3000.00 |     4 |
| KING   | 5000.00 |     5 |
| TURNER | 1500.00 |     3 |
| ADAMS  | 1100.00 |     1 |
| JAMES  |  950.00 |     1 |
| FORD   | 3000.00 |     4 |
| MILLER | 1300.00 |     2 |
+--------+---------+-------+
```

2.7、自连接：最大的特点是：一张表看做两张表。自己连接自己。

案例：找出每个员工的上级领导，要求显示员工名和对应的领导名。

```mysql
mysql> select empno,ename,mgr from emp;
emp a 员工表
+-------+--------+------+
| empno | ename  | mgr  |
+-------+--------+------+
|  7369 | SMITH  | 7902 |
|  7499 | ALLEN  | 7698 |
|  7521 | WARD   | 7698 |
|  7566 | JONES  | 7839 |
|  7654 | MARTIN | 7698 |
|  7698 | BLAKE  | 7839 |
|  7782 | CLARK  | 7839 |
|  7788 | SCOTT  | 7566 |
|  7839 | KING   | NULL |
|  7844 | TURNER | 7698 |
|  7876 | ADAMS  | 7788 |
|  7900 | JAMES  | 7698 |
|  7902 | FORD   | 7566 |
|  7934 | MILLER | 7782 |
+-------+--------+------+
emp b 领导表
+-------+--------+
| empno | ename  |
+-------+--------+
|  7566 | JONES  |
|  7698 | BLAKE  |
|  7782 | CLARK  |
|  7788 | SCOTT  |
|  7839 | KING   |
|  7902 | FORD   |
+-------+--------+

员工的领导编号 = 领导的员工编号

select 
	a.ename as '员工名',b.ename as '领导名'
from
	emp a
inner join
	emp b
on
	a.mgr = b.empno;

+--------+--------+
| 员工名 | 领导名 |
+--------+--------+
| SMITH  | FORD   |
| ALLEN  | BLAKE  |
| WARD   | BLAKE  |
| JONES  | KING   |
| MARTIN | BLAKE  |
| BLAKE  | KING   |
| CLARK  | KING   |
| SCOTT  | JONES  |
| TURNER | BLAKE  |
| ADAMS  | SCOTT  |
| JAMES  | BLAKE  |
| FORD   | JONES  |
| MILLER | CLARK  |
+--------+--------+
```

2.8、外连接？
	
什么是外连接，和内连接有什么区别？

	内连接：
		假设A和B表进行连接，使用内连接的话，凡是A表和B表能够匹配上的记录查询出来，这就是内连接。
		AB两张表没有主副之分，两张表是平等的。
	
	外连接：
		假设A和B表进行连接，使用外连接的话，AB两张表中有一张表是主表，一张表是副表，主要查询主表中
		的数据，捎带着查询副表，当副表中的数据没有和主表中的数据匹配上，副表自动模拟出NULL与之匹配。
	
	外连接的分类？
		左外连接（左连接）：表示左边的这张表是主表。
		右外连接（右连接）：表示右边的这张表是主表。
	
		左连接有右连接的写法，右连接也会有对应的左连接的写法。

案例：找出每个员工的上级领导？（所有员工必须全部查询出来。）

```mysql
emp a 员工表
+-------+--------+------+
| empno | ename  | mgr  |
+-------+--------+------+
|  7369 | SMITH  | 7902 |
|  7499 | ALLEN  | 7698 |
|  7521 | WARD   | 7698 |
|  7566 | JONES  | 7839 |
|  7654 | MARTIN | 7698 |
|  7698 | BLAKE  | 7839 |
|  7782 | CLARK  | 7839 |
|  7788 | SCOTT  | 7566 |
|  7839 | KING   | NULL |
|  7844 | TURNER | 7698 |
|  7876 | ADAMS  | 7788 |
|  7900 | JAMES  | 7698 |
|  7902 | FORD   | 7566 |
|  7934 | MILLER | 7782 |
+-------+--------+------+
emp b 领导表
+-------+--------+
| empno | ename  |
+-------+--------+
|  7566 | JONES  |
|  7698 | BLAKE  |
|  7782 | CLARK  |
|  7788 | SCOTT  |
|  7839 | KING   |
|  7902 | FORD   |
+-------+--------+

内连接：
select 
	a.ename '员工', b.ename '领导'
from
	emp a
join
	emp b
on
	a.mgr = b.empno;

外连接：（左外连接/左连接）
select 
	a.ename '员工', b.ename '领导'
from
	emp a
left join
	emp b
on
	a.mgr = b.empno;

// outer是可以省略的。
select 
	a.ename '员工', b.ename '领导'
from
	emp a
left outer join
	emp b
on
	a.mgr = b.empno;

外连接：（右外连接/右连接）
select 
	a.ename '员工', b.ename '领导'
from
	emp b
right join
	emp a
on
	a.mgr = b.empno;

// outer可以省略。
select 
	a.ename '员工', b.ename '领导'
from
	emp b
right outer join
	emp a
on
	a.mgr = b.empno;

+--------+-------+
| 员工      | 领导    |
+--------+-------+
| SMITH  | FORD  |
| ALLEN  | BLAKE |
| WARD   | BLAKE |
| JONES  | KING  |
| MARTIN | BLAKE |
| BLAKE  | KING  |
| CLARK  | KING  |
| SCOTT  | JONES |
| KING   | NULL  |
| TURNER | BLAKE |
| ADAMS  | SCOTT |
| JAMES  | BLAKE |
| FORD   | JONES |
| MILLER | CLARK |
+--------+-------+
```

外连接最重要的特点是：主表的数据无条件的全部查询出来。

案例：找出哪个部门没有员工？

```mysql
EMP表
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
DEPT
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+

select 
	d.*
from
	emp e
right join
	dept d
on
	e.deptno = d.deptno
where
	e.empno is null;

+--------+------------+--------+
| DEPTNO | DNAME      | LOC    |
+--------+------------+--------+
|     40 | OPERATIONS | BOSTON |
+--------+------------+--------+
```

2.9、三张表怎么连接查询？
案例：找出每一个员工的部门名称以及工资等级。

```mysql
EMP e
+-------+--------+---------+--------+
| empno | ename  | sal     | deptno |
+-------+--------+---------+--------+
|  7369 | SMITH  |  800.00 |     20 |
|  7499 | ALLEN  | 1600.00 |     30 |
|  7521 | WARD   | 1250.00 |     30 |
|  7566 | JONES  | 2975.00 |     20 |
|  7654 | MARTIN | 1250.00 |     30 |
|  7698 | BLAKE  | 2850.00 |     30 |
|  7782 | CLARK  | 2450.00 |     10 |
|  7788 | SCOTT  | 3000.00 |     20 |
|  7839 | KING   | 5000.00 |     10 |
|  7844 | TURNER | 1500.00 |     30 |
|  7876 | ADAMS  | 1100.00 |     20 |
|  7900 | JAMES  |  950.00 |     30 |
|  7902 | FORD   | 3000.00 |     20 |
|  7934 | MILLER | 1300.00 |     10 |
+-------+--------+---------+--------+
DEPT d
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+
SALGRADE s
+-------+-------+-------+
| GRADE | LOSAL | HISAL |
+-------+-------+-------+
|     1 |   700 |  1200 |
|     2 |  1201 |  1400 |
|     3 |  1401 |  2000 |
|     4 |  2001 |  3000 |
|     5 |  3001 |  9999 |
+-------+-------+-------+

注意，解释一下：
	....
		A
	join
		B
	join
		C
	on
		...
```

?	

```mysql
表示：A表和B表先进行表连接，连接之后A表继续和C表进行连接。

select 
	e.ename,d.dname,s.grade
from
	emp e
join
	dept d
on
	e.deptno = d.deptno
join
	salgrade s
on
	e.sal between s.losal and s.hisal;

+--------+------------+-------+
| ename  | dname      | grade |
+--------+------------+-------+
| SMITH  | RESEARCH   |     1 |
| ALLEN  | SALES      |     3 |
| WARD   | SALES      |     2 |
| JONES  | RESEARCH   |     4 |
| MARTIN | SALES      |     2 |
| BLAKE  | SALES      |     4 |
| CLARK  | ACCOUNTING |     4 |
| SCOTT  | RESEARCH   |     4 |
| KING   | ACCOUNTING |     5 |
| TURNER | SALES      |     3 |
| ADAMS  | RESEARCH   |     1 |
| JAMES  | SALES      |     1 |
| FORD   | RESEARCH   |     4 |
| MILLER | ACCOUNTING |     2 |
+--------+------------+-------+
```

案例：找出每一个员工的部门名称、工资等级、以及上级领导。
	select 
		e.ename '员工',d.dname,s.grade,e1.ename '领导'
	from
		emp e
	join
		dept d
	on
		e.deptno = d.deptno
	join
		salgrade s
	on
		e.sal between s.losal and s.hisal
	left join
		emp e1
	on
		e.mgr = e1.empno;

```mysql
+--------+------------+-------+-------+
| 员工      | dname      | grade | 领导    |
+--------+------------+-------+-------+
| SMITH  | RESEARCH   |     1 | FORD  |
| ALLEN  | SALES      |     3 | BLAKE |
| WARD   | SALES      |     2 | BLAKE |
| JONES  | RESEARCH   |     4 | KING  |
| MARTIN | SALES      |     2 | BLAKE |
| BLAKE  | SALES      |     4 | KING  |
| CLARK  | ACCOUNTING |     4 | KING  |
| SCOTT  | RESEARCH   |     4 | JONES |
| KING   | ACCOUNTING |     5 | NULL  |
| TURNER | SALES      |     3 | BLAKE |
| ADAMS  | RESEARCH   |     1 | SCOTT |
| JAMES  | SALES      |     1 | BLAKE |
| FORD   | RESEARCH   |     4 | JONES |
| MILLER | ACCOUNTING |     2 | CLARK |
+--------+------------+-------+-------+
```

3、子查询

3.1、什么是子查询？子查询都可以出现在哪里？
	select语句当中嵌套select语句，被嵌套的select语句是子查询。
	子查询可以出现在哪里？
		select
			..(select).
		from
			..(select).
		where
			..(select).

3.2、where子句中使用子查询

案例：找出高于平均薪资的员工信息。
select * from emp where sal > avg(sal); //错误的写法，where后面不能直接使用分组函数。

第一步：找出平均薪资
	

```mysql
select avg(sal) from emp;
	+-------------+
	| avg(sal)    |
	+-------------+
	| 2073.214286 |
	+-------------+
```

第二步：where过滤
	

```mysql
select * from emp where sal > 2073.214286;
	+-------+-------+-----------+------+------------+---------+------+--------+
	| EMPNO | ENAME | JOB       | MGR  | HIREDATE   | SAL     | COMM | DEPTNO |
	+-------+-------+-----------+------+------------+---------+------+--------+
	|  7566 | JONES | MANAGER   | 7839 | 1981-04-02 | 2975.00 | NULL |     20 |
	|  7698 | BLAKE | MANAGER   | 7839 | 1981-05-01 | 2850.00 | NULL |     30 |
	|  7782 | CLARK | MANAGER   | 7839 | 1981-06-09 | 2450.00 | NULL |     10 |
	|  7788 | SCOTT | ANALYST   | 7566 | 1987-04-19 | 3000.00 | NULL |     20 |
	|  7839 | KING  | PRESIDENT | NULL | 1981-11-17 | 5000.00 | NULL |     10 |
	|  7902 | FORD  | ANALYST   | 7566 | 1981-12-03 | 3000.00 | NULL |     20 |
	+-------+-------+-----------+------+------------+---------+------+--------+
```

第一步和第二步合并：
	select * from emp where sal > (select avg(sal) from emp);

3.3、from后面嵌套子查询

案例：找出每个部门平均薪水的等级。

```mysql
第一步：找出每个部门平均薪水（按照部门编号分组，求sal的平均值）
select deptno,avg(sal) as avgsal from emp group by deptno;
+--------+-------------+
| deptno | avgsal      |
+--------+-------------+
|     10 | 2916.666667 |
|     20 | 2175.000000 |
|     30 | 1566.666667 |
+--------+-------------+
第二步：将以上的查询结果当做临时表t，让t表和salgrade s表连接，条件是：t.avgsal between s.losal and s.hisal
select 
	t.*,s.grade
from
	(select deptno,avg(sal) as avgsal from emp group by deptno) t
join
	salgrade s
on
	t.avgsal between s.losal and s.hisal;

+--------+-------------+-------+
| deptno | avgsal      | grade |
+--------+-------------+-------+
|     30 | 1566.666667 |     3 |
|     10 | 2916.666667 |     4 |
|     20 | 2175.000000 |     4 |
+--------+-------------+-------+
```

案例：找出每个部门平均的薪水等级。

```mysql
第一步：找出每个员工的薪水等级。
select e.ename,e.sal,e.deptno,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;
+--------+---------+--------+-------+
| ename  | sal     | deptno | grade |
+--------+---------+--------+-------+
| SMITH  |  800.00 |     20 |     1 |
| ALLEN  | 1600.00 |     30 |     3 |
| WARD   | 1250.00 |     30 |     2 |
| JONES  | 2975.00 |     20 |     4 |
| MARTIN | 1250.00 |     30 |     2 |
| BLAKE  | 2850.00 |     30 |     4 |
| CLARK  | 2450.00 |     10 |     4 |
| SCOTT  | 3000.00 |     20 |     4 |
| KING   | 5000.00 |     10 |     5 |
| TURNER | 1500.00 |     30 |     3 |
| ADAMS  | 1100.00 |     20 |     1 |
| JAMES  |  950.00 |     30 |     1 |
| FORD   | 3000.00 |     20 |     4 |
| MILLER | 1300.00 |     10 |     2 |
+--------+---------+--------+-------+
第二步：基于以上结果，继续按照deptno分组，求grade平均值。
select 
	e.deptno,avg(s.grade)
from 
	emp e 
join 
	salgrade s 
on 
	e.sal between s.losal and s.hisal
group by
	e.deptno;

+--------+--------------+
| deptno | avg(s.grade) |
+--------+--------------+
|     10 |       3.6667 |
|     20 |       2.8000 |
|     30 |       2.5000 |
+--------+--------------+
```

3.4、在select后面嵌套子查询。
案例：找出每个员工所在的部门名称，要求显示员工名和部门名。

```mysql
select 
	e.ename,d.dname
from
	emp e
join
	dept d
on
	e.deptno = d.deptno;

select 
	e.ename,(select d.dname from dept d where e.deptno = d.deptno) as dname 
from 
	emp e;

+--------+------------+
| ename  | dname      |
+--------+------------+
| SMITH  | RESEARCH   |
| ALLEN  | SALES      |
| WARD   | SALES      |
| JONES  | RESEARCH   |
| MARTIN | SALES      |
| BLAKE  | SALES      |
| CLARK  | ACCOUNTING |
| SCOTT  | RESEARCH   |
| KING   | ACCOUNTING |
| TURNER | SALES      |
| ADAMS  | RESEARCH   |
| JAMES  | SALES      |
| FORD   | RESEARCH   |
| MILLER | ACCOUNTING |
+--------+------------+
```

4、union （可以将查询结果集相加）

案例：找出工作岗位是SALESMAN和MANAGER的员工？

```mysql
第一种：select ename,job from emp where job = 'MANAGER' or job = 'SALESMAN';
第二种：select ename,job from emp where job in('MANAGER','SALESMAN');
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
第三种：union
select ename,job from emp where job = 'MANAGER'
union
select ename,job from emp where job = 'SALESMAN';
+--------+----------+
| ename  | job      |
+--------+----------+
| JONES  | MANAGER  |
| BLAKE  | MANAGER  |
| CLARK  | MANAGER  |
| ALLEN  | SALESMAN |
| WARD   | SALESMAN |
| MARTIN | SALESMAN |
| TURNER | SALESMAN |
+--------+----------+

两张不相干的表中的数据拼接在一起显示？
select ename from emp
union
select dname from dept;

+------------+
| ename      |
+------------+
| SMITH      |
| ALLEN      |
| WARD       |
| JONES      |
| MARTIN     |
| BLAKE      |
| CLARK      |
| SCOTT      |
| KING       |
| TURNER     |
| ADAMS      |
| JAMES      |
| FORD       |
| MILLER     |
| ACCOUNTING |
| RESEARCH   |
| SALES      |
| OPERATIONS |
+------------+

mysql> select ename,sal from emp
    -> union
    -> select dname from dept;
ERROR 1222 (21000): The used SELECT statements have a different number of columns
```

5、limit (重点中的重点，以后分页查询全靠它了。)

5.1、limit是mysql特有的，其他数据库中没有，不通用。（Oracle中有一个相同的机制，叫做rownum）

5.2、limit取结果集中的部分数据，这时它的作用。

5.3、语法机制：
	limit startIndex, length
		startIndex表示起始位置，从0开始，0表示第一条数据。
		length表示取几个
	
```mysql
案例：取出工资前5名的员工（思路：降序取前5个）
	select ename,sal from emp order by sal desc;
	取前5个：
		select ename,sal from emp order by sal desc limit 0, 5;
		select ename,sal from emp order by sal desc limit 5;
```

5.4、limit是sql语句最后执行的一个环节：
	select		5
		...
	from			1
		...		
	where			2
		...	
	group by		3
		...
	having		4
		...
	order by		6
		...
	limit			7
		...;

5.5、案例：找出工资排名在第4到第9名的员工？
	

```mysql
select ename,sal from emp order by sal desc limit 3,6;
	+--------+---------+
	| ename  | sal     |
	+--------+---------+
	| JONES  | 2975.00 |
	| BLAKE  | 2850.00 |
	| CLARK  | 2450.00 |
	| ALLEN  | 1600.00 |
	| TURNER | 1500.00 |
	| MILLER | 1300.00 |
	+--------+---------+
```

5.6、通用的标准分页sql？

每页显示3条记录：
第1页：0, 3
第2页：3, 3
第3页：6, 3
第4页：9, 3
第5页：12, 3

每页显示pageSize条记录：
第pageNo页：(pageNo - 1) * pageSize, pageSize

pageSize是什么？是每页显示多少条记录
pageNo是什么？显示第几页

java代码{
	int pageNo = 2; // 页码是2
	int pageSize = 10; // 每页显示10条
	
	limit (pageNo - 1) * pageSize, pageSize
}

6、创建表：
	
```mysql
建表语句的语法格式：
	create table 表名(
		字段名1 数据类型,
		字段名2 数据类型,
		字段名3 数据类型,
		....
	);

关于MySQL当中字段的数据类型？以下只说常见的
	int		整数型(java中的int)
	bigint	长整型(java中的long)
	float		浮点型(java中的float double)
	char		定长字符串(String)
	varchar	可变长字符串(StringBuffer/StringBuilder)
	date		日期类型 （对应Java中的java.sql.Date类型）
	BLOB		二进制大对象（存储图片、视频等流媒体信息） Binary Large OBject （对应java中的Object）
	CLOB		字符大对象（存储较大文本，比如，可以存储4G的字符串。） Character Large OBject（对应java中的Object）
	......

char和varchar怎么选择？
	在实际的开发中，当某个字段中的数据长度不发生改变的时候，是定长的，例如：性别、生日等都是采用char。
	当一个字段的数据长度不确定，例如：简介、姓名等都是采用varchar。

BLOB和CLOB类型的使用？
	电影表: t_movie
	id(int)	name(varchar)		playtime(date/char)		haibao(BLOB)		history(CLOB)
	----------------------------------------------------------------------------------------
	1			蜘蛛侠	
	2
	3

表名在数据库当中一般建议以：t_或者tbl_开始。

创建学生表：
	学生信息包括：
		学号、姓名、性别、班级编号、生日
		学号：bigint
		姓名：varchar
		性别：char
		班级编号：int
		生日：char
	
	create table t_student(
		no bigint,
		name varchar(255),
		sex char(1),
		classno varchar(255),
		birth char(10)
	);
```

7、insert语句插入数据
	语法格式：
		insert into 表名(字段名1,字段名2,字段名3,....) values(值1,值2,值3,....)
		要求：字段的数量和值的数量相同，并且数据类型要对应相同。
	
```mysql
insert into t_student(no,name,sex,classno,birth) values(1,'zhangsan','1','gaosan1ban');
ERROR 1136 (21S01): Column count doesn't match value count at row 1

insert into t_student(no,name,sex,classno,birth) values(1,'zhangsan','1','gaosan1ban', '1950-10-12');

mysql> select * from t_student;
+------+----------+------+------------+------------+
| no   | name     | sex  | classno    | birth      |
+------+----------+------+------------+------------+
|    1 | zhangsan | 1    | gaosan1ban | 1950-10-12 |
+------+----------+------+------------+------------+

insert into t_student(name,sex,classno,birth,no) values('lisi','1','gaosan1ban', '1950-10-12',2);

mysql> select * from t_student;
+------+----------+------+------------+------------+
| no   | name     | sex  | classno    | birth      |
+------+----------+------+------------+------------+
|    1 | zhangsan | 1    | gaosan1ban | 1950-10-12 |
|    2 | lisi     | 1    | gaosan1ban | 1950-10-12 |
+------+----------+------+------------+------------+

insert into t_student(name) values('wangwu'); // 除name字段之外，剩下的所有字段自动插入NULL。
mysql> select * from t_student;
+------+----------+------+------------+------------+
| no   | name     | sex  | classno    | birth      |
+------+----------+------+------------+------------+
|    1 | zhangsan | 1    | gaosan1ban | 1950-10-12 |
|    2 | lisi     | 1    | gaosan1ban | 1950-10-12 |
| NULL | wangwu   | NULL | NULL       | NULL       |
+------+----------+------+------------+------------+

insert into t_student(no) values(3); 
mysql> select * from t_student;
+------+----------+------+------------+------------+
| no   | name     | sex  | classno    | birth      |
+------+----------+------+------------+------------+
|    1 | zhangsan | 1    | gaosan1ban | 1950-10-12 |
|    2 | lisi     | 1    | gaosan1ban | 1950-10-12 |
| NULL | wangwu   | NULL | NULL       | NULL       |
|    3 | NULL     | NULL | NULL       | NULL       |
+------+----------+------+------------+------------+

drop table if exists t_student; // 当这个表存在的话删除。
create table t_student(
	no bigint,
	name varchar(255),
	sex char(1) default 1,
	classno varchar(255),
	birth char(10)
);

insert into t_student(name) values('zhangsan');
mysql> select * from t_student;
+------+----------+------+---------+-------+
| no   | name     | sex  | classno | birth |
+------+----------+------+---------+-------+
| NULL | zhangsan | 1    | NULL    | NULL  |
+------+----------+------+---------+-------+

需要注意的地方：
	当一条insert语句执行成功之后，表格当中必然会多一行记录。
	即使多的这一行记录当中某些字段是NULL，后期也没有办法在执行
	insert语句插入数据了，只能使用update进行更新。

// 字段可以省略不写，但是后面的value对数量和顺序都有要求。
insert into t_student values(1,'jack','0','gaosan2ban','1986-10-23');
mysql> select * from t_student;
+------+----------+------+------------+------------+
| no   | name     | sex  | classno    | birth      |
+------+----------+------+------------+------------+
| NULL | zhangsan | 1    | NULL       | NULL       |
|    1 | jack     | 0    | gaosan2ban | 1986-10-23 |
+------+----------+------+------------+------------+

insert into t_student values(1,'jack','0','gaosan2ban');
ERROR 1136 (21S01): Column count doesn't match value count at row 1

// 一次插入多行数据
insert into t_student
	(no,name,sex,classno,birth) 
values
	(3,'rose','1','gaosi2ban','1952-12-14'),(4,'laotie','1','gaosi2ban','1955-12-14');

mysql> select * from t_student;
+------+----------+------+------------+------------+
| no   | name     | sex  | classno    | birth      |
+------+----------+------+------------+------------+
| NULL | zhangsan | 1    | NULL       | NULL       |
|    1 | jack     | 0    | gaosan2ban | 1986-10-23 |
|    3 | rose     | 1    | gaosi2ban  | 1952-12-14 |
|    4 | laotie   | 1    | gaosi2ban  | 1955-12-14 |
+------+----------+------+------------+------------+
```

8、表的复制
	语法：
		create table 表名 as select语句;
		将查询结果当做表创建出来。

9、将查询结果插入到一张表中？
	

```mysql
mysql> insert into dept1 select * from dept;
	mysql> select * from dept1;
	+--------+------------+----------+
	| DEPTNO | DNAME      | LOC      |
	+--------+------------+----------+
	|     10 | ACCOUNTING | NEW YORK |
	|     20 | RESEARCH   | DALLAS   |
	|     30 | SALES      | CHICAGO  |
	|     40 | OPERATIONS | BOSTON   |
	|     10 | ACCOUNTING | NEW YORK |
	|     20 | RESEARCH   | DALLAS   |
	|     30 | SALES      | CHICAGO  |
	|     40 | OPERATIONS | BOSTON   |
	+--------+------------+----------+
```

10、修改数据：update

```mysql
语法格式：
	update 表名 set 字段名1=值1,字段名2=值2... where 条件;

注意：没有条件整张表数据全部更新。

案例：将部门10的LOC修改为SHANGHAI，将部门名称修改为RENSHIBU
update dept1 set loc = 'SHANGHAI', dname = 'RENSHIBU' where deptno = 10;
mysql> select * from dept1;
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | RENSHIBU   | SHANGHAI |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
|     10 | RENSHIBU   | SHANGHAI |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+

更新所有记录
	update dept1 set loc = 'x', dname = 'y';
	mysql> select * from dept1;
	+--------+-------+------+
	| DEPTNO | DNAME | LOC  |
	+--------+-------+------+
	|     10 | y     | x    |
	|     20 | y     | x    |
	|     30 | y     | x    |
	|     40 | y     | x    |
	|     10 | y     | x    |
	|     20 | y     | x    |
	|     30 | y     | x    |
	|     40 | y     | x    |
	+--------+-------+------+
```

11、删除数据？
	语法格式：
		delete from 表名 where 条件;
	
```mysql
注意：没有条件全部删除。

删除10部门数据？
	delete from dept1 where deptno = 10;

删除所有记录？
	delete from dept1;

怎么删除大表中的数据？（重点）
	truncate table 表名; // 表被截断，不可回滚。永久丢失。
删除表？
	drop table 表名; // 这个通用。
	drop table if exists 表名; // oracle不支持这种写法。
```

12、对于表结构的修改，这里不讲了，大家使用工具完成即可，因为在实际开发中表一旦
设计好之后，对表结构的修改是很少的，修改表结构就是对之前的设计进行了否定，即使
需要修改表结构，我们也可以直接使用工具操作。修改表结构的语句不会出现在Java代码当中。
出现在java代码当中的sql包括：insert delete update select（这些都是表中的数据操作。）

增删改查有一个术语：CRUD操作
Create（增） Retrieve（检索） Update（修改） Delete（删除）

13、约束(Constraint)

13.1、什么是约束？常见的约束有哪些呢？
	在创建表的时候，可以给表的字段添加相应的约束，添加约束的目的是为了保证表中数据的
	合法性、有效性、完整性。
	常见的约束有哪些呢？
		非空约束(not null)：约束的字段不能为NULL
		唯一约束(unique)：约束的字段不能重复
		主键约束(primary key)：约束的字段既不能为NULL，也不能重复（简称PK）
		外键约束(foreign key)：...（简称FK）
		检查约束(check)：注意Oracle数据库有check约束，但是mysql没有，目前mysql不支持该约束。


13.2、非空约束 not null
	drop table if exists t_user;
	create table t_user(
		id int,
		username varchar(255) not null,
		password varchar(255)
	);
	insert into t_user(id,password) values(1,'123');
	ERROR 1364 (HY000): Field 'username' doesn't have a default value

	insert into t_user(id,username,password) values(1,'lisi','123');


