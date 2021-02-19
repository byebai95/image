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

![img](https://www.runoob.com/wp-content/uploads/2014/01/2243690-9cd9c896e0d512ed.gif)

####  ArrayList 

ArrayList 是一个数组队列，相当于动态数组，提供了随机访问，支持序列化

默认未指定初始化容量为 10 

**扩容** 

ArrayList 新增元素都会校验数据是否安全存储，如果插入需要的最小容量小于数组长度，则**进行扩容**   grow 

扩容的新的长度为之前容量的 **1.5** 倍 ，最大不能超过 **Integer.MAX_VALUE**

ArrayList 本质还是 Object[] , 扩容的本质是创建一个新长度的数组，将旧的数据拷贝到新的数组

```java
Arrays.copyOf()
```

####  LinkedList

LinkedList 是一种**双向循环链表**，内部包含**头指针、尾指针** 。可以通过链表实现队列、栈

```
push()  等价于  addFirst()
pop()   等价于  removeFirst()
poll()  查询并移除第一个元素
peek()  查询第一个元素
```

![img](https://images2015.cnblogs.com/blog/249993/201611/249993-20161125134834096-1280829764.jpg)

> 对比 ArrayList 与 LinkedList 
>
> 1. ArrayList 提供随机访问，可以快速访问，增删效率较第，需要移动批量数据，LinkedList 不能随机访问，增删效率较高
> 2. 使用容量LinkedList 较大，每个数据节点都存储2个引用的数据

#### **Vector**

Vector 是 ArrayList 线程安全的容器，但与 ArrayList 不同，可以设置扩容增量

**扩容**

如果未设置增量，则扩容为原本的**2倍**

> vector 增加元素，get 获取元素都使用了 synchronized ，效率低
>
> 初始化未指定容量，则默认为 10 ，未指定增量默认为 0

#### HashMap 

HashMap 是一个**散列表**，存储的是键值对 key-value 的映射 

1. HashMap 根据 HashCode 值存储数据，具有很高的访问速度
2. 允许一条记录的键为 null
3. 无序，线程不安全
4. 基本结构**数组+链表**

**散列表**

散列表也叫哈希表，在哈希表中进行添加、查找、删除等操作，性能非常高，在不考虑哈希冲突的情况下，仅需要一次定位即可完成，时间复杂度 O(1)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181102221702492.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvc2hpbWF4aWFvMQ==,size_16,color_FFFFFF,t_70)

HashMap 是由数组+链表组成的，数组是HashMap 的主体，链表则是为了解决哈希冲突而存在的。如果定位到的位置不含元素，则查找、添加一次即完成。

如果包含链表，则添加操作的时间复杂度 O(n) ，首先遍历列表，存在即覆盖，否则新增。对于查询，需要逐一通过 key 对象比对查找。考虑到性能，*HashMap*

*中链表出现越少，性能越好*

**存储原理**

几个重要的参数

```java
/**
* 加载因子，代表 table 的填充度
*/
static final float DEFAULT_LOAD_FACTOR = 0.75f;

/**
* 默认初始化容量 16
*/
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

/**
* 最大容量
*/
static final int MAXIMUM_CAPACITY = 1 << 30;

/**
* 链表转换为树的阈值
*/
static final int TREEIFY_THRESHOLD = 8;

/**
* 树退化为链表的阈值
*/
static final int UNTREEIFY_THRESHOLD = 6;

/**
* 阈值， 阈值 = 容量 * 加载因子
*/
int threshold;

/**
* 链表转换为红黑树的条件，链表节点数大于等于 8 且 数组容量大于等于 64
*/
static final int MIN_TREEIFY_CAPACITY = 64;
```

HashMap 存储的流程

1. 计算该值的 hashCode 
2. 通过 hashCode 参与位运算得到 hash值
3. 通过 hash 值与 table 长度获取存储下标
4. 根据下标确定位置，如果发生哈希冲突，同 equal 判断 key 是否冲突

```java
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

![HashMap如何确定元素位置](https://img-blog.csdnimg.cn/20181102214046362.png)

> HashMap 中对象是否可以作为 key 存储？
>
> 可以，但是必须要重写 equals() 与 hashCode(), 如果不重写，会导致不同的对象（属性参数均相同），获取的值是不一样的。
>
> 因为不同的对象的hashCode 是不一样的

**扩容**

HashMap 的容量始终保持是2的幂次方，目的是在 hash 值与 length -1 计算，保在 length -1 的地位全为 1 ，此外也保证在调整链表幅度在小范围内

**jdk 1.8 优化**

在 jdk 1.7 中链表过长会导致效率下降， jdk1.8 在链表超过 8 时，链表转换为红黑树

https://blog.csdn.net/woshimaxiao1/article/details/83661464

### 1.3 异常

Java 所有的异常都有一个共同的祖先**Throwable** , Throwable 有2个重要的子类 1.Exception 异常   2.Error 错误

> Error 是程序无法处理的错误，表示允许程序出现严重的问题，具有代表的是 OutOfMemoryError 
>
> Exception 是程序本身可以处理的异常，其可以分为 1.运行时异常   2.非运行时异常

![img](https://img-blog.csdnimg.cn/2019101117003396.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI5MjI5NTY3,size_16,color_FFFFFF,t_70)

异常参考博文：https://blog.csdn.net/qq_29229567/article/details/80773970

### 1.4 JVM

JVM( Java Virtual Machine ，Java虚拟机 )

![img](https://pic1.zhimg.com/v2-bf438382f881edb81d49a2ddb8888d68_b.png)

### 1.5 JMM

JMM( Java Memory Model , Java 内存模型 )

https://zhuanlan.zhihu.com/p/29881777

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

#### 	 锁

#### 	并发容器

#### 	 原子类

#### ThreadLocal 了解

### 2.2 Aop







## 三、Web



## 四、框架

### 4.1 spring 

Bean 的生命周期



### 4.2 mybatis

### 4.3 spring MVC

![img](https://images2015.cnblogs.com/blog/249993/201612/249993-20161212142542042-2117679195.jpg)



> @RestController  默认是单例模式，如果需要使用多例，使用 @Scope("prototype") 
>
> 单例模式的controller ,使用成员属性不安全，属于会共享
>
> spring boot web 模块是集成了 spring mvc 

### 4.4 spring boot

#### 4.4.1 核心注解

spring boot 核心注解 **@SpringBootApplication** 包括三个注解组成

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
```

@SpringBootConfiguration = @Configuration 

@EnableAutoConfiguration 包括 2个注解

```Java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
```

@AutoConfigurationPackage  作用是配置启动类所在的包

AutoConfigurationImportSelector 自动配置选择器，加载需要导入的配置

#### 4.4.1 自动装配原理

spring boot 提倡阅定大于配置， 默认的加载配置文件

```java
application.yml

application.yaml

application.properties
```

配置文件中指定配置属性通过 **@ConfigurationProperties(prefix = "spring.redis")** 注入，再加载到自动配置文件中

spring boot 自动装配原理：https://zhuanlan.zhihu.com/p/95217578



### 4.5 spring cloud

spring cloud 是一套规范，具体是实现包括 2种，netflix 实现 与 alibaba 实现

#### 4.5.1 spring cloud netflix

##### 4.5.1.1 eureka

##### 4.5.1.2  zuul

#### 4.5.2 spring cloud alibaba

##### 4.5.2.1  nacos

##### 4.5.2.2 gateway

### 4.6 shiro

### 4.8 Rpc 原理

rpc ( Remote Procedure Call ,远程过程调用)， RPC 是一种通信协议，一个服务调用另一个服务就像在调用本地服务一样，而不用关注网络通信的细节 。

实现 rpc 的技术与架构： 

> 采用 Socket 通信、Java 动态代理、反射、序列化

**序列化**即将对象转换为二进制流的过程

**反序列化**即将二进制流转换为对象的过程

rpc 需要关注的三个技术点：

1. 服务寻址
2. 数据流的序列化与反序列化
3. 网络传输

服务寻址即所有的函数都有自己的id,这个id是唯一确定的，网络传输大部分是通过 tcp 协议，但其实 UDP 也是可以的

**通信 BIO /NIO /AIO**



## 五、中间件

### 5.1 Redis

> Redis 是一个高性能的 Nosql 内存数据库，支持数据的持久化，可以将内存的数据保存在磁盘中，重启的时候可以再次加载进行使用
>
> Redis 不仅仅支持简单的 key-value 类型的数据，同时提供 list、set、zset、hash 等数据结构的存储
>
> Redis 支持数据的备份， 即 master - slave 模式

#### 5.1.1 下载安装

官网：https://redis.io/

下载地址：https://github.com/tporadowski/redis/tags

安装：https://www.runoob.com/redis/redis-install.html

#### 5.1.2 基本语法

Redis 支持五种数据类型： string 、hash、list 、set、zset

| 类型                 | 简介                                                   | 特性                                                         | 场景                                                         |
| :------------------- | :----------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| String(字符串)       | 二进制安全                                             | 可以包含任何数据,比如jpg图片或者序列化的对象,一个键最大能存储512M | ---                                                          |
| Hash(字典)           | 键值对集合,即编程语言中的Map类型                       | 适合存储对象,并且可以像数据库中update一个属性一样只修改某一项属性值(Memcached中需要取出整个字符串反序列化成对象修改完再序列化存回去) | 存储、读取、修改用户属性                                     |
| List(列表)           | 链表(双向链表)                                         | 增删快,提供了操作某一段元素的API                             | 1,最新消息排行等功能(比如朋友圈的时间线) 2,消息队列          |
| Set(集合)            | 哈希表实现,元素不重复                                  | 1、添加、删除,查找的复杂度都是O(1) 2、为集合提供了求交集、并集、差集等操作 | 1、共同好友 2、利用唯一性,统计访问网站的所有独立ip 3、好友推荐时,根据tag求交集,大于某个阈值就可以推荐 |
| Sorted Set(有序集合) | 将Set中的元素增加一个权重参数score,元素按score有序排列 | 数据插入集合时,已经进行天然排序                              | 1、排行榜 2、带权重的消息队列                                |

#### 5.1.3 持久化

> Redis 是一个内存数据库，数据保存在内存中，断电即丢失。因此提供了持久化机制，分别是RDB、AOF

持久化流程

1. 客户端向服务端发送写操作（数据在客户端内存）
2. 服务端收到写请求是数据（数据在服务端内存）
3. 服务端调用write,将数据往磁盘上写（数据在系统内存的缓冲区中）
4. 操作系统将缓冲区中的数据转移到磁盘控制器上（数据在磁盘缓存中）
5. 磁盘控制器将数据写到磁盘物理介质中

以上是理想的情况下保存的流程，在很多情况下机器会发送各种故障

1. redis 数据库发生故障
2. 操作系统发生故障

##### **RDB**

RDB 是把数据以快照的形式保存在磁盘上，（快照：当前时刻的数据）,RDB 持久化是指在指定的时间间隔内将内存中的数据集快照写入磁盘，是Redis 默认的持久化方式，将数据写入二进制文件中，默认的文件名 dump.rdb

RDB 提供了三种保存机制： **save、bgsave**

**save 触发方式**

该命令会阻塞当前Redis 服务去，执行save 命令期间，Redis 不能处理其他命令，指导RDB 过程完成

![img](https://pics1.baidu.com/feed/e7cd7b899e510fb3aa8c05042b22c093d0430ca7.jpeg?token=7ed4cf784a82d04e60b8dc72cf7e3c24&s=EDBAA5565D1859C85444707E02005071)

执行的过程中如果有老的RDB 文件，就把新的替代旧的。（该方式不可取）

**bgsave 触发方式**

该模式Redis 会在后台异步进行快照操作，快照同时还可以响应客户端请求

![img](https://pics5.baidu.com/feed/023b5bb5c9ea15cefb035bc8431132f53b87b21e.jpeg?token=a72f072d65d2de548d71bb459cd0bf4f&s=05AAFE168FF04C8A10FD2DEE0300E032)

Redis 进程执行 fork 操作创建子进程，RDB 持久化过程由子进程负责，完成后自动结束。阻塞只发生在 fork 阶段，一般时间很短。基本上Redis 内存所有的RDB 操作都是采用 bgsave 命令

**触发时间**

在 redis.conf 文件中，rdb 核心配置

```xml
save 900 1
save 300 10
save 60 10000
```

##### AOF 机制

全量备份总是耗时的，Redis 提供了更加有效的方式 AOF ,将每一个收到写命令都通过write 函数追加到文件中。

![img](https://pics3.baidu.com/feed/32fa828ba61ea8d3c2502e396b1b3848251f58b0.jpeg?token=394597ccd73bd15778c518b5c5be6998&s=2D62E7169D305F8A847546E20200B036)

每当有一个写命令过来时，都会保存在 aof 文件中

AOF  bgrewriteaof ,将内存中的数据以命令的方式保存到临时文件中，同时 fork 一条新的进程将文件重写

![img](https://pics7.baidu.com/feed/09fa513d269759ee28454d2c4cea4b106c22dfd3.jpeg?token=86eda46b8bcd54a7a0e7d8a37d87bee8&s=EDB2A4579D317B824660D4DF0200E036)

**AOF 触发机制**

1. always 每次变化都持久化，性能比较差
2. everysec  异步操作，每秒记录，可能丢失数据
3. no  不同步

**RDB 与 AOF 比较**

RDB 的优点是全量备份，恢复数据块，数据紧凑，缺点是在持久化期间修改的数据不会被保存

AOF   可以更好的保护数据不丢失，性功高，缺点是文件更大，一般配置每秒备份

**选择RDB 还是 AOF ,或者结合一起使用根据不同的环境需求而定**

#### 5.1. 发布订阅



#### 5.1.5 事务





### 5.1 Elasticsearch

下载安装配置

分片、副本

查询

原理聚合



### 5.3 kafka

### 5.4 zookeeper

### 5.5 Dubbo

### 5.6 Tomcat

### 5.7 RocketMQ

### 5.8 MongoDB

### 5.9 Ehcache



## 六、Mysql数据库

#### 6.1 简介

Mysql  是一个开源的关系型数据库管理系统（Releational Database Management System）,现在属于Oracle 公司，Mysql 凭借着出色的性能、低廉的成本、丰富的资源已经成为绝大多数互联网公司的首选关系型数据库，职位招聘描述如 “精通 Mysql”、"SQL语句优化"、“了解数据库原理”等。一般的应用系统读写比例10：1 左右，而且插入操作和一般的更新操作很少出现性能的问题，遇到**最容易出现问题的是复杂的查询操作**，所以查询语句优化显然重中之重

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

![img](https://images2017.cnblogs.com/blog/679616/201801/679616-20180116184232756-1519061918.png)

**如何解决脏读、不可重复读、幻读？**

脏读发生在 读未提交事务级别，提高到其他等级即可解决

不可重复读发生在读未提交、读已提交，提高到可重复读或序列化即可

幻读发生在读未提交、读已提交、可重复读。只有序列化才可解决

***mysql 如何解决幻读？***

mysql 存储引擎 innodb 在可重读读隔离级别，是会产生幻读,如何解决？通过 next-key lock 在当前事务开启时，通过给涉及到的行加写锁，

行2端加间隙锁防止新增写入

事务博文：https://www.cnblogs.com/balfish/p/8298296.html

#### 6.1.5 索引

##### **索引目的**

> 索引的目的在于提高查询效率，可以类比字典，如果要查“mysql”这个单词，我们肯定需要定位到m字母，然后从下往下找到y字母，再找到剩下的sql。如果没有索引，那么你可能需要把所有单词看一遍才能找到你想要的，如果我想找到m开头的单词呢？或者ze开头的单词呢？是不是觉得如果没有索引，这个事情根本无法完成？

##### 索引分类

| 分类           |          |                      |          |
| -------------- | -------- | -------------------- | -------- |
| 按物理存储分类 | 聚簇索引 | 非聚簇索引           |          |
| 按数据结构分类 | b+tree   | hash                 | fulltext |
| 按字段特性分类 | 主键索引 | 普通索引             | 前缀索引 |
| 按字段个数分类 | 单列索引 | 复合索引（联合索引） |          |

##### **索引原理**

磁盘IO 与预读

访问磁盘花费的时间 = 寻道时间 + 旋转延迟 + 传输    ，一台 7200 传，一次磁盘 IO 的时间约等于 9 ms

考虑到磁盘 IO 高昂的成本，计算机操作系统进行了一些优化，在一次IO 时，不光把当前磁盘地址的数据，而且把相邻的数据也读到内存缓冲区，因为局部预读性原理告诉我们，当计算机访问一个地址的数据的时候，于其相邻的数据也很快被访问到。 每一次 IO 读取的数据我们成之为一页，页的大小与操作系统有关，一般为 4k/8k 。

##### **索引的数据结构**

任何一种数据结构不是凭空产生的，一定会有它的背景与使用背景，我们需要一种数据结构，目的是每次查找数据时把磁盘IO 控制在一个很小的数量级，最好是常量级。而高度可控的多路搜索树就产生了，即 b+ 树

##### **详解b+树**

![img](https://img-blog.csdnimg.cn/20190822142812954.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoYWk0NzE3OTM=,size_16,color_FFFFFF,t_70)

如上图，浅蓝色（数据项），黄色（指针）

p1 表示 5-28 

p2 表示 28-65 

p3 表示大于65

非叶子节点不存储真实数据，只存储指引搜索方向的数据项

##### **b+ 树查找过程**

如果要查找数据 30 ，首先将最顶层磁盘块加载到内存**，发生第一次IO**，在内存中用二分查找确定 30 在 28-65之间，通过顶层磁盘块的指针 p2 把磁盘块加载到内存，**发生第二次IO**，加载第二层中间的磁盘块确定30在 28-35 之间，再通过指针 p1 加载磁盘块，**发生第三次IO**。

真实情况是 3 层的b+ 树可以表示上百万的数据，如果上百万的数据只需要三次IO ，性能将是巨大的，没有索引，每个数据项都要发生一次IO ，成本非常高

##### **b+树性质**

1. 通过上面的分析，我们知道 IO 次数取决于 b+ 树的高度 h 。 假设当前数据表的数据为N，每个磁盘块的数据项的数量是m,则有如下关系

$$
h = log_{m+1}N
$$

当数据项 N 一定的情况下， m 越大， 高度 h 越小。数据项 m  = 磁盘块的大小 / 数据项的大小，磁盘块的大小也就是一个数据页的大小，是固定的，如果数据项占的空间越小，数据项的数量越多，树的高度越低。这就是为什么每个数据项，即索引字段要尽量的小，比如 int 占 4 个字节，要比bigint 8 个字节少一半。这也是为什么 b+ 树要求把真实的数据放到叶子节点而不是内层节点，一旦放到内存节点，磁盘块的数据项会大幅下降，导致树增高。当数据项等于1 时将退化成线性表。

2. 当 b+ 树的数据项是复合的数据结构，比如 (name,age,sex) 的时候，b+树是按照从左到右的顺序建立搜索树的，比如当 **（zhangsan, 20, 男）**这样的数据来检索时， b+ 树会优先比较 name 来确定下一步的搜索方向。如果 name 相同 再依次比较 age 与 sex , 最后得到检索的数据。但当 **（20，男）**这样的没有name 的数据来的时候， b+ 树就不知道下一步该查找哪个节点，因为建立搜索树的时候 name 就是第一个比较因子，即索引失效。比如当（**zhangsan,男）**这样的数据来检索时，b+ 树可以用 name 来指定搜索方向，但下一个字段 age 缺失，即只有 name 索引生效，该性质即**最左匹配特性**



typora 数据公式：https://www.cnblogs.com/YanQing1998/p/10986911.html

##### **慢查询优化**

explain 执行引擎分析sql

##### **建立索引的几大原则**

1.最左前缀匹配原则，非常重要的原则，mysql 会一直向右匹配直到遇到范围查询（<，>, between，like） 就停止匹配 ，比如

```sql
a=1 and b=2 and c>3 and d=4
```

如果建立 （abcd）顺序索引，d 是用不到索引的

如果建立（abdc）的索引，abd 的顺序可以任意调整

2.= 和 in 可以乱序，比如

```sql
a=1 and b=2 and c=3
```

建立（a,b,c）索引可以任意顺序，mysql 的查询优化器会帮你优化索引可以识别的形式

3.尽量选择区分度高的列作为索引 ，区分度 count(distinct col) / count(*)  ,表示字段不重复比例，比例越大扫描的记录数越少

4.索引列不能参与计算，保持“干净” ， 比如 from_unixtime(create_time) = '2020-02-01' ,就不能使用到索引。原因很简单，b+树中存储的都是数据表中的字段值，但进行检索时，需要把所有的元素都引用函数才能比较，成本太大，函数应该放在右边， create_time = from_unixtime( '2020-02-01');

5.尽量的扩展索引，不要新建索引。比如表中已经有a 的索引，现在要加 （a,b） 的索引，那么只需要修原来的索引即可

##### **explain 查询优化器**

查看sql 执行计划

通过 explain 分析 sql 扫描记录数、是否使用索引、以及执行时间等根据具体的业务逻辑进行优化

https://tech.meituan.com/2014/06/30/mysql-index.html





## 七、计算机基础

## 八、数据结构与算法

### 红黑树

## 九、版本控制

### 9.1 git



### 9.2 svn

## 十、运维相关

### 10.1 nginx

### 10.2 jenkins

### 10.3 gitlab

## 十一、分布式

### 10.1 一致性哈希算法

### 10.2 redis 分布式锁

https://www.cnblogs.com/study-everyday/p/8629100.html