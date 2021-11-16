# Hive

## 一、基本概念

### 1.1 什么是Hive

#### 1.1.1 Hive简介

​	Hive：由 Facebook 开源用于解决海量**结构化**日志的数据统计工具。

Hive 是基于 Hadoop 的一个**数据仓库工具**，可以将**结构化的数据文件映射为一张表**，并 提供**类 SQL** 查询功能。

#### 1.1.2 Hive本质

将HQL转化成MapReduce程序

![image-20211115111058365](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211115111110.png)

（1）Hive 处理的数据存储在 HDFS 

（2）Hive 分析数据底层的实现是 MapReduce 

（3）执行程序运行在 Yarn 上

### 1.2 Hive的优缺点

#### 1.2.1 优点

1. 操作接口采用**类 SQL** 语法，提供快速开发的能力（简单、容易上手）。
2. 避免了去写 MapReduce，**减少开发人员的学习成本**。
3. Hive 的执行**延迟比较高**，因此 Hive 常用于数据分析，对实时性要求不高的场合。
4. Hive **优势在于处理大数据**，对于处理小数据没有优势，因为 Hive 的执行延迟比较高。
5. Hive **支持用户自定义函数**，用户可以根据自己的需求来实现自己的函数。

#### 1.2.2 缺点

1. **Hive 的 HQL 表达能力有限**
   - 迭代式算法无法表达
   - 数据挖掘方面不擅长，由于 MapReduce 数据处理流程的限制，效率更高的算法却无法实现。
2. **Hive 的效率比较低**
   - Hive 自动生成的 MapReduce 作业，通常情况下不够智能化
   - Hive 调优比较困难，粒度较粗

### 1.3 Hive架构原理

![image-20211115170429221](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211115170429.png)

#### 1.3.1 用户接口：Client

- CLI（command-line interface）
- JDBC/ODBC(jdbc 访问 hive)
- WEBUI（浏览器访问 hive）

#### 1.3.2 元数据：Metastore

元数据包括：

- 表名
- 表所属的数据库（默认是 default）
- 表的拥有者
- 列/分区字段
-  表的类型（是否是外部表）
- 表的数据所在目录等；

默认存储在自带的 derby 数据库中，推荐使用 MySQL 存储 Metastore

#### 1.3.3 Hadoop

使用 HDFS 进行存储，使用 MapReduce 进行计算。

#### 1.3.4 驱动器：Driver

1. **解析器（SQL Parser）**：将 SQL 字符串转换成抽象语法树 AST，这一步一般都用第 三方工具库完成，比如 antlr；对 AST 进行语法分析，比如表是否存在、字段是否存在、SQL 语义是否有误。 
2. **编译器（Physical Plan）**：将 AST 编译生成逻辑执行计划。 
3. **优化器（Query Optimizer）**：对逻辑执行计划进行优化。 
4. **执行器（Execution）**：把逻辑执行计划转换成可以运行的物理计划。对于 Hive 来 说，就是 MR/Spark。

![image-20211115171249656](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211115171249.png)

​	Hive 通过给用户提供的一系列交互接口，接收到用户的指令(SQL)，使用自己的 Driver， 结合元数据(MetaStore)，将这些指令翻译成 MapReduce，提交到 Hadoop 中执行，最后，将执行返回的结果输出到用户交互接口。

### 1.4 Hive与数据库比较

​	由于 Hive 采用了类似 SQL 的查询语言 HQL(Hive Query Language)，因此很容易将 Hive 理解为数据库。其实从结构上来看，Hive 和数据库除了拥有类似的查询语言，再无类似之处。

#### 1.4.1 查询语言

​	由于 SQL 被广泛的应用在数据仓库中，因此，专门针对 Hive 的特性设计了类 SQL 的查 询语言 HQL。熟悉 SQL 开发的开发者可以很方便的使用 Hive 进行开发。

#### 1.4.2 数据更新

​	由于 Hive 是针对数据仓库应用设计的，而数据仓库的内容是读多写少的。因此，**Hive 中 不建议对数据的改写，所有的数据都是在加载的时候确定好的**。**而数据库中的数据通常是需要经常进行修改的**，因此可以使用 INSERT INTO … VALUES 添加数据，使用 UPDATE … SET 修 改数据。

#### 1.4.3 执行延迟

​	Hive 在查询数据的时候，由于没有索引，需要扫描整个表，因此**延迟较高**。另外一个导 致 Hive 执行延迟高的因素是 MapReduce 框架。由于 MapReduce 本身具有较高的延迟，因此 在利用 MapReduce 执行 Hive 查询时，也会有**较高的延迟**。相对的，数据库的执行延迟较低。 当然，这个低是有条件的，即数据规模较小，当数据规模大到超过数据库的处理能力的时候， Hive 的并行计算显然能体现出优势。

#### 1.4.4 数据规模

​	由于 Hive 建立在集群上并可以利用 MapReduce 进行并行计算，因此可以**支持很大规模的数据**；对应的，数据库可以支持的数据规模较小。