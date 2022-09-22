# MYSQL



## 基础查询



### **字段设置别名**


```mysql
SELECT 字段1 [ AS 别名1 ] , 字段2 [ AS 别名2 ] ... FROM 表名; 

SELECT 字段1 [ 别名1 ] , 字段2 [ 别名2 ] ... FROM 表名; 
```





### **去除重复记录**

```mysql
SELECT DISTINCT 字段列表 FROM 表名;
```



**案例：**



* 查询公司员工的上班地址有哪些(不要重复)

```mysql
select distinct workaddress '工作地址' from emp;
```



### 运算符



| <> 或 !=            | 不等于                                   |
| ------------------- | ---------------------------------------- |
| BETWEEN ... AND ... | 在某个范围之内(含最小、最大值)           |
| IN(...)             | 在in之后的列表中的值，多选一             |
| LIKE 占位符         | 模糊匹配(_匹配单个字符, %匹配任意个字符) |
| IS NULL             | 是NULL                                   |
| AND 或 &&           | 并且 (多个条件同时成立)                  |
| OR 或               | 或者 (多个条件任意一个成立)              |
| NOT 或 !            | 非 , 不是                                |



**案例：**



*  查询没有身份证号的员工信息

  ```mysql
  select * from emp where idcard is null;
  ```

* 查询有身份证号的员工信息

  ```mysql
  select * from emp where idcard is not null;
  ```

*  查询年龄不等于 88 的员工信息

  ```mysql
  select * from emp where age != 88;
  select * from emp where age <> 88;
  ```

* 查询年龄在15岁(包含) 到 20岁(包含)之间的员工信息

  ```mysql
  select * from emp where age between 15 and 20;
  ```

* 查询年龄等于18 或 20 或 40 的员工信息

  ```mysql
  select * from emp where age in(18,20,40);
  ```

* 查询姓名为两个字的员工信息

  ```mysql
  select * from emp where name like '__';
  ```

*  查询身份证号最后一位是X的员工信息

  ```mysql
  select * from emp where idcard like '%X';
  select * from emp where idcard like '_________________X';
  ```

  

### **聚合函数**



| **函数** | **功能** |
| -------- | -------- |
| max      | 最大值   |
| min      | 最小值   |
| avg      | 平均值   |
| sum      | 求和     |
| count    | 统计数量 |

**语法:**

```mysql
SELECT 聚合函数(字段列表) FROM 表名 ;
```

> 注意 : NULL值是不参与所有聚合函数运算的。



### **分组查询**

**语法:**

```mysql
SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组 后过滤条件 ];
```



 **where与having区别:**

* 执行时机不同：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤。

* 判断条件不同：where不能对聚合函数进行判断，而having可以。

  

**案例:**



查询年龄小于45的员工 , 并根据工作地址分组 , 获取员工数量大于等于3的工作地址

```mysql
select workaddress, count(*) address_count from emp where age < 45 group by workaddress having address_count >= 3
```



### **排序查询**



**语法:**

```mysql
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ;
```

**排序方式:**

* ASC : 升序(默认值)
* DESC: 降序



### **分页查询**



**语法:**

```mysql
SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数 ;
```



 **注意事项:**

> *  起始索引从0开始，起始索引 = （查询页码 - 1）* 每页显示记录数。
> * 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是LIMIT。
> *  如果查询的是第一页数据，起始索引可以省略，直接简写为 limit 10。



### **执行顺序**



**from ... where ... group by ...having ... select ... order by ... limit ...**



## **函数**



### **字符串函数**



| **函数**                 | **功能**                                                  |
| ------------------------ | --------------------------------------------------------- |
| CONCAT(S1,S2,...Sn)      | 字符串拼接，将S1，S2，... Sn拼接成一个字符串              |
| LOWER(str)               | 将字符串str全部转为小写                                   |
| UPPER(str)               | 将字符串str全部转为大写                                   |
| LPAD(str,n,pad)          | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度 |
| RPAD(str,n,pad)          | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度 |
| TRIM(str)                | 去掉字符串头部和尾部的空格                                |
| SUBSTRING(str,start,len) | 返回从字符串str从start位置起的len个长度的字符串           |



**案例:**

由于业务需求变更，企业员工的工号，统一为5位数，目前不足5位数的全部在前面补0。比如： 1号员工的工号应该为00001。

```mysql
update emp set id = lpad(id, 5, '0'); 1
```



### **数值函数**



| **函数**   | **功能**                           |
| ---------- | ---------------------------------- |
| CEIL(x)    | 向上取整                           |
| FLOOR(x)   | 向下取整                           |
| MOD(x,y)   | 返回x/y的模                        |
| RAND()     | 返回0~1内的随机数                  |
| ROUND(x,y) | 求参数x的四舍五入的值，保留y位小数 |



**案例：**

通过数据库的函数，生成一个六位数的随机验证码。

思路:获取随机数可以通过rand()函数，但是获取出来的随机数是在0-1之间的，所以可以在其基础上乘以1000000，然后舍弃小数部分，如果长度不足6位，补0 

```mysql
select lpad(round(rand()*1000000 , 0), 6, '0');
```



### **日期函数**



| **函数**                          | **功能**                                          |
| --------------------------------- | ------------------------------------------------- |
| CURDATE()                         | 返回当前日期                                      |
| CURTIME()                         | 返回当前时间                                      |
| NOW()                             | 返回当前日期和时间                                |
| YEAR(date)                        | 获取指定date的年份                                |
| MONTH(date)                       | 获取指定date的月份                                |
| DAY(date)                         | 获取指定date的日期                                |
| DATE_ADD(date, INTERVAL exprtype) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| DATEDIFF(date1,date2)             | 返回起始时间date1 和 结束时间date2之间的天数      |

**案例：**

查询所有员工的入职天数，并根据入职天数倒序排序。

思路：入职天数，就是通过当前日期 - 入职日期，所以需要使用datediff函数来完成。

```mysql
select name, datediff(curdate(), entrydate) as 'entrydays' from emp order by entrydays desc;
```

### **流程函数**



| **函数**                                                     | **功能**                                                  |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| IF(value , t , f)                                            | 如果value为true，则返回t，否则返回f                       |
| IFNULL(value1 , value2)                                      | 如果value1不为空，返回value1，否则返回value2              |
| CASE WHEN [ val1 ] THEN [res1] ...ELSE [ default ] END       | 如果val1为true，返回res1，... 否则返回default默认值       |
| CASE [ expr ] WHEN [ val1 ] THEN[res1] ... ELSE [ default ] END | 如果expr的值等于val1，返回res1，... 否则返回default默认值 |



**案例:**



 查询emp表的员工姓名和工作地址 (北京/上海 ----> 一线城市 , 其他 ----> 二线城市)

```mysql
select name,
( case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end ) as '工作地址' 
from emp;
```



```mysql
selectid, name,
(case when math >= 85 then '优秀' when math >=60 then '及格' else '不及格' end ) '数学',
(case when english >= 85 then '优秀' when english >=60 then '及格' else '不及格' end ) '英语',
(case when chinese >= 85 then '优秀' when chinese >=60 then '及格' else '不及格' end ) '语文' 
from score;
```



## 约束



| 约束                      | 描述                                                      | 关键字      |
| ------------------------- | --------------------------------------------------------- | ----------- |
| 非空约束                  | 限制该字段的数据不能为null                                | NOT NULL    |
| 唯一约束                  | 保证该字段的所有数据都是唯一、不重复的                    | UNIQUE      |
| 主键约束                  | 主键是一行数据的唯一标识，要求非空且唯一                  | PRIMARY KEY |
| 默认约束                  | 保存数据时，如果未指定该字段的值，则采用默认值            | DEFAULT     |
| 检查约束(8.0.16版本 之后) | 保证字段值满足某一个条件                                  | CHECK       |
| 外键约束                  | 用来让两张表的数据之间建立连接，保证数据的一致 性和完整性 | FOREIGN KEY |



> 注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束。  







### 外键约束  



```mysql
CREATE TABLE 表名(
字段名 数据类型,
...
[CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
);
```
```mysql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名)
REFERENCES 主表 (主表列名) ;
```



**案例:**  

```mysql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references
dept(id);
```



**删除外键**  

```mysql
alter table emp drop foreign key fk_emp_dept_id;
```



### 删除/更新行为  



| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| NO ACTION   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致)*默认行为* |
| RESTRICT    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) *默认行为* |
| CASCADE     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。     *(cascade级联效果  )* |
| SET NULL    | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取null）。 |
| SET DEFAULT | 父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持)  |

**具体语法为:**  

```mysql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES
主表名 (主表字段名) ON UPDATE CASCADE ON DELETE CASCADE;
```



**案例：**

```mysql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references
dept(id) on update cascade on delete cascade ;
```

```mysql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references
dept(id) on update set null on delete set null ;
```



## 多表查询



原来查询单表数据，执行的SQL形式为：select * from emp;
那么我们要执行多表查询，就只需要使用逗号分隔多张表即可，如： select * from emp , dept  where emp.dept_id = dept.id;  



### 分类



* 连接查询  
  * 内连接：相当于查询A、B交集部分数据  
  * 外连接：  
    * 左外连接：查询左表所有数据，以及两张表交集部分数据  
    * 右外连接：查询右表所有数据，以及两张表交集部分数据  
  * 自连接：当前表与自身的连接查询，自连接必须使用表别名  

* 子查询  

### 内连接  



内连接查询的是两张表交集部分的数据。
内连接的语法分为两种: 隐式内连接、显式内连接。  



**隐式内连接**  

~~~mysql
SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ;
~~~

**显式内连接**  

```mysql
 SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ... ;
```



**案例:**  

查询每一个员工的姓名 , 及关联的部门的名称 (隐式内连接实现)
表结构: emp , dept  

```mysql
select emp.name , dept.name from emp , dept where emp.dept_id = dept.id ;
-- 为每一张表起别名,简化SQL编写
select e.name,d.name from emp e , dept d where e.dept_id = d.id;
```



查询每一个员工的姓名 , 及关联的部门的名称 (显式内连接实现)  

```mysql
select e.name, d.name from emp e inner join dept d on e.dept_id = d.id;
```



**注意事项:**  

> 一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字段  



### 外连接  



**左外连接**  

```mysql
SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ;
```



**右外连接**  

```mysql
SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;
```



**案例:**  

查询emp表的所有数据, 和对应的部门信息
由于需求中提到，要查询emp的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。  

~~~mysql
select e.*, d.name from emp e left outer join dept d on e.dept_id = d.id;

select e.*, d.name from emp e left join dept d on e.dept_id = d.id;
~~~



查询dept表的所有数据, 和对应的员工信息(右外连接)  

由于需求中提到，要查询dept表的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。  



~~~mysql
select d.*, e.* from emp e right outer join dept d on e.dept_id = d.id;

select d.*, e.* from dept d left outer join emp e on e.dept_id = d.id;
~~~



**注意事项：**  

> 左外连接和右外连接是可以相互替换的，只需要调整在连接查询时SQL中，表结构的先后顺
> 序就可以了。而我们在日常开发使用时，更偏向于左外连接。  



### 自连接



自连接查询，顾名思义，就是自己连接自己，也就是把一张表连接查询多次。  

~~~mysql
 SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ;
~~~

对于自连接查询，可以是内连接查询，也可以是外连接查询。  



**案例：**  

查询员工 及其 所属领导的名字  

~~~mysql
select a.name , b.name from emp a , emp b where a.managerid = b.id;
~~~



查询所有员工 emp 及其领导的名字 emp , 如果员工没有领导, 也需要查询出来  

~~~mysql
select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid =b.id;
~~~



**注意事项:**  

> 在自连接查询中，必须要为表起别名，要不然我们不清楚所指定的条件、返回的字段，到底
> 是哪一张表的字段。  



### 联合查询  



对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。  

```mysql
SELECT 字段列表 FROM 表A ...
UNION [ ALL ]
SELECT 字段列表 FROM 表B ....;
```



* 对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致。  
* union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重。  



```mysql
select * from emp where salary < 5000
union all
select * from emp where age > 50;
```

*union all查询出来的结果，仅仅进行简单的合并，并未去重*  



```mysql
select * from emp where salary < 5000
union
select * from emp where age > 50;
```

*union 联合查询，会对查询出来的结果进行去重处理*。  



### 子查询  

SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询  

~~~mysql
SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2 );
~~~

*子查询外部的语句可以是INSERT / UPDATE / DELETE / SELECT 的任何一个。*  



* 根据子查询结果不同，分为：  
  * 标量子查询（子查询结果为单个值）  
  * 列子查询(子查询结果为一列)  
  * 行子查询(子查询结果为一行)  
  * 表子查询(子查询结果为多行多列)  

* 根据子查询位置，分为：  
  * WHERE之后  
  * FROM之后  
  * SELECT之后  

**案例:**  

查询 "销售部" 的所有员工信息  

```mysql
select * from emp where dept_id = (select id from dept where name = '销售部');
```



#### 列子查询  

| 操作符 | 描述                                   |
| ------ | -------------------------------------- |
| IN     | 在指定的集合范围之内，多选一           |
| NOT IN | 不在指定的集合范围之内                 |
| ANY    | 子查询返回列表中，有任意一个满足即可   |
| SOME   | 与ANY等同，使用SOME的地方都可以使用ANY |
| ALL    | 子查询返回列表的所有值都必须满足       |

 

**案例:**  



查询 "销售部" 和 "市场部" 的所有员工信息  

~~~mysql
select * from emp where dept_id in (
    select id from dept where name = '销售部' or name = '市场部'
)
~~~





查询比财务部所有人工资都高的员工信息  

~~~mysql
select * from emp where salary > all (
    select salary from emp where dept_id =(
        select id from dept where name = '财务部'
    )
);
~~~





查询比研发部其中任意一人工资高的员工信息  

~~~mysql
select * from emp where salary > any (
    select salary from emp where dept_id =(
        select id from dept where name = '研发部'
    ) 
);
~~~



#### 行子查询  

常用的操作符：= 、<> 、IN 、NOT IN  



**案例:**  





查询与 "张无忌" 的薪资及直属领导相同的员工信息 ;  

~~~mysql
select * from emp where (salary,managerid) = (
    select salary, managerid from empwhere name = '张无忌'
);
~~~



#### 表子查询  

常用的操作符：IN  



**案例:**  



查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息  

~~~mysql
select * from emp where (job,salary) in ( 
    select job, salary from emp where name ='鹿杖客' or name = '宋远桥' 
);
~~~





查询入职日期是 "2006-01-01" 之后的员工信息 , 及其部门信息  

~~~mysql
select e.*, d.* from (
    select * from emp where entrydate > '2006-01-01'
) e 
left join dept d 
on e.dept_id = d.id ;
~~~





## 事务  

> 注意： 默认MySQL的事务是自动提交的，也就是说，当执行完一条DML语句时，MySQL会立即隐
> 式的提交事务  





### 事务操作  



**查看/设置事务提交方式**  

```mysql
SELECT @@autocommit ;
SET @@autocommit = 0 ;
```

**提交事务**  

```mysql
COMMIT
```

**回滚事务**  

```mysql
ROLLBACK;
```



> 注意：上述的这种方式，我们是修改了事务的自动提交行为, 把默认的自动提交交, 此时我们执行的DML语句都不会提交, 需要手动的执行commit进行提交  



**开启事务**  

```mysql
START TRANSACTION 或 BEGIN ;
```



### 事务四大特性  



* 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。  

* 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态 。

* 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立
  环境下运行。  

* 持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。  

  

  上述就是事务的四大特性，简称*ACID*  

  

  而对于这四大特性，实际上分为两个部分:

  ![image-20220922204257121](C:\Users\22862\Desktop\笔记\MYSQL.assets\image-20220922204257121.png)

  

  ​	其中的**原子性、一致性、持久性**，实际上是由InnoDB中的两份日志来保证的，一份是*redo log*日志，一份是*undo log*日志。 

  ​	而**隔离性**是通过数据库的*锁*，加上*MVCC*来保证的。  

  

  

  

  ### 并发事务问题  

* 赃读：一个事务读到另外一个事务还没有提交的数据  

* 不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读  

* 幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据
  已经存在，好像出现了 "幻影"。  



### 事务隔离级别  



为了解决并发事务所引发的问题，在数据库中引入了事务隔离级别。主要有以下几种：  

| 隔离级别              | 脏读 | 不可重复读 | 幻读 |
| --------------------- | ---- | ---------- | ---- |
| Read uncommitted      | √    | √          | √    |
| Read committed        | ×    | √          | √    |
| Repeatable Read(默认) | ×    | ×          | √    |
| Serializable          | ×    | ×          | ×    |



**查看事务隔离级别** 

 ~~~mysql
 SELECT @@TRANSACTION_ISOLATION;
 ~~~



**设置事务隔离级别**  

```mysql
SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED |
READ COMMITTED | REPEATABLE READ | SERIALIZABLE }
```

> 注意：事务隔离级别越高，数据越安全，但是性能越低。  



## 存储引擎  



### MySQL体系结构  



* 连接层
  最上层是一些客户端和链接服务，包含本地sock 通信和大多数基于客户端/服务端工具实现的类似于TCP/IP的通信。主要完成一些类似于连接处理、授权认证、及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于SSL的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。
* 服务层
  第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如 过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定表的查询的顺序，是否利用索引等，最后生成相应的执行操作。如果是select语句，服务器还会查询内部的缓存，如果缓存空间足够大，这样在解决大量读操作的环境中能够很好的提升系统的性能。
* 引擎层
  存储引擎层， 存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎。数据库中的索引是在存储引擎层实现的。
* 存储层
  数据存储层， 主要是将数据(如: redolog、undolog、数据、索引、二进制日志、错误日志、查询日志、慢查询日志等)存储在文件系统之上，并完成与存储引擎的交互。和其他数据库相比，MySQL有点与众不同，它的架构可以在多种不同场景中应用并发挥良好作用。主要体现在存储引擎上，插件式的存储引擎架构，将查询处理和其他的系统任务以及数据的存储提取分离。这种架构可以根据业务的需求和实际需要选择合适的存储引擎。  

### 存储引擎介绍  



存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式 。

存储引擎是基于表的，而不是基于库的，所以存储引擎也可被称为表类型。

我们可以在创建表的时候，来指定选择的存储引擎，如果没有指定将自动选择默认的存储引擎。  



**建表时指定存储引擎**  

```mysql
CREATE TABLE 表名(
字段1 字段1类型 [ COMMENT 字段1注释 ] ,
......
字段n 字段n类型 [COMMENT 字段n注释 ]
) ENGINE = INNODB [ COMMENT 表注释 ] ;
```

**查询当前数据库支持的存储引擎**  

```mysql
show engines;
```



**示例演示:**  



查询建表语句 --- ==默认存储引擎: InnoDB== 

~~~mysql
show create table account;
~~~



查询当前数据库支持的存储引擎  

~~~mysql
show engines ;
~~~



创建表 my_myisam , 并指定MyISAM存储引擎  

~~~mysql
create table my_myisam(
id int,
name varchar(10)
) engine = MyISAM ;
~~~



创建表 my_memory , 指定Memory存储引擎  

~~~mysql
create table my_memory(
id int,
name varchar(10)
) engine = Memory ;
~~~



### 存储引擎特点  



#### InnoDB  



**介绍**
InnoDB是一种兼顾高可靠性和高性能的通用存储引擎，在 MySQL 5.5 之后，InnoDB是默认的MySQL 存储引擎。  



**特点**
* DML操作遵循ACID模型，支持事务；
* 行级锁，提高并发访问性能；
* 支持外键FOREIGN KEY约束，保证数据的完整性和正确性；  



**文件**
xxx.ibd：xxx代表的是表名，innoDB引擎的每张表都会对应这样一个表空间文件，存储该表的表结
构（frm-早期的 、sdi-新版的）、数据和索引。  



参数：innodb_file_per_table  

~~~mysql
show variables like 'innodb_file_per_table';
~~~

如果该参数开启，代表对于InnoDB引擎的表，每一张表都对应一个ibd文件。



**逻辑存储结构**



![image-20220917104437682](C:\Users\22862\AppData\Roaming\Typora\typora-user-images\image-20220917104437682.png)



* **表空间** : InnoDB存储引擎逻辑结构的最高层，ibd文件其实就是表空间文件，在表空间中可以包含多个Segment段。  
* **段** : 表空间是由各个段组成的， 常见的段有数据段、索引段、回滚段等。InnoDB中对于段的管理，都是引擎自身完成，不需要人为对其控制，一个段中包含多个区。  
* **区** : 区是表空间的单元结构，每个区的大小为1M。 默认情况下， InnoDB存储引擎页大小为16K， 即一个区中一共有64个连续的页。  
* **页** : 页是组成区的最小单元，页也是InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为 16KB。为了保证页的连续性，InnoDB 存储引擎每次从磁盘申请 4-5 个区。  
* **行** : InnoDB 存储引擎是面向行的，也就是说数据是按行进行存放的，在每一行中除了定义表时所指定的字段以外，还包含两个隐藏字段。  



#### MyISAM  



**介绍**  

MyISAM是MySQL早期的默认存储引擎。  



**特点**  

不支持事务，不支持外键
支持表锁，不支持行锁
访问速度快  



**文件**  

xxx.sdi：存储表结构信息
xxx.MYD: 存储数据
xxx.MYI: 存储索引  





#### Memory  



**介绍**  

Memory引擎的表数据时存储在内存中的，由于受到硬件问题、或断电问题的影响，只能将这些表作为临时表或缓存使用。  



**特点**  

内存存放
hash索引（默认）  



**文件**  

xxx.sdi：存储表结构信息  



#### 区别特点



| 特点         | InnoDB            | MyISAM | Memory |
| ------------ | ----------------- | ------ | ------ |
| 存储限制     | 64TB              | 有     | 有     |
| 事务安全     | 支持              | -      | -      |
| 锁机制       | 行锁              | 表锁   | 表锁   |
| B+tree索引   | 支持              | 支持   | 支持   |
| Hash索引     | -                 | -      | 支持   |
| 全文索引     | 支持(5.6版本之后) | 支持   | -      |
| 空间使用     | 高                | 低     | N/A    |
| 内存使用     | 高                | 低     | 中等   |
| 批量插入速度 | 低                | 高     | 高     |
| 支持外键     | 支持              | -      | -      |



**面试题:**  

==InnoDB引擎与MyISAM引擎的区别 ?==

* InnoDB引擎, 支持事务, 而MyISAM不支持  
* InnoDB引擎, 支持行锁和表锁, 而MyISAM仅支持表锁, 不支持行锁  
* InnoDB引擎, 支持外键, 而MyISAM是不支持的  



主要是上述三点区别，当然也可以从索引结构、存储限制等方面，更加深入的回答，具体参考如下官方文档：
https://dev.mysql.com/doc/refman/8.0/en/innodb-introduction.html
https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html  



### 存储引擎选择  

在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。

对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合 。 



* InnoDB: 是Mysql的默认存储引擎，支持事务、外键。如果应用**对事务的完整性有比较高的要**
  **求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操**
  **作**，那么InnoDB存储引擎是比较合适的选择  
* MyISAM ： 如果应用是**以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完**
  **整性、并发性要求不是很高**，那么选择这个存储引擎是非常合适的。  
* MEMORY：将所有数据保存在内存中，访问速度快，通常用于临时表及缓存。MEMORY的缺陷就是
  对表的大小有限制，太大的表无法缓存在内存中，而且无法保障数据的安全性。  



## 索引  



### 概述  



索引（index）是帮助MySQL高效获取数据的数据结构(有序)。

在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据， 这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引  。



**特点**  

| 优势                                                         | 劣势                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 提高数据检索的效率，降低数据库 的IO成本                      | 索引列也是要占用空间的。                                     |
| 通过索引列对数据进行排序，降低 数据排序的成本，降低CPU的消 耗。 | 索引大大提高了查询效率，同时却也降低更新表的速度， 如对表进行INSERT、UPDATE、DELETE时，效率降低。 |



### 结构

MySQL的索引是在存储引擎层实现的，不同的存储引擎有不同的索引结构，主要包含以下几种：  

| 索引结构             | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| B+Tree索引           | 最常见的索引类型，大部分引擎都支持 B+ 树索引                 |
| Hash索引             | 底层数据结构是用哈希表实现的, 只有精确匹配索引列的查询才有效, 不 支持范围查询 |
| R-tree(空间索 引）   | 空间索引是MyISAM引擎的一个特殊索引类型，主要用于地理空间数据类 型，通常使用较少 |
| Full-text(全文 索引) | 是一种通过建立倒排索引,快速匹配文档的方式。类似于 Lucene,Solr,ES |





| 索引        | InnoDB          | MyISAM | Memory |
| ----------- | --------------- | ------ | ------ |
| B+tree索引  | 支持            | 支持   | 支持   |
| Hash 索引   | 不支持          | 不支持 | 支持   |
| R-tree 索引 | 不支持          | 支持   | 不支持 |
| Full-text   | 5.6版本之后支持 | 支持   | 不支持 |



> 注意： 我们平常所说的索引，如果没有特别指明，都是指B+树结构组织的索引。  



#### B-Tree  

B-Tree，B树是一种多叉路衡查找树，相对于二叉树，B树每个节点可以有多个分支，即多叉。


![image-20220917114946662](C:\Users\22862\AppData\Roaming\Typora\typora-user-images\image-20220917114946662.png)



**特点：**  

* 5阶的B树，每一个节点最多存储4个key，对应5个指针。  
* 一旦节点存储的key数量到达5，就会裂变，中间元素向上分裂。  
* 在B树中，非叶子节点和叶子节点都会存放数据。 

 

> 数据结构可视化网站  :https://www.cs.usfca.edu/~galles/visualization/BTree.html  



#### B+Tree  

B+Tree是B-Tree的变种  

![image-20220917120012511](C:\Users\22862\AppData\Roaming\Typora\typora-user-images\image-20220917120012511.png)



* 绿色框框起来的部分，是索引部分，仅仅起到索引数据的作用，不存储数据  
* 红色框框起来的部分，是数据存储部分，在其叶子节点中要存储具体的数据。  



**B+Tree 与 B-Tree相比，主要有以下三点区别**：  

* 所有的数据都会出现在叶子节点。  
* 叶子节点形成一个单向链表。  
* 非叶子节点仅仅起到索引数据作用，具体的数据都是在叶子节点存放的  



MySQL索引数据结构对经典的B+Tree进行了优化。

在原B+Tree的基础上，增加一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的B+Tree，提高区间访问的性能，利于排序。 

![image-20220917120243925](C:\Users\22862\AppData\Roaming\Typora\typora-user-images\image-20220917120243925.png)





#### Hash  

MySQL中除了支持B+Tree索引，还支持一种索引类型---Hash索引。  



**特点**  

* Hash索引只能用于对等比较(=，in)，不支持范围查询（between，>，< ，...）  
* 无法利用索引完成排序操作  
* 查询效率高，通常(不存在hash冲突的情况)只需要一次检索就可以了，效率通常要高于B+tree索
  引  



**存储引擎支持**  

在MySQL中，支持hash索引的是Memory存储引擎。 

而InnoDB中具有自适应hash功能，hash索引是InnoDB存储引擎根据B+Tree索引在指定条件下自动构建的。  





**思考题**： 为什么InnoDB存储引擎选择使用B+tree索引结构?  



* 相对于二叉树，层级更少，搜索效率高；  
* 对于B-tree，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储
  的键值减少，指针跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低；  
* 相对Hash索引，B+tree支持范围匹配及排序操作；  



### 索引分类  



在MySQL数据库，将索引的具体类型主要分为以下几类：主键索引、唯一索引、常规索引、全文索引。  

| 分类     | 含义                                                  | 特点                      | 关键字   |
| -------- | ----------------------------------------------------- | ------------------------- | -------- |
| 主键索引 | 针对于表中主键创建的索引                              | 默认自动创建, 只能 有一个 | PRIMARY  |
| 唯一索引 | 避免同一个表中某数据列中的值重复                      | 可以有多个                | UNIQUE   |
| 常规索引 | 快速定位特定数据                                      | 可以有多个                |          |
| 全文索引 | 全文索引查找的是文本中的关键词，而不是比 较索引中的值 | 可以有多个                | FULLTEXT |



而在在InnoDB存储引擎中，根据索引的存储形式，又可以分为以下两种：  

| 分类                      | 含义                                                        | 特点                 |
| ------------------------- | ----------------------------------------------------------- | -------------------- |
| 聚集索引(Clustered Index) | 将数据存储与索引放到了一块，索引结构的叶子 节点保存了行数据 | 必须有,而且只 有一个 |
| 二级索引(Secondary Index) | 将数据与索引分开存储，索引结构的叶子节点关 联的是对应的主键 | 可以存在多个         |



聚集索引选取规则:  

* 如果存在主键，主键索引就是聚集索引。  
* 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引  
* 如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索引。  



![image-20220917153422219](C:\Users\22862\AppData\Roaming\Typora\typora-user-images\image-20220917153422219.png)



* 聚集索引的叶子节点下挂的是这一行的数据 。  
* 二级索引的叶子节点下挂的是该字段值对应的主键值  



> 回表查询： 先到二级索引中查找数据，找到主键值，然后再到聚集索引中根据主键值，获取数据的方式，就称之为回表查询。  



**思考题**：  

以下两条SQL语句，那个执行效率高? 为什么?（ id为主键，name字段创建的有索引）
		A. select * from user where id = 10 ;
		B. select * from user where name = 'Arm' ;



解答：A 语句的执行性能要高于B 语句。

因为A语句直接走聚集索引，直接返回数据。 而B语句需要先查询name字段的二级索引，然后再查询聚集索引，也就是需要进行回表查询。  





### 索引语法  



创建索引  

~~~mysql
CREATE [ UNIQUE | FULLTEXT ] INDEX index_name ON table_name (index_col_name,... ) ;
~~~



查看索引  

~~~mysql
DROP INDEX index_name ON table_name ;
~~~



删除索引  

~~~mysql
DROP INDEX index_name ON table_name ;
~~~







**案例演示**  



name字段为姓名字段，该字段的值可能会重复，为该字段创建索引。  

~~~mysql
CREATE INDEX idx_user_name ON tb_user(name);
~~~



phone手机号字段的值，是非空，且唯一的，为该字段创建唯一索引  

```mysql
CREATE UNIQUE INDEX idx_user_phone ON tb_user(phone);
```



为profession、age、status创建联合索引  

~~~mysql
CREATE INDEX idx_user_pro_age_sta ON tb_user(profession,age,status);
~~~



完成上述的需求之后，我们再查看tb_user表的所有的索引数据。  

~~~mysql
 show index from tb_user;
~~~



### SQL性能分析  



MySQL 客户端连接成功后，通过 show [session|global] status 命令可以提供服务器状态信息。通过如下指令，可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次：  



~~~mysql
-- session 是查看当前会话 ;
-- global 是查询全局数据 ;
SHOW GLOBAL STATUS LIKE 'Com_______';
~~~

Com_delete: 删除次数  
Com_insert: 插入次数
Com_select: 查询次数
Com_update: 更新次数  



> 通过上述指令，我们可以查看到当前数据库到底是以查询为主，还是以增删改为主，从而为数据库优化提供参考依据。
>
>  如果是以增删改为主，我们可以考虑不对其进行索引的优化。 
>
> 如果是以查询为主，那么就要考虑对数据库的索引进行优化了。  





#### 慢查询日志  



慢查询日志记录了所有执行时间超过指定参数（long_query_time，单位：秒，默认10秒）的所有SQL语句的日志 

MySQL的慢查询日志默认没有开启，我们可以查看一下系统变量 slow_query_log。  



如果要开启慢查询日志，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息：  

~~~mysql
# 开启MySQL慢日志查询开关
slow_query_log=1
# 设置慢日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
long_query_time=2
~~~



配置完毕之后，通过以下指令重新启动MySQL服务器进行测试，查看慢日志文件中记录的信息
/var/lib/mysql/localhost-slow.log。  

~~~
systemctl restart mysqld
~~~



#### profile  



show profiles 能够在做SQL优化时帮助我们了解时间都耗费到哪里去了。通过have_profiling
参数，能够看到当前MySQL是否支持profile操作：  

~~~mysql
SELECT @@have_profiling ;
~~~



可以通过set语句在session/global级别开启profiling：  

~~~mysql
SET profiling = 1;
~~~



执行一系列的业务SQL的操作，然后通过如下指令查看指令的执行耗时：  

~~~mysql
-- 查看每一条SQL的耗时基本情况
show profiles;
-- 查看指定query_id的SQL语句各个阶段的耗时情况
show profile for query query_id;
-- 查看指定query_id的SQL语句CPU的使用情况
show profile cpu for query query_id;
~~~



#### explain  

EXPLAIN 或者 DESC命令获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行
过程中表如何连接和连接的顺序。 

 ```mysql
 -- 直接在select语句之前加上关键字 explain / desc
 EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件 ;
 ```





Explain 执行计划中各个字段的含义:  

| 字段         | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| id           | select查询的序列号，表示查询中执行select子句或者是操作表的顺序 (id相同，执行顺序从上到下；id不同，值越大，越先执行)。 |
| select_type  | 表示 SELECT 的类型，常见的取值有 SIMPLE（简单表，即不使用表连接 或者子查询）、PRIMARY（主查询，即外层的查询）、 UNION（UNION 中的第二个或者后面的查询语句）、 SUBQUERY（SELECT/WHERE之后包含了子查询）等 |
| type         | 表示连接类型，性能由好到差的连接类型为NULL、system、const、 eq_ref、ref、range、 index、all 。 |
| possible_key | 显示可能应用在这张表上的索引，一个或多个。                   |
| key          | 实际使用的索引，如果为NULL，则没有使用索引。                 |
| key_len      | 表示索引中使用的字节数， 该值为索引字段最大可能长度，并非实际使用长 度，在不损失精确性的前提下， 长度越短越好 。 |
| rows         | MySQL认为必须要执行查询的行数，在innodb引擎的表中，是一个估计值， 可能并不总是准确的。 |
| filtered     | 表示返回结果的行数占需读取行数的百分比， filtered 的值越大越好。 |



### 索引使用  



#### 最左前缀法则  



如果索引了多列（联合索引），要遵守最左前缀法则。最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。如果跳跃某一列，索引将会部分失效(后面的字段索引失效)。  



> 注意 ： 
>
> 最左前缀法则中指的最左边的列，是指在查询时，联合索引的最左边的字段(即是第一个字段)必须存在，与我们编写SQL时，条件编写的先后顺序无关。  





#### 范围查询  



联合索引中，出现范围查询(>,<)，范围查询右侧的列索引失效。  



**案例：**



~~~mysql
explain select * from tb_user where profession = '软件工程' and age > 30 and status = '0';
~~~

当范围查询使用> 或 < 时，走联合索引了，范围查询右边的status字段是没有走索引的。  



```mysql
explain select * from tb_user where profession = '软件工程' and age >= 30 and status = '0';
```

当范围查询使用>= 或 <= 时，走联合索引了，所有的字段都是走索引的。   



==所以，在业务允许的情况下，尽可能的使用类似于 >= 或 <= 这类的范围查询，而避免使用 > 或 <==



### 索引失效情况  



#### 索引列运算  



不要在索引列上进行运算操作，否则索引将失效。  



**案例：**



当根据phone字段进行等值匹配查询时, 索引生效。  

```mysql
explain select * from tb_user where phone = '17799990015';
```



当根据phone字段进行函数运算操作之后，索引失效。  

~~~
explain select * from tb_user where substring(phone,10,2) = '15';
~~~





#### 字符串不加引号  



字符串类型字段使用时，不加引号，索引将失效。  

**案例**：



生效

```mysql
select * from tb_user where phone = '17799990015';
```



失效

```mysql
select * from tb_user where phone = 17799990015;
```



如果字符串不加单引号，对于查询结果，没什么影响，但是数据库存在隐式类型转换，索引将失效。  





#### 模糊查询  



如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效 。 



**案例：**

生效：

```mysql
select * from tb_user where profession like '软件%';
```



失效：

```mysql
select * from tb_user where profession like '%工程';
select * from tb_user where profession like '%工%';
```



在like模糊查询中，在关键字后面加%，索引可以生效。而如果在关键字前面加了%，索引将会失效。  



#### or连接条件  

用or分割开的条件， 如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到。 



**案例：**



```mysql
select * from tb_user where id = 10 or age = 23;
```

于age没有索引，所以即使id有索引，索引也会失效。因此需要针对于age建立索引。  



**当or连接的条件，左右两侧字段都有索引时，索引才会生效。**  



#### 数据分布影响  

如果MySQL评估使用索引比全表更慢，则不使用索引  



MySQL在查询时，会评估使用索引的效率与走全表扫描的效率，如果走全表扫描更快，则放弃索引，走全表扫描。 因为索引是用来索引少量数据的，如果通过索引查询返回大批量的数据，则还不如走全表扫描来的快，此时索引就会失效。  



### SQL提示  



SQL提示，是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的 。



**use index** ： 建议MySQL使用哪一个索引完成此次查询（仅仅是建议，mysql内部还会再次进行评估）  

~~~mysql
select * from tb_user use index(idx_user_pro) where profession = '软件工程';
~~~



**ignore index** ： 忽略指定的索引。  

~~~mysql
select * from tb_user ignore index(idx_user_pro) where profession = '软件工程';
~~~



**force index** ： 强制使用索引。  

```mysql
explain select * from tb_user force index(idx_user_pro) where profession = '软件工程';
```



### 覆盖索引  



尽量使用覆盖索引，减少select *。  

我们如果一直使用select * 查询返回所有字段值，很容易就会造成回表查询（除非是根据主键查询，此时只会扫描聚集索引）。 

 

覆盖索引是指：查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到 。  



| Extra                 | 含义                                                         |
| --------------------- | ------------------------------------------------------------ |
| Usingwhere;UsingIndex | 查找使用了索引，但是需要的数据都在索引列中能找到，所以不需 要回表查询数据 |
| Using index condition | 查找使用了索引，但是需要回表查询数据                         |



**思考题：**  

 一张表, 有四个字段(id, username, password, status), 由于数据量大, 需要对以下SQL语句进行优化, 该如何进行才是最优方案?

```mysql
 select id,username,password from tb_user where username ='itcast';
```



 答案: 针对于 username, password建立联合索引,  sql为:

```mysql
create indexidx_user_name_pass on tb_user(username,password);
```

 这样可以避免上述的SQL语句，在查询的过程中，出现回表查询。  



### 前缀索引  

当字段类型为字符串（varchar，text，longtext等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO， 影响查询效率。此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。  



* **语法** 

~~~mysql
create index idx_xxxx on table_name(column(n)) ;
~~~



**示例:**  

```mysql
create index idx_email_5 on tb_user(email(5));
```



* **前缀长度**

可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高则查询效率越高， 唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。  



求选择性：

~~~mysql
select count(distinct email) / count(*) from tb_user ;
select count(distinct substring(email,1,5)) / count(*) from tb_user ;
~~~



### 单列索引与联合索引  



单列索引：即一个索引只包含单个列。

联合索引：即一个索引包含了多个列。  



> 在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，
> 而非单列索引。  



### 索引设计原则  

* 针对于数据量较大，且查询比较频繁的表建立索引。  
* 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索
  引。
* 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。  
* 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引 。
* 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，
  避免回表，提高查询效率。  
* 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增
  删改的效率。  
* 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含
  NULL值时，它可以更好地确定哪个索引最有效地用于查询。  



## SQL优化  



### 插入数据  





如果我们需要一次性往数据库表中插入多条记录，可以从以下三个方面进行优化。  

```mysql
insert into tb_test values(1,'tom');
insert into tb_test values(2,'cat');
insert into tb_test values(3,'jerry');
.....
```



**优化方案一：批量插入数据**  

```mysql
Insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');
```



**优化方案二：手动控制事务**  

~~~mysql
start transaction;
insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');
insert into tb_test values(4,'Tom'),(5,'Cat'),(6,'Jerry');
insert into tb_test values(7,'Tom'),(8,'Cat'),(9,'Jerry');
commit;
~~~



**优化方案三 ：主键顺序插入，性能要高于乱序插入。**  

```
主键乱序插入 : 8 1 9 21 88 2 4 15 89 5 7 3
主键顺序插入 : 1 2 3 4 5 7 8 9 15 21 88 89  
```





**大批量插入数据** ：

如果一次性需要插入大批量数据(比如: 几百万的记录)，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令进行插入。操作如下：  

~~~mysql
-- 客户端连接服务端时，加上参数 -–local-infile
mysql –-local-infile -u root -p

-- 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile = 1;

-- 执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table tb_user fields
terminated by ',' lines terminated by '\n' ;
~~~

> 主键顺序插入性能高于乱序插入  



### 主键优化  





* 数据组织方式  

在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为索引组织表(index organized table IOT)。  



* 页分裂  

页可以为空，也可以填充一半，也可以填充100%。每个页包含了2-N行数据(如果一行数据过大，会行
溢出)，根据主键排列。  



* 页合并  

当页中删除的记录达到 MERGE_THRESHOLD（默认为页的50%），InnoDB会开始寻找最靠近的页（前
或后）看看是否可以将两个页合并以优化空间使用  



> MERGE_THRESHOLD：合并页的阈值，可以自己设置，在创建表或者创建索引时指定。  



* 索引设计原则  
  * 满足业务需求的情况下，尽量降低主键的长度。(主键过长，二级索引将会大量空间；搜索时会耗费大量IO) 
  * 插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键。  
  * 尽量不要使用UUID做主键或者是其他自然主键，如身份证号。（自然主键无序、长度长） 
  * 业务操作时，避免对主键的修改。  



### order by优化  





MySQL的排序，有两种方式：

* Using filesort : 通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sortbuffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫 FileSort 排序。

* Using index : 通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要额外排序，操作效率高。  

对于以上的两种排序方式，Using index的性能高，而Using filesort的性能低，我们在优化排序操作时，尽量要优化为 Using index。  



* order by优化原则:  

  * 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则  

  * 尽量使用覆盖索引。  

  * 多字段排序, 一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）  

    * ~~~mysql
      create index idx_user_age_phone_ad on tb_user(age asc ,phone desc);
      ~~~

  * 如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小
    sort_buffer_size(默认256k)  

 



### group by优化  



* 在分组操作时，可以通过索引来提高效率。  
* 分组操作时，索引的使用也是满足最左前缀法则的。  



### limit优化  



* 一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化  



案例：

~~~mysql
select t.* from tb_sku t ,
(select id from tb_sku order by idlimit 2000000,10) a 
where t.id = a.id;
~~~



### count优化  



* MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 count(*) 的时候会直接返回这个
  数，效率很高； 但是如果是带条件的count，MyISAM也慢  

* InnoDB 引擎就麻烦了，它执行 count(*) 的时候，需要把数据一行一行地从引擎里面读出
  来，然后累积计数。  

  

* 主要的优化思路：
  * 自己计数(可以借助于redis这样的数据库进行,但是如果是带条件的count又比较麻烦了)。  



count() 是一个聚合函数，对于返回的结果集，一行行地判断，如果 count 函数的参数不是NULL，累计值就加 1，否则不加，最后返回累计值  



| count用 法   | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| count(主 键) | InnoDB 引擎会遍历整张表，把每一行的 主键id 值都取出来，返回给服务层。 服务层拿到主键后，直接按行进行累加(主键不可能为null) |
| count(字 段) | 没有not null 约束 : InnoDB 引擎会遍历整张表把每一行的字段值都取出 来，返回给服务层，服务层判断是否为null，不为null，计数累加。 有not null 约束：InnoDB 引擎会遍历整张表把每一行的字段值都取出来，返 回给服务层，直接按行进行累加。 |
| count(数 字) | InnoDB 引擎遍历整张表，但不取值。服务层对于返回的每一行，放一个数字“1” 进去，直接按行进行累加。 |
| count(*)     | InnoDB引擎并不会把全部字段取出来，而是专门做了优化，不取值，服务层直接 按行进行累加。 |



> 按照效率排序的话，count(字段) < count(主键 id) < count(1) ≈ count(* )，所以尽
> 量使用 count(*)。  





### update优化  

 

* 优化思路：避免行锁升级为表锁（用索引）

  

InnoDB的行锁是针对索引加的锁，不是针对记录加的锁 ,并且该索引不能失效，否则会从行锁升级为表锁 。  



## 视图



视图只保存了查询的SQL逻辑，不保存查询结果。  



### 语法  



* **创建**  

~~~mysql
CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [CASCADED | LOCAL ] CHECK OPTION ]
~~~



* **查询**  

~~~mysql
查看创建视图语句：SHOW CREATE VIEW 视图名称;
查看视图数据：SELECT * FROM 视图名称 ...... ;  
~~~

  

* **修改**  

~~~mysql
方式一：CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH
[ CASCADED | LOCAL ] CHECK OPTION ]
方式二：ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED |
LOCAL ] CHECK OPTION ]
~~~



* **删除** 

 ~~~mysql
 DROP VIEW [IF EXISTS] 视图名称 [,视图名称] ...
 ~~~

 

**案例：**

~~~mysql
-- 创建视图
create or replace view stu_v_1 as select id,name from student where id <= 10;
-- 查询视图
show create view stu_v_1;
select * from stu_v_1;
select * from stu_v_1 where id < 3;
-- 修改视图
create or replace view stu_v_1 as select id,name,no from student where id <= 10;
alter view stu_v_1 as select id,name from student where id <= 10;
-- 删除视图
drop view if exists stu_v_1;
~~~



### 检查选项  



​	当使用WITH CHECK OPTION子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如 插入，更新，删除，以使其符合视图的定义。

~~~mysql
create or replace view stu_v_1 as select id,name from student where id <= 10 with CASCADED/LOCAL check option;
~~~



​	 MySQL允许基于另一个视图创建视图，它还会检查依赖视图中的规则以保持一致性。为了确定检查的范围，mysql提供了两个选项： CASCADED 和 LOCAL，默认值为 CASCADED 。  



**CASCADED**   **级联**  



​	v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 cascaded，但是v1视图创建时未指定检查选项。 则在执行检查时，==不仅==会检查v2，==还==会级联检查v2的关联视图v1。  



![image-20220920175847731](C:\Users\22862\AppData\Roaming\Typora\typora-user-images\image-20220920175847731.png)





**LOCAL**  **本地**  



​	v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 local ，但是v1视图创建时未指定检查选项。 则在执行检查时，==只会==检查v2，==不会==检查v2的关联视图v1。  



![image-20220920175959039](C:\Users\22862\AppData\Roaming\Typora\typora-user-images\image-20220920175959039.png)





### 视图的更新  



​	要使视图可更新，视图中的行与基础表中的行之间必须存在一对一的关系。如果视图包含以下任何一项，则该视图不可更新：  

* 聚合函数或窗口函数（SUM()、 MIN()、 MAX()、 COUNT()等） 
* DISTINCT  
* GROUP BY  
* HAVING  
* UNION 或者 UNION ALL  



**示例演示:**  

~~~mysql
create view stu_v_count as select count(*) from student;
insert into stu_v_count values(10);  -- 报错
~~~





### 视图作用  



1. **简单：**

​	视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视图，从而使得用户不必为以后的操作每次指定全部的条件。  

2. **安全：**

​	数据库可以授权，但不能授权到数据库特定行和特定的列上。通过视图用户只能查询和修改他们所能见到的数据  

3. **数据独立：**

视图可帮助用户屏蔽真实表结构变化带来的影响。



### 案例  



* 为了保证数据库表的安全性，开发人员在操作tb_user表时，只能看到的用户的基本字段，屏蔽手机号和邮箱两个字段。  

```mysql
create view tb_user_view as select id,name,profession,age,gender,status,createtime from tb_user;

select * from tb_user_view;
```



* 查询每个学生所选修的课程（三张表联查），这个功能在很多的业务中都有使用到，为了简化操
  作，定义一个视图。  

~~~mysql
create view tb_stu_course_view as
select s.name student_name , s.no student_no ,c.name course_name from student s, student_course sc , course c where s.id =sc.studentid and sc.courseid = c.id;


select * from tb_stu_course_view;
~~~





## 存储过程  

**（数据库 SQL 语言层面的代码封装与重用）**



​	存储过程是事先经过编译并存储在数据库中的一段 SQL 语句的集合，调用存储过程可以简化应用开发
人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。  



特点:  

* 封装，复用
  * 可以把某一业务SQL封装在存储过程中，需要用到的时候直接调用即可。  

* 可以接收参数，也可以返回数据  
  * 再存储过程中，可以传递参数，也可以接收返回值。  

* 减少网络交互，效率提升  
  * 如果涉及到多条SQL，每执行一次都是一次网络传输。 而如果封装在存储过程中，我们只需要网络交互一次可能就可以了。  





### 基本语法  



* 创建  

 ```mysql
 CREATE PROCEDURE 存储过程名称 ([ 参数列表 ])
 BEGIN
 -- SQL语句
 END ;
 ```

>在命令行中，执行创建存储过程的SQL时，需要通过关键字 delimiter 指定SQL语句的结束符。  



* 调用  

~~~mysql
CALL 名称 ([ 参数 ]);
~~~



* 查看  

~~~mysql
-- 查询指定数据库的存储过程及状态信息
SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = 'xxx'; 

-- 查询某个存储过程的定义
SHOW CREATE PROCEDURE 存储过程名称 ; 
~~~



* 删除  

~~~mysql
DROP PROCEDURE [ IF EXISTS ] 存储过程名称 ；
~~~



**演示示例:**  

~~~mysql
-- 存储过程基本语法
-- 创建
create procedure p1()
begin
select count(*) from student;
end;

-- 调用
call p1();

-- 查看
select * from information_schema.ROUTINES where ROUTINE_SCHEMA = 'itcast';
show create procedure p1;

-- 删除
drop procedure if exists p1;
~~~



### 系统变量  



​	系统变量 是MySQL服务器提供，不是用户定义的，属于服务器层面。分为全局变量（GLOBAL）、会话变量（SESSION）。  



* 查看系统变量  

~~~mysql
-- 查看所有系统变量
SHOW [ SESSION | GLOBAL ] VARIABLES ; 

-- 可以通过LIKE模糊匹配方式查找变量
SHOW [ SESSION | GLOBAL ] VARIABLES LIKE '......'; 

-- 查看指定变量的值
SELECT @@[SESSION | GLOBAL] 系统变量名; 
~~~



* 设置系统变量  

~~~mysql
SET [ SESSION | GLOBAL ] 系统变量名 = 值 ;
SET @@[SESSION | GLOBAL]系统变量名 = 值 ;
~~~

​	如果没有指定SESSION/GLOBAL，默认是SESSION，会话变量  



> mysql服务重新启动之后，所设置的全局参数会失效，要想不失效，可以在 /etc/my.cnf 中配置。  



全局变量(GLOBAL):全局变量针对于所有的会话。  

会话变量(SESSION): 会话变量针对于单个会话，在另外一个会话窗口就不生效了。  



**演示示例:**  

~~~mysql
-- 查看系统变量
show session variables ;

show session variables like 'auto%';

show global variables like 'auto%';

select @@global.autocommit;

select @@session.autocommit;


-- 设置系统变量
set session autocommit = 1;

insert into course(id, name) VALUES (6, 'ES');

set global autocommit = 0;

select @@global.autocommit;
~~~





### 用户定义变量  

​	用户定义变量是用户根据需要自己定义的变量，用户变量不用提前声明，在用的时候直接用 "@变量名" 使用就可以。其作用域为当前连接。  



* 赋值 
  

方式一: 
~~~mysql
SET @var_name = expr [, @var_name = expr] ... ;
SET @var_name := expr [, @var_name := expr] ... ;
~~~

赋值时，可以使用 = ，也可以使用 := 。  



  方式二:  

~~~mysql
SELECT @var_name := expr [, @var_name := expr] ... ;
SELECT 字段名 INTO @var_name FROM 表名;
~~~



* 使用  

~~~mysql
 SELECT @var_name ;
~~~



> 户定义的变量无需对其进行声明或初始化，只不过获取到的值为NULL。  



**演示示例:**  

~~~mysql
-- 赋值
set @myname = 'itcast';
set @myage := 10;
set @mygender := '男',@myhobby := 'java';
select @mycolor := 'red';
select count(*) into @mycount from tb_user;

-- 使用
select @myname,@myage,@mygender,@myhobby;
select @mycolor , @mycount;
select @abc;
~~~



### 局部变量  

​	局部变量是根据需要定义的在局部生效的变量，访问之前，需要DECLARE声明。可用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的BEGIN ... END块。  



* 声明  

~~~mysql
DECLARE 变量名 变量类型 [DEFAULT ... ] ;
~~~



* 赋值  

~~~mysql
SET 变量名 = 值 ;
SET 变量名 := 值 ;
SELECT 字段名 INTO 变量名 FROM 表名 ... ;
~~~



**演示示例:**  

~~~mysql
-- 声明局部变量 - declare
-- 赋值
create procedure p2()
begin
	declare stu_count int default 0;
	select count(*) into stu_count from student;
	select stu_count;
end;

call p2();
~~~



### if  



**语法结构 :**

~~~mysql
IF 条件1 THEN
.....
ELSEIF 条件2 THEN -- 可选
.....
ELSE -- 可选
.....
END IF;
~~~



### 参数 

​	参数的类型，主要分为以下三种：IN、OUT、INOUT。 具体的含义如下：  

| 类型  | 含义                                         | 备注 |
| ----- | -------------------------------------------- | ---- |
| IN    | 该类参数作为输入，也就是需要调用时传入值     | 默认 |
| OUT   | 该类参数作为输出，也就是该参数可以作为返回值 |      |
| INOUT | 既可以作为输入参数，也可以作为输出参数       |      |



**用法：**  

~~~mysql
CREATE PROCEDURE 存储过程名称 ([ IN/OUT/INOUT 参数名 参数类型 ])
BEGIN

-- SQL语句

END ;
~~~



**案例：**

~~~mysql
create procedure p5(inout score double)
begin
set score := score * 0.5;
end;
set @score = 198;

call p5(@score);

select @score;
~~~



### case  



* **语法1：**  （switch case）

~~~mysql
-- 含义： 当case_value的值为 when_value1时，执行statement_list1，当值为 when_value2时，执行statement_list2， 否则就执行 statement_list
CASE case_value

WHEN when_value1 THEN statement_list1
[ WHEN when_value2 THEN statement_list2] ...
[ ELSE statement_list ]

END CASE;
~~~





* **语法2：**  （if else）

~~~mysql
-- 含义： 当条件search_condition1成立时，执行statement_list1，当条件search_condition2成立时，执行statement_list2， 否则就执行 statement_list
CASE

WHEN search_condition1 THEN statement_list1
[WHEN search_condition2 THEN statement_list2] ...
[ELSE statement_list]

END CASE;
~~~



> 如果判定条件有多个，多个条件之间，可以使用 and 或 or 进行连接。  



### while  

~~~mysql
-- 先判定条件，如果条件为true，则执行逻辑，否则，不执行逻辑
WHILE 条件 DO
.......				-- SQL逻辑
END WHILE;
~~~



### repeat  

~~~mysql
-- 先执行一次逻辑，然后判定UNTIL条件是否满足，如果满足，则退出。如果不满足，则继续下一次循环
REPEAT
.......				-- SQL逻辑

UNTIL 条件

END REPEAT;
~~~



### loop

* LOOP可以配合一下两个语句使用：  
  * *LEAVE* ：配合循环使用，退出循环。  
  * *ITERATE*：必须用在循环中，作用是跳过当前循环剩下的语句，直接进入下一次循环。  



~~~mysql
[begin_label:] LOOP
.......				-- SQL逻辑
END LOOP [end_label];


LEAVE label; -- 退出指定标记的循环体
ITERATE label; -- 直接进入下一次循环
~~~

上述语法中出现的 begin_label，end_label，label 指的都是我们所自定义的标记。  



**案例 ：**

~~~mysql
-- A. 定义局部变量, 记录累加之后的值;
-- B. 每循环一次, 就会对n进行-1 , 如果n减到0, 则退出循环 ----> leave xx

create procedure p9(in n int)
begin
	declare total int default 0;
	
	
	sum:loop
		if n<=0 then
			leave sum;
		end if;
		set total := total + n;
		set n := n - 1;
	end loop sum;


	select total;
end;

call p9(100);
~~~



### 游标  



​	游标（CURSOR）是用来存储查询结果集的数据类型 , 在存储过程和函数中可以使用游标对结果集进行循环的处理。游标的使用包括游标的声明、OPEN、FETCH 和 CLOSE，其语法分别如下  



* 声明游标

~~~mysql
DECLARE 游标名称 CURSOR FOR 查询语句 ;
~~~



* 打开游标  

~~~mysql
OPEN 游标名称 ;
~~~



* 获取游标记录  

~~~mysql
FETCH 游标名称 INTO 变量 [, 变量 ] ;
~~~



* 关闭游标  

~~~mysql
CLOSE 游标名称 ;
~~~



**案例**  

​	根据传入的参数uage，来查询用户表tb_user中，所有的用户年龄小于等于uage的用户姓名（name）和专业（profession），并将用户的姓名和专业插入到所创建的一张新表(id,name,profession)中。  

~~~mysql
-- 逻辑:
-- A. 声明游标, 存储查询结果集
-- B. 准备: 创建表结构
-- C. 开启游标
-- D. 获取游标中的记录
-- E. 插入数据到新表中
-- F. 关闭游标
create procedure p11(in uage int)
begin
	declare uname varchar(100);
	declare upro varchar(100);
	declare u_cursor cursor for select name,profession from tb_user where age <= uage;
	
	drop table if exists tb_user_pro;
	create table if not exists tb_user_pro(
		id int primary key auto_increment,
		name varchar(100),
		profession varchar(100)
	);
	
	open u_cursor;
	
	while true do
		fetch u_cursor into uname,upro;
		insert into tb_user_pro values (null, uname, upro);
	end while;
	
	close u_cursor;
end;

call p11(30);
~~~



### 条件处理程序  



~~~mysql
DECLARE handler_action HANDLER FOR condition_value [, condition_value]... statement ;
~~~

* handler_action 的取值：  

  * CONTINUE: 继续执行当前程序  
  * EXIT: 终止执行当前程序  

  

* condition_value 的取值：  
  * SQLSTATE sqlstate_value: 状态码，如 02000  
    * SQLWARNING: 所有以01开头的SQLSTATE代码的简写  
    * NOT FOUND: 所有以02开头的SQLSTATE代码的简写  
    * SQLEXCEPTION: 所有没有被SQLWARNING 或 NOT FOUND捕获的SQLSTATE代码的简写  



**案例：**

~~~mysql
-- 声明条件处理程序 ： 当SQL语句执行抛出的状态码为02000时，将关闭游标u_cursor，并退出
declare exit handler for SQLSTATE '02000' close u_cursor;

-- 声明条件处理程序 ： 当SQL语句执行抛出的状态码为02开头时，将关闭游标u_cursor，并退出
declare exit handler for not found close u_cursor;
~~~



> 具体的错误状态码，可以参考官方文档：
> https://dev.mysql.com/doc/refman/8.0/en/declare-handler.html
> https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html  



### 存储函数  



​	存储函数是有返回值的存储过程，存储函数的参数只能是IN类型的。



~~~mysql
CREATE FUNCTION 存储函数名称 ([ 参数列表 ])
RETURNS type [characteristic ...]
BEGIN
....     -- SQL语句
RETURN ...;
END ;
~~~

* characteristic说明：  
  * DETERMINISTIC：相同的输入参数总是产生相同的结果  
  * NO SQL ：不包含 SQL 语句  
  * READS SQL DATA：包含读取数据的语句，但不包含写入数据的语句。  
  



> 在mysql8.0版本中binlog默认是开启的，一旦开启了，mysql就要求在定义存储过程时，需要指定characteristic特性，否则就会报如下错误：  



## 触发器  



​	触发器是与表有关的数据库对象，指在insert/update/delete之前(BEFORE)或之后(AFTER)，触发并执行触发器中定义的SQL语句集合。

​	触发器的这种特性可以协助应用在数据库端确保数据的完整性, 日志记录 , 数据校验等操作 。

​	使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。

​	mysql还只支持行级触发，不支持语句级触发。  



| 触发器类型      | NEW 和 OLD                                              |
| --------------- | ------------------------------------------------------- |
| INSERT 型触发器 | NEW 表示将要或者已经新增的数据                          |
| UPDATE 型触发器 | OLD 表示修改之前的数据 , NEW 表示将要或已经修改后的数据 |
| DELETE 型触发器 | OLD 表示将要或者已经删除的数据                          |



* 创建  

```mysql
CREATE TRIGGER trigger_name
BEFORE/AFTER INSERT/UPDATE/DELETE
ON tbl_name FOR EACH ROW -- 行级触发器

BEGIN
	trigger_stmt ;
END;
```



* 查看  

~~~mysql
SHOW TRIGGERS ;
~~~



* 删除  

~~~ mysql
DROP TRIGGER [schema_name.]trigger_name ; -- 如果没有指定 schema_name，默认为当前数据库 。
~~~



## 锁  



* MySQL中的锁，按照锁的粒度分，分为以下三类：  
  * 全局锁：锁定数据库中的所有表。  
  * 表级锁：每次操作锁住整张表。  
  * 行级锁：每次操作锁住对应的行数据。  



### 全局锁  



​	对整个数据库实例加锁，加锁后整个实例就处于只读状态 。

​	其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性。  



**语法**  



* 加全局锁  

~~~mysql
flush tables with read lock ;
~~~



* 释放锁  

~~~mysql
unlock tables ;
~~~



* 全局锁存在问题：  

  * 如果在主库上备份，那么在备份期间都不能执行更新 

  * 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导致主从延迟。  



> 在InnoDB引擎中，我们可以在备份时加上参数 --single-transaction 参数来完成不加锁的一致性数据备份  



### 表级锁  



​	锁定粒度大，发生锁冲突的概率最高，并发度最低。应用在MyISAM、InnoDB、BDB等存储引擎中。  



* 对于表级锁，主要分为以下三类：  
  * 表锁  
  * 元数据锁（meta data lock，MDL）  
  * 意向锁  



#### 表锁  



* 对于表锁，分为两类：  
  * 表共享读锁（read lock）  *阻塞写*  
  * 表独占写锁（write lock）  *阻塞读和写*  





**语法：**  



* 加锁：  

~~~mysql
lock tables 表名... read/write。
~~~



**释放锁：**  

~~~mysql
unlock tables / 客户端断开连接 。
~~~



#### 元数据锁  



元数据锁 （meta data lock）简写MDL。  （避免DML与DDL冲突，保证读写的正确性。）



​	MDL加锁过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上。

​	MDL锁主要作用是维护表元数据的数据一致性，在表上有活动事务的时候，不可以对元数据进行写入操作。

​	（元数据可以简单理解为就是一张表的表结构  ）



​	在MySQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁(共享)；当对表结构进行变更操作的时候，加MDL写锁(排他)。  





| 对应SQL                                         | 锁类型                                  | 说明                                               |
| ----------------------------------------------- | --------------------------------------- | -------------------------------------------------- |
| lock tables xxx read / write                    | SHARED_READ_ONLY / SHARED_NO_READ_WRITE |                                                    |
| select 、select ... lock in share mode          | SHARED_READ                             | 与SHARED_READ、 SHARED_WRITE兼容，与 EXCLUSIVE互斥 |
| insert 、update、 delete、select ... for update | SHARED_WRITE                            | 与SHARED_READ、 SHARED_WRITE兼容，与 EXCLUSIVE互斥 |
| alter table ...                                 | EXCLUSIVE  【MDL写锁(排他)】            | 与其他的MDL都互斥                                  |





查看数据库中的元数据锁情况：  

~~~mysql
select object_type,object_schema,object_name,lock_type,lock_duration from
performance_schema.metadata_locks ;
~~~



#### 意向锁  



​	为了避免DML在执行时，加的行锁与表锁的冲突，在InnoDB中引入了意向锁，使得表锁不用检查每行数据是否加锁，使用意向锁来减少表锁的检查。  



* 分类：
  * 意向共享锁(IS): 与 表锁共享锁(read)兼容，与表锁排他锁(write)互斥。  
  * 意向排他锁(IX): 与表锁共享锁(read)及排他锁(write)都互斥，意向锁之间不会互斥。  

 语法：



* 意向共享锁(IS):

~~~mysql
select ... lock in share mode
~~~



* 意向排他锁(IX):  

~~~mysql
insert、update、delete、select...for update
~~~



一旦事务提交了，意向共享锁、意向排他锁，都会自动释放。  



* 可以通过以下SQL，查看意向锁及行锁的加锁情况：  

~~~mysql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from
performance_schema.data_locks;
~~~



### 行级锁  



​	锁定粒度最小，发生锁冲突的概率最低，并发度最高。

​	行锁是通过对**索引**上的索引项加锁来实现的，而不是对记录加的锁。  



对于行级锁，主要分为以下三类：  



* 行锁（Record Lock）：

锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。   

![image-20220922190326125](C:\Users\22862\Desktop\笔记\MYSQL.assets\image-20220922190326125.png)



* 间隙锁（Gap Lock）：  

锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙进行insert，产生幻读。在RR隔离级别下都支持。  

![image-20220922190556370](C:\Users\22862\Desktop\笔记\MYSQL.assets\image-20220922190556370.png)



* 临键锁（Next-Key Lock）：  

行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap。在RR隔离级别下支持。  

![image-20220922190650068](C:\Users\22862\Desktop\笔记\MYSQL.assets\image-20220922190650068.png)





#### 行锁  



InnoDB实现了以下两种类型的行锁：  

* 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排它锁。  
* 排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁。  

![image-20220922191215368](C:\Users\22862\Desktop\笔记\MYSQL.assets\image-20220922191215368.png)





| SQL                           | 行锁类型    | 说明                                     |
| ----------------------------- | ----------- | ---------------------------------------- |
| INSERT ...                    | 排他锁      | 自动加锁                                 |
| UPDATE ...                    | 排他锁      | 自动加锁                                 |
| DELETE ...                    | 排他锁      | 自动加锁                                 |
| SELECT（正常）                | 不加任何 锁 |                                          |
| SELECT ... LOCK IN SHARE MODE | 共享锁      | 需要手动在SELECT之后加LOCK IN SHARE MODE |
| SELECT ... FOR UPDATE         | 排他锁      | 需要手动在SELECT之后加FOR UPDATE         |



* 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁  
* **InnoDB的行锁是针对于索引加的锁，不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时就会升级为表锁。**  



* 查看意向锁及行锁的加锁情况：  

~~~mysql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from
performance_schema.data_locks;
~~~



#### 间隙锁&临键锁  



默认情况下，InnoDB在 ==REPEATABLE READ==事务隔离级别运行，InnoDB使用==临键锁（Next-Key Lock）==进行搜索和索引扫描，以防止幻读  



* 索引上的等值查询(唯一索引)，给不存在的记录加锁时, 优化为间隙锁 。  
* 索引上的等值查询(非唯一普通索引)，向右遍历时最后一个值不满足查询需求时，临键锁退化为间隙锁。 

* 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。  

> 间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁。  



## InnoDB引擎  



### 逻辑存储结构  

![image-20220922194456189](C:\Users\22862\Desktop\笔记\MYSQL.assets\image-20220922194456189.png)



* 表空间  

​	表空间是InnoDB存储引擎逻辑结构的最高层， 如果用户启用了参数 innodb_file_per_table(在8.0版本中默认开启) ，则每张表都会有一个表空间（xxx.ibd），一个mysql实例可以对应多个表空间，用于存储记录、索引等数据。  

* 段 

​	段，分为数据段（Leaf node segment）、索引段（Non-leaf node segment）、回滚段（Rollback segment），InnoDB是索引组织表，数据段就是B+树的叶子节点， 索引段即为B+树的非叶子节点。段用来管理多个Extent（区）。

* 区  

​	区，表空间的单元结构，每个区的大小为1M。 默认情况下， InnoDB存储引擎页大小为16K， 即一个区中一共有64个连续的页。  

* 页  

​	页，是InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为 16KB。为了保证页的连续性，InnoDB 存储引擎每次从磁盘申请 4-5 个区。  

* 行  

​	行，InnoDB 存储引擎数据是按行进行存放的。  





* 在行中，默认有两个隐藏字段：  
  * *Trx_id*：每次对某条记录进行改动时，都会把对应的事务id赋值给trx_id隐藏列。  
  * *Roll_pointer*：每次对某条引记录进行改动时，都会把旧的版本写入到undo日志中，然后这个隐藏列就相当于一个指针，可以通过它来找到该记录修改前的信息。  



### 内存结构



![image-20220922195629563](C:\Users\22862\Desktop\笔记\MYSQL.assets\image-20220922195629563.png)





*  在内存结构中，主要分为这么四大块儿： *Buffer Pool、Change Buffer、AdaptiveHash Index、Log Buffer。*



* *Buffer Pool*  （缓冲池）
  * **作用**：弥补硬盘和内存I/O效率的差值，避免每次访问都进行磁盘I/O。  
  
  * **内容**：在InnoDB的缓冲池中不仅缓存了索引页和数据页，还包含了undo页、插入缓存、自适应哈希索引以及InnoDB的锁信息等等。  
  
  * **结构**：缓冲池以Page页为单位，底层采用链表数据结构管理Page。根据状态，将Page分为三种类型：  
    * free page：空闲page，未被使用  
    * clean page：被使用page，数据没有被修改过。  
    * dirty page：脏页，被使用page，数据被修改过，当中数据与磁盘的数据产生了不一致  



> 在专用服务器上，通常将多达80％的物理内存分配给缓冲池 。参数设置： 
>
> ~~~mysql
> show variableslike 'innodb_buffer_pool_size';  
> ~~~





* *Change Buffer* （更改缓冲区 -->针对于非唯一二级索引页 ）

  * **存在原因**：二级索引通常是非唯一的，并且以相对随机的顺序插入二级索引。删除和更新可能会影响索引树中不相邻的二级索引页 ，如果每一次都操作磁盘，会造成大量的磁盘IO。  

  * **作用**：增、删、改操作数据不在*Buffer Pool* 时，将数据变更存在*Change Buffer*。在未来数据被读取时，将数据合并恢复到Buffer Pool中，再将合并后的数据刷新到磁盘中。  （减少磁盘IO ）

  * **内容**：被增、删、改数据





* *Adaptive Hash Index* （自适应hash索引  ）
  * **作用**：优化对Buffer Pool数据的查询。  

> 自适应哈希索引，无需人工干预，是系统根据情况自动完成。  





* *Log Buffer* （日志缓冲区  ）

  * **内容**：保存要写入到磁盘中的log日志数据（redo log 、undo log）

  * **作用**：默认大小为 16MB ，定期刷新到磁盘中。如果需要更新、插入或删除许多行的事务，增加日志缓冲区的大小可以节省磁盘 I/O。  



### 磁盘结构  

.......



* *System Tablespace*  	
* *File-Per-Table Tablespaces*  
* *General Tablespaces*  
* *Undo Tablespaces*  
* *Temporary Tablespaces*  
* *Doublewrite Buffer Files*  
* *Redo Log*  



### 后台线程  



InnoDB的后台线程分为4类，分别是：

*Master Thread 、IO Thread、Purge Thread、Page Cleaner Thread。*  



* Master Thread 

​	核心后台线程，负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中, 保持数据的一致性，还包括脏页的刷新、合并插入缓存、undo页的回收 。  



* IO Thread  

​	在InnoDB存储引擎中大量使用了AIO来处理IO请求, 这样可以极大地提高数据库的性能，而IO Thread主要负责这些IO请求的回调。  

| 线程类型             | 默认个数 | 职责                         |
| -------------------- | -------- | ---------------------------- |
| Read thread          | 4        | 负责读操作                   |
| Write thread         | 4        | 负责写操作                   |
| Log thread           | 1        | 负责将日志缓冲区刷新到磁盘   |
| Insert buffer thread | 1        | 负责将写缓冲区内容刷新到磁盘 |



* Purge Thread  

​	主要用于回收事务已经提交了的undo log，在事务提交之后，undo log可能不用了，就用它来回收。  



* Page Cleaner Thread  

​	协助 Master Thread 刷新脏页到磁盘的线程，它可以减轻 Master Thread 的工作压力，减少阻塞。  



### redo log  

（重做日志  ）

* 作用：记录事务提交时数据页的物理修改，用来实现事务的持久性。  
* 组成：
  * 重做日志缓冲（redo log buffer）：在内存中  
  * 重做日志文件（redo logfile）： 在磁盘中 。当事务提交之后会把所有修改信息都存到该日志文件中, 用于在刷新脏页到磁盘,发生错误时, 进行数据恢复使用。  

* 工作过程：

​	当对缓冲区的数据进行增删改之后，会首先将操作的数据页的变化，记录在redolog buffer中。在事务提交时，将redo log buffer中的数据刷新到redo log磁盘文件中。

​	一段时间后，如果刷新缓冲区的脏页到磁盘时，发生错误，此时就可以借助于redo log进行数据恢复，这样就保证了事务的持久性。 

​	而如果脏页成功刷新到磁盘 或 或者涉及到的数据已经落盘，此时redolog就没有作用了，就可以删除了，所以存在的两个redolog文件是循环写的。  

![image-20220922211016930](C:\Users\22862\Desktop\笔记\MYSQL.assets\image-20220922211016930.png)



>  为什么每一次提交事务，要刷新redo log 到磁盘中呢，而不是直接将buffer pool中的脏页刷新到磁盘呢 ?  



​	我们操作数据一般都是随机读写磁盘的，而不是顺序读写磁盘。 而redo log在往磁盘文件中写入数据，由于是日志文件，所以都是顺序写的。顺序写的效率，要远大于随机写。 这种先写日志的方式，称之为 WAL（Write-Ahead Logging）。  



### undo log  

（回滚日志）

* 作用：记录数据被修改前的信息 , 作用包含两个 : 提供回滚（保证事务的原子性）和MVCC（多版本并发控制）。  

* 工作过程：undo log和redo log记录物理日志不一样，它是逻辑日志。可以认为当delete一条记录时，undo
  log中会记录一条对应的insert记录，反之亦然。

  ​    当执行rollback时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。  

  

* 销毁：undo log在事务执行时产生，事务提交时，并不会立即删除undo log，因为这些
  日志可能还用于MVCC。  
* 存储：undo log采用段的方式进行管理和记录，存放在前面介绍的 rollback segment
  回滚段中，内部包含1024个undo log segment。  



​	而update、delete的时候，产生的undo log日志不仅在回滚时需要，在快照读时也需要，不会立即被删除。  



* 版本链  

​	不同事务或相同事务对同一条记录进行修改，会导致该记录的undolog生成一条记录版本链表，链表的头部是最新的旧记录，链表尾部是最早的旧记录。  

![image-20220922214259477](C:\Users\22862\Desktop\笔记\MYSQL.assets\image-20220922214259477.png)

### MVCC  

​	

* 当前读  ：

​	读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁。对于我们日常的操作，如：select ... lock in share mode(共享锁)，select ...for update、update、insert、delete(排他锁)都是一种当前读。  

* 快照读 ：

​	简单的select（不加锁）就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据，不加锁，是非阻塞读。  



* Read Committed：每次select，都生成一个快照读。  
* Repeatable Read：开启事务后第一个select语句才是快照读的地方。 （保证了可重复读） 
* Serializable：快照读会退化为当前读。  



* MVCC  

​	全称 Multi-Version Concurrency Control，多版本并发控制。指维护一个数据的多个版本，使得读写操作没有冲突，快照读为MySQL实现MVCC提供了一个非阻塞读功能。MVCC的具体实现，还需要依赖于数据库记录中的三个隐式字段、undo log日志、readView。  



#### 隐藏字段  

| 隐藏字段    | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| DB_TRX_ID   | 最近修改事务ID，记录插入这条记录或最后一次修改该记录的事务ID。 |
| DB_ROLL_PTR | 回滚指针，指向这条记录的上一个版本，用于配合undo log，指向上一个版 本。 |
| DB_ROW_ID   | 隐藏主键，如果表结构没有指定主键，将会生成该隐藏字段。       |



#### ReadView 

（读视图）

​	快照读 SQL执行时MVCC提取数据的依据，记录并维护系统当前活跃的事务（未提交的）id。  



​	ReadView中包含了四个核心字段：

| 字段           | 含义                                                 |
| -------------- | ---------------------------------------------------- |
| m_ids          | 当前活跃的事务ID集合                                 |
| min_trx_id     | 最小活跃事务ID                                       |
| max_trx_id     | 预分配事务ID，当前最大事务ID+1（因为事务ID是自增的） |
| creator_trx_id | ReadView创建者的事务ID                               |

在readview中规定了版本链数据的访问规则：（trx_id 代表当前undolog版本链对应事务ID。）  

| 条件                               | 是否可以访问                               | 说明                                        |
| ---------------------------------- | ------------------------------------------ | ------------------------------------------- |
| trx_id == creator_trx_id           | 可以访问该版本                             | 成立，说明数据是当前这个事 务更改的。       |
| trx_id < min_trx_id                | 可以访问该版本                             | 成立，说明数据已经提交了。                  |
| trx_id > max_trx_id                | 不可以访问该版本                           | 成立，说明该事务是在 ReadView生成后才开启。 |
| min_trx_id <= trx_id <= max_trx_id | 如果trx_id不在m_ids中， 是可以访问该版本的 | 成立，说明数据已经提交。                    |

* READ COMMITTED ：在事务中每一次执行快照读时生成ReadView。  
* REPEATABLE READ：仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView。  



