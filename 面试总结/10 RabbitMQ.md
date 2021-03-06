# RabbitMQ总结

## MQ 产品选择

| RabbitMQ       | ActiveMQ                                                    | RocketMQ                             | Kafka                    |                                               |
| -------------- | ----------------------------------------------------------- | ------------------------------------ | ------------------------ | --------------------------------------------- |
| 开发语言       | Erlang                                                      | Java                                 | Java                     | Scala                                         |
| 客户端支持语言 | 官方支持Erlang,Java,Ruby等,社区产出多种API,几乎支持所有语言 | Java，C, C++,Python，PHP,Perl,.net等 | Java，C++                | 官方支持Java,社区产出多种API，如PHP,Python等  |
| 单机吞吐量     | 万级（其次）                                                | 万级（最差）                         | 十万级（最好）           | 十万级（次之）                                |
| 消息延迟       | 微秒级                                                      | 毫秒级                               | 毫秒级                   | 毫秒以内                                      |
| 功能特性       | 并发能力强，性能极其好，延时低，社区活跃，管理界面丰富      | 老牌产品，成熟度高，文档较多         | MQ功能比较完备，扩展性佳 | 只支持主要的MQ功能,毕竟是为大数据领域准备的。 |

## 一、 RabbitMQ的消息模型及使用场景分析

### 1.1 Hello World（基本）模型

生产者将消息发送到队列，消费者从队列中获取消息，队列是存储消息的缓冲区。

![基本消息模型](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211019203551.png)

### 1.2 Work模型

工作队列或者竞争消费者模式。

当消息处理比较耗时的时候，可能生产消息的速度会远远大于消息的消费速度。长此以往，消息就会堆积越来越多，无法及时处理。此时就可以使用work模型:让多个消费者绑定到一个队列，共同消费队列中的消息。队列中的消息一旦消费，就会消失，因此任务是不会被重复执行的。

**一个消息只能被一个消费者消费**。

可以将平均消费，配置成能者多劳的模式。

![Work消息模型](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211019203600.png)

### 1.3 Publish/Subscribe（订阅）模型——FanOut

**Fanout，也称为广播。**

- 1个生产者对应多个消费者
- 每一个消费者都有自己的队列。
- 每个队列都要绑定到交换机。
- 生产者只能将消息发送到交换机，交换机把消息发送给绑定过的所有队列。
- 队列的消费者都可以拿到消息，实现**一个消息被多个消费者获取**的目的。

X（Exchanges）：交换机一方面：接收生产者发送的消息。另一方面：知道如何处理消息，例如递交给某个特别队列、递交给所有队列、或是将消息丢弃。到底如何操作，取决于Exchange的类型。

![订阅模型-FanOut](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211019203603.png)

### 1.4 Routing模型—Direct



在FanOut模式中，一个消息会被所有订阅的队列都消费。在某些场景下，需要不同的消息被不同的队列消费，这是要使用Direct类型的交换机。

在Direct下：

- 交换机与指定RoutingKey的队列绑定
- 消息发送方向交换机发送时，必须指定消息的RoutingKey
- 交换机将消息发送给指定的RoutingKey的队列，只有队列RoutingKey和消息的RoutingKey一致的才会接到消息

![订阅模型-Routing](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211019203608.png)

### 1.5 Topic模型

与Direct类似，只是可以使用通配符来决定将消息发送给那些队列。

- `*`匹配到一个单词
- `#`匹配到多个单词



![订阅模型-Topic](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211019203612.png)

具体使用可参看：[rabbitmq五种消息模型整理](https://www.cnblogs.com/ifme/p/12024064.html)

## 二、 RabbitMQ常见面试题及解决方案

### 2.1 为什么使用消息队列

#### 2.1.1 解耦

​		看这么个场景。A 系统发送数据到 BCD 三个系统，通过接口调用发送。如果 E 系统也要这个数据呢？那如果 C 系统现在不需要了呢？A 系统负责人几乎崩溃......

​		在这个场景中，A 系统跟其它各种乱七八糟的系统严重耦合，A 系统产生一条比较关键的数据，很多系统都需要 A 系统将这个数据发送过来。A 系统要时时刻刻考虑 BCDE 四个系统如果挂了该咋办？要不要重发，要不要把消息存起来？头发都白了啊！

​		如果使用 MQ，A 系统产生一条数据，发送到 MQ 里面去，哪个系统需要数据自己去 MQ 里面消费。如果新系统需要数据，直接从 MQ 里消费即可；如果某个系统不需要这条数据了，就取消对 MQ 消息的消费即可。这样下来，A 系统压根儿不需要去考虑要给谁发送数据，不需要维护这个代码，也不需要考虑人家是否调用成功、失败超时等情况。

**总结：通过一个 MQ，Pub/Sub 发布订阅消息这么一个模型，A 系统就跟其它系统彻底解耦了。**

#### 2.1.2 异步

​		再来看一个场景，A 系统接收一个请求，需要在自己本地写库，还需要在 BCD 三个系统写库，自己本地写库要 3ms，BCD 三个系统分别写库要 300ms、450ms、200ms。最终请求总延时是 3+300 + 450 + 200 = 953ms，接近 1s，用户感觉搞个什么东西，慢死了慢死了。用户通过浏览器发起请求，等待个 1s，这几乎是不可接受的。

​		一般互联网类的企业，对于用户直接的操作，一般要求是每个请求都必须在 200 ms 以内完成，对用户几乎是无感知的。

​		如果使用 MQ，那么 A 系统连续发送 3 条消息到 MQ 队列中，假如耗时 5ms，A 系统从接受一个请求到返回响应给用户，总时长是 3 + 5 = 8ms，对于用户而言，其实感觉上就是点个按钮，8ms 以后就直接返回了，爽！网站做得真好，真快！

#### 2.1.3 削峰

​		每天 0:00 到 12:00，A 系统风平浪静，每秒并发请求数量就 50 个。结果每次一到 12:00 ~ 13:00，每秒并发请求数量突然会暴增到 5k+ 条。但是系统是直接基于 MySQL 的，大量的请求涌入MySQL，每秒钟对 MySQL 执行约 5k 条 SQL。一般的 MySQL，扛到每秒 2k 个请求就差不多了，如果每秒请求到 5k 的话，可能就直接把MySQL 给打死了，导致系统崩溃，用户也就没法再使用系统了。但是高峰期一过，到了下午的时候，就成了低峰期，可能也就 1w 的用户同时在网站上操作，每秒中的请求数量可能也就 50 个请求，对整个系统几乎没有任何的压力。

​		如果使用 MQ，每秒 5k 个请求写入 MQ，A 系统每秒钟最多处理 2k 个请求，因为 MySQL 每秒钟最多处理 2k 个。A 系统从 MQ 中慢慢拉取请求，每秒钟就拉取 2k 个请求，不要超过自己每秒能处理的最大请求数量就 ok，这样下来，哪怕是高峰期的时候，A 系统也绝对不会挂掉。而MQ 每秒钟 5k 个请求进来，就 2k 个请求出去，结果就导致在中午高峰期（1 个小时），可能有几十万甚至几百万的请求积压在 MQ 中。

​		这个短暂的高峰期积压是 ok 的，因为高峰期过了之后，每秒钟就 50 个请求进 MQ，但是 A 系统依然会按照每秒 2k 个请求的速度在处理。所以说，只要高峰期一过，A 系统就会快速将积压的消息给解决掉。

### 2.2 MQ性能对比

![image-20211019210924108](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211019210924.png)

​		一般的业务系统要引入 MQ，最早大家都用 ActiveMQ，但是现在确实大家用的不多了，没经过大规模吞吐量场景的验证，社区也不是很活跃，所以大家还是算了吧，我个人不推荐用这个了；


​		后来大家开始用 RabbitMQ，但是确实 erlang 语言阻止了大量的 Java 工程师去深入研究和掌控它，对公司而言，几乎处于不可控的状态，但是确实人家是开源的，比较稳定的支持，活跃度也高；

​		不过现在确实越来越多的公司会去用 RocketMQ，确实很不错，毕竟是阿里出品，但社区可能有突然黄掉的风险（目前 RocketMQ 已捐给 Apache，但 GitHub 上的活跃度其实不算高）对自己公司技术实力有绝对自信的，推荐用 RocketMQ，否则回去老老实实用 RabbitMQ 吧，人家有活跃的开源社区，绝对不会黄。

​		**所以中小型公司，技术实力较为一般，技术挑战不是特别高，用 RabbitMQ 是不错的选择；大型公司，基础架构研发实力较强，用 RocketMQ 是很好的选择。**
​		如果是**大数据领域的实时计算、日志采集等场景，用 Kafka 是业内标准的，绝对没问题，社区活跃度很高**，绝对不会黄，何况几乎是全世界这个领域的事实性规范。

### 2.3 如何保证RabbitMQ的高可用

RabbitMQ 是比较有代表性的，因为是基于主从（非分布式）做高可用性的

**RabbitMQ 有三种模式：单机模式、普通集群模式、镜像集群模式。**

#### 2.3.1 单机模式

​		单机模式，就是 Demo 级别的，一般就是你本地启动了玩玩儿的😄，没人生产用单机模式。

#### 2.3.2 普通集群模式（无高可用性）

​		普通集群模式，意思就是在多台机器上启动多个 RabbitMQ 实例，每个机器启动一个。你创建的 queue，只会放在一个 RabbitMQ 实例上，但是每个实例都同步 queue 的元数据（元数据可以认为是 queue 的一些配置信息，通过元数据，可以找到 queue 所在实例）。你消费的时候，实际上如果连接到了另外一个实例，那么那个实例会从 queue 所在实例上拉取数据过来。

**优点：**

​		集群中多个节点服务某个queue的读写操作，提高了吞吐量。

**缺点：**

- 如果消费者每次随机连接一个实例然后拉取数据，会造成数据拉取的开销；
- 存放数据实例宕机，会导致其他实例无法拉取实例；
- 如果固定连接那个 queue 所在实例消费数据，会导致单实例性能瓶颈；

#### 2.3.3 镜像集群模式（高可用性）

​		跟普通集群模式不一样的是，在镜像集群模式下，你创建的 queue，无论元数据还是 queue 里的消息都会存在于多个实例上，就是说，每个 RabbitMQ 节点都有这个 queue 的一个完整镜像，包含 queue 的全部数据的意思。然后每次你写消息到 queue 的时候，都会自动把消息同步到多个实例的 queue 上。

**优点：**

​		高可用，任何一个机器宕机，其他机器还包含了这个queue的完整数据。

**缺点：**

- 消息需要同步到所有机器，导致网络带宽压力和消耗很重；
- 如果某个queue负载很重，扩展的时候，不容易线性扩展；

### 2.4 如何保证消息不被重复消费（消息幂等性问题）

**什么是幂等性？**

> 通俗点说，就一个数据，或者一个请求，给你重复来多次，你得确保对应的数据是不会改变的，不能出错。

​		为了防止消息在消费者端丢失，会采用手动回复MQ的方式来解决，同时也引出了一个问题，消费者处理消息成功，手动回复MQ时由于网络不稳定，连接断开，导致MQ没有收到消费者回复的消息，那么该条消息还会保存在MQ的消息队列，由于MQ的消息重发机制，会重新把该条消息发给和该队列绑定的消息者处理，这样就会导致消息重复消费。而有些操作是不允许重复消费的，比如下单，减库存，扣款等操作。

#### 2.4.1 生产时消息重复

由于生产者发送消息给MQ，在MQ确认的时候出现了网络波动，生产者没有收到确认，实际上MQ已经接收到了消息。这时候生产者就会重新发送一遍这条消息。

生产者中如果消息未被确认，或确认失败，我们可以使用定时任务+（redis/db）来进行消息重试。

```java
@Component
@Slf4J
public class SendMessage {
    @Autowired
    private MessageService messageService;

    @Autowired
    private RabbitTemplate rabbitTemplate;

    // 最大投递次数
    private static final int MAX_TRY_COUNT = 3;

    /**
     * 每30s拉取投递失败的消息, 重新投递
     */
    @Scheduled(cron = "0/30 * * * * ?")
    public void resend() {
        log.info("开始执行定时任务(重新投递消息)");

        List<MsgLog> msgLogs = messageService.selectTimeoutMsg();
        msgLogs.forEach(msgLog -> {
            String msgId = msgLog.getMsgId();
            if (msgLog.getTryCount() >= MAX_TRY_COUNT) {
                messageService.updateStatus(msgId, Constant.MsgLogStatus.DELIVER_FAIL);
                log.info("超过最大重试次数, 消息投递失败, msgId: {}", msgId);
            } else {
                messageService.updateTryCount(msgId, msgLog.getNextTryTime());// 投递次数+1

                CorrelationData correlationData = new CorrelationData(msgId);
                rabbitTemplate.convertAndSend(msgLog.getExchange(), msgLog.getRoutingKey(), MessageHelper.objToMsg(msgLog.getMsg()), correlationData);// 重新投递

                log.info("第 " + (msgLog.getTryCount() + 1) + " 次重新投递消息");
            }
        });

        log.info("定时任务执行结束(重新投递消息)");
    }
}
```

**也可以生产消息时，具备唯一ID，消费时通过消费端进行处理（参看消费重复的解决方案）。**

#### 2.4.2 消费时消息重复

**两个思路**：

1. 不让消费端执行两次
2. 让它重复消费了，但是不让其对业务数据造成影响

##### 2.4.2.1 确保消费端只执行一次

一般来说消息重复消费都是在短暂的一瞬间消费多次，我们可以使用 redis 将消费过的消息唯一标识存储起来，然后在消费端业务执行之前判断 redis 中是否已经存在这个标识。

举个例子，订单使用优惠券后，要通知优惠券系统，增加使用流水。这里可以用订单号 + 优惠券 id 做唯一标识。业务开始先判断 redis 是否已经存在这个标识，如果已经存在代表处理过了。不存在就放进 redis 设置过期时间，执行业务。

```java
Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent("orderNo+couponId");
    //先检查这条消息是不是已经消费过了
    if (!Boolean.TRUE.equals(flag)) {
        return;
    }
    //执行业务...
    //消费过的标识存储到 Redis，10 秒过期
    stringRedisTemplate.opsForValue().set("orderNo+couponId","1", Duration.ofSeconds(10L));
```

##### 2.4.2.2 允许消费端执行多次，保证数据不受影响

- **数据库唯一键约束**

如果消费端业务是新增操作，我们可以利用数据库的唯一键约束，比如优惠券流水表的优惠券编号，如果重复消费将会插入两条相同的优惠券编号记录，数据库会给我们报错，可以保证数据库数据不会插入两条。

- **数据库乐观锁思想**

如果消费端业务是更新操作，可以给业务表加一个 version 字段，每次更新把 version 作为条件，更新之后 version + 1。由于 MySQL 的 innoDB 是行锁，当其中一个请求成功更新之后，另一个请求才能进来，由于版本号 version 已经变成 2，必定更新的 SQL 语句影响行数为 0，不会影响数据库数据。

### 2.5 如何保证消息的可靠性传输

如何处理丢失问题？

用MQ有个原则，就是数据不能多一条，也不能少一条。

不能多，就是重复消费和幂等性问题；

不能少，就是说数据不能丢失，如果说MQ是用来传递非常核心的消息，比如说计费，扣费的信息，那必须确保这个MQ传递过程中绝对不会把计费的消息弄丢。

#### 2.5.1 消息丢失的情况

1. 消息在生产者将数据发送给RabbitMQ的过程中丢失；
2. RabbitMQ收到消息，暂时存在内存中，还没消费，自己挂掉了，内存中的数据丢失；
3. 消费者消费到这个消息，但是还没来得及处理，就挂了，RabbitMQ以为消息已经被处理。

#### 2.5.2 生产者数据丢失

有可能因为网络波动，导致生产者将数据发送给RabbitMQ的过程中丢失，我们必须有一种机制来确保丢失之后，还能重发该条信息。

##### 2.5.2.1 使用RabbitMQ提供的事务功能

**实现思路：**

​	生产者发送数据之前开启 RabbitMQ 事务 `channel.txSelect` ，然后发送消息，如果消息没有成功被 RabbitMQ 接收到，那么生产者会 收到异常报错，此时就可以回滚事务 `channel.txRollback` ，然后重试发送消息；如果收到 了消息，那么可以提交事务 `channel.txCommit` 。

```java
// 开启事务
channel.txSelect;
try {
	//这里发送消息
}catch (Exception e) {
    // 事务回滚
    channel.txRollback
    // 这里再次重发这条消息
}
// 提交事务
channel.tzCommit;
```

**存在问题：**

​	 RabbitMQ事务机制开始之后，吞吐量会下来，因为太消耗性能。

##### 2.5.2.2 开启confirm模式

**实现思路：**

​	在生产者那里设置开启 confirm 模式之后，你每次写的消息都会分配一个唯一的 id，然后如果写入 了 RabbitMQ 中，RabbitMQ 会给你回传一个 ack 消息，告诉你说这个消息 ok 了。如果 RabbitMQ 没能处理这个消息，会回调你的一个 nack 接口，告诉你这个消息接收失败，你可 以重试。而且你可以结合这个机制自己在内存里维护每个消息 id 的状态，如果超过一定时间还 没接收到这个消息的回调，那么你可以重发。

**与事务机制对比：**

​	事务机制是同步的，提交一个事务之后会阻塞，但是confirm是异步的，发送一个消息以后可以发送下一个消息，然后那 个消息 RabbitMQ 接收了之后会异步回调你的一个接口通知你这个消息接收到了。

**一般在生产者这块避免数据丢失，都是用 confirm 机制的。**

##### 2.5.2.3 confirm实现

1. 添加配置参数

```yml
# Confirm 机制与Return 机制可以确定消息是否到达MQ服务器，如果没有到达可以做重试与异常通知
rabbitmq:
	publisher-confirm-type: CORRELATED
	publisher-returns: true
```



![image-20211204172902812](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211204172930.png)

2. 实现`setConfirmCallback()`方法

![image-20211204173345890](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211204173346.png)

#### 2.5.3 RabbitMQ弄丢了数据

 **解决思路：**

​	开始RabbitMQ的持久化，就是消息写入之后会持久化到磁盘，哪怕是RabbitMQ自己挂了，恢复之后会自动读取之前存储的数据，一般数据不会丢。

**持久化实现：**

1. 创建queue的时候将其设置为持久化

   这样可以保证RabbitMQ持久化queue的元数据，但是它是不会持久化queue里的数据的。

2. 发送消息的时候将消息的`deliveryMode`设置为2

   将消息设置为持久化，此时RabbitMQ就会将消息持久化到磁盘上去。

**必须要同时设置这两个持久化才行**，RabbitMQ哪怕是挂了，再次重启，也会从磁盘上重启恢复queue，恢复这个queue的数据。



![image-20211204174754623](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211204174754.png)

![image-20211204174823600](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211204174823.png)

**存在问题：**

​	RabbitMQ还没持久化就挂了，可能会导致少量数据丢失，但是这种情况的概率比较小。

**解决思路：**

​	持久化可以与生产者的`confirm`机制配合起来，只有消息被持久化到磁盘之后，才会通知生产者ack。

​	如果持久化之前，RabbitMQ挂了，数据丢了。这样生产者接收不到ack，生产者会进行消息重发。

#### 2.5.4 消费端弄丢了数据

​	RabbitMQ 如果丢失了数据，主要是因为你消费的时候，刚消费到，还没处理，结果进程挂 了，比如重启了，那么就尴尬了，RabbitMQ 认为你都消费了，这数据就丢了。

解决思路：

​	**使用RabbitMQ提供的ack机制**，需要关闭RabbitMQ的自动ack，每次业务处理完毕之后，手动ack，这是如果业务异常导致消息丢失触发会导致消息重发，并且可以自定义重发次数。

![image-20211204174024754](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211204174024.png)

![image-20211204174316525](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211204174316.png)

#### 2.5.5 总结

![image-20211113104009839](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211113104019.png)

### 2.6 如何保证消息的顺序性

#### 2.6.1 RabbitMQ消息顺序错乱场景

​	RabbitMQ：一个 queue，多个 consumer。比如，生产者向 RabbitMQ 里发送了三条数据， 顺序依次是 data1/data2/data3，压入的是 RabbitMQ 的一个内存队列。有三个消费者分别从 MQ 中消费这三条数据中的一条，结果消费者2先执行完操作，把 data2 存入数据库，然后 是 data1/data3。这不明显乱了。

![image-20211113104326944](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211113104327.png)

#### 2.6.2 解决方案

##### 2.6.2.1 简单队列

就一个 queue 对应一个 consumer，然后这个 consumer 内部用内存队列做排队，将消息ID进行hash计算，将相同值放入同一个内存队列，让指定线程执行，即可解决顺序消费问题。

![简单队列消费顺序问题解决](https://gitee.com/koala010/typora/raw/master/img/20210810105816.png)

##### 2.6.2.2 工作队列

生产者根据商品id计算hash值，对队列取余，将相同id的操作压入同一个队列。每个队列一个消费者就不会出现乱序情况。

![image-20211113110615853](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211113110615.png)

### 2.7 如何解决消息队列的延时以及过期失效

#### 2.7.1 大量消息在MQ中积压了几个小时

**场景：**

消费者出现故障，几千万条数据在 MQ 里积压了七八个小时，一个消费者一秒是 1000 条，一秒 3 个消费者是 3000 条，一分钟就是 18 万条。所以如果你积压 了几百万到上千万的数据，即使消费者恢复了，也需要大概 1 小时的时间才能恢复过来。

**解决方案：**

1. 先修复 consumer 的问题，确保其恢复消费速度，然后将现有 consumer 都停掉。 
2. 新建一个 topic，partition 是原来的 10 倍，临时建立好原先 10 倍的 queue 数量。 
3. 然后写一个临时的分发数据的 consumer 程序，这个程序部署上去消费积压的数据，消费之后不做耗时的处理，直接均匀轮询写入临时建立好的 10 倍数量的 queue。
4. 接着临时征用 10 倍的机器来部署 consumer，每一批 consumer 消费一个临时 queue 的数 据。这种做法相当于是临时将 queue 资源和 consumer 资源扩大 10 倍，以正常的 10 倍速度 来消费数据。 
5. 等快速消费完积压数据之后，得恢复原先部署的架构，重新用原先的 consumer 机器来消费消息。

#### 2.7.2 MQ的消息过期失效了

**场景：**	

​	RabbtiMQ 是可以设置过期时间的，也就是 TTL。如果消息在 queue 中积压超过一定的时间就会被 RabbitMQ 给清理掉，这个数据就没了。

**解决方案：**

​	批量重导，手动将丢失的数据，一点一点的查出来，重新加入 MQ  里面去，

#### 2.7.3 MQ快写满了

​	临时写程序，接入数据来消费，消 费一个丢弃一个，都不要了，快速消费掉所有的消息。然后等在线用户少的时候在手动将丢失的数据，一点一点的查出来，重新加入到 MQ 里

## 三、RabbitMQ实现延迟队列

### 3.1 实现原理

![在这里插入图片描述](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211213110713.png)

+



AMQP协议，以及RabbitMQ本身没有直接支持延迟队列的功能，但是可以通过**TTL**和**DLX**模拟出延迟队列的功能。

#### 3.1.1 TTL(Time To Live)

RabbitMQ可以针对Queue和Message设置 `x-message-ttl`，来控制消息的生存时间，如果超时，则消息变为`dead letter`

RabbitMQ针对队列中的消息过期时间有两种方法可以设置。

- A: 通过队列属性设置，队列中所有消息都有相同的过期时间。
- B: 对消息进行单独设置，每条消息TTL可以不同。

如果同时使用，则消息的过期时间以两者之间TTL较小的那个数值为准。消息在队列的生存时间一旦超过设置的TTL值，就成为`dead letter`

#### 3.1.2 DLX(Dead-Letter-Exchange)

RabbitMQ的Queue可以配置`x-dead-letter-exchange` 和`x-dead-letter-routing-key`（可选）两个参数，如果队列内出现了dead letter，则按照这两个参数重新路由。

- `x-dead-letter-exchange`：出现dead letter之后将dead letter重新发送到指定exchange
- `x-dead-letter-routing-key`：指定routing-key发送

队列出现`dead letter`的情况有：

- 消息或者队列的TTL过期
- 队列达到最大长度
- 消息被消费端拒绝（basic.reject or basic.nack）并且requeue=false

利用DLX，当消息在一个队列中变成死信后，它能被重新publish到另一个Exchange。这时候消息就可以重新被消费。

### 3.2 实现

#### 3.2.1 建立exchange和queue

首先建立2个`exchange`和2个`queue`

- `exchange_delay_begin`：这个是producer端发送时调用的exchange, 将消息发送至queue_dealy_begin中。
- `queue_delay_begin`: 通过routingKey=”delay”绑定exchang_delay_begin, 同时**配置DLX=exchange_delay_done, 当消息变成死信时，发往exchange_delay_done中**。
- `exchange_delay_done`: 死信的exchange, 如果不配置x-dead-letter-routing-key则采用原有默认的routingKey，即queue_delay_begin绑定exchange_delay_begin采用的“delay”。
- queue_delay_done：消息在TTL到期之后，最终通过exchang_delay_done发送值此queue，消费端通过消费此queue的消息，即可以达到延迟的效果。

```java
// 声明交换机
channel.exchangeDeclare("exchange_delay_begin", "direct", true);
channel.exchangeDeclare("exchange_delay_done", "direct", true);

// 初始化交换机，给死信交换机配置 x-dead-letter-exchange 参数
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-dead-letter-exchange", "exchange_delay_done");
channel.queueDeclare("queue_delay_begin", true, false, false, args);
channel.queueDeclare("queue_delay_done", true, false, false, null);

// 见队列绑定到对应交换机
channel.queueBind("queue_delay_begin", "exchange_delay_begin", "delay");
channel.queueBind("queue_delay_done", "exchange_delay_done", "delay");
```

#### 3.2.2 Consumer端

```java
// 从死信队列取出消息消费
QueueingConsumer consumer = new QueueingConsumer(channel);
channel.basicConsume("queue_delay_done", false, consumer);

while (true) {
    QueueingConsumer.Delivery delivery = consumer.nextDelivery();
    String msg = new String(delivery.getBody());
    System.out.println("receive msg time:" + new Date() + ", msg body:" + msg);
    channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
}
```

### 3.2.3 Producer端

```java
AMQP.BasicProperties.Builder builder = new AMQP.BasicProperties.Builder();
builder.expiration("60000");//设置消息TTL
builder.deliveryMode(2);//设置消息持久化
AMQP.BasicProperties properties = builder.build();

String message = String.valueOf(new Date());
channel.basicPublish("exchange_delay_begin","delay",properties,message.getBytes());
```

在创建完exchange和queue之后，首先执行consumer端的代码，之后执行producer端的代码，待producer发送完毕之后，查看consumer端的输出：

可以看到延迟1min消费了相关消息。

