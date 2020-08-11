# JUC并发编程

## 1. 什么是JUC

 JUC就是java.util.concurrent下面的类包，专门用于多线程的开发。

## 2. 线程和进程



>进程是操作系统中的应用程序、是资源分配的基本单位，线程是用来执行具体的任务和功能，是CPU调度和分派的最小单位
>
>一个进程往往可以包含多个线程，至少包含一个

### 1）进程

**一个程序，QQ.EXE Music.EXE；数据+代码+pcb**

一个进程可以包含多个线程，至少包含一个线程！

Java默认有几个线程？**2个线程！** main线程、GC线程

### 2）线程

**开了一个进程Typora，写字，等待几分钟会进行自动保存(线程负责的)**

对于Java而言：Thread、Runable、Callable进行开启线程的。

**提问？JAVA真的可以开启线程吗？ 开不了的！**

Java是没有权限去开启线程、操作硬件的，这是一个native的一个本地方法，它调用的底层的C++代码。

```java
    public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
	//这是一个C++底层，Java是没有权限操作底层硬件的
    private native void start0();

```

### 3）并发

多线程操作同一个资源。

- CPU 只有一核，模拟出来多条线程，天下武功，唯快不破。那么我们就可以使用CPU快速交替，来模拟多线程。
- 并发编程的本质：**充分利用CPU的资源！**

### 4）并行

**并行：** 多个人一起行走

- CPU多核，多个线程可以同时执行。 我们可以使用线程池！

**获取cpu的核数**

```java
public class Test1 {
    public static void main(String[] args) {
        //获取cpu的核数
        System.out.println(Runtime.getRuntime().availableProcessors());
    }
}
```

### 5）线程的状态

```java
public enum State {

    	//运行
        NEW,

    	//运行
        RUNNABLE,

    	//阻塞
        BLOCKED,

    	//等待
        WAITING,

    	//超时等待
        TIMED_WAITING,

    	//终止
        TERMINATED;
    }

```

### 6）wait/sleep

**1、来自不同的类**

wait => Object

sleep => Thread

一般情况企业中使用休眠是：

```java
TimeUnit.DAYS.sleep(1); //休眠1天
TimeUnit.SECONDS.sleep(1); //休眠1s
```

**2、关于锁的释放**

wait 会释放锁；

sleep睡觉了，不会释放锁；

**3、使用的范围是不同的**

wait 必须在同步代码块中；

sleep 可以在任何地方睡；

**4、是否需要捕获异常**

wait是不需要捕获异常；

sleep必须要捕获异常；

## 3.Lock

###  1）传统的 synchronized

```java
package com.marchsoft.juctest;

import lombok.Synchronized;

/**
 * Description：synchronized
 *
 * @author jiaoqianjin
 * Date: 2020/8/10 21:36
 **/

public class Demo01 {
    public static void main(String[] args) {
        final Ticket ticket = new Ticket();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"A").start();
        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"B").start();
        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"C").start();
    }
}
// 资源类 OOP 属性、方法
class Ticket {
    private int number = 30;

    //卖票的方式
    public synchronized void sale() {
        if (number > 0) {
            System.out.println(Thread.currentThread().getName() + "卖出了第" + (number--) + "张票剩余" + number + "张票");
        }
    }
}
```

### 2）Lock

![image-20200810221525974](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200810221525974.png)

![image-20200810221731649](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200810221731649.png)

**公平锁：** 十分公平，必须先来后到~；

**非公平锁：** 十分不公平，可以插队；**(默认为非公平锁)**

```java
package com.marchsoft.juctest;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/10 22:05
 **/

public class LockDemo {
    public static void main(String[] args) {
        final Ticket2 ticket = new Ticket2();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        }, "B").start();
        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        }, "C").start();
    }
}
//lock三部曲
//1、    Lock lock=new ReentrantLock();
//2、    lock.lock() 加锁
//3、    finally=> 解锁：lock.unlock();
class Ticket2 {
    private int number = 30;
	
    // 创建锁
    Lock lock = new ReentrantLock();
    //卖票的方式
    public synchronized void sale() {
        lock.lock(); // 开启锁
        try {
            if (number > 0) {
                System.out.println(Thread.currentThread().getName() + "卖出了第" + (number--) + "张票剩余" + number + "张票");
            }
        }finally {
            lock.unlock(); // 关闭锁
        }

    }
}
```

### 3. Synchronized 与Lock 的区别

1、Synchronized 内置的Java关键字，Lock是一个Java类

2、Synchronized 无法判断获取锁的状态，Lock可以判断

3、Synchronized 会自动释放锁，lock必须要手动加锁和手动释放锁！**可能会遇到死锁**

4、Synchronized 线程1(获得锁->阻塞)、线程2(等待)；lock就不一定会一直等待下去，**lock会有一个trylock去尝试获取锁**，不会造成长久的等待。

5、Synchronized 是可重入锁，不可以中断的，非公平的；Lock，可重入的，可以判断锁，可以自己设置公平锁和非公平锁；

6、Synchronized 适合锁少量的代码同步问题，Lock适合锁大量的同步代码；

## 4. 生产者和消费者的关系

### 1）Synchronzied 版本

```java
package com.marchsoft.juctest;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/10 22:33
 **/

public class ConsumeAndProduct {
    public static void main(String[] args) {
        Data data = new Data();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "B").start();
    }
}

class Data {
    private int num = 0;

    // +1
    public synchronized void increment() throws InterruptedException {
        // 判断等待
        if (num != 0) {
            this.wait();
        }
        num++;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        // 通知其他线程 +1 执行完毕
        this.notifyAll();
    }

    // -1
    public synchronized void decrement() throws InterruptedException {
        // 判断等待
        if (num == 0) {
            this.wait();
        }
        num--;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        // 通知其他线程 -1 执行完毕
        this.notifyAll();
    }
}
```

### 2）存在问题（虚假唤醒）

**问题，如果有四个线程**，会出现虚假唤醒

![image-20200810224629273](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200810224629273.png)

![image-20200810224826214](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200810224826214.png)

解决方式 ，**if 改为while即可，防止虚假唤醒**

>结论：就是用if判断的话，唤醒后线程会从wait之后的代码开始运行，但是不会重新判断if条件，直接继续运行if代码块之后的代码，而如果使用while的话，也会从wait之后的代码运行，但是唤醒后会重新判断循环条件，如果不成立再执行while代码块之后的代码块，成立的话继续wait。
>
>这也就是为什么用while而不用if的原因了，因为线程被唤醒后，执行开始的地方是wait之后

```java
package com.marchsoft.juctest;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/10 22:33
 **/

public class ConsumeAndProduct {
    public static void main(String[] args) {
        Data data = new Data();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "B").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "C").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "D").start();
    }
}

class Data {
    private int num = 0;

    // +1
    public synchronized void increment() throws InterruptedException {
        // 判断等待
        while (num != 0) {
            this.wait();
        }
        num++;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        // 通知其他线程 +1 执行完毕
        this.notifyAll();
    }

    // -1
    public synchronized void decrement() throws InterruptedException {
        // 判断等待
        while (num == 0) {
            this.wait();
        }
        num--;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        // 通知其他线程 -1 执行完毕
        this.notifyAll();
    }
}
```

### 3）Lock版

![image-20200811094721678](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200811094721678.png)

```java
package com.marchsoft.juctest;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * Description：
 *
 * @author jiaoqianjin
 * Date: 2020/8/11 9:48
 **/

public class LockCAP {
    public static void main(String[] args) {
        Data2 data = new Data2();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {

                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "B").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "C").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "D").start();
    }
}

class Data2 {
    private int num = 0;
    Lock lock = new ReentrantLock();
    Condition condition = lock.newCondition();
    // +1
    public  void increment() throws InterruptedException {
        lock.lock();
        try {
            // 判断等待
            while (num != 0) {
                condition.await();
            }
            num++;
            System.out.println(Thread.currentThread().getName() + "=>" + num);
            // 通知其他线程 +1 执行完毕
            condition.signalAll();
        }finally {
            lock.unlock();
        }

    }

    // -1
    public  void decrement() throws InterruptedException {
        lock.lock();
        try {
            // 判断等待
            while (num == 0) {
                condition.await();
            }
            num--;
            System.out.println(Thread.currentThread().getName() + "=>" + num);
            // 通知其他线程 +1 执行完毕
            condition.signalAll();
        }finally {
            lock.unlock();
        }

    }
}
```

### 4）Condition的优势

精准的通知和唤醒的线程！

**如果我们要指定通知的下一个进行顺序怎么办呢？ 我们可以使用Condition来指定通知进程~**

```java
package com.marchsoft.juctest;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * Description：
 * A 执行完 调用B
 * B 执行完 调用C
 * C 执行完 调用A
 *
 * @author jiaoqianjin
 * Date: 2020/8/11 9:58
 **/

public class ConditionDemo {
    public static void main(String[] args) {
        Data3 data3 = new Data3();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data3.printA();
            }
        },"A").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data3.printB();
            }
        },"B").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data3.printC();
            }
        },"C").start();
    }

}
class Data3 {
    private Lock lock = new ReentrantLock();
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();
    private int num = 1; // 1A 2B 3C

    public void printA() {
        lock.lock();
        try {
            // 业务代码 判断 -> 执行 -> 通知
            while (num != 1) {
                condition1.await();
            }
            System.out.println(Thread.currentThread().getName() + "==> AAAA" );
            num = 2;
            condition2.signal();
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    public void printB() {
        lock.lock();
        try {
            // 业务代码 判断 -> 执行 -> 通知
            while (num != 2) {
                condition2.await();
            }
            System.out.println(Thread.currentThread().getName() + "==> BBBB" );
            num = 3;
            condition3.signal();
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    public void printC() {
        lock.lock();
        try {
            // 业务代码 判断 -> 执行 -> 通知
            while (num != 3) {
                condition3.await();
            }
            System.out.println(Thread.currentThread().getName() + "==> CCCC" );
            num = 1;
            condition1.signal();
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
/*
A==> AAAA
B==> BBBB
C==> CCCC
A==> AAAA
B==> BBBB
C==> CCCC
...
*/
```

## 5. 8锁现象

如何判断锁的是谁！锁到底锁的是谁？

锁会锁住：对象、Class

深刻理解我们的锁

**问题1**

两个同步方法，先执行发短信还是打电话

```java
public class dome01 {
    public static void main(String[] args) {
        Phone phone = new Phone();

        new Thread(() -> { phone.sendMs(); }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone.call(); }).start();
    }
}

class Phone {
    public synchronized void sendMs() {
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
}
```

输出结果为 

发短信

打电话

**为什么？ 如果你认为是顺序在前？ 这个答案是错误的！**

**问题2：**

**我们再来看：我们让发短信 延迟4s**

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone = new Phone();

        new Thread(() -> {
            try {
                phone.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone.call(); }).start();
    }
}

class Phone {
    public synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
}
```

现在结果是什么呢？

结果：**还是先发短信，然后再打电话！**

**why？**

>原因：并不是顺序执行，而是synchronized 锁住的对象是方法的调用！对于两个方法用的是同一个锁，谁先拿到谁先执行，另外一个等待

**问题三**

加一个普通方法

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone = new Phone();

        new Thread(() -> {
            try {
                phone.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone.hello(); }).start();
    }
}

class Phone {
    public synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
    public void hello() {
        System.out.println("hello");
    }
}
```

输出结果为

hello

发短信

> 原因：hello是一个普通方法，不受synchronized锁的影响，不用等待锁的释放

**问题四**

**如果我们使用的是两个对象，一个调用发短信，一个调用打电话，那么整个顺序是怎么样的呢？**

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(() -> {
            try {
                phone1.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone2.call(); }).start();
    }
}

class Phone {
    public synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
    public void hello() {
        System.out.println("hello");
    }
}
```

输出结果

打电话

发短信

> 原因：两个对象两把锁，不会出现等待的情况，发短信睡了4s,所以先执行打电话

**问题五、六**

**如果我们把synchronized的方法加上static变成静态方法！那么顺序又是怎么样的呢？**

（1）我们先来使用一个对象调用两个方法！

答案是：**先发短信,后打电话**

（2）如果我们使用两个对象调用两个方法！

答案是：**还是先发短信，后打电话**

原因是什么呢？ **为什么加了static就始终前面一个对象先执行呢！为什么后面会等待呢？**

原因是：**对于static静态方法来说，对于整个类Class来说只有一份，对于不同的对象使用的是同一份方法，相当于这个方法是属于这个类的，如果静态static方法使用synchronized锁定，那么这个synchronized锁会锁住整个对象！不管多少个对象，对于静态的锁都只有一把锁，谁先拿到这个锁就先执行，其他的进程都需要等待！**

---

**问题七**

**如果我们使用一个静态同步方法、一个同步方法、一个对象调用顺序是什么？**

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone1 = new Phone();
//        Phone phone2 = new Phone();

        new Thread(() -> {
            try {
                phone1.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone1.call(); }).start();
    }
}

class Phone {
    public static synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
    public void hello() {
        System.out.println("hello");
    }
}
```

输出结果

打电话

发短信

> 原因：因为一个锁的是Class类的模板，一个锁的是对象的调用者。所以不存在等待，直接运行。

**问题八**

**如果我们使用一个静态同步方法、一个同步方法、两个对象调用顺序是什么？**

```java
public class dome01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(() -> {
            try {
                phone1.sendMs();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> { phone2.call(); }).start();
    }
}

class Phone {
    public static synchronized void sendMs() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("发短信");
    }
    public synchronized void call() {
        System.out.println("打电话");
    }
    public void hello() {
        System.out.println("hello");
    }
}
```

输出结果

打电话

发短信

> 原因：两把锁锁的不是同一个东西

**小解**

**new** 出来的 this 是具体的一个对象

**static Class** 是唯一的一个模板

## 6. 集合不安全

### 1）List 不安全

```java
//java.util.ConcurrentModificationException 并发修改异常！
public class ListTest {
    public static void main(String[] args) {

        List<Object> arrayList = new ArrayList<>();

        for(int i=1;i<=10;i++){
            new Thread(()->{
                arrayList.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(arrayList);
            },String.valueOf(i)).start();
        }

    }
}
```

会导致 java.util.ConcurrentModificationException 并发修改异常！

**ArrayList 在并发情况下是不安全的**

解决方案：

```java
public class ListTest {
    public static void main(String[] args) {
        /**
         * 解决方案
         * 1. List<String> list = new Vector<>();
         * 2. List<String> list = Collections.synchronizedList(new ArrayList<>());
         * 3. List<String> list = new CopyOnWriteArrayList<>();
         */
        List<String> list = new CopyOnWriteArrayList<>();
        

        for (int i = 1; i <=10; i++) {
            new Thread(() -> {
                list.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(list);
            },String.valueOf(i)).start();
        }
    }
}
```

**CopyOnWriteArrayList**：写入时复制！ **COW 计算机程序设计领域的一种优化策略** 

核心思想是，如果有多个调用者（Callers）同时要求相同的资源（如内存或者是磁盘上的数据存储），他们会共同获取相同的指针指向相同的资源，直到某个调用者视图修改资源内容时，系统才会真正复制一份专用副本（private copy）给该调用者，而其他调用者所见到的最初的资源仍然保持不变。这过程对其他的调用者都是透明的（transparently）。此做法主要的优点是如果调用者没有修改资源，就不会有副本（private copy）被创建，因此多个调用者只是读取操作时可以共享同一份资源。

读的时候不需要加锁，如果读的时候有多个线程正在向CopyOnWriteArrayList添加数据，读还是会读到旧的数据，因为写的时候不会锁住旧的CopyOnWriteArrayList。

多个线程调用的时候，list，读取的时候，固定的，写入（存在覆盖操作）；在写入的时候避免覆盖，造成数据错乱的问题；

> **CopyOnWriteArrayList**比**Vector**厉害在哪里？

**Vector**底层是使用**synchronized**关键字来实现的：效率特别低下。

![image-20200811144549151](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200811144549151.png)

**CopyOnWriteArrayList**使用的是Lock锁，效率会更加高效！

![image-20200811144447781](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200811144447781.png)

### 2）set 不安全

**Set和List同理可得:** 多线程情况下，普通的Set集合是线程不安全的；

解决方案还是两种：

- 使用Collections工具类的**synchronized**包装的Set类
- 使用CopyOnWriteArraySet 写入复制的**JUC**解决方案

```java
public class SetTest {
    public static void main(String[] args) {
        /**
         * 1. Set<String> set = Collections.synchronizedSet(new HashSet<>());
         * 2. Set<String> set = new CopyOnWriteArraySet<>();
         */
//        Set<String> set = new HashSet<>();
        Set<String> set = new CopyOnWriteArraySet<>();

        for (int i = 1; i <= 30; i++) {
            new Thread(() -> {
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(set);
            },String.valueOf(i)).start();
        }
    }
}
```

**HashSet底层是什么？**

hashSet底层就是一个**HashMap**；

![image-20200811150415187](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200811150415187.png)

### 3）Map不安全

```java
//map 是这样用的吗？  不是，工作中不使用这个
//默认等价什么？ new HashMap<>(16,0.75);
Map<String, String> map = new HashMap<>();
//加载因子、初始化容量
```

默认**加载因子是0.75**,默认的**初始容量是16**

![image-20200811150700927](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200811150700927.png)

同样的HashMap基础类也存在**并发修改异常**！

```java
public class MapTest {
    public static void main(String[] args) {
        //map 是这样用的吗？  不是，工作中不使用这个
        //默认等价什么？ new HashMap<>(16,0.75);
        /**
         * 解决方案
         * 1. Map<String, String> map = Collections.synchronizedMap(new HashMap<>());
         *  Map<String, String> map = new ConcurrentHashMap<>();
         */
        Map<String, String> map = new ConcurrentHashMap<>();
        //加载因子、初始化容量
        for (int i = 1; i < 100; i++) {
            new Thread(()->{
                map.put(Thread.currentThread().getName(), UUID.randomUUID().toString().substring(0,5));
                System.out.println(map);
            },String.valueOf(i)).start();
        }
    }
}
```

**TODO:研究ConcurrentHashMap底层原理：**

## 7. Callable

**1、可以有返回值；
2、可以抛出异常；
3、方法不同，run()/call()**

```java
public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        for (int i = 1; i < 10; i++) {
            MyThread1 myThread1 = new MyThread1();

            FutureTask<Integer> futureTask = new FutureTask<>(myThread1);
            // 放入Thread中使用，结果会被缓存
            new Thread(futureTask,String.valueOf(i)).start();
            // 这个get方法可能会被阻塞，如果在call方法中是一个耗时的方法，所以一般情况我们会把这个放在最后，或者使用异步通信
            int a = futureTask.get();
            System.out.println("返回值:" + s);
        }

    }

}
class MyThread1 implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        System.out.println("call()");
        return 1024;
    }
}
```

## 8. 常用的辅助类

### 1）CountDownLatch

```java
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        // 总数是6
        CountDownLatch countDownLatch = new CountDownLatch(6);

        for (int i = 1; i <= 6; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "==> Go Out");
                countDownLatch.countDown(); // 每个线程都数量 -1
            },String.valueOf(i)).start();
        }
        countDownLatch.await(); // 等待计数器归零 然后向下执行
        System.out.println("close door");
    }
}
```

主要方法：

- countDown 减一操作；
- await 等待计数器归零

await 等待计数器归零，就唤醒，再继续向下运行

### 2）CyclickBarrier

![image-20200811202603352](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200811202603352.png)

其实

```java
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        // 主线程
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,() -> {
            System.out.println("召唤神龙");
        });

        for (int i = 1; i <= 7; i++) {
            // 子线程
            int finalI = i;
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "收集了第" + finalI + "颗龙珠");
                try {
                    cyclicBarrier.await(); // 加法计数 等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```



### 3）Semaphore

```java
public class SemaphoreDemo {
    public static void main(String[] args) {

        // 线程数量，停车位，限流
        Semaphore semaphore = new Semaphore(3);
        for (int i = 0; i <= 6; i++) {
            new Thread(() -> {
                // acquire() 得到
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "抢到车位");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName() + "离开车位");
                }catch (Exception e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release(); // release() 释放
                }
            }).start();
        }
    }
}
```

```java
Thread-1抢到车位
Thread-0抢到车位
Thread-2抢到车位
Thread-0离开车位
Thread-2离开车位
Thread-1离开车位
Thread-5抢到车位
Thread-3抢到车位
Thread-4抢到车位
Thread-5离开车位
Thread-3离开车位
Thread-6抢到车位
Thread-4离开车位
Thread-6离开车位

Process finished with exit code 0
```

原理：

**semaphore.acquire()获得资源，如果资源已经使用完了，就等待资源释放后再进行使用！**

**semaphore.release()释放，会将当前的信号量释放+1，然后唤醒等待的线程！**

作用： 多个共享资源互斥的使用！ 并发限流，控制最大的线程数！

## 9. 读写锁

```java
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        int num = 6;
        for (int i = 1; i <= num; i++) {
            int finalI = i;
            new Thread(() -> {

                myCache.write(String.valueOf(finalI), String.valueOf(finalI));

            },String.valueOf(i)).start();
        }

        for (int i = 1; i <= num; i++) {
            int finalI = i;
            new Thread(() -> {

                myCache.read(String.valueOf(finalI));

            },String.valueOf(i)).start();
        }
    }
}

/**
 *  方法未加锁，导致写的时候被插队
 */
class MyCache {
    private volatile Map<String, String> map = new HashMap<>();

    public void write(String key, String value) {
        System.out.println(Thread.currentThread().getName() + "线程开始写入");
        map.put(key, value);
        System.out.println(Thread.currentThread().getName() + "线程写入ok");
    }

    public void read(String key) {
        System.out.println(Thread.currentThread().getName() + "线程开始读取");
        map.get(key);
        System.out.println(Thread.currentThread().getName() + "线程写读取ok");
    }
}

```

```java
2线程开始写入
2线程写入ok
3线程开始写入
3线程写入ok
1线程开始写入    # 插入了其他线程的写入，导致数据不一致
4线程开始写入
4线程写入ok
1线程写入ok
6线程开始写入
6线程写入ok
5线程开始写入
5线程写入ok
1线程开始读取
1线程写读取ok
2线程开始读取
2线程写读取ok
3线程开始读取
3线程写读取ok
4线程开始读取
4线程写读取ok
5线程开始读取
6线程开始读取
6线程写读取ok
5线程写读取ok

Process finished with exit code 0
```

所以如果我们不加锁的情况，多线程的读写会造成数据不可靠的问题。

我们也可以采用**synchronized**这种重量锁和轻量锁 **lock**去保证数据的可靠。

但是这次我们采用更细粒度的锁：**ReadWriteLock** 读写锁来保证

![image-20200811213503631](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/image-20200811213503631.png)

```java
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache2 myCache = new MyCache2();
        int num = 6;
        for (int i = 1; i <= num; i++) {
            int finalI = i;
            new Thread(() -> {

                myCache.write(String.valueOf(finalI), String.valueOf(finalI));

            },String.valueOf(i)).start();
        }

        for (int i = 1; i <= num; i++) {
            int finalI = i;
            new Thread(() -> {

                myCache.read(String.valueOf(finalI));

            },String.valueOf(i)).start();
        }
    }

}
class MyCache2 {
    private volatile Map<String, String> map = new HashMap<>();
    private ReadWriteLock lock = new ReentrantReadWriteLock();

    public void write(String key, String value) {
        lock.writeLock().lock(); // 写锁
        try {
            System.out.println(Thread.currentThread().getName() + "线程开始写入");
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "线程写入ok");

        }finally {
            lock.writeLock().unlock(); // 释放写锁
        }
    }

    public void read(String key) {
        lock.readLock().lock(); // 读锁
        try {
            System.out.println(Thread.currentThread().getName() + "线程开始读取");
            map.get(key);
            System.out.println(Thread.currentThread().getName() + "线程写读取ok");
        }finally {
            lock.readLock().unlock(); // 释放读锁
        }
    }
}
```

```java
1线程开始写入
1线程写入ok
6线程开始写入
6线程写入ok
3线程开始写入
3线程写入ok
2线程开始写入
2线程写入ok
5线程开始写入
5线程写入ok
4线程开始写入
4线程写入ok
    
1线程开始读取
5线程开始读取
2线程开始读取
1线程写读取ok
3线程开始读取
2线程写读取ok
6线程开始读取
6线程写读取ok
5线程写读取ok
4线程开始读取
4线程写读取ok
3线程写读取ok

Process finished with exit code 0
```

