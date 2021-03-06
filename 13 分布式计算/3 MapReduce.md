#  MapReduce

## 一、MapReduce概述

###  1.1 MapReduce定义

MapReduce是一个分布式运算程序的编程框架，是用户开发“基于Hadoop的数据分析应用”的核心框架。

MapReduce核心功能是将用户编写的业务逻辑代码和自带默认组件整合成一个完整的分布式运算程序，并发运行在一个Hadoop集群上。

### 1.2. MapReduce优缺点

#### 优点

1. **MapReduce易于编程**

   ​	**它简单的实现一些接口，就可以完成一个分布式程序**，这个分布式程序可以分布到大量廉价的PC机器上运行。也就是说你写一个分布式程序，跟写一个简单的串行程序是一模一样的。就是因为这个特点使得MapReduce编程变得非常流行。

2. **良好的扩展性**

   ​	当你的计算资源不能得到满足的时候，你可以通过**简单的增加机器来扩展它的计算能力**。

3. 高容错性

   ​	MapReduce设计的初衷就是使程序能够部署在廉价的PC机器上，这就要求它具有很高的容错性。比如其中一台机器挂了，它可以把上面的计算任务转移到另外一个节点上运行，不至于这个任务运行失败，而且这个过程**不需要人工参与，而完全是由Hadoop内部完成的**。

4. **适合PB级以上海量数据的离线处理**

   可以实现**上千台**服务器集群并发工作，提供数据处理能力。

#### 缺点

1. **不擅长实时计算**

   MapReduce无法像MySQL一样，在毫秒或者秒级内返回结果。

2. **不擅长流式计算**

   ​	流式计算的输入数据是动态的，而**MapReduce的输入数据集是静态的**，不能动态变化。这是因为MapReduce自身的设计特点决定了数据源必须是静态的。	

3. **不擅长DAG（有向无环图）计算**

   ​	多个应用程序存在依赖关系，后一个应用程序的输入为前一个的输出。在这种情况下，MapReduce并不是不能做，而是使用后，**每个MapReduce作业的输出结果都会写入到磁盘，会造成大量的磁盘IO，导致性能非常的低下。**

### 1.3 MapReduce的核心思想

![image-20211108150126590](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211108150705.png)

（1）分布式的运算程序往往需要分成至少2个阶段。

（2）第一个阶段的**MapTask并发实例，完全并行运行**，互不相干。

（3）第二个阶段的**ReduceTask并发实例互不相干**，但是他们的数据依赖于上一个阶段的所有MapTask并发实例的输出。

（4）MapReduce编程模型只能包含一个Map阶段和一个Reduce阶段，如果用户的业务逻辑非常复杂，那就只能多个MapReduce程序，串行运行。

总结：分析WordCount数据流走向深入理解MapReduce核心思想。

### 1.4常用数据类型

| **Java****类型** | **Hadoop Writable****类型** |
| ---------------- | --------------------------- |
| Boolean          | BooleanWritable             |
| Byte             | ByteWritable                |
| Int              | IntWritable                 |
| Float            | FloatWritable               |
| Long             | LongWritable                |
| Double           | DoubleWritable              |
| String           | Text                        |
| Map              | MapWritable                 |
| Array            | ArrayWritable               |
| Null             | NullWritable                |

###  1.5 MapReduce编程规范

用户编写的程序分成三个部分：Mapper、Reducer和Driver。

## 二、常见问题

### 2.1 小文件切片问题

框架默认的TextInputFormat切片机制是对任务按文件规划切片，不管文件多小，都会是一个单独的切片，都会交给一个MapTask，这样如果有大量小文件，就会产生大量的MapTask，处理效率极其低下。