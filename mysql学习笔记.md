# mysql学习笔记day1

## mysql用户管理

### 用户权限

1.主机处填写：%，代表可以用任何ip地址的主机通过dev登陆到item数据库中

2.创建后分配权限：在权限中分配增删改查权限

## sql语句

1.在navicat中打开mysql命令行的方法：对localhost点击右键，新建查询，则会出来界面；

2.create database test;

### 重设root密码

1.创建一个txt文件，定义修改密码的SQL语句：ALTER USER 'root'@'localhost' (只允许本地登录)IDENTIFIED BY '123456';

## mysql配置文件（重要参数）

还未誊写

## 管理逻辑库和数据表

### SQL语言注意事项

1.SQL语言不区分大小写，但字符串里面的内容区分大小写(SELECT "HelloWorld";)

2.SQL语句中的空白换行没有限制，但不能破坏语法

3.注释：

#这是一段注释文字

/*这是另一段注释文字*/

### 创建逻辑库

创建：CREATE DATABASE 逻辑库名称;

显示已创建逻辑库：SHOW DATABASES;

删除逻辑空间：DROP DATABASE 逻辑库名；

使用：USE 逻辑库名；

### 创建数据表

1.创建

CREATE TABLE student（

id **INT UNSIGNED**(表示id为无符号int型) PRIMARY KEY,

name **VARCHAR(20)**（字符长度最大不超过20） NOT NULL,

sex **CHAR(1)** (固定字符串长度1)NOT NULL,

birthday DATE  NOT NULL,

tel CHAR(1)  NOT  NULL,

remark VARCHAR(200)

);

2.插入数据

INSERT INTO student VALUES(1,''李强'',''男'',''1995-05-15'',''13312345678'',NULL);

3.查看数据表

SHOW tables；

4.查看数据表的具体情况

DESC student；

5.查看当初创建该表时的sql语句是什么

SHOW CREATE TABLE student；

6.删除数据表

 DROP TABLE student；

### SQL语言数据类型

未誊写

### 修改数据表结构

1.添加字段

ALTER TABLE student

ADD  address VARCHAR(200) NOT NULL,

ADD home_tel CHAR(11) NOT NULL;

2.修改字段类型和约束

ALTER TABLE student

MODIFY home_tel VARCHAR(20) NOT NULL;

3.修改字段名称

ALTER TABLE student

CHANGE  address  home_address VARCHAR(200) NOT NULL;

4.删除字段

ALTER TABLE student

DROP home_address;

DROP home_tel;

### 数据库表字段约束 

1.字段约束   

CREATE TABLE t_teacher( 

  id INT UNSIGNED PRIMARY KEY **AUTO_INCREMENT**(自动生成),

  name VARCHAR(20)  NOT NULL,

  tel CHAR(11) NOT NULL **UNIQUE**（电话唯一）,

  married BOOLEAN **NOT NULL DEFAULT FALSE**（婚姻状况字段默认未婚）

);

2.外键约束创建（写在子表中的）

有了外键约束，我们必须首先删除子表中关联的数据，然后才可以删除父表中关联的数据

### 数据库的索引机制

1.本质就是对数据进行排序

2.创建索引

CREATE TABLE t_message(

 id INT UNSIGNED PRIMARY KEY,

content  VARCHAR(200) NOT NULL,

type ENUM("公告","通报","个人通知")  NOT NULL,

create_time TIMESTAMP NOT NULL,

**INDEX idx_type(type)**(以type为索引)

)

3.添加与删除索引

删除索引：DROP INDEX idx_type ON t_message;

添加索引：CREATE INDEX idx_type ON t_message(type);

ALTER TABLE  t_message ADD INDEX idx_type(type);

展示索引：SHOW INDEX FROM t_message;

## 数据库查询

### 数据库的基本查询

1.记录查询

SELECT * FROM t_emp;

SELECT empno,ename,sal  FROM t_emp;

2.使用列别名

SELECT empno,sal*12 AS "income"  FROM t_emp;

### 数据分页

SELECT empno, ename FROM t emp LIMIT 0, 20（偏移量）;取t_emp中前二十个数据

SELECT empno, ename FROM t emp LIMIT  20（偏移量）;起始值默认为0

执行顺序：SELECT->SELECT->LIMIT

### 对查询结果进行排序

1.SELECT ename, sal FROM t emp ORDER BY sal ;默认升序（ASC）

SELECT ename, sal FROM t emp ORDER BY sal  DESC;这种情况则按照降序进行排序

2.对于字段内容相同的情况，默认情况下是按照主键大小进行升序排序，同时也用多个排序字段，并分清首要排序和次要排序

SELECT empno, ename, sal, hi redate
FROM t_ emp 
ORDER BY sal DESC, hiredate ASC;首要排序：sal

3.SELECT empno, ename, sal,
FROM t_ emp 
**ORDER BY sal DESC LIMIT 0,5**(同样数据的前5位)

### 去除结果集中的重复记录

SELECT **DISTINCT** job FROM t emp;但如果select多个字段，distinct字段就会失效

### 条件查询语句

1.SELECT empnO, ename, Sal FROM t emp
WHERE deptno=10 AND sal >= 2000 ;

2.SELECT empnO, ename, Sal FROM t emp
WHERE （deptno=10 OR deptno=20）  AND sal >= 2000 ;

3.SELECT empno,ename,sal,hiredate

FROM t_emp

WHERE deptno=10 AND (sal+**ISFULL(comm,0)**(如果comm不为full，则comm的值不变，变，否则comm由full变为0))*12>=15000

AND **DATEDIFF(NOW(),hiredate)**（NOW()即取当前时间，DATEDIFF即两日期之差）/365>=20;

4.ename LIKE ''A%'';条件为名字以A开头

ename LIKE ''%A'';条件为名字以A结尾

ename LIKE ''%A%'';条件为名字中含有A

ename LIKE ''_LAKE'';条件为名字中只能有一个A

5.正则表达式

6.SELECT 3&7,3和7进行二进制逻辑与运算

# mysql学习笔记day2

## 聚合函数

1.求部门员工的平均月工资（注意：这里只返回一条记录）

SELECT AVG (sal + IFNULL(comm, 0) ) FROM t emp;

2.查询员工名字最长的是几个字符?
SELECT MAX (LENGTH (ename) )   FROM  t_ emp ;

3.COUNT(*)用于获得包含空值的记录数，COUNT(列名)用于获得包含非空值的记录数。
SELECT COUNT(*)  FROM temp;
SELECT COUNT (comm)  FROM temp;

4.聚合函数不可以出现在where子句中

## GROUP BY语句

1.SELECT deptno,**ROUND(AVG(sal))** (将sal的平均值四舍五入为整数) FROM temp  GROUP BY deptno;

2.错误语法：SELECT deptno, COUNT (*) , AVG(sal) , **sal**(不能加上这个，因为sal是多条目录，其余为单个目录）  FROM  temp GROUP BY deptno;

3.SELECT deptno, **GROUP CONCAT (ename)**（将得出来的符合条件的多个记录连接为一个记录，就符合语法了） , COUNT (*)    FROM t emp WHERE  sal>=2000   GROUP BY deptno;

## HAVING子句

１．要和GROUP BY子句联合使用，不能单独使用

２．解决分组查询的不足：

查询每个部门中，1982年以后入职的员工超过2个人的部门门编号
SELECT deptno FROM t emp
WHERE hi redate>="1982-01-01 "
GROUP BY deptno HAVING COUNT (*)>=2
ORDER BY deptno ASC;

３.拿聚合函数和某一个字段做对比是绝对不行的

SELECT deptno FROM t emp
WHERE hi redate>="1982-01-01 "
GROUP BY deptno HAVING COUNT (*)>=2　

## 表连接查询

**内连接**

1.内连接三种语法

SELECT  ...... FROM表1 JOIN表2 ON连接条件;
SELECT  .......FROM表1 JOIN 表2 WHERE连接条件;
SELECT  .......FROM表1  表2 WHERE连接条件;

2.相同的数据表也可以连查询与SCOTT相同部门J的员工都有谁?
SELECT e2 . en ame
FROM t_emp e1 JOIN t_emp e2
ON e1. deptno=e2. deptno
WHERE e1 . ename="SCOTT" AND e2 . ename ! =" SCOTT";
3.结果集也可以和其他表进行连接
SELECT  e1.ename   FROM  t_emp e1 JOIN
(SELECT AVG(sal) avg FROM t emp) e2
ON   e1.sal>e2.avg;

**外连接**

1.外连接中，除了符合条件的记录之外，结果集中还会保留不符合条件的记录。
SELECT e.empno,e.ename,d.dname.
FROM t_emp e
**LEFT JOIN**(左外连接，e为左表，d为右表，当左表中的deptno值与右表对应的不等式，赋予为Null值；同理右外连接是保留右表的所有记录和左表连接) t_dept d ON e.deptno = d. deptno;

2.使用外连接表的情况

a.查询每个部门的名称和部J的人数？但要考虑如果第四个部门没有员工，用内连接第四个部门你就会忽略

3.UNION关键字可以将多个查询语句的结果集进行合并，语法：(查询语包)UNION(查询语句)UNION(查询语句)....

查询每个部门门的名称和部门的人数?如果没有部门门的员工，部门名称用NULL代替。

## 子查询

1.子查询可以写在WHERE子句、FROM子句、SELECT子句，只有FROM字句子查询最可取，只会执行一次，因为from不是相关子查询

2.where子句的子查询不推荐使用，但没有语法错误，但效率最低，每筛选一次都会执行一次子查询

3.SELECT是相关子查询，每输出一条记录都要执行一次

4.SELECT语句不能用于多行子查询，因为与其他的单个数据无法合并

5.WHERE子句中，可以使用IN、ALL、 ANY、EXISTS关键字来处理多行表达式结果集的条件判断

### mysql学习笔记day3

### Insert语句

1.可以插入一条也可以插入多条记录

INSERT INTO 表名(字段1,字段2,....)  VALUES (值1,值2,..........);
INSERT INTO 表名( 字段1,字段2,......)  VALUES(值1,值2, .... ) ,(值1,值2, ....);

2.INSERT INTO表名SET字段1=值1,字段2=值2，.......;

3.IGNORE关键字会让INSERT只插入数据库不存在的记录
INSERT [ IGNORE ] INTO表名........;

4.UPDATE语句用于修改表的记录
UPDATE [ IGNORE ]表名
SET 字段1=值1,字段2=值2,........
[ WHERE条件1,条件2,.....]
[ORDER  BY....]
[LIMIT];

执行排序：UPDATE，WHERE，ORDER BY，LIMIT，SET

5.UPDATE语句表连接

UPDATE 表1 JOIN 表2 ON ，条件
SET字段1=值1，字段2=值2,.....;

### DELETE语句

1.删除数据记录而不能删除数据表

DELETE  [ IGNORE] FROM 表名
[ WHERE条件1,条件2,......]
[ ORDER BY......]
[ LIMIT ......];

2.DELETE表连接

DELETE表1，......  FROM表1 JOIN 表2 ON条件
[ WHERE  条件1 ,条件2，.... ]
[ ORDER BY  ......]
[ LIMIT......];

3.DELETE语句的表连接既可以是内连接，又可以是外连接
DELETE表1 ,....  FROM  表1 [ LEFT | RIGHT ]  JOIN 表2
ON条件.....;

## MySQL常用函数

### 数字函数

ABS(-100)，求绝对值：100
ROUND(4.62)，四舍五入：5
FLOOR(9.9)，强制舍位：9
CEIL(3.2)，强制进位：4
POWER(2,3)，2的三次方为8
LOG(7,3)，log以7为底数
LN(10)，ln 10

SQRT(9)，开平方，结果为3
PI()，圆周率
SIN(1)
COS(1)
TAN(1)
COT(1)
RADIANS(30)，角度转弧度
DEGREES(1)，弧度转角度

### 字符函数

1.日期时间函数

NOW()函数能获得系统日期和时间，格式yyyy-MM-dd hh:mm:ss
CURDATE()函数能获得当前系统日期，格式yyy-MM-dd
CURTIME()函数能获得当前系统时间，格式hh:mm:ss

2.DATE_ FORMAT()函数用于格式化日期，返回用户想要的日期格式
DATE_ FORMAT(日期，表达式)

例：

格式化函数
%Y:年份;%m:月份;%d:日期.
%w:星期(数字);%W:星期(名称);%j:本年第几天
%U:本年第几周;%H:小时(24);%h:小时(12);%i:分钟
%S:秒;%r:时间(12);%T:时间(24)

3.DATE_ ADD()函数可以实现日期的偏移计算，而且时间单位很灵活，既可以往前偏移，也可以往后偏移
DATE ADD(日期, INTERVAL偏移量时间单位)

4.DATEDIFF()函数用来计算两个日期之间相差的天数
DATEDIFF(日期，日期)

### 字符函数

转换为小写字母：LOWER(ename)
转换为大写字母：UPPER(ename)
字符数量LENGTH(ename)
连接字符串：CONCAT(sal, "$"),在薪水后加上美元符号
字符出现的位置：INSTR(ename, "A")，员工名字中是否出现大写A写出大的位置
插入/替换字符：INSERT("你好",1,0,"先生")，插入先生，即你好先生；INSERT("你好",1,2,"先生")，替换两个字符，即先生
替换字符:REPLACE("你好先生",'先生","女士")，将先生替换成女士，即你好女士

截取字符串：SUBSTR("你好世界",3,4)，3代表开始位置，4代表结束位置
截取字符串：SUBSTRING("你好世界",3,2)，3代表开始位置，2代表偏移量
左侧填充字符：LPAD("Hello",10,"*")10为填充后总字符串数，即*****Hello
右侧填充字符：RPAD(" Hello",10,"*")
去除首尾空格：TRIM(" 你好先生  ")

### 条件函数

1.IFNULL(表达式，值)
IF(表达式，值1,值2)

2.更复杂的可以用case语句

CASE
WHEN表达式THEN值1,
WHEN表达式THEN值2 ,
ELSE值N
END



















