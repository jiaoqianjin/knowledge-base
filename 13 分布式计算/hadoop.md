# Hadoop

## 一、Hadoop 概述

### 1.1 Hadoop 是什么

1）Hadoop是一个由Apache基金会所开发的分布式系统基础架构。 

2）主要解决，海量数据的**存储**和海量数据的**分析计算**问题。

3）广义上来说，Hadoop通常是指一个更广泛的概念——Hadoop生态圈。

### 1.2 Hadoop 三大发行版本

Hadoop 三大发行版本：Apache、Cloudera、Hortonworks。 

- Apache 版本最原始（最基础）的版本，对于入门学习最好。2006 
- Cloudera 内部集成了很多大数据框架，对应产品 CDH。2008 
- Hortonworks 文档较好，对应产品 HDP。

2011 Hortonworks 现在已经被 Cloudera 公司收购，推出新的品牌 CDP。

### 1.3 Hadoop优势

1）高可靠性：Hadoop底层维护多个数据副本，所以即使Hadoop某个计算元 素或存储出现故障，也不会导致数据的丢失。

2）高扩展性：在集群间分配任务数据，可方便的扩展数以千计的节点。

3）高效性：在MapReduce的思想下，Hadoop是并行工作的，以加快任务处 理速度。

4）高容错性：能够自动将失败的任务重新分配。

### 1.4 Hadoop 组成（面试重点）

#### 1.4.1 Hadoop1.x、2.x、3.x区别

![image-20211007153430541](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211007153438.png)

在 Hadoop1.x 时 代 ， Hadoop中 的MapReduce同 时处理业务逻辑运算和资 源的调度，耦合性较大。

在Hadoop2.x时 代，增 加 了Yarn。Yarn只负责 资 源 的 调 度 ， MapReduce 只负责运算。

Hadoop3.x在组成上没 有变化。

#### 1.4.2 HDFS 架构概述

Hadoop Distributed File System，简称 HDFS，是一个分布式文件系统。

1）NameNode（nn）：存储文件的元数据，如文件名，文件目录结构，文件属性（生成时间、副本数、 文件权限），以及每个文件的块列表和块所在的DataNode等。

2）DataNode(dn)：在本地文件系统存储文件块数据，以及块数据的校验和。

3）Secondary NameNode(2nn)：每隔一段时间对NameNode元数据备份。

#### 1.4.3 YARN 架构概述

Yet Another Resource Negotiator 简称 YARN ，另一种资源协调者，是 Hadoop 的资源管理器。

1）ResourceManager（RM）：整个集群资源（内存、CPU等）的老大 

2）NodeManager（N M）：单个节点服务器资源老大 

3）ApplicationMaster（AM）：单个任务运行的老大 

4）Container：容器，相当一台独立的服务器，里面封装了任务运行所需要的资源，如内存、CPU、磁盘、网络等。

#### 1.4.4 MapReduce 架构概述

MapReduce 将计算过程分为两个阶段：Map 和 Reduce 

1）Map 阶段并行处理输入数据 

2）Reduce 阶段对 Map 结果进行汇总

### 1.5 大数据技术生态体系

![image-20211007155528050](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211007155528.png)

图中涉及的技术名词解释如下： 

1）Sqoop：Sqoop 是一款开源的工具，主要用于在 Hadoop、Hive 与传统的数据库（MySQL） 间进行数据的传递，可以将一个关系型数据库（例如 ：MySQL，Oracle 等）中的数据导进 到 Hadoop 的 HDFS 中，也可以将 HDFS 的数据导进到关系型数据库中。 

2）Flume：Flume 是一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统， Flume 支持在日志系统中定制各类数据发送方，用于收集数据； 

3）Kafka：Kafka 是一种高吞吐量的分布式发布订阅消息系统；

4）Spark：Spark 是当前最流行的开源大数据内存计算框架。可以基于 Hadoop 上存储的大数据进行计算。

5）Flink：Flink 是当前最流行的开源大数据内存计算框架。用于实时计算的场景较多。 

6）Oozie：Oozie 是一个管理 Hadoop 作业（job）的工作流程调度管理系统。 

7）Hbase：HBase 是一个分布式的、面向列的开源数据库。HBase 不同于一般的关系数据库， 它是一个适合于非结构化数据存储的数据库。 

8）Hive：Hive 是基于 Hadoop 的一个数据仓库工具，可以将结构化的数据文件映射为一张 数据库表，并提供简单的 SQL 查询功能，可以将 SQL 语句转换为 MapReduce 任务进行运行。其优点是学习成本低，可以通过类 SQL 语句快速实现简单的 MapReduce 统计，不必开发专门的 MapReduce 应用，十分适合数据仓库的统计分析。 

9）ZooKeeper：它是一个针对大型分布式系统的可靠协调系统，提供的功能包括：配置维护、 名字服务、分布式同步、组服务等。

### 1.6 推荐系统框架图

![image-20211007160541941](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211007160542.png)