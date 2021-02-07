# Java 总结

## 一、基础

### 1.1 语法

​	Java 是一门面向对象的编程语言，吸收 c++ 语言的有点，摒弃难以理解的多继承、指针等概念，具有简单性、面向对象、分布式、健壮性、安全性、可移植性等特点

面向对象三大特性： **封装、继承、多态**

访问权限：public 、protected、default、private (**默认权限不能访问包外子类，protected 可以**)

|           | 类内 | 包内 | 不同包子类 | 不同包非子类 |
| --------- | ---- | ---- | ---------- | ------------ |
| public    | √    | √    | √          | √            |
| protected | √    | √    | √          |              |
| default   | √    | √    |            |              |
| private   | √    |      |            |              |

基本数据类型：

| 简单类型 | boolean  | byte | char      | short | int     | long | float | double | void |
| -------- | -------- | ---- | --------- | ----- | ------- | ---- | ----- | ------ | ---- |
| 字节数   | 1 或者 4 | 1    | 2         | 4     | 4       | 8    | 4     | 8      | -    |
| 封装器类 | Boolean  | Byte | Character | Short | Integer | Long | Float | Double | Void |

### 1.2 集合

#### 1.2.1  Set

##### 1.2.1.1 HashSet

##### 1.2.1.2 TreeSet

##### 1.2.1.3 LinkedHashSet

#### 1.2.2 List

#### 1.2.3 Map

##### 1.2.3.1 HashMap

##### 1.2.3.2 TreeMap

##### 1.2.3.3 HashTable

#### 1.2.4 java.util.concurrent



### 1.3 异常

Java 所有的异常都有一个共同的祖先**Throwable** , Throwable 有2个重要的子类 1.Exception 异常   2.Error 错误

> Error 是程序无法处理的错误，表示允许程序出现严重的问题，具有代表的是 OutOfMemoryError 
>
> Exception 是程序本身可以处理的异常，其可以分为 1.运行时异常   2.非运行时异常

![img](https://img-blog.csdnimg.cn/2019101117003396.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI5MjI5NTY3,size_16,color_FFFFFF,t_70)

异常参考博文：https://blog.csdn.net/qq_29229567/article/details/80773970

### 1.4 jvm



### 1.5 jdk 特性

|      | 发布时间 | 特性                                                 |
| ---- | -------- | ---------------------------------------------------- |
| 1.0  | 1996-01  |                                                      |
| 1.1  | 1996-01  |                                                      |
| 1.2  | 1998-12  |                                                      |
| 1.3  | 2005-05  |                                                      |
| 1.4  | 2002-02  | 正则表达式、异常链、NIO、日志类、xml解析             |
| 5.0  | 2004-09  | 自动装箱、动态注解、枚举、可变长参、遍历循环         |
| 6.0  | 2006-04  | 提供动态语言支持、改进jvm锁、同步垃圾回收、类加载    |
| 7.0  | 2011-07  | 提供 G1 收集器、加强对非Java语言的调用支持           |
| 8.0  | 2014-03  | Lambda 表达式、方法引用、默认方法、新时间、Option 类 |
| 9.0  | 2017-09  |                                                      |
| 10.0 | 2018-03  |                                                      |
| 11.0 | 2018-09  |                                                      |



## 二、 进阶

### 2.1 多线程

#### 	2.1.1 锁

#### 	2.1.2 并发容器

#### 	2.1.3 原子类

### 2.2 Aop







## 三、Web



## 四、框架

### 4.1 spring 

### 4.2 mybatis

### 4.3 spring MVC





### 4.4 spring boot



@RestController  默认是单例模式，如果需要使用多例，使用 @Scope("prototype") 

> 单例模式的controller ,使用成员属性不安全，属于会共享
>
> spring boot web 模块是集成了 spring mvc 



### 4.5 spring cloud

spring cloud 是一套规范，具体是实现包括 2种，netflix 实现 与 alibaba 实现

#### 4.5.1 spring cloud netflix

##### 4.5.1.1 eureka

##### 4.5.1.2  zuul

#### 4.5.2 spring cloud alibaba

##### 4.5.2.1  nacos

##### 4.5.2.2 gateway

### 4.6 shiro

### 4.7 Jpa







## 五、中间件

### 5.1 redis





### 5.1 elasticsearch

### 5.3 kafka

### 5.4 zookeeper

### 5.5 dubbo

### 5.6 tomcat

### 5.7 rocketMQ

### 5.8  mongodb

### 5.9 Ehcache



## 六、数据库

### 6.1 mysql

Mysql  是一个开源的关系型数据库管理系统（Releational Database Management System）,现在属于Oracle 公司

#### 6.1.1 创建表

```sql
create table  `student`(
`id` int not null,
`name` varchar(20),
  primary key(id)
) engine = innodb default charset = utf8;
```

在cmd 窗口下删除表

```
mysql -u root -p123456
show databases;
use db;
show tables;
drop table student;
```

#### 6.1.2  curd

插入数据/批量插入

```
insert into student(id,name) values(1,'zhangsan');
```

批量插入

```
 insert into student(id,name) values(2,'lisi'),(3,'wangwu');
```

其他操作类似

查询语句

**as**  为字段取别名

**distinct** 去除重复

**like**  模糊查询，需要与 % 搭配使用

**group by** 分组查询（灵活使用可以当 count 统计）

**having**  对分组数据过滤

**limit**  分页查询，需要接受2个参数，当前记录下标与偏移量 。如果只传递一个值，即查询前 n 个值

**order**  排序，asc 升序， desc 降序。多个字段使用 order  a asc , b desc

**inner join**  连接查询

#### 6.1.3 函数

***数学函数***

| 函数      | 作用                 | 案例                         |
| --------- | -------------------- | ---------------------------- |
| abs()     | 绝对值               | select abs(-10) ;  **10**    |
| ceiling() | 大于当前值的最小整数 | select ceiling(2.6);   **3** |
| floor()   | 小于当前值的最大整数 | select floor(2.6);  **2**    |

***字符串函数***

| 函数                  | 作用       | 案例                                                     |
| --------------------- | ---------- | -------------------------------------------------------- |
| concat(s1,s2)         | 合并字符串 | select * from student where id like concat('%','1','%'); |
| substr(str,begin,end) | 截取字符串 | select substr('zhangsan',1,5);                           |
| lower(str)            | 转换为小写 |                                                          |
| upper(str)            | 转换为大写 |                                                          |

***日期与时间函数***

| 函数          | 作用                           | 案例                                  |
| ------------- | ------------------------------ | ------------------------------------- |
| curdate()     | 获取当前日期                   | select curdate();   2020-20-20        |
| now()         | 获取当前时间                   | select now()；  2020-20-20 10:10:10   |
| sysdate()     | 获取当前时间                   |                                       |
| year()        | 获取指定日期的年，月日分表同理 |                                       |
| date_format() | 格式化时间                     | select date_format(now(),'%Y-%m-%d'); |

***聚合函数***

| 函数    | 作用       | 案例 |
| ------- | ---------- | ---- |
| count() | 返回列统计 |      |
| sum()   | 求和       |      |
| avg()   | 求平均     |      |
| max()   | 求最大值   |      |
| min()   | 求小值     |      |

#### 6.1.4 事务

事务是为了维护数据库完整性，要么成批的 sql 全部执行，要么全部不执行，mysql 只有 innodb 存储引擎支持事务

事务需要满足 4个条件（ACID) 

- 原子性 不可再分割
- 一致性 在事务开始之前与事务结束以后，数据库的完整性没有被破坏 
- 持久性  事务对数据的修改是永久的
- 隔离性  数据库允许多个事务，隔离性可以防止多个事务并发执行时由于交叉执行而导致的不一致 ，因此有***不同的隔离级别*** 
  - 读未提交			<u>最低的隔离级别，一个事务可以读取另一个事务未提交的结果</u>
  - 读提交                <u>大部分数据库采用的隔离级别，一个事务的更新操作结果只有当前事务提交，另一个事务才可以读取</u>
  - 可重复读             <u>myql 默认级别</u>
  - 串行化                <u>最高的隔离级别，所有的事务依次顺序执行，性能最差</u>

mysql 执行事务： begin 、rollback、commit

**事务的隔离级别不同导致的问题**

脏读：事务A 读取被另一个事务 B 修改，但未提交的数据。事务B 回退，则事务A 读取的数据是无效的

不可重复读：一个事务的2次读取，结果不同（另一个事务进行了**修改**，并且commit）

幻读：2次读取的记录数不一样（另一个事务进行了**插入**或**删除**）

**如何解决脏读、不可重复读、幻读？**

脏读发生在 读未提交事务级别，提高到其他等级即可解决

不可重复读发生在读未提交、读已提交，提高到可重复读或序列化即可

幻读发生在读未提交、读已提交、可重复读。只有序列化才可解决

***mysql 如何解决幻读？***

mysql 存储引擎 innodb 在可重读读隔离级别，是会产生幻读,如何解决？通过 next-key lock 在当前事务开启时，通过给涉及到的行加写锁，

行2端加间隙锁防止新增写入

事务博文：https://www.cnblogs.com/balfish/p/8298296.html

#### 6.1.5 索引





### 6.2 oracle

### 6.3 postgreSql



## 七、完整的项目





## 八、计算机基础





## 九、版本控制

### 9.1 git



### 9.2 svn

## 十、运维相关

### 10.1 nginx

### 10.2 jenkins

### 10.3 gitlab



