====



# MySQL

[Java 全栈知识体系](https://www.pdai.tech/)

[JavaGuide](https://snailclimb.gitee.io/javaguide/)

## 一、常见问题

### 1.1. 怎么优化一个慢查询

#### 1.1.1. 什么是慢查询

**MySQL慢查询就是在日志中记录运行比较慢的SQL语句**，这个功能需要开启才能用。

在MySQL的配置文件my.cnf中写上：

```cpp
long_query_time = 10
log-slow-queries = /var/lib/mysql/mysql-slow.log
```

`long_query_time`是指执行超过多久的SQL会被日志记录下来，这里是10 秒。
`log-slow-queries`设置把日志写在那里（例子中慢查询日志会写到文件/var/lib/mysql/mysql-slow.log中），为空的时候，系统会给慢查询日志赋予主机名，并加上slow.log。如果设置了参数log-long-format ，那么所有没有使用索引的查询也将被记录。

这是一个非常有用的日志。它对于性能的影响不大（假设所有查询都很快），并且强调了那些最需要注意的查询（丢失了索引或索引没有得到最佳应用）。

#### 1.1.2. 查询SQL为什么慢=慢查询最根本的问题就是需要访问的数据太多，导致查询不可避免的需要筛选大量的数据。

​	面对慢查询，我们需要注意以下两点：

- 查询了过多不需要的数据

- 扫描了额外的记录

##### 1.1.2.1 **查询了过多不需要的数据**

​	MySQL并不是只返回需要的数据，实际上会返回全部结果集再进行计算。

​	尤其是多表关联查询 select * 的情况，我们是不是真的需要全部的列呢？如果不是，那我们**直接指定对应字段就好了**。

​	取出全部列，会让优化器无法完成索引覆盖扫描这类优化，还会为服务器带来额外的I/O、内存和CPU的消耗。

##### 1.1.2.2 扫描了额外的记录

​	此种情况大部分属于索引应用不当造成的（包括：应该建的索引没有建，或者未应用到最佳索引）。

​	示例表结构如下：

```SQL
CREATE TABLE test_table (
  name varchar(32) DEFAULT NULL,
  desc varchar(32) DEFAULT NULL,
  age int(16) DEFAULT NULL,
  id bigint(11) DEFAULT NULL,
  KEY idx_age (age)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

​	存在索引 `idx_age` 的情况下，查询执行计划结果展示如下：

```sql
EXPLAIN SELECT * FROM test_table WHERE age = 10;
```

​	预估访问1行数据即可命中数据，如删除有效索引 `idx_age` 后则会变成全表扫描（ALL），预估需要扫描表中全部记录才能完成这个查询

#### 1.1.3. 如何定位慢查询

 直接分析mysql慢查询日志 ,利用explain关键字可以模拟优化器执行SQL查询语句，来分析sql慢查询语句。

通常我们**使用EXPLAIN**，会得到如下下的执行计划信息

![image-20211110152413751](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211110152436.png) 

关于定位分析问题，关键看如下几点：

1）**select_type**

表示查询类型，用于区别普通查询、联合查询、子查询等复杂查询。

2）**type**

显示查询使用类型，从好到差依次为：system > const > eq_ref > ref > range > **index > all**

如果发现type的值是最后两个中的其中一个时，证明语句需要优化了。

3）**possible_keys 和 key**

分别指可能应用的索引和实际应用的索引。

注意：查询中若使用了覆盖索引（select 后要查询的字段刚好和创建的索引字段完全相同），则该索引仅出现在key列表中。

4）**rows**

大致估算出找到所需记录所需要读取的行数（从效率上来讲，数值越小越好）

5）**Extra**

重要的额外信息。包含MySQL解决查询的详细信息，也是关键参考项之一。不太友好的值有：Using filesort, Using temporary。

#### 1.14. 如何优化慢查询

对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。

**避免索引失效**

- **使用**`**!=**` **或者** `**<>**` **导致索引失效**。
- **类型不一致导致的索引失效**。
- **函数导致的索引失效**。如：`SELECT * FROM user WHERE DATE(create_time) = '2020-09-03';` 如果`create_time`添加了索引，索引会失效。
- **运算符导致的索引失效**。如：`SELECT * FROM user WHERE age - 1 = 20;` 如果对列进行了（+，-，*，/，!）, 那么都将不会走索引。
- `**OR**`**引起的索引失效**。OR导致索引是在特定情况下的，并不是所有的OR都是使索引失效，如果OR连接的是同一个字段，那么索引不会失效，反之`OR`右侧字段索引失效。
- **模糊搜索导致的索引失效**。当`%`放在匹配字段前是不走索引的，放在后面才会走索引。
- `**NOT IN**`**、**`**NOT EXISTS**`**导致索引失效**。

### 1.2. 主从复制、读写分离

#### 1.2.1 为什么使用主从复制、读写分离

**主从复制、读写分离是为了数据库能够支持更大的并发**

随着业务量的扩展，如果是单机部署的MySQL，会导致I/O频率过高。采用**主从复制、读写分离可以提高数据库的可用性。**

#### 1.2.2 主从复制原理

1. 当**Master**执行insert,delete,update操作时，会按顺序写入**binlog**中；
2. **Salve**从库连接**master**主库，**Master**有多少个**Salve**就会创建多少个**binlong dump**线程；
3. 当**Master**节点的**binlog**发生变化时，**binlog dump** 线程会通知所有的**salve**节点，并将相应的**binglog**内容推送给**Salve**节点；
4. **Salve**节点的**I/O**线程接收到**binlog**内容后，将内容写入到本地的**relay-log**;
5. **SQL**线程读取**I/O线程写入的relay-log**，并且根据relay-log的内容对从数据库做对应的操作。

#### 1.2.3 主从复制、读写分离存在的问题

1. **网络延迟**

   **问题描述：**

   ​	从机是通过binlog日志从master同步数据的，如果在网络延迟的情况，从机就会出现数据延迟。那么就有可能出现master写入数据后，slave读取数据不一定能马上读出来。

   **解决方法：**

   ​	ShardingSphere-JDBC框架在实现读写分离的时候已经考虑到了该问题，它的解决方式为：

   同一线程且同一数据库连接内，如有写入操作，以后的操作均从主库读取，用户保证数据一致性

2. **主从同步延时**

   **问题描述：**

   ​	从库同步主库数据的过程是串行化的，也就是说主库上并行的操作，在从库上会串行执行。所以这就是一个非常重要的点了，由于从库从主库拷贝日志以及串行执行 SQL 的特点，在高并发场景下，从库的数据一定会比主库慢一些，是**有延时**的。所以经常出现，刚写入主库的数据可能是读不到的，要过几十毫秒，甚至几百毫秒才能读取到。

   **解决方法：**

   ​	**并行复制**，指的是从库开启多个线程，并行读取 relay log 中不同库的日志，然后**并行重放不同库的日志？**，这是库级别的并行。

3. 主库数据丢失

   **问题描述：**

   ​	如果主库突然宕机，然后恰好数据还没同步到从库，那么有些数据可能在从库上是没有的，有些数据可能就丢失了。

   **解决方法：**

   ​	**半同步复制**，也叫`semi-sync`复制，指的就是主库写入 binlog 日志之后，就会将**强制**此时立即将数据同步到从库，从库将日志写入自己本地的 relay log 之后，接着会返回一个 ack 给主库，主库接收到**至少一个从库**的 ack 之后才会认为写操作完成了。

### 1.3. MySQL最左前缀原则

1. 如果你创建一个联合索引, 那这个索引的任何前缀都会用于查询,` (col1, col2, col3)`这个联合索引的所有前缀 就是`(col1), (col1, col2)`, `(col1, col2, col3)`, 包含这些列的查询都会启用索引查询，值得注意的是，当遇到范围查询(>、<、between、like)就会停止匹配。
2.  其他所有不在最左前缀里的列都不会启用索引, 即使包含了联合索引里的部分列也不行. 即上述中的`(col2)`, `(col3), (col2, col3) `都不会启用索引去查询.
    **注意, (col1, col3)会启用(col1)的索引查询**

**最左前缀原则原理**

最左匹配原则是针对联合索引来说的，所以我们有必要了解一下联合索引的原理。

索引的底层是一颗B+树，联合索引也是一颗B+树。只不过联合所有的键值数量不是一个，而是多个，构建一颗B+树只能根据一个值来构建，因此数据库依据联合索引最左的字段来构建B+树。

例子：假如创建一个（a,b)的联合索引，那么它的索引树是这样的

![在这里插入图片描述](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211110171603.png)

可以看到a的值是有顺序的，1，1，2，2，3，3，而b的值是没有顺序的1，2，1，4，1，2。所以b = 2这种查询条件没有办法利用索引，因为联合索引首先是按a排序的，b是无序的。

同时我们还可以发现在a值相等的情况下，b值又是按顺序排列的，但是这种顺序是相对的。所以**最左匹配原则遇上范围查询就会停止**，剩下的字段都无法使用索引。例如a = 1 and b = 2 a,b字段都可以使用索引，因为在a值确定的情况下b是相对有序的，而a>1and b=2，a字段可以匹配上索引，但b值不可以，因为a的值是一个范围，在这个范围中b是无序的。

### 1.4 如何做MySQL的性能优化

#### 1.4.1 使用Explain进行分析

Explain 用来分析 SELECT 查询语句，开发人员可以通过分析 Explain 结果来优化查询语句。

比较重要的字段有:

- select_type : 查询类型，有简单查询、联合查询、子查询等
- key : 使用的索引
- rows : 扫描的行数，大致估算出找到所需记录所需要读取的行数（从效率上来讲，数值越小越好）

#### 1.4.2 优化数据访问

##### 减少请求的数据量

- 只返回必要的列：最好不要使用select * 语句。
- 只返回必要的行：使用 LIMIT 语句来限制返回的数据。
- 缓存重复查询的数据：使用缓存可以避免在数据库中进行查询，特别在要查询的数据经常被重复查询时，缓存带来的查询性能提升将会是非常明显的。

##### 减少服务器端扫描的行数

- 最有效的方式就是使用索引来覆盖查询
  - 遵循索引创建原则
    - **较频繁的作为查询条件**的字段应该创建索引
    - **唯一性太差的字段**不合适单独创建索引，即使该字段频繁作为查询条件
    - **更新频繁的字段**不适合创建索引
    - 创建**索引字段应该设为非空**：含有控制的列很难进行查询优化
    - **取值离散大的字段**适合做索引
    - **索引字段越小越好**：数据库的数据存储以页为单位，一页存储的数据越多，一次IO操作获取的数据越大，效率越高
  - 避免索引失效
    - **使用 != 或<>**导致索引失效
    - **类型不一致**导致索引失效
    - **函数**导致索引失效
    - **运算符**导致索引失效
    - **OR** 引起的索引失效，ORl 连接的不是同一字段会导致右侧字段索引失效
    - **模糊搜索**导致的索引失效
    - **NOT IN、NOT EXISTS** 导致索引失效、

#### 1.4.3 重构查询方式

##### 切分大查询

一个大查询如果一次性执行的话，可能一次锁住很多数据、占满整个事务日志、耗尽系统资源、阻塞很多小而重要的查询

##### 分解大连接查询

将一个大连接查询分解成对每一个表进行一次的单表查询，然后将结果在应用程序中进行关联，这样做的好处有：

- 让缓存更高效。对于连接查询，如果其中一个表发生变化，那么整个查询缓存就无法使用。而分解后的多个查询，即使其中一个表发生变化，对其他表的查询缓存依然可以使用。
- 分解成多个单表查询，这些单表查询的缓存结果更可能被其他查询使用到，从而减少冗余记录的查询。
- 减少锁竞争。
- 在应用层进行连接，更容易对数据库进行拆分，从而更容易做到高性能和可伸缩。
- 查询本身效率也可能会有所提升。下面的例子使用 IN() 代替连接查询，可以让MySQL按照 ID 顺序进行查询，这可能比随机的连接更高效

```sql
SELECT * FROM tab
JOIN tag_post ON tag_post.tag_id=tag.id
JOIN post ON tag_post.post_id=post.id
WHERE tag.tag='mysql';
```

```sql
SELECT * FROM tag WHERE tag='mysql';
SELECT * FROM tag_post WHERE tag_id=1234;
SELECT * FROM post WHERE post.id IN (123,456,567,9098,8904);
```

### 1.5 如何保证mysql与redis的数据一致性？

![image-20211202102839856](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20211202102839856.png)



## 二、MVCC

版权声明：本文为CSDN博主「SnailMann」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/SnailMann/article/details/94724197

[InnoDB存储引擎对MVCC的实现](https://snailclimb.gitee.io/javaguide/#/docs/database/mysql/innodb-implementation-of-mvcc)

### 2.1 什么是MVCC

> MVCC，全称Multi-Version Concurrency Control，即多版本并发控制。MVCC是一种并发控制的方法，一般在数据库管理系统中，实现对数据库的并发访问，在编程语言中实现事务内存。 mvcc - @百度百科

MVCC在MySQL InnoDB中的实现主要是为了

- 提高数据库并发性能
- 用更好的方式去处理读-写冲突，做到即使有读写冲突时，也能做到不加锁，非阻塞并发读

### 2.2 当前读和快照读

#### 2.2.1 当前读

如果执行的是下列语句，就是当前读

- `select ... lock in share mode`
- `select ... for update`
- `insert`、`update`、`delete` 操作

**它读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁**

#### 2.2.2 快照读

- 像不加锁的select操作就是快照读，即不加锁的非阻塞读；
- 快照读的前提是隔离级别不是串行级别，串行级别下的快照读会退化成当前读；
- 之所以出现快照读的情况，是基于提高并发性能的考虑，快照读的实现是基于多版本并发控制，\
- 即MVCC,可以认为MVCC是行锁的一个变种，但它在很多情况下，避免了加锁操作，降低了开销；既然是基于多版本，即快照读可能读到的并不一定是数据的最新版本，而有可能是之前的历史版本

说白了**MVCC就是为了实现读-写冲突不加锁**，而这个读指的就是**快照读**, 而非当前读，**当前读实际上是一种加锁的操作，是悲观锁的实现**

### 2.3 MVCC解决的问题

**数据库并发场景：**

- **读-读**：不存在任何问题，也不需要并发控制
- **读-写**：有线程安全问题，可能会造成事务隔离性问题，可能遇到脏读，幻读，不可重复读
- **写-写**：有线程安全问题，可能会存在更新丢失问题，比如第一类更新丢失，第二类更新丢失

**MVCC好处：**

多版本并发控制（MVCC）是一种用来解决读-写冲突的无锁并发控制，也就是**为事务分配单向增长的时间戳，为每个修改保存一个版本，版本与事务时间戳关联，读操作只读该事务开始前的数据库的快照**。 所以MVCC可以为数据库解决以下问题

- 在并发读写数据库时，可以做到在读操作时不用阻塞写操作，写操作也不用阻塞读操作，提高了数据库并发读写的性能
- 同时还可以解决脏读，幻读，不可重复读等事务隔离问题，
- 但不能解决更新丢失问题

### 2.4 MVCC的实现原理

MVCC的目的就是多版本并发控制，在数据库中的实现，就是为了解决读写冲突，它的实现原理主要是依赖记录中的**4个隐式字段**，**undo日志**，**Read View**来实现

> 在内部实现中，`InnoDB` 通过数据行的 `DB_TRX_ID` 和 `Read View` 来判断数据的可见性，如不可见，则通过数据行的 `DB_ROLL_PTR` 找到 `undo log` 中的历史版本。每个事务读到的数据版本可能是不一样的，在同一个事务中，用户只能看到该事务创建 `Read View` 之前已经提交的修改和该事务本身做的修改

#### 2.4.1 隐式字段

每行出除了我们自定义的字段外，还有数据库隐式定义的DB_TRX_ID，DB_ROLL_PTR,DB_ROW_ID等字段

| 字段名          | 大小  | 名称                      | 功能                                                        |
| --------------- | ----- | ------------------------- | ----------------------------------------------------------- |
| **DB_ROW_ID**   | 6byte | 隐含的自增ID（隐藏主键）  | 如果数据表没有主键，InnoDB会自动以DB_ROW_ID产生一个聚簇索引 |
| **DB_TRX_ID**   | 6byte | 最近修改(修改/插入)事务ID | 记录创建这条记录/最后一次修改该记录的事务ID                 |
| **DB_ROLL_PTR** | 7byte | 回滚指针                  | 指向这条记录的上一个版本（存储于rollback segment里）        |
| **DELETED_BIT** | 1byte | 记录被更新或删除          | 并不代表真的删除，而是删除flag变了                          |

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211126202620.png)

#### 2.4.2 undo日志

`undo log` 主要有两个作用：

- 当事务回滚时用于将数据恢复到修改前的样子
- 另一个作用是 `MVCC` ，当读取记录时，若该记录被其他事务占用或当前版本对该事务不可见，则可以通过 `undo log` 读取之前的版本数据，以此实现非锁定读

undo log主要分为3种：

- **Insert undo log** ：插入一条记录时，至少要把这条记录的主键值记下来，之后回滚的时候只需要把这个主键值对应的记录删掉就好了。
- **Update undo log**：修改一条记录时，至少要把修改这条记录前的旧值都记录下来，这样之后回滚时再把这条记录更新为旧值就好了。
- **Delete undo log**：删除一条记录时，至少要把这条记录中的内容都记下来，这样之后回滚时再把由这些内容组成的记录插入到表中就好了。
  - 删除操作都只是设置一下老记录的DELETED_BIT，并不真正将过时的记录删除。
  - 为了节省磁盘空间，InnoDB有专门的purge线程来清理DELETED_BIT为true的记录。为了不影响MVCC的正常工作，purge线程自己也维护了一个read view（这个read view相当于系统中最老活跃事务的read view）;如果某个记录的DELETED_BIT为true，并且DB_TRX_ID相对于purge线程的read view可见，那么这条记录一定是可以被安全清除的。

对MVCC有帮助的实质是**update undo log** ，undo log实际上就是存在rollback segment中旧记录链，它的执行流程如下：

1. **比如一个有个事务插入persion表插入了一条新记录，记录如下，name为Jerry, age为24岁，隐式主键是1，事务ID和回滚指针，我们假设为NULL**

![image-20211127103015932](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211127103053.png)

2. **现在来了一个事务1对该记录的name做出了修改，改为Tom**
   1. 在事务1修改该行(记录)数据时，数据库会**先对该行加排他锁**
   2. 然后把**该行数据拷贝到undo log**中，作为旧记录，既在undo log中有当前行的拷贝副本
   3. 拷贝完毕后，修改该行name为Tom，并且**修改隐藏字段的事务ID**为当前事务1的ID, 我们默认从1开始，之后递增，**回滚指针指向拷贝到undo log的副本记录**，既表示我的上一个版本就是它
   4. 事务提交后，**释放锁**

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211127103158.png)

3. **又来了个事务2修改person表的同一个记录，将age修改为30岁**
   1. 在事务2修改该行数据时，数据库也**先为该行加锁**

   2. 然后把**该行数据拷贝到undo log**中，作为旧记录，**发现该行记录已经有undo log了，那么最新的旧数据作为链表的表头，插在该行记录的undo log最前面**

   3. 修改该行age为30岁，并且**修改隐藏字段的事务ID**为当前事务2的ID, 那就是2，**回滚指针指向刚刚拷贝到undo log的副本记录**

   4. 事务提交，**释放锁**

      <img src="https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211127103312.png" alt="img" style="zoom:80%;" />

从上面，我们就可以看出，**不同事务或者相同事务的对同一记录的修改，会导致该记录的undo log成为一条记录版本线性表**，既链表，undo log的链首就是最新的旧记录，链尾就是最早的旧记录（当然就像之前说的该undo log的节点可能是会purge线程清除掉，向图中的第一条insert undo log，其实在事务提交之后可能就被删除丢失了

#### 2.4.3 Read View(读视图)

##### 2.4.3.1 什么是Read View

**Read View就是事务进行快照读操作的时候生产的读视图(Read View)**，在该事务执行的快照读的那一刻，会生成数据库系统当前的一个快照，记录并维护系统当前活跃事务的ID(当每个事务开启时，都会被分配一个ID, 这个ID是递增的，所以最新的事务，ID值越大)

##### 2.4.3.2 Read View的作用

Read View主要是用来做可见性判断的, 即当我们某个事务执行快照读的时候，对该记录创建一个Read View读视图，把它比作条件用来判断当前事务能够看到哪个版本的数据，既可能是当前最新的数据，也有可能是该行记录的undo log里面的某个版本的数据。

```java
class ReadView {
  /* ... */
private:
  trx_id_t m_low_limit_id;      /* 大于等于这个 ID 的事务均不可见 */

  trx_id_t m_up_limit_id;       /* 小于这个 ID 的事务均可见 */

  trx_id_t m_creator_trx_id;    /* 创建该 Read View 的事务ID */

  trx_id_t m_low_limit_no;      /* 事务 Number, 小于该 Number 的 Undo Logs 均可以被 Purge */

  ids_t m_ids;                  /* 创建 Read View 时的活跃事务列表 */

  m_closed;                     /* 标记 Read View 是否 close */
}
```

[`Read View`](https://github.com/facebook/mysql-8.0/blob/8.0/storage/innobase/include/read0types.h#L298) 主要是用来做可见性判断，里面保存了 “当前对本事务不可见的其他活跃事务”

主要有以下字段：

- `m_low_limit_id`：目前出现过的最大的事务 ID+1，即下一个将被分配的事务 ID。大于等于这个 ID 的数据版本均不可见
- `m_up_limit_id`：活跃事务列表 `m_ids` 中最小的事务 ID，如果 `m_ids` 为空，则 `m_up_limit_id` 为 `m_low_limit_id`。小于这个 ID 的数据版本均可见
- `m_ids`：`Read View` 创建时其他未提交的活跃事务 ID 列表。创建 `Read View`时，将当前未提交事务 ID 记录下来，后续即使它们修改了记录行的值，对于当前事务也是不可见的。`m_ids` 不包括当前事务自己和已提交的事务（正在内存中）
- `m_creator_trx_id`：创建该 `Read View` 的事务 ID

**事务可见性示意图**（[图源](https://leviathan.vip/2019/03/20/InnoDB的事务分析-MVCC/#MVCC-1)）：

![img](https://leviathan.vip/2019/03/20/InnoDB的事务分析-MVCC/trans_visible.jpg)

##### 2.4.3.3 可见性算法

在 `InnoDB` 存储引擎中，创建一个新事务后，执行每个 `select` 语句前，都会创建一个快照（Read View），**快照中保存了当前数据库系统中正处于活跃（没有 commit）的事务的 ID 号**。其实简单的说保存的是系统中当前不应该被本事务看到的其他事务 ID 列表（即 m_ids）。当用户在这个事务中要读取某个记录行的时候，`InnoDB` 会将该记录行的 `DB_TRX_ID` 与 `Read View` 中的一些变量及当前事务 ID 进行比较，判断是否满足可见性条件

[具体的比较算法](https://github.com/facebook/mysql-8.0/blob/8.0/storage/innobase/include/read0types.h#L161)如下：[图源](https://leviathan.vip/2019/03/20/InnoDB的事务分析-MVCC/#MVCC-1)

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211127112749.png)

1. 如果记录 DB_TRX_ID < m_up_limit_id，那么表明最新修改该行的事务（DB_TRX_ID）在当前事务创建快照之前就提交了，所以该记录行的值对当前事务是可见的
2. 如果 DB_TRX_ID >= m_low_limit_id，那么表明最新修改该行的事务（DB_TRX_ID）在当前事务创建快照之后才修改该行，所以该记录行的值对当前事务不可见。跳到步骤 5
3. m_ids 为空，则表明在当前事务创建快照之前，修改该行的事务就已经提交了，所以该记录行的值对当前事务是可见的
4. 如果 m_up_limit_id <= DB_TRX_ID < m_low_limit_id，表明最新修改该行的事务（DB_TRX_ID）在当前事务创建快照的时候可能处于“活动状态”或者“已提交状态”；所以就要对活跃事务列表 m_ids 进行查找（源码中是用的二分查找，因为是有序的）
   - 如果在活跃事务列表 m_ids 中能找到 DB_TRX_ID，表明：① 在当前事务创建快照前，该记录行的值被事务 ID 为 DB_TRX_ID 的事务修改了，但没有提交；或者 ② 在当前事务创建快照后，该记录行的值被事务 ID 为 DB_TRX_ID 的事务修改了。这些情况下，这个记录行的值对当前事务都是不可见的。跳到步骤 5
   - 在活跃事务列表中找不到，则表明“id 为 trx_id 的事务”在修改“该记录行的值”后，在“当前事务”创建快照前就已经提交了，所以记录行对当前事务可见
5. 在该记录行的 DB_ROLL_PTR 指针所指向的 `undo log` 取出快照记录，用快照记录的 DB_TRX_ID 跳到步骤 1 重新开始判断，直到找到满足的快照版本或返回空

#### 2.4.4 整体流程

当事务2对某行数据执行了快照读，数据库为该行数据生成一个Read View读视图，假设当前事务ID为2，此时还有事务1和事务3在活跃中，事务4在事务2快照读前一刻提交更新了，所以Read View记录了系统当前活跃事务1，3的ID，维护在一个列表上，假设我们称为trx_list

| 事务1    | 事务2    | 事务3    | 事务4        |
| -------- | -------- | -------- | ------------ |
| 事务开始 | 事务开始 | 事务开始 | 事务开始     |
| …        | …        | …        | 修改且已提交 |
| 进行中   | 快照读   | 进行中   |              |
| …        | …        | …        |              |

Read View不仅仅会通过一个列表trx_list来维护事务2执行快照读那刻系统正活跃的事务ID，还会有两个属性up_limit_id（记录trx_list列表中事务ID最小的ID），low_limit_id(记录trx_list列表中下一个事务ID，也就是目前已出现过的事务ID的最大值+1)；所以在这里例子中up_limit_id就是1，low_limit_id就是4 + 1 = 5，trx_list集合的值是1,3，Read View如下图

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211127104755.png)

我们的例子中，只有事务4修改过该行记录，并在事务2执行快照读前，就提交了事务，所以当前该行当前数据的undo log如下图所示；我们的事务2在快照读该行记录的时候，就会拿该行记录的DB_TRX_ID去跟up_limit_id,low_limit_id和活跃事务ID列表(trx_list)进行比较，判断当前事务2能看到该记录的版本是哪个。

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211127104944.png)

所以先拿该记录DB_TRX_ID字段记录的事务ID 4去跟Read View的的up_limit_id比较，看4是否小于up_limit_id(1)，所以不符合条件，继续判断 4 是否大于等于 low_limit_id(5)，也不符合条件，最后判断4是否处于trx_list中的活跃事务, 最后发现事务ID为4的事务不在当前活跃事务列表中, 符合可见性条件，所以事务4修改后提交的最新结果对事务2快照读时是可见的，所以事务2能读到的最新数据记录是事务4所提交的版本，而事务4提交的版本也是全局角度上最新的版本

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211127105308.png)

### 2.5 相关问题

#### 2.5.1 RC,RR级别下的InnoDB快照读有什么不同？

正是Read View生成时机的不同，从而造成RC,RR级别下快照读的结果的不同

- 在RR级别下的某个事务的对某条记录的第一次快照读会创建一个快照及Read View, 将当前系统活跃的其他事务记录起来，此后在调用快照读的时候，还是使用的是同一个Read View，所以只要当前事务在其他事务提交更新之前使用过快照读，那么之后的快照读使用的都是同一个Read View，所以对之后的修改不可见；
- 即RR级别下，快照读生成Read View时，Read View会记录此时所有其他活动事务的快照，这些事务的修改对于当前事务都是不可见的。而早于Read View创建的事务所做的修改均是可见
- 而在RC级别下的，事务中，每次快照读都会新生成一个快照和Read View, 这就是我们在RC级别下的事务中可以看到别的事务提交的更新的原因

**总之在RC隔离级别下，是每个快照读都会生成并获取最新的Read View；而在RR隔离级别下，则是同一个事务中的第一个快照读才会创建Read View, 之后的快照读获取的都是同一个Read View**。

#### 2.5.2 如果解决幻读

`InnoDB`存储引擎在 RR 级别下通过 `MVCC`和 `Next-key Lock` 来解决幻读问题：

**1、执行普通 `select`，此时会以 `MVCC` 快照读的方式读取数据**

在快照读的情况下，RR 隔离级别只会在事务开启后的第一次查询生成 `Read View` ，并使用至事务提交。所以在生成 `Read View` 之后其它事务所做的更新、插入记录版本对当前事务并不可见，实现了可重复读和防止快照读下的 “幻读”

**2、执行 select...for update/lock in share mode、insert、update、delete 等当前读**

在当前读下，读取的都是最新的数据，如果其它事务有插入新的记录，并且刚好在当前事务查询范围内，就会产生幻读！`InnoDB` 使用 [Next-key Lock](https://dev.mysql.com/doc/refman/5.7/en/innodb-locking.html#innodb-next-key-locks) 来防止这种情况。当执行当前读时，会锁定读取到的记录的同时，锁定它们的间隙，防止其它事务在查询范围内插入数据。只要我不让你插入，就不会发生幻读

## 三、sharding-jdbc

## 四、平衡二叉树、B树，B+树	

[平衡二叉树、B树、B+树、B*树 理解其中一种你就都明白了](https://zhuanlan.zhihu.com/p/27700617)

### 4.1 平衡二叉树

#### 4.1.1 概念

平衡二叉树是基于二分法的策略提高数据的查找速度的二叉树的数据结构；

#### 4.1.2 特点

平衡二叉树是采用二分法思维把数据按规则组装成一个树形结构的数据，**用这个树形结构的数据减少无关数据的检索，大大的提升了数据检索的速度**；平衡二叉树的数据结构组装过程有以下规则：

（1）非叶子节点只能允许最多两个子节点存在。

（2）每一个非叶子节点数据分布规则为左边的子节点小当前节点的值，右边的子节点大于当前节点的值(这里值是基于自己的算法规则而定的，比如hash值)；

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211220145402.jpeg)

平衡树的层级结构：**因为平衡二叉树查询性能和树的层级（h高度）成反比，h值越小查询越快**、为了保证树的结构左右两端数据大致平衡降低二叉树的查询难度一般会采用一种算法机制实现节点数据结构的平衡，实现了这种算法的有比如[Treap](https://link.zhihu.com/?target=http%3A//baike.baidu.com/item/Treap)、红黑树，使用平衡二叉树能保证数据的左右两边的节点层级相差不会大于1，通过这样避免树形结构由于删除增加变成线性链表影响查询效率，保证数据平衡的情况下查找数据的速度近于二分法查找；

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211220145452.jpeg)

总结平衡二叉树特点：

（1）非叶子节点最多拥有两个子节点；

（2）非叶子节值大于左边子节点、小于右边子节点；

（3）树的左右两边的层级数相差不会大于1;

（4）没有值相等重复的节点;

### 4.2 B树(B-tree)

#### 4.2.1 概念

B树和平衡二叉树稍有不同的是B树属于多叉树又名平衡多路查找树（查找路径不只两个），数据库索引技术里大量使用者B树和B+树的数据结构，让我们来看看他有什么特点;

#### 4.2.2 规则

1. 排序方式：所有节点关键字是按递增次序排列，并遵循左小右大原则；
2. 子节点数：非叶节点的子节点数>1，且<=M ，且M>=2，空树除外（注：M阶代表一个树节点最多有多少个查找路径，M=M路,当M=2则是2叉树,M=3则是3叉）；
3. 关键字数：枝节点的关键字数量大于等于ceil(m/2)-1个且小于等于M-1个（注：ceil()是个朝正无穷方向取整的函数 如ceil(1.1)结果为2);
4. 所有叶子节点均在同一层、叶子节点除了包含了关键字和关键字记录的指针外也有指向其子节点的指针只不过其指针地址都为null

#### 4.2.3 特点

B树相对于平衡二叉树的不同是，每个节点包含的关键字增多了，特别是在B树应用到数据库中的时候，数据库充分利用了磁盘块的原理（磁盘数据存储是采用块的形式存储的，每个块的大小为4K，每次IO进行数据读取时，同一个磁盘块的数据可以一次性读取出来）把节点大小限制和充分使用在磁盘快大小范围；把**树的节点关键字增多后树的层级比原来的二叉树少了，减少数据查找的次数和复杂度**;

### 4.3 B+树

#### 4.3.1 概念

B+树是B树的一个升级版，相对于B树来说B+树更充分的利用了节点的空间，让查询速度更加稳定，其速度完全接近于二分法查找。为什么说B+树查找的效率要比B树更高、更稳定；我们先看看两者的区别

#### 4.3.2 规则

1. B+跟B树不同B+树的**非叶子**节点不保存关键字记录的指针，只进行数据索引，这样使得B+树每个**非叶子**节点所能保存的关键字大大增加；

2. B+树**叶子**节点保存了父节点的所有关键字记录的指针，所有数据地址必须要到叶子节点才能获取到。所以每次数据查询的次数都一样；

3. B+树叶子节点的关键字从小到大有序排列，左边结尾数据都会保存右边节点开始数据的指针。

4. 非叶子节点的子节点数=关键字数（来源百度百科）（根据各种资料 这里有两种算法的实现方式，另一种为非叶节点的关键字数=子节点数-1（来源维基百科)，虽然他们数据排列结构不一样，但其原理还是一样的Mysql 的B+树是用第一种方式实现）;

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211220150806.jpeg)

#### 4.3.3 特点

1. B+**树的层级更少**：相较于B树B+每个**非叶子**节点存储的关键字数更多，树的层级更少所以查询数据更快；

2. B+**树查询速度更稳定**：B+所有关键字数据地址都存在**叶子**节点上，所以每次查找的次数都相同所以查询速度要比B树更稳定;

3. B+**树天然具备排序功能：**B+树所有的**叶子**节点数据构成了一个有序链表，在查询大小区间的数据时候更方便，数据紧密性很高，缓存的命中率也会比B树高。

4. B+**树全节点遍历更快：**B+树遍历整棵树只需要遍历所有的**叶子**节点即可，，而不需要像B树一样需要对每一层进行遍历，这有利于数据库做全表扫描。

**B树**相对于**B+树**的优点是，如果经常访问的数据离根节点很近，而**B树**的**非叶子**节点本身存有关键字其数据的地址，所以这种数据检索的时候会要比**B+树**快。