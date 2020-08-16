# JVM

## 0. 这些你会了吗？

- 请谈谈你对jvm的理解？，java8虚拟机和之前的变化更新？
- 什么是OOM，什么是栈溢出StackOverFlowError? 怎么分析？
- jvm的常用调优参数有哪些？
- 内存快照如何抓取，怎么分析Dump文件？知道吗？
- 谈谈jvm中，类加载器你的认识？

## 1. JVM的位置

![image-20200814094438487](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200814094438487.png)

## 2. JVM的体系结构

![image-20200814154358995](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200814154358995.png)

## 3. 类加载器

作用：加载 Class 文件

![image-20200814154813912](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200814154813912.png)

   1.虚拟机自带的加载器  CustomClassLoader

 2.启动类(根)加载器  BootstrapClassLoader

 3.扩展类加载器  ExtClassLoader

 4.应用程序加载器  AppClassLoader

 5.百度:双亲委派机制

```java
 //双亲委派机制:安全
 // 1. APP–>EXC—B0OT(最终执行)
 // B0OT
 // EXC
 // APP
```



 1.类加载器收到类加载的请求
 2.将这个请求向上委托给父类加载器去完成，一 直向上委托，知道启动类加载器
 3.启动加载器检查是否能够加载当前这个类，能加载就结束， 使用当前的加载器，否则， 抛出异常，通知子加载器进行加载
 4.重复步骤3
 Class Not Found异常就是这么来的
 Java早期的名字：C+±-
 Java = C++:去掉繁琐的东西，指针，内存管理~

## 4. 沙箱安全机制



## 5. Native

 	

## 6. PC寄存器



## 7. 方法区

方法区是被所有线程共享,所有字段和方法字节码，以及一些特殊方法，如构造函数,接口代码也在此定义,简单说，所有定义的方法的信息都保存在该区域,此区域属于共享区间;
 **静态变量（static）、常量（final）、类信息(构造方法、接口定义)、运行时的常量池存在方法区中，但是实例变量存在堆内存中，和方法区无关**

## 8. 栈

程序 = 数据结构 + 算法

程序 = 框架 + 业务逻辑

栈：先进后出、后进先出  弹夹

队列：先进先出（FIFO:First Input First Output）

**喝多了吐就是栈，吃多了拉是队列**

栈：栈内存，主管程序的运行，生命周期和线程同步；

线程结束，栈内存也就是释放，对于栈来说，**不存在垃圾回收问题**

一旦线程结束，栈就over

栈：八大基本类型 + 对象引用 + 实例的方法



栈运行原理：栈帧

栈满了 ：报 StackOverfloeError 错误

![image-20200815160352891](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200815160352891.png)

![image-20200815160435555](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200815160435555.png)

图 栈帧图解 栈底部子帧指向上一个栈的方法 上一个栈的父帧指向栈底部方法

## 9. 三种JVM

●Sun公司HotSpot Java Hotspot™ 64-Bit Server VM (build 25.181-b13,mixed mode)
●BEA JRockit
●IBM J9VM
我们学习都是: Hotspot

## 11. 堆

Heap, 一个JVM只有一个堆内存，堆内存的大小是可以调节的。
类加载器读取了类文件后，一般会把什么东西放到堆中?
**类, 方法，常量,变量~**，保存我们所有引用类型的真实对象;
堆内存中还要细分为三个区域:
●新生区(伊甸园区) Young/New
●养老区old
●永久区Perm

![image-20200815162723527](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200815162723527.png)

GC垃圾回收,主要是在伊甸园区和养老区~
假设内存满了,OOM,堆内存不够! java.lang.OutOfMemoryError:Java heap space
永久存储区里存放的都是Java自带的 例如lang包中的类 如果不存在这些，Java就跑不起来了
在JDK8以后，**永久存储区改了个名字(元空间)**

![image-20200815163017113](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200815163017113.png)

## 12. 新生区

- 类：
- 伊甸园：
- 幸存区：