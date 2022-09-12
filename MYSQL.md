# MYSQL



## 基础查询



### **字段设置别名**


```
SELECT 字段1 [ AS 别名1 ] , 字段2 [ AS 别名2 ] ... FROM 表名; 

SELECT 字段1 [ 别名1 ] , 字段2 [ 别名2 ] ... FROM 表名; 
```





### **去除重复记录**

```
SELECT DISTINCT 字段列表 FROM 表名;
```



**案例：**



* 查询公司员工的上班地址有哪些(不要重复)

```
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

  ```
  select * from emp where idcard is null;
  ```

* 查询有身份证号的员工信息

  ```
  select * from emp where idcard is not null;
  ```

*  查询年龄不等于 88 的员工信息

  ```
  select * from emp where age != 88;
  select * from emp where age <> 88;
  ```

* 查询年龄在15岁(包含) 到 20岁(包含)之间的员工信息

  ```
  select * from emp where age between 15 and 20;
  ```

* 查询年龄等于18 或 20 或 40 的员工信息

  ```
  select * from emp where age in(18,20,40);
  ```

* 查询姓名为两个字的员工信息

  ```
  select * from emp where name like '__';
  ```

*  查询身份证号最后一位是X的员工信息

  ```
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

```
SELECT 聚合函数(字段列表) FROM 表名 ;
```

> 注意 : NULL值是不参与所有聚合函数运算的。



### **分组查询**

**语法:**

```
SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组 后过滤条件 ];
```



 **where与having区别:**

* 执行时机不同：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤。

* 判断条件不同：where不能对聚合函数进行判断，而having可以。

  

**案例:**



查询年龄小于45的员工 , 并根据工作地址分组 , 获取员工数量大于等于3的工作地址

```
select workaddress, count(*) address_count from emp where age < 45 group by workaddress having address_count >= 3
```



### **排序查询**



**语法:**

```
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ;
```

**排序方式:**

* ASC : 升序(默认值)
* DESC: 降序



### **分页查询**



**语法:**

```
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

```
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

```
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

```
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

```
select name,
( case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end ) as '工作地址' 
from emp;
```



```
selectid, name,
(case when math >= 85 then '优秀' when math >=60 then '及格' else '不及格' end ) '数学',
(case when english >= 85 then '优秀' when english >=60 then '及格' else '不及格' end ) '英语',
(case when chinese >= 85 then '优秀' when chinese >=60 then '及格' else '不及格' end ) '语文' 
from score;
```



### 约束

