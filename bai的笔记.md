一、基础

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

**Stack** 

> Stack 容器继承 Vector 

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

HashMap 的容量始终保持是2的幂次方，目的是在 hash 值与 length -1 计算，保在 length -1 的地位全为 1 ，此外也保证在调整链表幅度在小范围内。

新的容量为原来容量的**2倍**

**jdk 1.8 优化**

在 jdk 1.7 中链表过长会导致效率下降， jdk1.8 在链表超过 8 时，链表转换为红黑树

https://blog.csdn.net/woshimaxiao1/article/details/83661464

**HashMap 在高并发下如何形参死循环？**

在高并发下使用HashMap，在扩容的时候容易在链表部分形成环，产生原因查看博客

> jdk1.7 链表扩容多线程形成环，因扩容后迁移数据采用的是头插的方式，数据的前后逻辑发送的变化，而另外的线程是在错误的逻辑上继续迁移导致形成环。
>
> jdk1.8 对链表进行了优化，包括红黑树，同时采用尾插，头、尾节点不改变前后逻辑，虽然不再形成环，但**多线程建议不使用 HashMap**

https://blog.csdn.net/m0_46405589/article/details/109206432

#### HashTable

```java
    public Hashtable() {
        this(11, 0.75f);
    }
```

1. HashTable 未指定初始化容量，默认初始化容量 11 ，加载因子 0.75
2. HashTable 数组在创建时即进行初始化，如指定容量等于0， 则置为  1
3. HashTable 相比于HashMap 属于线程安全容器（**get ()也同步**）
4. 扩容为旧容量的**二倍再+1**

#### TreeMap

1. 有序的Map 集合容器，创建可以指定一个比较器
2. 树形结构

#### ConcurrentHashMap

数据结构仍然采用的是数组+链表的结构

> 默认的容量 16
>
> 默认加载因子 0.75
>
> 链表转换树阈值 8
>
> 树转换链表 6
>
> 链表转换为树的最小数组容量 64
>
> 默认的并发度 16（即默认 16个Segment）

jdk1.7 

ConcurrentHashMap 由 Segment 数组、HashEntity 组成，与HashMap一样，采用数组与链表结构

jdk1.8

弃用了 Segment 分段锁，采用了 **CAS + synchronized** 保证并发安全，同时链表也可以进行红黑树转换

https://blog.csdn.net/weixin_44460333/article/details/86770169

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

#### 自动内存管理

xmn xms xmx 栈深度设置   OOM 发生的情况  xoss 

#### 垃圾收集器

Java **判断哪些对象需要被回收**

1. 引用计数算法
2. 可达性分析算法

引用计数算法即将对象添加一个引用计数器，无法解决对象循环引用的问题。

可达性分析算法将通过一系列 GC roots 的对象为起点，，不可达的对象将被回收

1. 虚拟机栈中引用的对象
2. 方法区类静态属性引用的对象
3. 方法区常量引用的对象
4. 方法区栈 jni 引用的对象

> Java 引用分为：强引用，软引用，弱引用，虚引用

**垃圾回收算法**

1. 标记清除算法
2. 复制算法
3. 标记整理算法

**内存分配策略与回收策略**

> 对象优先在 Eden 区分配
>
> 大对象直接进入老年代
>
> 长期存活的对象进入老年代
>
> 空间分配担保

#### 类文件结构

对象结构

> markword , 实例信息，对齐填充



#### 类加载机制

> jvm 将 class 文件字节码加载到内存，并将静态数据转换成方法区中运行时数据结构
>
> 类加载机制分为五个部分： 加载、验证、准备、解析、初始化 

![img](https://upload-images.jianshu.io/upload_images/13202633-3cb11d1712a9efc9.png?imageMogr2/auto-orient/strip|imageView2/2/w/739/format/webp)

> ASM 字节码处理工具，可以动态的操作字节码
>
> 加载： 通过类的全限定名获取类的二进制字节流，并生成 Java.lang.Class 对象
>
> 验证： 文件格式验证，元数据验证，字节码验证，符号引用验证
>
> 准备： 为类变量分配内存，并初始化为默认值
>
> 解析:   把类中的符号引用转换为直接引用
>
> 初始化

**双亲委派机制**

![img](https://upload-images.jianshu.io/upload_images/13202633-4c819649aebff4df.png?imageMogr2/auto-orient/strip|imageView2/2/w/590/format/webp)



#### Java 内存模型

##### ***物理计算机的并发***

> 由于计算机的存储设备与运算速度存在量级的差距，中间加入告诉缓存（cache）作为内存与处理器的缓冲：将运算的数据复制到缓存中，让运算
>
> 能够快速进行，当运算结束后再同步回内存，这样处理器就无需等待缓慢的内存读写

***缓存一致性***

> 多路处理器系统中，每个处理器都有自己的高速缓存，而他们共享一个主内存（MainMemory）,将可能导致数据不一致，为了解决一致性问题，各个处理器访问缓存都需要遵循一些协议，读写时需要根据协议进行操作，协议包括 **MSI , MESI , MOSI** 等



> Java 内存模型 （**Java Memory Model ，JMM**) ,作用是屏蔽各种硬件和操作系统的内存访问差异，实现 Java 程序在各种平台下达到一致的内存访问效果
>
> Java 内存模型规定所有的变量都存储在主内存（Main Memory）中

![image-20210226102500245](https://raw.githubusercontent.com/byebai95/image/main/typora/image-20210226102500245.png)

> 除了增加高速缓存外，为了处理器内部的运算能够被充分利用，处理器可能对输入的代码进行乱序执行优化，处理器在计算后将乱序执行的结果重组，保证
>
> 结果与顺序执行结果一致，但不保证程序中语句计算的先后顺序与输入代码中的顺序一致。因此如果一个计算的结果依赖另一个计算任务的中间结果，因此顺序性不能依靠代码的顺序保证。**与处理器乱序执行类似的， Java 虚拟机的即时编译器也有指令重排序优化**

##### **Java 内存模式**

> **Java Memory Model , JMM**  用来屏蔽各种硬件与操作系统的内存访问差异，实现Java程序在各个平台下都能达到一致的内存访问效果
>
> 规定所有的变量都存储在主内存（**Main Memory**）,每条线程都有自己的工作内存（**Working Memory**）
>
> 线程的工作内存保存需要使用主内存变量的副本，对变量的操作必须在工作内存
>
> 线程之间的变量交互通过主内存完成

![image-20210226103957035](https://raw.githubusercontent.com/byebai95/image/main/typora/image-20210226103957035.png)

##### 内存间交互操作

> Jvm 主内存与工作内存之间的具体交互协议，即变量在主内存与工作内存之间的拷贝，Java 通过定义8种操作完成
>
> **lock** ： 锁定，作用于主内存变量，将一个变量标识为线程独占状态
>
> **unlock**： 解锁，作用主内存变量，将锁定状态的变量释放，其他线程才可以进行锁定
>
> **read** ： 读取，作用主内存的变量，将主内存的变量值传输到线程工作内存
>
> **load** ： 载入，作用工作内存，将 read 读取到主内存的值存入工作内存变量副本中
>
> **use** ： 使用，作用工作内存，将工作内存的变量值传递给执行引擎
>
> **assign** ： 赋值，作用工作内存的变量，将执行引擎传递的值赋给工作内存的变量
>
> **store** ： 存储，作用工作内存的变量，将工作内存的变量传递到主内存中
>
> **write ** ： 写入，作用主内存变量，将 store 传递的值放入主内存变量中

##### 原子性、可见性、有序性

> **原子性 Atomicity**
>
> synchronized 块操作具备原子性
>
> lock 与 unlock ，本质是 moniterenter 与 moniterexit 隐式的操作，也具备原子性
>
> **可见性 Visibility**
>
> volatile 保证变量的可见性，即新值能立即同步到主内存，以及每次使用前立即从主内存刷新
>
> synchronized 与 final 也能够实现可见性， synchronized 解锁前先将变量刷新到主内存
>
> **有序性 Ordering**
>
> volatile 禁止指令重排序 ，synchronized 的有序性是保证串行执行

**先行发生原则 Happens-Before**

> 判断数据是否存在竞争，线程是否安全

https://raw.githubusercontent.com/byebai95/image/main/typora/image-20210226102500245.png

https://www.javazhiyin.com/857.html

https://zhuanlan.zhihu.com/p/29881777

#### JVM 调优

分析 jvm 工具  : jdk bin 路径下  **jvisualvm.exe**

| **参数名称**                | **含义**                                                   | **默认值**           |                                                              |
| --------------------------- | ---------------------------------------------------------- | -------------------- | ------------------------------------------------------------ |
| -Xms                        | 初始堆大小                                                 | 物理内存的1/64(<1GB) | 默认(MinHeapFreeRatio参数可以调整)空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制. |
| -Xmx                        | 最大堆大小                                                 | 物理内存的1/4(<1GB)  | 默认(MaxHeapFreeRatio参数可以调整)空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制 |
| -Xmn                        | 年轻代大小(1.4or lator)                                    |                      | **注意**：此处的大小是（eden+ 2 survivor space).与jmap -heap中显示的New gen是不同的。 整个堆大小=年轻代大小 + 年老代大小 + 持久代大小. 增大年轻代后,将会减小年老代大小.此值对系统性能影响较大,Sun官方推荐配置为整个堆的3/8 |
| -XX:NewSize                 | 设置年轻代大小(for 1.3/1.4)                                |                      |                                                              |
| -XX:MaxNewSize              | 年轻代最大值(for 1.3/1.4)                                  |                      |                                                              |
| -XX:PermSize                | 设置持久代(perm gen)初始值                                 | 物理内存的1/64       |                                                              |
| -XX:MaxPermSize             | 设置持久代最大值                                           | 物理内存的1/4        |                                                              |
| -Xss                        | 栈大小(影响每个线程请求深度)                               |                      | JDK5.0以后每个线程堆栈大小为1M,以前每个线程堆栈大小为256K.更具应用的线程所需内存大小进行 调整.在相同物理内存下,减小这个值能生成更多的线程.但是操作系统对一个进程内的线程数还是有限制的,不能无限生成,经验值在3000~5000左右 一般小的应用， 如果栈不是很深， 应该是128k够用的 大的应用建议使用256k。这个选项对性能影响比较大，需要严格的测试。（校长） 和threadstacksize选项解释很类似,官方文档似乎没有解释,在论坛中有这样一句话:"” -Xss is translated in a VM flag named ThreadStackSize” 一般设置这个值就可以了。 |
| -*XX:ThreadStackSize*       | Thread Stack Size                                          |                      | (0 means use default stack size) [Sparc: 512; Solaris x86: 320 (was 256 prior in 5.0 and earlier); Sparc 64 bit: 1024; Linux amd64: 1024 (was 0 in 5.0 and earlier); all others 0.] |
| -XX:NewRatio                | 年轻代(包括Eden和两个Survivor区)与年老代的比值(除去持久代) |                      | -XX:NewRatio=4表示年轻代与年老代所占比值为1:4,年轻代占整个堆栈的1/5 Xms=Xmx并且设置了Xmn的情况下，该参数不需要进行设置。 |
| -XX:SurvivorRatio           | Eden区与Survivor区的大小比值                               |                      | 设置为8,则两个Survivor区与一个Eden区的比值为2:8,一个Survivor区占整个年轻代的1/10 |
| -XX:LargePageSizeInBytes    | 内存页的大小不可设置过大， 会影响Perm的大小                |                      | =128m                                                        |
| -XX:+UseFastAccessorMethods | 原始类型的快速优化                                         |                      |                                                              |
| -XX:+DisableExplicitGC      | 关闭System.gc()                                            |                      | 这个参数需要严格的测试                                       |
| -XX:MaxTenuringThreshold    | 垃圾最大年龄                                               |                      | 如果设置为0的话,则年轻代对象不经过Survivor区,直接进入年老代. 对于年老代比较多的应用,可以提高效率.如果将此值设置为一个较大值,则年轻代对象会在Survivor区进行多次复制,这样可以增加对象再年轻代的存活 时间,增加在年轻代即被回收的概率 该参数只有在串行GC时才有效. |
| -XX:+AggressiveOpts         | 加快编译                                                   |                      |                                                              |
| -XX:+UseBiasedLocking       | 锁机制的性能改善                                           |                      |                                                              |
| -Xnoclassgc                 | 禁用垃圾回收                                               |                      |                                                              |
| -XX:SoftRefLRUPolicyMSPerMB | 每兆堆空闲空间中SoftReference的存活时间                    | 1s                   | softly reachable objects will remain alive for some amount of time after the last time they were referenced. The default value is one second of lifetime per free megabyte in the heap |
| -XX:PretenureSizeThreshold  | 对象超过多大是直接在旧生代分配                             | 0                    | 单位字节 新生代采用Parallel Scavenge GC时无效 另一种直接在旧生代分配的情况是大的数组对象,且数组中无外部引用对象. |
| -XX:TLABWasteTargetPercent  | TLAB占eden区的百分比                                       | 1%                   |                                                              |
| -XX:+*CollectGen0First*     | FullGC时是否先YGC                                          | false                |                                                              |

https://www.cnblogs.com/anyehome/p/9071619.html

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
| 12.0 | 2019-03  |                                                      |
| 13.0 | 2019-09  |                                                      |
| 14.0 | 2020-03  |                                                      |
| 15.0 | 2020-09  |                                                      |
| 16.0 | 2021-03  | 即将发布                                             |



## 二、 进阶

### 2.1 多线程

#### **进程与线程**

> **进程**：是并发执行程序在执行过程中**资源分配的基本单位**，进程可以理解为一个应用程序的执行过程，应用一旦启动就是一个进程。
>
> 进程拥有自己独立的地址空间，每新建一个进程，系统会为其分配地址空间、建立数据表维护代码段、堆栈段、数据段
>
> **线程**：程序执行的最小单位，即 cup 调度的基本单位
>
> 一个程序在使用期间，往往会接受大量的并发请求，为每个请求创建一个进程是行不通（系统开销大，响应低），因此在系统中引入线程
>
> 

**创建线程**

通过 new Thread 创建线程

```java
 new Thread(()->{
            System.out.println(Thread.currentThread().getName());
        }).start();
```

继承 Runnable

```java
  static class MyRunnable implements Runnable{
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName());
        }
    }

  MyRunnable runnable = new MyRunnable();
  new Thread(runnable,"thread"+i).start();
```

**Thread 方法**

priority 优先级

> 线程的默认优先级 5 ，值的范围【1-10】，优先级最的线程先执行的概览高。
>
> 当前线程的优先级与创建它的线程的优先级相同

Thread.sleep(long millis)

> 当前线程睡眠多少毫秒，线程睡眠不会失去对象锁，让出 cpu ,调用的目的是不让当前线程霸占 cpu 资源，让其他的线程执行。
>
> 休眠到达后，无法保证立即被调度（不释放对象锁）

Thread.yield()

> 让出 cpu的使用权，给其他线程执行机会，让同等优先权的线程运行，如果没有同等优先权的线程，那么该方法将不起作用
>
> 并不保证当前线程被 jvm 再次调度(不释放对象锁)

thread.join() / thread.join(long millis)

> 使用该方法的线程在此执行完毕后再继续执行
>
> 当前线程调用其他线程 t 的 join() 方法，当前线程进入 waiting / timed_waiting 状态，当前线程不会释放已持有的锁，
>
> 线程 t 执行完毕或者时间到后，当前线程进入就绪状态

Object.wait()

> 当线程执行到 wait() 时，就进入到一个和该对象相关的等待池(Waiting pool) ，同时失去对象锁，**wait 后需要返还对象锁。**
>
> 如果当前线程不是锁的拥有者，将抛出异常 **IllegalMonitorStateException**，因此该方法必须在同步块中调用

Object.notify() 与 Object.notifyAll()

> notify唤醒在当前对象等待池中等待的第一个线程，notifyAll 唤醒所有线程，使用该方法也必须持有对象的锁，
>
> 否则抛出异常 **IllegalMonitorStateException**

**线程的状态**

> 1. **初始 new** : 新创建一个线程，未调用 start()
> 2. **运行 Runnable** : 此处就绪与运行统称为运行态，线程调用 start() 方法后，该线程就位于可运行的线程池中，等待被调度选中
> 3. **阻塞 Blocked** : 表示线程阻塞于锁
> 4. **等待 Waiting** ：进入该状态的线程需要等待其他线程通知或中断
> 5. **超时等待 Timed_Waiting** : 不同于 Waiting , 它可以在指定的时间后自行返回
> 6. **中止 Terminated** : 表示该线程已经执行完毕

**状态图**

<img src="https://img-blog.csdn.net/2018070117435683?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BhbmdlMTk5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="img"  />初始状态

> 实现Runnable接口或者继承 Thread 可以得到一个线程类，new 一个实例出来，线程就进入了初始状态

就绪状态

> 1. 就绪状态只是有资格运行，调度器没有选中，就无法运行
> 2. 调用 start() 方法，线程就进入就绪状态
> 3. 当前线程 sleep() 结束，其他线程 join() 结束，某个线程拿到对象锁，这些线程都进入就绪状态
> 4. 当前线程时间片用完，调用当前线程的 yield（） 方法，也进入就绪状态

运行中

> 线程调度从可运行池中选择一个线程作为运行状态

阻塞状态

> 线程阻塞在进入 synchronized 修饰的方法或代码块时的状态

等待

> 处于这种状态的线程不会被分配，他们要被显示地唤醒，否则无期间的等待

超时等待

> 与等待不同，无需被其他线程唤醒，到达一定时间后他们会自动唤醒

中止状态

> 线程 run 方法完成时或者 main 方法完成时，线程就进入终止
>
> 在进入终止的线程上调用 start() ，将抛出异常 **IllegalThreadStateException**

**等待队列**

<img src="https://img-blog.csdn.net/20180701221233161?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BhbmdlMTk5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="img"  />****

**同步队列**

> 当线程调用同步方法时，对象被其他线程占有，此时当前线程进入**同步队列**
>
> 当线程被其他线程唤醒，则进入同步队列
>
> 同步队列是在同步环境下的概念，一个对象对应一个同步队列
>
> 同步队列的线程都是可以争抢对象的线程

#### ThreadLocal 

> ThreadLocal 是线程 Thread 中属性 threadLocations 的管理者，ThreadLocal 提供的 get, set , remove 操作是对 Thread 实例中 **threadLocals** 
>
> 存，取，删除操作

![在这里插入图片描述](https://img-blog.csdn.net/20181001212036758?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6ZzEyMjkwNTk3MzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**ThreadLocak 原理**

> 包含三个方法： get , set , remove 
>
> **ThreadLocal 内部类 ThreadLocalMap**
>
> **ThreadLocalMap 内部类 Entity**



![在这里插入图片描述](https://img-blog.csdn.net/20180920212005665?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6ZzEyMjkwNTk3MzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**ThreadLocal 以及 Thread 之间的关系**

> ```java
> static class Entry extends WeakReference<ThreadLocal<?>> {
>             /** The value associated with this ThreadLocal. */
>             Object value;
> 
>             Entry(ThreadLocal<?> k, Object v) {
>                 super(k);
>                 value = v;
>             }
>         }
> ```
>
> ThreadLocamMap 中Entity 中存储当前线程的引用，使用的是弱引用，即第一次 gc 就会被回收 

<img src="https://img-blog.csdn.net/20180920212201150?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6ZzEyMjkwNTk3MzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"  />

> **ThreadLocal 被回收，线程仍未结束，将会导致内存泄露。ThreadLocal 管理 Object 对象，但是一条若引用，线程无法管理改对象**

![在这里插入图片描述](https://img-blog.csdn.net/2018092021395091?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6ZzEyMjkwNTk3MzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

https://blog.csdn.net/zzg1229059735/article/details/82715741

#### synchronized

> synchronized 可以保证在同一时刻，只有一个线程可以执行某个方法或者某个代码块
>
> 1. 修饰实例方法   相当于给当前实例加锁，进入同步代码块要获得**当前实例的锁**
> 2. 修饰静态方法   相当于给当前类对象加锁，进入同步代码块需要获得**当前类对象的锁**
> 3. 修饰代码块  指定加锁对象，进入同步代码块需要获得**给定对象的锁**





https://www.cnblogs.com/wangwudi/p/12302668.html

#### volatile



https://blog.csdn.net/qq_22771739/article/details/82529874

https://blog.csdn.net/u013262534/article/details/81676657

#### 	并发容器

#### 	 原子类

#### 

#### java.util.concurrent 并发包

##### CountDownLatch

##### CyclicBarrier

#### **Reactor模式**



#### CAS 失败重试



#### 锁优化

自旋锁与自适应自旋

锁消除

锁粗花

轻量级锁

偏向锁

https://www.cnblogs.com/crazymakercircle/p/9833847.html

### 2.2 设计模式

#### 单例

#### 工厂

#### 观察者

#### reactor模式





## 三、Web



## 四、框架

### 4.1 spring 

##### spring 事务

```
@Transactional


```

事务的隔离级别： 默认的，即使用数据库的隔离级别

Propagation  7种





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

历史版本

| 版本号 | 特点                                                         | 时间    |
| ------ | ------------------------------------------------------------ | ------- |
| 2.6    | 支持 Lua脚本、去掉虚拟内存功能                               | 2012    |
| 2.8    | 添加部分主从复制功能、尝试支持ipv6                           | 2013-11 |
| 3.0    | redis cluster 分布式实现、lru 算法提升、优化小对象内存访问   | 2015-04 |
| 3.2    | 优化 rdb 、从节点读取过去数据保证一致性                      | 2016-05 |
| 4.0    | 优化主从切换全量复制、提供新的剔除算法 LFU、提供rdb-aof 混合持久化格式 | 2017    |

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

#### 5.1.4 发布订阅



#### 5.1.5  事务



#### 5.1.6 热点数据淘汰算法

> 当 redis 使用的内存超过设置的最大内存时，会触发redis的key 淘汰机制，在 redis 3.0 中的6种淘汰策略

***redis 淘汰策略***

> 1. noeviction 不删除策略 ，当达到最大内存限制时，如果需要使用更多的内存，则直接返回错误信息（redis 默认策略）
> 2. allkeys-lru 所有的key中删除最近最少使用（less recently used ， LRU）
> 3. allkeys-random 所有的keys中随机删除一部分 key
> 4. volatile-lru 在设置了超时时间的 key 中优先删除最近最少使用的key
> 5. volatile-random 在设置超时时间的key 中随机删除一部分key
> 6. volatile-ttl 在设置了超时时间的key中优先删除剩余时间的key

***如何保证 redis 中存放的都是热点数据？***

> 限定 redis 占用内存，redis 会根据自身数据淘汰策略，留下热数据到内存，然后设置淘汰策略为 allkeys-lru 或者 volatile-lru 
>
> 设置 redis 最大占用内存： maxmemory 字节数
>
> 设置过期策略: maxmemory-policy volatile-lru (默认的淘汰策略 noeviction)

默认端口： 6379

#### redis 分布式锁

Redisson 是一个在 Redis 基础上实现的 Java 驻内存数据网络（In-Memory Data Grid） , 提供一系列分布式的 Java 常用对象，还实现了

可重入锁（Reentrant Lock）、公平锁（Fair Lock）、联锁（MultiLock）、红锁（RedLock）、读写锁（ReadWriteLock）等

Redisson 支持**单点模式、主从模式、哨兵模式、集群模式**



https://www.cnblogs.com/javazhiyin/p/11737403.html

### 5.2 Elasticsearch

**下载安装 https://www.elastic.co/cn/downloads/elasticsearch** 

> 注意：
>
> 1. Elasticsearch 依赖 jdk 版本问题
> 2. 启动需要使用普通用户，启动内存设置是否何是等

Elasticsearch 功能

1. 分布式的搜索引擎和数据分析引擎
2. 全文检索，结构化检索，数据分析
3. 对海量数据实时处理
4. 基于 lucene 构建的开源、分布式的搜索引擎

**Elasticsearch 发展历史**

| 版本  | 特性                                                         | 时间       |
| ----- | ------------------------------------------------------------ | ---------- |
| 0.7   | 支持ICU 分词器                                               | 2010-05-14 |
| 1.0.0 | 支持联合查询、cat api 、聚合分析 aggregations                | 2014-02-14 |
| 2.0.0 | 增加 pipleline Aggregations 、支持 root 启动、 query/filter 查询合并 | 2015-10-28 |
| 5.0.0 | Lucene 6.x 支持，性能提升，新的类型 text/keyword, 仅支持非root启动 | 2016-10-26 |
| 6.0.0 | 不支持一个index 多个 type ,基于负载的请求路由，顺序号的支持  | 2017-08-31 |
| 7.0.0 | 集群连接变化，lucene  9.0 支持，正式废除单个索引下多type(默认_doc作为type) ,es8 将彻底移除 | 2019-04-10 |

**基本概念**

Elasticsearch 天生是分布式的，屏蔽分布式的复杂性，集群拥有健康属性（green、yellow、red）

> green 所有主分片以及副本分片正常运行
>
> yellow 所有主分片正常，副本分片至少一个异常
>
> red 至少一个主分片异常

当集群中只有一个节点在运行，则意味着有单点故障的问题，需要多个节点防止故障，如下图（2个节点，一个索引包含 3个主分片，3个）

![拥有两个节点的集群](https://www.elastic.co/guide/cn/elasticsearch/guide/current/images/elas_0203.png)

*集群默认的副本是1 ，可以修改副本的个数。分片数量在创建索引时即确定不可修改*

**索引 index** 

类比数据库中的数据库，数据存储在索引中

**类型 type**

类比数据库中的表，类型是索引内部的逻辑分区

**文档 document**

文档是索引的原子单位，基于 json 格式进行表示

**分片 shard**

将索引内部的数据分布存储在多个节点，每个物理的 lucene 索引称为一个分片

**副本 replica**

每个分片存储的副本个数

**为何Elasticsarch 速度快？**

> Elasticsearch 使用一种倒排索引的结构，它非常适用于快速的全文搜索。一个倒排索引由文档中所有不重复词的列表构成，每个词都有一个包含它的文档列表。为了创建倒排索引，首先将每个文档的 content 域拆分成单独的词（词条、tokens）,查询只需要查询包含它的相关文档即可

http://www.mybatis.cn/archives/1145.html

https://www.elastic.co/guide/cn/elasticsearch/guide/current/mapping-intro.html

### 5.3 kafka

kafka 中文官方文档 https://kafka.apachecn.org/intro.html

> kafka 是分布式的、基于发布订阅的消息系统，主要解决***应用解耦、异步消息、流量削峰***

#### **发布订阅模型**

生产者将消息发送到 Topic 中，同时有多个消费者订阅该消息，消费者消费数据后，并不会清除消息，属于一对多模式

![发布订阅模型.png](https://segmentfault.com/img/remote/1460000021175586)

**系统架构**

![系统总架构.png](https://segmentfault.com/img/remote/1460000021175587)



![preview](https://segmentfault.com/img/remote/1460000021175588/view)

**producer**

消息生产者，将消息 push 到 kafka 集群中的Broker

**consumer**

消息消费者，从kafka 集群中pull 消息，消费消息

**consumer group**

消费者组，每个 consumer 都有一个consumer group .消费者组整体是一个消费者

一个分区只能被组内一个消费者消费

**broker**

一台kafka 服务器就是一个 broker ,一个集群由多个broker组成，每个broker可以容纳多个 topic

**topic**

消息的类别或者主题，逻辑上可以理解为队列，生产者与消费者的中间商

**partition**

负载均衡与扩容性考虑，一个topic可以分为多个Partition ，物理存储在kafka 集群中多个 Broker 上

> 为了提高 kafka 的吞吐量，物理上将 topic 分成一个或多个 partition , partition 在存储层面是 append log 文件，消息 push 进来后，
>
> 会被追加到 log 文件的尾部，每条消息在文件中的位置成为 **offset** 偏移量 （**offset** 是一个 long 型数字，唯一标识一条信息）

**replica**

Partition 的副本，为了保证集群中某个节点发生故障时，该节点上的Partition 数据不会丢失，且Kafka仍能继续工作。kafka提供副本机制，

一个topic都有若干个副本，一个 leader 和若干个 follower

**leader**

Replica 的主角，Producer 与 Consumer 只跟 Leader 交互

**follower** 

Replica 的从角，实时从 Leader 中同步数据，保持和 Leader数据的同步。Leader 发送故障时，Follower 会变为新的 Leader

#### kafka 中 NIO 

https://www.jianshu.com/p/a6b9e5342878

<img src="https://upload-images.jianshu.io/upload_images/4325076-41f7b454a8d7f67b.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp" alt="img"  />

#### **kafka 多线程Reactor**

https://segmentfault.com/a/1190000021175583?utm_source=sf-related

### 5.4 zookeeper

zookeeper 是 Apache 软件基金会的一个软件项目，它为大型分布式计算提供开源的**分布式配置服务、同步服务、命名注册**

zookeeper 的架构通过冗余服务实现高可用

#### **数据结构**

zookeeper 提供的命名空间类似标准文件系统， key-value 的形式存储，名称 key 由斜线 **/** 分割 ，命名空间中的每个节点由一个路径标识

![img](https://www.runoob.com/wp-content/uploads/2020/09/zknamespace.jpg)

**相关 CAP 理论**

cap 理论支出分布式计算系统，不能同时满足一下三点

1. **一致性 **Consistency

   > 数据在多个副本之间保持一致

2. **可用性 **Availability

   > 每次请求都能获取正确的数据，但不保证是最新的

3. **分区容错性** Partition tolerance

   > 在发生网络故障时，仍然需要保证提供一致性与可用性服务

以上三者只能同时满足2者， p 是必须的，所以有 CP 与 AP 两中，**zookeeper 保证的是 CP ， spring cloud eureka 保证的是 AP**

<img src="https://www.runoob.com/wp-content/uploads/2020/09/cap-theorem-diagram.png" alt="img" style="zoom: 50%;" />

**折中的 BASE 理论**

BASE 是 Basically Available （基本可用），Soft-state(软状态)、Eventually Consistent (最终一致性)

> 基本可用：在分布式系统出现故障，允许损失部分可用性
>
> 软状态：允许数据备份节点出现中间状态，延时的最终一致性
>
> 最终一致性：数据备份节点经过一段时间达到一致性

#### 集群搭建

![img](https://www.runoob.com/wp-content/uploads/2020/09/clus-01.png)

zoo.cfg 三个端口

> **2181** 对客户端提供服务
>
> **2888**  集群内机器通信使用
>
> **3888** 选举leader 使用

#### **znode**

zookeeper 所有存储的数据是由 znode 组成，并以 key-value 的形式存储数据，结构类似 Linux 的文件系统（znode 信息如下）

![img](https://www.runoob.com/wp-content/uploads/2020/09/data-model-03.png)

| 属性           | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| cZxid          | 创建节点时的事务ID                                           |
| ctime          | 创建节点时的时间                                             |
| mZxid          | 最后修改节点时的事务ID                                       |
| mtime          | 最后修改节点时的时间                                         |
| pZxid          | 表示该节点的子节点列表最后一次修改的事务ID，添加子节点或删除子节点就会影响子节点列表，但是修改子节点的数据内容则不影响该ID**（注意，只有子节点列表变更了才会变更pzxid，子节点内容变更不会影响pzxid）** |
| cversion       | 子节点版本号，子节点每次修改版本号加1                        |
| dataversion    | 数据版本号，数据每次修改该版本号加1                          |
| aclversion     | 权限版本号，权限每次修改该版本号加1                          |
| ephemeralOwner | 创建该临时节点的会话的sessionID。**（\****如果该节点是持久节点，那么这个属性值为0）** |
| dataLength     | 该节点的数据长度                                             |
| numChildren    | 该节点拥有子节点的数量**（只统计直接子节点的数量）**         |

####  zookeeper Session 原理

客户端与服务端之间的连接是基于 TCP 长连接的， Client 端连接 server 端的默认端口 2181 ，也就是 session 会话。

**sessionID**

会话id,用来唯一标识一个会话，每次客户端创建会话的时候， zookeeper 都会为其分配一个全局的唯一 sessionId

#### 数据同步流程

在 zookeeper 中，主要依赖 ZAB 协议来实现分布式数据一致性，协议分为两部分

1. 消息广播
2. 崩溃恢复

**消息广播**

> Zookeeper 使用单一的主进程 Leader 来接受和处理客户端所有事务请求，并采用 ZAB 协议的原子广播协议，将事务请求以 Proposal 提议广播到所有
>
> 的 Follower 节点，当集群中有过半的 Follower 服务器进行正确的 ACK 反馈，那么Leader 就会再次向所有的 Follower 服务器发送 commit 消息，将此
>
> 次提议进行提交。这个过程简称 2pc 事务提交，Observer 节点只负责同步 leader 数据，不参与 2pc 数据同步过程。

<img src="https://www.runoob.com/wp-content/uploads/2020/09/zk-data-stream-async.png" alt="img" style="zoom: 67%;" />

**奔溃恢复**

一旦Leader服务器出现奔溃，或者由于网络原因导致 Leader 服务器失去了过半 Follower 的通信，那么就会进入奔溃恢复模式，需要选举出一个

新的Leader 服务器，这个过程可能存在两种数据不一致的隐患，需要 ZAB 协议避免

1. Leader 服务器将消息 commit 发出后，立即奔溃
2. Leader 服务器刚提出 proposal 后，立刻奔溃

ZAB 协议的恢复模式下使用了一下策略

1. 选举zxid最大的节点作为新的leader
2. 新 leader 将事务日志中尚未提交的消息进行处理

#### Leader 选举原理

> zookeeper 的 Leader 选举存在两个阶段，一个是服务器启动时，Leader 选举，另一个是运行过程中 Leader 服务器宕机
>
> 1. 服务器 ID（myid）: 编号越大在选举算法中全重越大
> 2. 事务ID (zxid) : 值越大说明数据越新，权重越大
> 3. 逻辑时钟（epoch-logicalclock）: 同一轮投票过程中的逻辑时钟值是相同的，每投完一次值会增加

**服务器启动时 Leader 选举**

每个节点启动的时候都是 looking 观望状态 ，接下来就开始选举流程。例如三台机器组成集群的选举，第一台服务器 server 1启动，

无法进行 leader 选举 ，当第二天服务器 server 2 启动时， 两台服务器可以通信， 进入 leader 选举

> ​		每台服务器发出一个投票，由于是初始情况， server1 与 server2 都将自己作为 leader 服务器进行投票，每次投票包含所
>
> 推荐服务器的 myid ,zxid , epoch ,使用（myid,zxid）表示，此时投票为 server 1（1，0），server 2（2，0）然后将各自
>
> 的投票发送给集群的其他机器
>
> ​		接受来自各自服务器的投票，集群中的每个服务器收到投票后，首先判断该投票的有效性，如检查本轮投票（epoch）、是否来
>
> 自 looking 状态的机器
>
> ​		分别处理投票，针对每一次投票，服务器都需要将其他服务器的投票和自己的投票进行比对，对比规则如下
>
> 1. 优先比较 epoch
>
> 2. 检查 zxid,zxid 比较大的服务器优先作为 leader
>
> 3. 如果 zxid 相同，那么就比较 myid ,myid 比较大的服务器作为 leader
>
>    ​	统计投票， 每次投票后，服务器统计投票信息，判断是否有过半机器收到相同的投票信息。server 1 、 server 2 都统计出集群中
>
>    有两台机器接受了 （2，0）的投票信息，此时已经选出 server2 为 leader 节点
>
>    ​	改变服务器状态，一旦确定 leader ,每个服务器响应更新自己的状态。如果是 follower ,那么变更为 following ,如果是 leader ,变
>
>    更为 leading 。 此时 server3 继续启动，直接加入变更自己为 following 

![img](https://www.runoob.com/wp-content/uploads/2020/09/vote-01.png)

**运行过程中的 leader 选举**

当集群中的 leader 服务器发生宕机，整个集群无法对外提供服务，进入新一轮 leader 选举

> 1. 变更状态，其他非 observer 服务器将自身服务器状态变更为 looking
> 2. 每个 server 发出一个投票，在运行期间，每个服务器上 zxid可能不同
> 3. 处理投票，规则同启动投票
> 4. 统计投票
> 5. 改变服务器状态 

#### 分布式锁实现原理

分布式锁是控制分布式系统之间同步访问共享资源的一种方式，以及实现**排他锁**与**共享锁**两种分布式锁

**排他锁**

排他锁（Exclusive Locks） ，又被称为写锁或独占锁，如果事务 T1 对数据对象 O1 加上排他锁，那么整个加锁期间，只允许事务 T1 对 O1

进行读取、更新操作，其他任何事务都不能进行读或写。

定义锁

```java
/exclusive_lock/lock
```

*实现方式*

利用 zookeeper 的同节点的唯一特性，在需要获取排他锁时，所有的客户端试图通过调用 create() 接口， 在 /exclusive_lock 节点下创建临时子节点

/exclusive_lock/lock ，最终只有一个客户端能创建成功，那么此客户端就获得了分布式锁。同时所有没有获取到锁的客户端可以在 /exclusive_lock 节点

上注册一个子节点变更的 watch 监听事件，以便重新获取得锁

**共享锁**

共享锁 (Shared Locks) , 又称为读锁。如果事务 T1 对数据对象 O1 加上了共享锁，那么当前事务只能对O1 进行读取操作，其他事务也只能对这个数据

对象加共享锁，直到改数据对象上的所有共享锁都释放

定义锁

```
/shared_lock/[hostname]-请求类型W/R-序号
```

*实现方式*

> 1. 客户端调用 create 方法创建类似定义锁方式的临时顺序节点
>
> 2. 客户端调用 getChildren 接口来获取所有已创建的子节点列表
>
> 3. 判断是否获得锁，对于读请求如果所有比自己小的子节点都是读请求或者没有比自己序号小的子节点，表明以及成功获取共享锁，同时
>
>    开始执行读逻辑。对于写请求，如果自己不是序号最小的子节点，那么就进入等待
>
> 4. 如果没有获取到共享锁，读请求向比自己序号小的最后一个写请求注册 watch 监听，写请求向比自己序号小的最后一个节点注册watch 监听

实际开发中，可以使用 **curator** 工具包封装的 API 来实现分布式锁

![0](https://www.runoob.com/wp-content/uploads/2020/09/lock-01.png)

```xml
<dependency>
  <groupId>org.apache.curator</groupId>
  <artifactId>curator-recipes</artifactId>
  <version>x.x.x</version>
</dependency>
```

curator 几种锁方案

> InterProcessMutex  **分布式可重入排他锁**
>
> InterProcessSemaphoreMutex    **分布式排他锁**
>
> InterProcessReadWriteLock **分布式读写锁**

**zookeeper 如何保证分布式事务最终一致性？**

https://mp.weixin.qq.com/s/TLfSQzrpfYu10QmtXws-kQ

菜鸟教程 https://www.runoob.com/w3cnote/zookeeper-tutorial.html

### 5.5 Dubbo

官网：https://dubbo.apache.org/zh/

架构：

<img src="https://camo.githubusercontent.com/e11a2ff9575abc290657ba3fdbff5d36f1594e7add67a72e0eda32e449508eef/68747470733a2f2f647562626f2e6170616368652e6f72672f696d67732f6172636869746563747572652e706e67" alt="Architecture" style="zoom:80%;" />

### 5.7 RocketMQ



### 5.8 Oauth2

Oauth 2.0 是目前流行的授权机制，用来授权第三方应用，获取用户数据，核心就是向第三方应用颁发令牌。

**令牌与密码**（令牌：token ，令牌的英文翻译 token ,所以令牌即token）

> 令牌是短期的，到期后自动失效，用户无法自己修改。密码是长期有效
>
> 令牌可以被数据所有者撤销，立即失效
>
> 令牌有权限范围，密码一般是完整的权限

注：只要知道了令牌，就能进入系统，不会进行身份确认，泄露令牌与泄露密码的后果是一样的，所以令牌一般设置比较短的有效期

**四种授权方式**

1. 授权码
2. 隐藏式
3. 密码式
4. 客户端凭证

> 不管哪种授权方式，第三方应用申请令牌前，都必须到系统备案，说明自己的身份，然后会拿到两个身份识别码（**客户端id** 与 **客户端密钥**），
>
> 这是为了防止令牌被滥用，没有备案过的第三方应用是拿不到令牌的

![img](https://upload-images.jianshu.io/upload_images/2177145-7892e818037db1ae.png?imageMogr2/auto-orient/strip|imageView2/2/w/578/format/webp)

**授权码方式**

最常用的流程，安全性比较高，适用于有后端应用的web应用。授权码通过前端传送，令牌则是存储在后端，所有与资源服务器的通信都在后端完成。

> 1. A 提供 B 网站连接，跳转到B ,用户登录B，授权给A 并返回授权码
> 2. A 通过授权码请求令牌，B 返回令牌
> 3. A 通过令牌请求用户相关的数据

![img](https://www.wangbase.com/blogimg/asset/201904/bg2019040905.jpg)

**隐藏式**

有些web应用是纯前端应用，没有后端。就不能用授权码方式，必须将令牌存储在前端。**RFC6749** 规定隐藏式，允许直接向前端颁发令牌，

这种方式没有授权码的中间步骤

> 这种方式把令牌直接传给前端，是不安全的，因此使用安全不高的场景，通常令牌是会话期间有效，关闭浏览器令牌就失效

**密码式**

高度信任的某个应用，**RFC6749** 允许用户把用户名与密码直接告诉应用，该应用使用密码申请令牌

> 密码式需要提供用户与密码，风险很大

**客户端凭证**

使用于没有前端的命令行应用，即在命令行下请求令牌，该方式是争对应用的，而非用户，可能多个用户共享一个令牌

**令牌的使用与更新**

申请到令牌后，A 网站可以访问 B网站的 API 请求数据，需要在请求头加一个 Authorization 字段，令牌就存放在该字段中

令牌是有有效期的，可以通过更新令牌延长，即在颁发令牌的时候一次颁发2个令牌，一个用于获取数据(**access token**)一个

用于获取新的令牌（**refresh token**）

**第三方登录原理**（github案例）

```txt
1. A网站让用户跳转到 Github
2. Github 要求用户登录，并询问是否允许 A网站获取xx 的权限
3. 用户同意，Github 会重定向回 A 网站，同时发回一个授权码
4. A网站使用授权码，向Github请求令牌
5. Github返回令牌
6. A网站使用令牌，向Github请求用户数据
```

##### **Cookie  Session Token**

> 彻底区别三个的关系
>
> **cookie** 服务器生成，存储在浏览器中，是key-value 键值对（包含sessionId）
>
> **session** 服务器生成，存储在服务器中，key-value 键值对存储
>
> **token(令牌)**  服务器生成，将登录凭证进行数字签名加密之后得到字符串作为token，不进行存储

**根本原因**

> 1. HTTP 请求是无状态的，无法识别请求与用户的关系，而 cookie 、session、token 就是为了实现带有状态的会话
> 2. cookie 与 session 进行有状态会话，缺点会导致服务器存储大量的session
> 3. 服务器组成集群，session 无法共享（解决：session sticky 集群机器之间session 复制）
> 4. 集群session 共享解决，session 统一存储到 Memcached
> 5. 基于session 复杂的处理，Token 用于解决分布式 session 的问题

**分布式session解决方案**

由于不同的服务器含有不同的web服务器，不同的web服务器之间 session 并不共享，分布式的情况下，如何实现session 共享？

*方案一 客户端存储 session*

> 存在安全隐患，增加网络开销，cookie 存储存在限制

*方案二 session 复制*

> 服务器之间通过session 复制保证一致性，通过广播的方式同步session .缺点是当用户量比较大，会出现内存不足

*方案三 基于redis 存储 session*

> 搭建redis 集群，统一存储session, 是最常见的方式之一

https://www.cnblogs.com/moyand/p/9047978.html

### 5.9 SSO 单点登录



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



## 七、网络

#### 序列化

Java 保存内存中各种对象的状态，可以通过序列化实现，然后通过反序列化将其读取

> 静态变量不会被序列化，序列化/串行化保存的是对象的状态，即实例变量，不保存类变量
>
> transient 关键字可以限制序列化
>
> 虚拟机是否允许反序列化，不仅取决于类路径与代码，还有是两个类的序列化id 是否一致

#### 网络协议

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161121173924409-1128983178.png)

会话层及以上：包文

传输层：段

网络层的数据单位： 包

数据链路层的单位： 帧

物理层：比特流

#### NIO BIO AIO

通过一个**烧开水**的案例分析三者的区别

![img](http://hccm.rongsoft.com/upload/img/202009/b22df162c4b54657a9f253dc66b1c2ad.png)

BIO 同步阻塞IO     数据的读取写入必须阻塞在一个线程内完成

NIO 同步非阻塞IO 线程不断轮询查看状态，直到满足条件从而执行下一步操作

AIO 异步非阻塞IO  不用通过轮训查看状态，而是通过回调或通知线程告知状态

> 同步  主动请求数据直到等待io 操作完成  
>
> 异步  主动请求数据后处理其他的任务
>
> 阻塞   线程等待资源中的数据准备完成，直到返回响应结果
>
> 非阻塞 线程直接返回结果，不用等待资源准备才响应

![img](http://hccm.rongsoft.com/upload/img/202009/77556f0fe0a1412bb20c14f0db1b7a03.jpg)

NIO 的优点

> 1. 通过 Channel 注册到 Selector 上的状态来实现客户端与服务端的通信
> 2. Channel 中数据的读取是通过 Buffer ，一种非阻塞的读取方式
> 3. Selector 多路复用器单线程模型，线程的资源开销较小

**为什么用 NIO ，传统的 IO 有什么缺陷？**

> 传统的 IO 是一个请求对应一个线程的模式，一旦有高并发的请求，就会出现如下问题
>
> 1. 线程不够用，可能无法创建新的线程，导致异常，程序崩溃
> 2. 阻塞 IO 模式下，会有大量线程被阻塞，一直等待数据，这时大量线程被挂起导致cup利用率低下，系统的吞吐量差
> 3. 如果网络 IO 阻塞或者故障，线程的阻塞时间可能很长，系统变的不可靠

最直接的提现在服务器与客户端通信时，每加入一台客户端需要等待一个 io 线程阻塞等待对方数据传送，会导致服务器不断的开启新的线程，

但这些大部分都是阻塞中，浪费资源，并且无法支持大并发

**传统IO 流 与 NIO** 

 **IO** 是面向流的、阻塞的, NIO 则是面向块的、非阻塞的

原有的IO流不存在缓存的概念，面向流意味着每次从流中读取一个或多个字节，直至读取所有字节，他们没有被缓存的地方，因为不能移动流中的数据。

传统的IO流是阻塞的，当一个线程调用read 或者 write 方法时，该线程被阻塞/

**NIO** 是面向缓冲区的 ， 数据读取到一个它稍后处理的缓冲区，NIO 是非阻塞模式，使线程从某通道发送请求读取数据，在读取数据之中，该线程可以做

其他的事情。

**NIO 是如何实现的？**

> **buffer**  缓存数组（容量、界限、位置）
>
> ![img](https://pic3.zhimg.com/80/v2-0894899e2dc54b8bb2959b0fdbd0be6e_720w.jpg)
>
> **channel**    通道
>
> 通道可以同时进行读写，流只能读或者只能写
>
> 通道可以实现异步读写数据
>
> 通道可以从换从读取数据，也可以写数据到缓存
>
> ![img](https://pic2.zhimg.com/80/v2-14be9b2fd5d638a20281e72ef557923d_720w.jpg)
>
> **selector**  选择器
>
> 可以检测多个 NIO channel ,查看读写事件是否就绪
>
> 多个 channel 以事件的方式可以注册到同一个 selector ，从而达到用一个线程处理多个请求的可能
>
> ![img](http://ifeve.com/wp-content/uploads/2013/06/overview-selectors.png)
>
> ![img](https://pic4.zhimg.com/80/v2-092382125d13983b0c91a168e2b35c77_720w.jpg)

并发编程网之 NIO: http://ifeve.com/overview/

#### TCP/ip 三次握手

![img](https://img-blog.csdn.net/20180717201939345?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4OTUwMzE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

序列号 seq : 占4个字节，用来标记数据端的顺序

确认号 ack : 占4个字节

确认ACK : 占 1 个字节 当 ACK = 1 时确认字段号才有效， ACK = 0 确认号无效

同步 SYN : 建立连接时用于同步序号，只有在建立连接时置为 1 

终止 FIN ： 用来释放一个连接

> ACK SYN FIN 标识标志位，值为 0 或者 1

**三次握手**

![img](https://img-blog.csdn.net/20180717202520531?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4OTUwMzE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

> 

**四次挥手**

![img](https://img-blog.csdn.net/20180717204202563?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4OTUwMzE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

***为什么建立连接需要三次，关闭连接需要四次？***

***为什么 TIME_WAITE 状态需要经过2MSL (msg 最大包文生存时间) 才进入 close 状态?***

***为什么不能用2次握手建立连接？***

***如果已经建立连接，客户端故障则么办？***



https://www.cnblogs.com/bj-mr-li/p/11106390.html

## 八、数据结构与算法

**1.输入两个正整数 n 与 m ，求最大公约数与最小公倍数**

```java
    public static int getGenericCommonNumber(int x, int y){
        int n = y;
        while( x%y != 0 ){
            n = x%y;
            x = y;
            y = n;
        }
        return n;
    }
```

> 辗转相除法求最大公约数
>
> x * y = 最大公约数 * 最小公倍数

```java
    public static int getLCM(int x ,int y){
        int n = getGenericCommonNumber(x,y);
        return x*y/n;
    }
```

**2.判断2个字符串是否存在包含，是返回第一次出现下标，不是返回-1**

```java
    /**
     * 判断两个字符串是否为字串，是返回下标，不是返回 -1
     */
    public static int getSubStr(String s1,String s2){
        return s1.length() > s2.length() ? s1.indexOf(s2) : s2.indexOf(s1);
    }
```

自己实现 indexof 同款方法

```java
public static int getIndex(String s1,String s2){
        char[] c1 = s1.toCharArray();
        char[] c2 = s2.toCharArray();
        int i =0 , j = 0;
        for(;i <= c1.length - c2.length;i++){
            for(j = 0;j<c2.length;j++){
                if(c1[i+j]!=c2[j]) {
                    break;
                }
            }
            if(c2.length == j){
                return i;
            }
        }
       return -1;
    }
```

### 分治、二分、贪心

### 排序算法

### 红黑树

搜索算法：回溯、递归、深度优先遍历，广度优先遍历，二叉搜索树等

动态规划：背包问题、最长子序列

### 数组与链表：单 / 双向链表

https://www.pianshen.com/article/25281968288/



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

## 十二、总结

https://segmentfault.com/a/1190000037612679?utm_source=sf-related



1. 对象如何进行深拷贝，除了clone

   > Java 中拷贝分为深拷贝与浅拷贝 ，浅拷贝是拷贝源对象的地址，所以源对象的值发生变化，拷贝对象也发生变化。
   >
   > 深拷贝是将源对象所有的值进行拷贝，重新生成一个新的对象，所以源对象与拷贝对象的值并不会互相影响。
   >
   > 实现深拷贝的方式：
   >
   > 1. 实现Cloneable 接口，并重写 clone 方法，在方法中创建进行赋值
   > 2.  使用构造函数实现深拷贝，通过构造函数创建新的对象
   > 3. 序列化实现深拷贝，此种方式有多种实现，有 Gson 序列化，JackSon 等

2. happen-before原则

   > happeen-before 是  Java 内存模型中一个核心的概念 ，Java 内存模式在多线程的场景下，所有的变量都存储在主内存中，每个线程都有自己的
   >
   > 工作内存，线程对变量的修改都是在工作内存中，最后刷新到主内存。
   >
   > happen-before   a 线程 happen-before b 线程，那么 a 线程中写的结果 b 线程是可以立即知道的 ，即前者的结果被后者可以立即可见
   >
   > a 线序 i = 1 , b 线程可以立即知道  b = i (此时 i = 1 )

3. jvm调优的实践

   > JvisualVM 可以查看本地或远程 jvm 运气情况
   >
   > -Xms ，-Xmx 设置 jvm 堆的初始大小和最大值
   >
   > jstat 查看堆内存使用情况
   >
   > jstat -gc 垃圾回收统计
   >
   > jmap  内存使用情况，对内存溢出分析与定位
   >
   > jhat 分析 dump 文件，内存发生溢出时，给内存生成一个 dump 文件

4. 单例对象会被jvm的gc时回收吗

   >  jvm 中判断对象是否应该被回收是通过可达性分析判断改对象是否可达，判断是否可达通过一系列根节点 gc  root 。
   >
   > 可以作为 gc root 的包括
   >
   > 1. 虚拟机栈中引用的对象
   > 2. 方法区中常量引用的对象
   > 3. **方法区中静态属性引用的对象**
   > 4. 本地方法栈中 jni 的引用的对象
   >
   > 单例对象是方法区静态属性引用的对象，是可达的，所以不会被回收。
   >
   > **什么情况下单例会被回收？**
   >
   > 对象被回收需要满足以下条件
   >
   > 1. **改类的所有实例都被回收**
   > 2. 加载改类的 ClassLoader 被回收
   > 3. 改类对应的 java.lang.Class 对象没有被引用，无法通过反射获取类的方法
   >
   > 单例无法满足第一条，因此单例类不会被卸载

5. redis如果list较大，怎么优化

   > 采用分片
   >
   > redids  list 可以存储 2*32 个 key , 单个 String value 可以存储 512M

6. tcp的沾包与半包

   > tcp 传输是面向数据流的，在发送与接受的缓冲区中，可能发生沾包，即将2个包一起发送或者接受
   >
   > udp 不会发生，因为udp 是面向数据包的，包之间有明显的边界

7. 项目中有使用过netty吗

   > JBoss 做的一个 jar 包， 快速的开发高性能，高可靠性的网络服务器与客户端程序
   >
   > 提供异步的，事件驱动的网络应用程序框架和工具

8. TSL1.3新特性

   > TLS1.3 是一种新的加密协议，它能提高各地互联网用户的发访问速度，又增强安全性
   >
   > TLS  把使用互联网安全通信的基础性技术称为传输层安全协议， TLS 是安全套接层 SSL 的进化版本

9. AES算法原理

   > https://blog.csdn.net/gulang03/article/details/81175854

10. redis集群的使用

11. mysql与mogo对比

12. 场景题：设计一个im系统包括群聊单聊

13. 场景题：设计数据库连接池

14. 场景题：秒杀场景的设计

> https://www.cnblogs.com/ilovejaney/p/13924895.html
>
> https://www.jianshu.com/p/d789ea15d060

联网 ip :

> 113.96.230.240:8000
>
> 192.168.2.255:8235
>
> 192.168.1.250

