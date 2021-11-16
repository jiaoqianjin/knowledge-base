# HDFS读写流程

## 概述

开始之前先看看其基本属性，HDFS（Hadoop Distributed File System）是GFS的开源实现。

**特点如下：**

- 能够运行在廉价机器上，硬件出错常态，需要具备高容错性
- 流式数据访问，而不是随机读写
- 面向大规模数据集，能够进行批处理、能够横向扩展
- 简单一致性模型，假定文件是一次写入、多次读取

**缺点：**

- 不支持低延迟数据访问
- 不适合大量小文件存储（因为每条元数据占用空间是一定的）
- 不支持并发写入，一个文件只能有一个写入者
- 不支持文件随机修改，仅支持追加写入

## 相关概念



## 写流程

我们要考虑的情况是如何新建一个文件，把数据写入该文件，最后关闭该文件。

![在这里插入图片描述](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211011091656.png)

① 客户端通过对 DistributedFileSystem 对象调用 create() 来新建文件。

② DistributedFileSystem 对 NameNode 创建一个RPC调用，在文件系统的命名空间中新建一个文件，此时该文件中还没有相应的数据块。

③ NameNode 执行各种不同的检查以确保这个文件不存在以及客户端有新建该文件的权限。

​	如果这些检查均通过， NameNode 就会为创建新文件记录一条记录；否则文件创建失败并向客户端抛一个 IOException 异常。

④ DistributedFileSystem 向客户端返回一个 FSDataOutputStream 对象，由此客户端可以开始写入数据。

​	就像读取事件一样， FSDataOutputStream 封装一个 DFSOutputStream 对象， 该对象负责处理 DataNode 和 NameNode 之间的通信。

⑤ 在客户端写人数据时， DFSOutputStream 将它分成一个个的数据包，并写入内部队列，称为“数据队列”(data queue)。

​	DataStreamer 处理数据队列，它的责任是挑选出适合存储数据副本的一组 DataNode ，并据此来要求 NameNode 分配新的数据块。

⑥ 这一组 DataNode 构成一个管线(Pipeline)一一我们假设副本数为3，所以管线中有3个节点。

⑦ DataStreamer 将数据包流式传输到管线中第1个 DataNode ，该 DataNode 存储数据包并将它发送到管线中的第2个 DataNode 。

⑧ 同样，第2个 DataNode 存储该数据包并且发送给管线中的第3个(也是最后一个) DataNode。

⑨ DFSOutputStream 也维护着一个内部数据包队列来等待 DataNode 的收到确认回执，称为“确认队列”(ack queue)。

​	收到管道中所有 DataNode 确认信息后，该数据包才会从确认队列删除。

⑩ 客户端完成数据的写入后，对数据流调用 close() 方法。