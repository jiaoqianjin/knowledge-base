

# Java基础

[Java 全栈知识体系](https://www.pdai.tech/)

著作权归https://pdai.tech所有。 链接：https://www.pdai.tech/md/java/thread/java-thread-x-thread-basic.html

[并发编程面试题（2020最新版）](https://thinkwon.blog.csdn.net/article/details/104863992)

版权声明：本文为CSDN博主「ThinkWon」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ThinkWon/article/details/104863992

[JavaGuide](https://javaguide.cn/)

## 一、面向对象

### 1.1 面向对象和面向过程的区别

#### 1.1.1 面向过程

**面向过程是具体化的，流程化的，解决一个问题，你需要一步一步的分析，一步一步的实现。**

**优点：**性能比面向对象高，因为类调用时需要实例化，开销比较大，比较消耗资源;比如单片机、嵌入式开发、Linux/Unix等一般采用面向过程开发，性能是最重要的因素。

**缺点：**没有面向对象易维护、易复用、易扩展

#### 1.1.2 面向对象

**面向对象是模型化的，你只需抽象出一个类，这是一个封闭的盒子，在这里你拥有数据也拥有解决问题的方法。需要什么功能直接使用就可以了，不必去一步一步的实现，至于这个功能是如何实现的，管我们什么事？我们会用就可以了**

**优点：**易维护、易复用、易扩展，由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统更加灵活、更加易于维护

**缺点：**性能比面向过程低

### 1.2 面向对象的三大特性	

#### 1.2.1 封装

​	隐藏对象的属性和实现细节，仅对外提供公共访问方式，将变化隔离，便于使用，提高复用性和安全性。

#### 1.2.2 继承

​	继承是使用已存在的类的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。通过使用继承可以提高代码复用性。继承是多态的前提。

**特点：**

1. 子类拥有父类非 private 的属性和方法。
2. 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
3. 子类可以用自己的方式实现父类的方法。

#### 1.2.3 多态

多态就是指程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编程时并不确定，而是在程序运行期间才确定，即一个引用变量到底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，必须在由程序运行期间才能决定。

多态分为**编译时多态**和**运行时多态**:

- 编译时多态主要指方法的**重载**
  - 发生在同一个类中（或者父类和子类之间），方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同。
- 运行时多态指程序中定义的对象引用所指向的具体类型在运行期间才确定

**运行时多态**有三个条件:

- 继承：在多态中必须存在有继承关系的子类和父类。
- **覆盖(重写)**：类对父类中某些方法进行重新定义，在调用这些方法时就会调用子类的方法。
  1. 返回值类型、方法名、参数列表必须相同，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类。
  2. 如果父类方法访问修饰符为 `private/final/static` 则子类就不能重写该方法，但是被 static 修饰的方法能够被再次声明。
  3. 构造方法无法被重写
- 向上转型：多态中需要将子类的引用赋给父类对象，只有这样该引用才能够具备技能调用父类的方法和子类的方法。

​	

## 二、基础语法

### 2.1 访问修饰符

#### 2.1.1 定义

Java中，可以使用访问修饰符来保护对类、变量、方法和构造方法的访问。Java 支持 4 种不同的访问权限。

#### 2.1.2 分类

- **private** : 在同一类内可见。使用对象：变量、方法。 注意：不能修饰类（外部类）
- **default (即缺省，什么也不写，不使用任何**关键字）: 在同一包内可见，不使用任何修饰符。使用对象：类、接口、变量、方法。
- **protected** : 对同一包内的类和所有子类可见。使用对象：变量、方法。 注意：不能修饰类（外部类）。
- **public** : 对所有类可见。使用对象：类、接口、变量、方法

#### 2.1.3 访问修饰符图

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211118205823.png)

### 2.2 关键字

#### 2.2.1 static

**存在意义**

1. static的主要意义是在于创建独立于具体对象的域变量或者方法。以致于即使没有创建对象，也能使用属性和调用方法！

2. static关键字还有一个比较关键的作用就是 用来形成静态代码块以优化程序性能。static块可以置于类中的任何地方，类中可以有多个static块。在类初次被加载的时候，会按照static块的顺序来执行每个static块，并且只会执行一次。

**特点**

1. 被static修饰的变量或者方法是独立于该类的任何对象，也就是说，这些变量和方法**不属于任何一个实例对象，而是被类的实例对象所共享**。
2. static变量值在类加载的时候分配空间，以后创建类对象的时候不会重新分配。赋值的话，是可以任意赋值的！静态变量在内存中只存在一份。
3. 被static修饰的变量或者方法是优先于对象存在的，也就是说当一个类加载完毕之后，即便没有创建对象，也可以去访问。可以直接通过类名来访问它。

**应用场景**

因为static是被类的实例对象所共享，因此如果**某个成员变量是被所有对象所共享的，那么这个成员变量就应该定义为静态变量**。

1. 修饰成员变量 
2. 修饰成员方法 
3. 静态代码块 
4. 修饰类【只能修饰内部类也就是静态内部类】
5. 静态导包

**注意事项**

1. 静态只能访问静态。 
2. 非静态既可以访问非静态的，也可以访问静态的。

#### 2.2.2 final

**1. 数据**

声明数据为常量，可以是编译时常量，也可以是在运行时被初始化后不能被改变的常量。

- 对于基本类型，final 使数值不变；
- 对于引用类型，final 使引用不变，也就不能引用其它对象，但是被引用的对象本身是可以修改的。

**2. 方法**

**声明方法不能被子类重写。**

private 方法隐式地被指定为 final，如果在子类中定义的方法和基类中的一个 private 方法签名相同，此时子类的方法不是重写基类方法，而是在子类中定义了一个新的方法。

**3. 类**

**声明类不允许被继承。**

## 三、泛型

### 3.1 为什么使用泛型

**适用于多种数据类型执行相同的代码**（代码复用）

### 3.2 泛型的类型擦除

#### 3.2.1 伪泛型

Java泛型这个特性是从JDK 1.5才开始加入的，因此为了兼容之前的版本，Java泛型的实现采取了“**伪泛型**”的策略，即Java在语法上支持泛型，但是在编译阶段会进行所谓的“**类型擦除**”（Type Erasure），将所有的泛型表示（尖括号中的内容）都替换为具体的类型（其对应的原生态类型），就像完全没有泛型一样。

#### 3.2.2 类型擦除原则

- 消除类型参数声明，即删除`<>`及其包围的部分。
- 根据类型参数的上下界推断并替换所有的类型参数为原生态类型：如果类型参数是无限制通配符或没有上下界限定则替换为Object，如果存在上下界限定则根据子类替换原则取类型参数的最左边限定类型（即父类）。
- 为了保证类型安全，必要时插入强制类型转换代码。
- 自动产生“桥接方法”以保证擦除类型后的代码仍然具有泛型的“多态性”。

#### 3.2.3 如何擦除

- 擦除类定义中的类型参数 - 无限制类型擦除

  当类定义中的类型参数没有任何限制时，在类型擦除中直接被替换为Object，即形如`<T>`和`<?>`的类型参数都被替换为Object。

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211120111158.png)



- 擦除类定义中的类型参数 - 有限制类型擦除

  当类定义中的类型参数存在限制（上下界）时，在类型擦除中替换为类型参数的上界或者下界，比如形如`<T extends Number>`和`<? extends Number>`的类型参数被替换为`Number`，`<? super Number>`被替换为Object。

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211120111249.png)



- 擦除方法定义中的类型参数

  ![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211120111319.png)

### 3.3 编译期检查

> 既然说类型变量会在编译的时候擦除掉，那为什么我们往 ArrayList 创建的对象中添加整数会报错呢？不是说泛型变量String会在编译的时候变为Object类型吗？为什么不能存别的类型呢？既然类型擦除了，如何保证我们只能使用泛型变量限定的类型呢？

Java编译器是通过先检查代码中泛型的类型，然后在进行类型擦除，再进行编译。

## 四、数据类型

### 1. 基本数据类型

- ### Java 中的几种基本数据类型是什么？对应的包装类型是什么？各自占用多少字节呢？

  Java 中有 8 种基本数据类型，分别为：

  1. 6 种数字类型 ：`byte`、`short`、`int`、`long`、`float`、`double`
  2. 1 种字符类型：`char`
  3. 1 种布尔型：`boolean`。

  这 8 种基本数据类型的默认值以及所占空间的大小如下：

  | 基本类型  | 位数 | 字节 | 默认值  |
  | :-------- | :--- | :--- | :------ |
  | `int`     | 32   | 4    | 0       |
  | `short`   | 16   | 2    | 0       |
  | `long`    | 64   | 8    | 0L      |
  | `byte`    | 8    | 1    | 0       |
  | `char`    | 16   | 2    | 'u0000' |
  | `float`   | 32   | 4    | 0f      |
  | `double`  | 64   | 8    | 0d      |
  | `boolean` | 1    |      | false   |

  另外，对于 `boolean`，官方文档未明确定义，它依赖于 JVM 厂商的具体实现。逻辑上理解是占用 1 位，但是实际中会考虑计算机高效存储因素。

### 2. 自动装箱与拆箱

- **装箱**：将基本类型用它们对应的引用类型包装起来；
- **拆箱**：将包装类型转换为基本数据类型；

举例：

```java
Integer i = 10;  //装箱
int n = i;   //拆箱
```

### 3. 缓存池

Java 基本类型的包装类的大部分都实现了常量池技术。`Byte`,`Short`,`Integer`,`Long` 这 4 种包装类默认创建了数值 **[-128，127]** 的相应类型的缓存数据，`Character` 创建了数值在[0,127]范围的缓存数据，`Boolean` 直接返回 `True` Or `False`。

new Integer(123) 与 Integer.valueOf(123) 的区别在于:

- new Integer(123) 每次都会新建一个对象
- Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

**基本类型对应的缓冲池如下:**

- boolean values true and false
- all byte values
- short values between -128 and 127
- int values between -128 and 127
- char in the range \u0000 to \u007F

在使用这些基本类型对应的包装类型时，就可以直接使用缓冲池中的对

## 五、String

### 5.1 基本概念

**String 被声明为 final，因此它不可被继承。**

内部使用 char 数组存储数据，该数组被声明为 final，这意味着 value 数组初始化之后就不能再引用其它数组。并且 String 内部没有改变 value 数组的方法，因此可以保 String 不可变。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
```

### 5.2 String的优势

1. **可以缓存 hash 值**

   因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。

2. **安全性**

​	String 经常作为参数，String 不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 String 是可变的，那么在网络连接过程中，String 被改变，改变 String 对象的那一方以为现在连接的是其它主机，而实际情况却不一定是。

3. **线程安全**

   String 不可变性天生具备线程安全，可以在多个线程中安全地使用。

### 5.3 String, StringBuffer and StringBuilder

#### 1. 可变性

- String 不可变
- StringBuffer 和 StringBuilder 可变

#### 2. 线程安全

- String 不可变，因此是线程安全的
- StringBuilder 不是线程安全的
- StringBuffer 是线程安全的，内部使用 synchronized 进行同步

### 5.4 String.intern()

使用 String.intern() 可以保证相同内容的字符串变量引用同一的内存对象。

下面示例中，s1 和 s2 采用 new String() 的方式新建了两个不同对象，而 s3 是通过 s1.intern() 方法取得一个对象引用。intern() 首先把 s1 引用的对象放到 String Pool(字符串常量池)中，然后返回这个对象引用。因此 s3 和 s1 引用的是同一个字符串常量池的对象。

```java
String s1 = new String("aaa");
String s2 = new String("aaa");
System.out.println(s1 == s2);           // false
String s3 = s1.intern();
System.out.println(s1.intern() == s3);  // true
```

## 六、Java8新特性

### 6.1 函数编程（lambda表达式）

#### 6.1.1 stream & parallelStream

每个Stream都有两种模式：顺序执行和并行执行。

**顺序流**

```java
List<Person> people = list.getStream.collect(Collectors.toList());
```

**并行流**

```java
List<Person> people = list.getStream.parallel().collect(Collectors.toList());
```

顾名思义，当使用顺序方式去遍历时，每个item读完后再读下一个item。而使用并行去遍历时，数组会被分成多个段，其中每一个都在不同的线程中处理，然后将结果一起输出。

**parallelStream原理：**

```java
List originList = someData;
split1 = originalList(0, mid) // 将数据分小部分
split2 = originalList(mid,end);
new Runnable(split1.process());//小部分执行操作
new Runnable(split2.process());
List revisedList = split1 + split2;//将结果合并
```

hadoop里面的 MapReduce 本身就是用于并行处理大数据集的软件框架，其处理大数据的核心思想就是大而化小，分配到不同机器去运行map，最终通过reduce将所有机器的结果结合起来得到一个最终结果，与MapReduce不同，Stream则是利用多核技术可将大数据通过多核并行处理，而MapReduce则可以分布式的。

**stream和parallelStream性能测试对比**

如果是多核机器，理论上并行流则会比顺序流快上一倍

```java
long t0 = System.nanoTime();

//初始化一个范围100万整数流,求能被2整除的数字，toArray()是终点方法

int a[]=IntStream.range(0, 1_000_000).filter(p -> p % 2==0).toArray();

long t1 = System.nanoTime();

//和上面功能一样，这里是用并行流来计算

int b[]=IntStream.range(0, 1_000_000).parallel().filter(p -> p % 2==0).toArray();

long t2 = System.nanoTime();

//我本机的结果是serial: 0.06s, parallel 0.02s，证明并行流确实比顺序流快

System.out.printf("serial: %.2fs, parallel %.2fs%n", (t1 - t0) * 1e-9, (t2 - t1) * 1e-9);
```

#### 6.1.2 Stream中常用的方法

##### 6.1.2.1 匿名类简写

```java
new Thread( () -> System.out.println("In Java8, Lambda expression rocks !!") ).start();

// 用法
(params) -> expression
(params) -> statement
(params) -> { statements }
```

##### 6.1.2.2 forEach

```java
// forEach
List features = Arrays.asList("Lambdas", "Default Method", "Stream API", "Date and Time API");
features.forEach(n -> System.out.println(n));
 
// 使用Java 8的方法引用更方便，方法引用由::双冒号操作符标示，
features.forEach(System.out::println);
```

##### 6.1.2.3 方法引用

构造引用

```java
// Supplier<Student> s = () -> new Student();
Supplier<Student> s = Student::new;
```

*对象::实例方法* Lambda表达式的(形参列表)与实例方法的(实参列表)类型，个数是对应

```java
// set.forEach(t -> System.out.println(t));
set.forEach(System.out::println);  
```

*类名::静态方法*

```java
// Stream<Double> stream = Stream.generate(() -> Math.random());
Stream<Double> stream = Stream.generate(Math::random); 
```

*类名::实例方法*

```java
//  TreeSet<String> set = new TreeSet<>((s1,s2) -> s1.compareTo(s2));
/*  这里如果使用第一句话，编译器会有提示: Can be replaced with Comparator.naturalOrder，这句话告诉我们
  String已经重写了compareTo()方法，在这里写是多此一举，这里为什么这么写，是因为为了体现下面
  这句编译器的提示: Lambda can be replaced with method reference。好了，下面的这句就是改写成方法引用之后: 
*/
TreeSet<String> set = new TreeSet<>(String::compareTo);
```

##### 6.1.2.4 Filter & Predicate

```java
    public static void main(String args[]) {
        List<String> names = Arrays.asList("张三丰", "张翠山", "张无忌", "金毛狮王", "赵敏");

        // 张开头的
        Predicate<String> zhang = (n) -> n.startsWith("张"); //
        // 含三丰的
        Predicate<String> sanfeng = (n) -> n.contains("三丰");
        // 王结尾的
        Predicate<String> wang = (n) -> n.endsWith("王");
        sanfeng.equals("金毛");

        //Predicate常和stream的filter配合使用，实现过滤
        //和filter结合用 默认调用的是test()方法 姓张的
        names.stream().filter(zhang).forEach(n -> System.out.println(n));

        //and()  姓张的，含三丰的
        names.stream().filter(zhang.and(sanfeng)).forEach(n -> System.out.println(n));

        //or()  张开头的,或者 王结尾的
        names.stream().filter(zhang.or(wang)).forEach(n -> System.out.println(n));

        //negate() 不是张开头的，但是王结尾的
        names.stream().filter(zhang.negate().and(wang)).forEach(n -> System.out.println(n));

        // equals() 方法还没懂什么意思
    }
```

##### 6.1.2.5 Map&Reduce

map将集合类(例如列表)元素进行转换的。还有一个 reduce() 函数可以将所有值合并成一个

```java
List costBeforeTax = Arrays.asList(100, 200, 300, 400, 500);
double bill = costBeforeTax.stream().map((cost) -> cost + .12*cost).reduce((sum, cost) -> sum + cost).get();
System.out.println("Total : " + bill);

//使用map方法获取list数据中的name
List<String> names = list.stream().map(Student::getName).collect(Collectors.toList());
System.out.println(names);

//使用map方法获取list数据中的name的长度
List<Integer> length = list.stream().map(Student::getName).map(String::length).collect(Collectors.toList());
System.out.println(length);

//将每人的分数-10
List<Integer> score = list.stream().map(Student::getScore).map(i -> i - 10).collect(Collectors.toList());
System.out.println(score);

//计算学生总分
Integer totalScore1 = list.stream().map(Student::getScore).reduce(0,(a,b) -> a + b);
System.out.println(totalScore1);

//计算学生总分，返回Optional类型的数据，改类型是java8中新增的，主要用来避免空指针异常
Optional<Integer> totalScore2 = list.stream().map(Student::getScore).reduce((a,b) -> a + b);
System.out.println(totalScore2.get());

//计算最高分和最低分
Optional<Integer> max = list.stream().map(Student::getScore).reduce(Integer::max);
Optional<Integer> min = list.stream().map(Student::getScore).reduce(Integer::min);
```

##### 6.1.2.6 Collectors

- Collectors.joining(", ")
- Collectors.toList()
- Collectors.groupingBy()
- Collectors.toSet() ，生成set集合
- Collectors.toMap(MemberModel::getUid, Function.identity())
- Collectors.toMap(ImageModel::getAid, o -> IMAGE_ADDRESS_PREFIX + o.getUrl())

```java
//算出分数最小的那个并输出
userList.stream().collect(Collectors.minBy(Comparator.comparingInt(User::getScore))).ifPresent(System.out::println);

//算出分数最大的那个并输出（无法做到多个并列的时候求值）
Optional optional = userList.stream().collect(Collectors.maxBy(Comparator.comparingInt(User::getScore)));
//optional.isPresent(System.out::println);//isPresent是判断是否存在，不能接受参数
optional.ifPresent(System.out::println);//直接使用时ifPresent

//算出分数平均值并输出
double averagint = userList.stream().collect(Collectors.averagingInt(User::getScore));
System.out.println("averagint = " + averagint);

//算出分数总和并输出
int summingInt = userList.stream().collect(Collectors.summingInt(User::getScore));
System.out.println("summingInt = " + summingInt);

//算出汇总信息
IntSummaryStatistics intSummaryStatistics = userList.stream().collect(Collectors.summarizingInt(User::getScore));
System.out.println("intSummaryStatistics = " + intSummaryStatistics);

//拼接名字
String nameStrs = userList.stream().map(User::getName).collect(Collectors.joining());
System.out.println("nameStrs = " + nameStrs);

//拼接名字，调用另外一个方法，可以加前缀和后缀
String nameStrs2 = userList.stream().map(User::getName).collect(Collectors.joining(", ", "[", "]"));
System.out.println("nameStrs2 = " + nameStrs2);

//分组：按照分数（返回的map的key是根据分组的条件来决定的，score是int，那么key就是Integer）
Map<Integer, List<User>> scoreUsers = userList.stream().collect(Collectors.groupingBy(User::getScore));
System.out.println("scoreUsers = " + scoreUsers);

//二级分组：线按照分数分组，返回一个Map<Integer, List<User>>, 在根据用户名分组
Map<Integer, Map<String, List<User>>> scoreNameUsers = userList.stream().collect(Collectors.groupingBy(User::getScore, Collectors.groupingBy(User::getName)));
System.out.println("scoreNameUsers = " + scoreNameUsers);

//分区,是否及格
Map<Boolean, List<User>> jigeUsers = userList.stream().collect(Collectors.partitioningBy(user -> user.getScore() >= 60));
System.out.println("jigeUsers = " + jigeUsers);

//二级分区,是否及格,及格里面是否大于80
Map<Boolean, Map<Boolean, List<User>>> youxiuUsers = userList.stream().collect(Collectors.partitioningBy(user -> user.getScore() >= 60, Collectors.partitioningBy(user -> user.getScore() >= 80)));
System.out.println("youxiuUsers = " + youxiuUsers);

//分区,是否及格,算出及格的个数
Map<Boolean, Long> jigeUserCount = userList.stream().collect(Collectors.partitioningBy(user -> user.getScore() >= 60, Collectors.counting()));
System.out.println("jigeUserCount = " + jigeUserCount);

//先按照名字分组,获取每个分组分数最小的
Map<String, User> UserCount = userList.stream().collect(Collectors.groupingBy(User::getName, Collectors.collectingAndThen(Collectors.minBy(Comparator.comparingInt(User::getScore)), Optional::get)));
System.out.println("UserCount = " + UserCount);
```

##### 6.1.2.7 flatMap

拼接Stream

```java
List<Integer> result= Stream.of(Arrays.asList(1,3),Arrays.asList(5,6)).flatMap(a->a.stream()).collect(Collectors.toList());
```

结果: [1, 3, 5, 6]



##### 6.1.2.8 distinct

去重

```java
List<LikeDO> likeDOs = new ArrayList<LikeDO>();
List<Long> likeTidList = likeDOs.stream().map(LikeDO::getTid)
                .distinct().collect(Collectors.toList());
```

##### 6.1.2.9 count

计总数

count方法，跟List接口的size一样，返回的都是这个集合流的元素的长度，不同的是，流是集合的一个高级工厂，中间操作是工厂里的每一道工序，我们对这个流操作完成后，可以进行元素的数量的和；

```java
int countOfAdult=persons.stream()
                       .filter(p -> p.getAge() > 18)
                       .map(person -> new Adult(person))
                       .count();
```

##### 6.1.2.10 Match

- anyMatch表示，判断的条件里，任意一个元素成功，返回true
- allMatch表示，判断条件里的元素，所有的都是，返回true
- noneMatch跟allMatch相反，判断条件里的元素，所有的都不是，返回true

```java
List<String> strs = Arrays.asList("a", "a", "a", "a", "b");
        boolean aa = strs.stream().anyMatch(str -> str.equals("a"));
        boolean bb = strs.stream().allMatch(str -> str.equals("a"));
        boolean cc = strs.stream().noneMatch(str -> str.equals("a"));
        long count = strs.stream().filter(str -> str.equals("a")).count();
        System.out.println(aa);// TRUE
        System.out.println(bb);// FALSE
        System.out.println(cc);// FALSE
        System.out.println(count);// 4
```

##### 6.1.2.11 min，max，summaryStatictics

最小值，最大值

```java
List<Person> lists = new ArrayList<Person>();
lists.add(new Person(1L, "p1"));
lists.add(new Person(2L, "p2"));
lists.add(new Person(3L, "p3"));
lists.add(new Person(4L, "p4"));
Person a = lists.stream().max(Comparator.comparing(t -> t.getId())).get();
System.out.println(a.getId());
```

如果比较器涉及多个条件，比较复杂，可以定制

```java
 Person a = lists.stream().min(new Comparator<Person>() {

      @Override
      public int compare(Person o1, Person o2) {
           if (o1.getId() > o2.getId()) return -1;
           if (o1.getId() < o2.getId()) return 1;
           return 0;
       }
 }).get();
```

summaryStatictics

```java
//获取数字的个数、最小值、最大值、总和以及平均值
List<Integer> primes = Arrays.asList(2, 3, 5, 7, 11, 13, 17, 19, 23, 29);
IntSummaryStatistics stats = primes.stream().mapToInt((x) -> x).summaryStatistics();
System.out.println("Highest prime number in List : " + stats.getMax());
System.out.println("Lowest prime number in List : " + stats.getMin());
System.out.println("Sum of all prime numbers : " + stats.getSum());
System.out.println("Average of all prime numbers : " + stats.getAverage());
```

##### 6.1.2.12 sort

```java
	public static void main(String[] args) {
        List<Student> list = new ArrayList<>();
 
        Student s1 = new Student("zhangsan","beijing",30);
        list.add(s1);
 
        Student s2 = new Student("lisi","shanghai",29);
        list.add(s2);
 
        Student s3 = new Student("lining","shandong",31);
        list.add(s3);
 
        // forEach循环
        list.stream().forEach(student -> System.out.println(student.getAge()));
 
        System.out.println("----------使用stream和sort--默认升序----------");
        // sort排序:原集合不变，新集合按顺序排序
        List<Student> sortList1 = list.stream().sorted((a, b) -> a.getAge().compareTo(b.getAge())).collect(Collectors.toList());
        sortList1.stream().forEach(s-> System.out.println(s.getAge()));
        System.out.println();
 
        System.out.println("---------使用stream和sort--降序排列-----------");
        List<Student> sortDesList = list.stream().sorted(Comparator.comparingInt(Student::getAge).reversed()).collect(Collectors.toList());
        sortDesList.stream().forEach(s-> System.out.println(s.getAge()));
        System.out.println();
 
        System.out.println("----------不使用stream和sort------------");
        // 使用集合的sort排序，集合自身排序发生变化
        list.sort((a,b)->a.getAge().compareTo(b.getAge()));
        list.stream().forEach(student -> System.out.println(student.getAge()));
        System.out.println();
    }
```

#### 6.1.3 Functionallnterface

##### 6.1.3.1 理解注解@FunctionInterFace

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface FunctionalInterface{}
```

interface做注解的注解类型，被定义成java语言规范

一个被它注解的接口只能有一个抽象方法，有两种例外

- 第一是接口允许有实现的方法，这种实现的方法是用default关键字来标记的(java反射中java.lang.reflect.Method#isDefault()方法用来判断是否是default方法)

- 第二如果声明的方法和java.lang.Object中的某个方法一样，它可以不当做未实现的方法，不违背这个原则: 一个被它注解的接口只能有一个抽象方法, 比如: `java public interface Comparator<T> { int compare(T o1, T o2); boolean equals(Object obj); }`

如果一个类型被这个注解修饰，那么编译器会要求这个类型必须满足如下条件:

- 这个类型必须是一个interface，而不是其他的注解类型、枚举enum或者类class
- 这个类型必须满足function interface的所有要求，如你个包含两个抽象方法的接口增加这个注解，会有编译错误。

编译器会自动把满足function interface要求的接口自动识别为function interface，所以你才不需要对上面示例中的 ITest接口增加@FunctionInterface注解。

##### 6.1.3.2 自定义函数接口

```java
@FunctionalInterface
public interface IMyInterface {
    void study();
}

package com.isea.java;
public class TestIMyInterface {
    public static void main(String[] args) {
        IMyInterface iMyInterface = () -> System.out.println("I like study");
        iMyInterface.study();
    }
}
```

##### 6.1.3.3 内置四大函数接口

- 消费型接口: Consumer< T> void accept(T t)有参数，无返回值的抽象方法；
- 供给型接口: Supplier < T> T get() 无参有返回值的抽象方法；
- 断定型接口: Predicate<T> boolean test(T t):有参，但是返回值类型是固定的boolean;
- 函数型接口: Function<T,R> R apply(T t)有参有返回值的抽象方法；

#### 6.1.4 一些例子

##### 6.1.4.1 输出 年龄 > 25 的女程序员中名字排名前3位的姓名

```java
javaProgrammers.stream()
          .filter((p) -> (p.getAge() > 25))
          .filter((p) -> ("female".equals(p.getGender())))
          .sorted((p, p2) -> (p.getFirstName().compareTo(p2.getFirstName())))
          .limit(3)
          //.forEach(e -> e.setSalary(e.getSalary() / 100 * 5 + e.getSalary()))//涨工资
          .forEach((p) -> System.out.printf("%s %s; ", p.getFirstName(), p.getLastName()));
```

##### 6.1.4.2 工资最高的 Java programmer

```java
Person person = javaProgrammer
    	.stream()
    	.max((p, p2) -> (p.getSalary() - p2.getSalart()))
    	.get()
```

##### 6.1.4.3 将Java programmers 的first name 存放到TreeSet

```java
TreeSet<String> javaDevLastName = javaProgrammers
    	.strem()
    	.mapToInt(P -> p.getSalary)
    	.sum();
```

##### 6.1.4.4 Comparator多属性排序: 先按名字不分大小写排，再按GID倒序排，最后按年龄正序排

```java
public static void main(String[] args) {
	List<Person> personList = getTestList();
	personList.sort(Comparator.comparing(Person::getName, String.CASE_INSENSITIVE_ORDER)
			.thenComparing(Person::getGid, (a, b) -> b.compareTo(a))
			.thenComparingInt(Person::getAge));
	personList.stream().forEach(System.out::println);
}

public static List<Person> getTestList() {
	return Lists.newArrayList(new Person("dai", "301", 10), new Person("dai", "303", 10),
			new Person("dai", "303", 8), new Person("dai", "303", 6), new Person("dai", "303", 11),
			new Person("dai", "302", 9), new Person("zhang", "302", 9), new Person("zhang", "301", 9),
			new Person("Li", "301", 8));
}

// 输出结果
// Person [name=dai, gid=303, age=6]
// Person [name=dai, gid=303, age=8]
// Person [name=dai, gid=303, age=10]
// Person [name=dai, gid=303, age=11]
// Person [name=dai, gid=302, age=9]
// Person [name=dai, gid=301, age=10]
// Person [name=Li, gid=301, age=8]
// Person [name=zhang, gid=302, age=9]
// Person [name=zhang, gid=301, age=9]
```

##### 6.1.4.5 处理字符串

两个新的方法可在字符串类上使用：join和chars。

jion()使用指定的分隔符，将任何数量的字符串连接为一个字符串

```java
String.jion(":", "foobar", "foo", "bar");
// => foobar:foo:bar
```

chars从字符串所有字符创建数据流，所以你可以在这些字符上使用流式操作。

```java
"foobar:foo:bar"
    .chars()
    .distinct()
    .mapToObj(c -> String.valueOf((char)c))
    .sorted()
    .collect(Collectors.joining());
// => :abfor
```

不仅仅是字符串，正则表达式模式串也能收益与数据流。我们可以分割任何模式串，并创建数据流来处理它们，而不是将字符串分割为单个字符的数据流，像下面这样

```java
Pattern.compile(":")
    .splitAsStream("foobar:foo:bar")
    .filter(s -> s.contaions("bar"))
    .sorted()
    .collect(Collectos.joining(":"))
 // => bar:foobar	
```

此外正则模式串可以转换为谓词，这些谓词可以像下面那样用于过滤字符串流：

```java
Pattern pattern = Pattern.compile(".*@gmail\\.com");
Stream.of("bob@gmail.com", "alice@hotmail.com")
    .filter(pattern.asPredicate())
    .count();
// => 1
```

上面的模式串接受任何以@gmail.com结尾的字符串，并且之后用作Java8的Predicate来过滤电子邮件地址流。

##### 6.1.4.6 集合--》取元素的一个属性--》去重---》组装成List--》返回

```java
List<LikeDO> likeDOs = new ArrayList<LikeDO>();
List<Long> likeTidList = likeDOs.stream().map(LikeDO::getTid)
                .distinct().collect(Collectors.toList());

```

##### 6.1.4.7 集合--》按表达式过滤--》遍历、每个元素处理--》放入预先定义的集合中

```java
// 预先定义的集合
Map<String, StkProduct> newStockName2Product = Maps.newConcurrentMap();
// 先过滤，然后遍历，对元素操作，之后放入map集合中
stockProducts.stream().filter(stkProduct -> stkProduct.enabled).forEach(stkProduct -> {
    String newName = BCConvert.bj2qj(StringUtils.replace(stkProduct.name, " ", ""));
    newStockName2Product.put(newName, stkProduct);
});
```

```java
 Set<String> qjStockNames;
 //过滤，遍历，获取value,存入对应map中
 qjStockNames.stream().filter(name -> !acAutomaton.getKey2link().containsKey(name)).forEach(name -> {
            String value = "";
            StkProduct stkProduct = stockNameQj2Product.get(name);
            if (stkProduct != null) {
                value = stkProduct.name;
            }
            acAutomaton.getKey2link().put(name, value);
        });

```

##### 6.1.4.8 集合--》Map

```java
List<ImageModel> imageModelList = null;
Map<Long, String> imagesMap = null;
// 对象list中的对象指定元素转为map
imagesMap = imageModelList.stream().collect(Collectors.toMap(ImageModel::getAid, o -> IMAGE_ADDRESS_PREFIX + o.getUrl()));
              

Map<String, String> kvMap = postDetailCacheList.stream().collect(Collectors.toMap((detailCache) ->
                getBbsSimplePostKey(detailCache.getTid()), JSON::toJSONString));


Map<Long, Long> pidToTid；
List<String> pidKeyList = pidToTid.entrySet().stream().map((o) -> getKeyBbsReplyPid(o.getValue(), o.getKey())).collect(Collectors.toList());

```

##### 6.1.4.9 DO模型---》Model模型

```java
List<AdDO> adDOList;
adDOList.stream().map(adDo -> convertAdModel(adDo))
                .collect(Collectors.toList());
```

##### 6.1.4.10 phones 是一个List<String>，将相同的元素分组、归类

```java
List<String> phones=new ArrayList<String>();
        phones.add("a");
        phones.add("b");
        phones.add("a");
        phones.add("a");
        phones.add("c");
        phones.add("b");
        Map<String, List<String>> phoneClassify = phones.stream().collect(Collectors.groupingBy(item -> item));
        System.out.println(phoneClassify);
//返回结果: 
//{a=[a, a, a], b=[b, b], c=[c]}
```

### 6.2 Optional类深度解析

Java 8引入了一个新的Optional类。Optional类的Javadoc描述如下:

> 这是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true，调用get()方法会返回该对象。

在利用对象或者容器取值的时候，为了避免空指针，通常在取值之前首先利用if/else进行判断，但是这样这复杂有冗余，为了是代码设计变得更加优雅，Java8提供了Optional类来优化这种写法