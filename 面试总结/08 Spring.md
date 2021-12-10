# Spring

参考文档：
[Spring常见问题总结（JavaGuide）](https://snailclimb.gitee.io/javaguide/#/docs/system-design/framework/spring/Spring常见问题总结)

[Spring面试题（ThinkWon）](https://thinkwon.blog.csdn.net/article/details/104397516)

## 一、Spring概述

### 1.1. 什么是spring?

Spring 是一款开源的轻量级 Java 开发框架，旨在提高开发人员的开发效率以及系统的可维护性。

我们一般说 Spring 框架指的都是 Spring Framework，它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发。

比如说 Spring **自带 IoC（Inverse of Control:控制反转） 和 AOP(Aspect-Oriented Programming:面向切面编程)**、可以很方便地对数据库进行访问、可以很方便地集成第三方组件（电子邮件，任务，调度，缓存等等）、对单元测试支持比较好、支持 RESTful Java 应用程序的开发。

### 1.2. Spring的优缺点是什么？

#### 优点

- **方便解耦，简化开发**

Spring就是一个大工厂，可以将所有对象的创建和依赖关系的维护，交给Spring管理。

- **AOP编程的支持**

Spring提供面向切面编程，可以方便的实现对程序进行权限拦截、运行监控等功能。

- **声明式事务的支持**

只需要通过配置就可以完成对事务的管理，而无需手动编程。

- **方便程序的测试**

Spring对Junit4支持，可以通过注解方便的测试Spring程序。

- **方便集成各种优秀框架**

Spring不排斥各种优秀的开源框架，其内部提供了对各种优秀框架的直接支持（如：Struts、Hibernate、MyBatis等）。

- **降低JavaEE API的使用难度**

Spring对JavaEE开发中非常难用的一些API（JDBC、JavaMail、远程调用等），都提供了封装，使这些API应用难度大大降低。

#### 缺点

- Spring明明一个很轻量级的框架，却给人感觉大而全
- Spring依赖反射，反射影响性能

- 使用门槛升高，入门Spring需要较长时间

### 1.3. 列举一些重要的 Spring 模块？

下图对应的是 Spring4.x 版本。目前最新的 5.x 版本中 Web 模块的 Portlet 组件已经被废弃掉，同时增加了用于异步响应式处理的 WebFlux 组件。

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211126103338.png)

**Spring Core**

核心模块， Spring 其他所有的功能基本都需要依赖于该类库，主要提供 IoC 依赖注入功能的支持。

**Spring Aspects**

该模块为与 AspectJ 的集成提供支持。

**Spring AOP**

提供了面向切面的编程实现。

**Spring Data Access/Integration ：**

Spring Data Access/Integration 由 5 个模块组成：

- - spring-jdbc : 提供了对数据库访问的抽象 JDBC。不同的数据库都有自己独立的 API 用于操作数据库，而 Java 程序只需要和 JDBC API 交互，这样就屏蔽了数据库的影响。
  - spring-tx : 提供对事务的支持。

- - spring-orm : 提供对 Hibernate 等 ORM 框架的支持。
  - spring-oxm ： 提供对 Castor 等 OXM 框架的支持。

- - spring-jms : Java 消息服务。

**Spring Web**

Spring Web 由 4 个模块组成：

- - spring-web ：对 Web 功能的实现提供一些最基础的支持。
  - spring-webmvc ： 提供对 Spring MVC 的实现。

- - spring-websocket ： 提供了对 WebSocket 的支持，WebSocket 可以让客户端和服务端进行双向通信。
  - spring-webflux ：提供对 WebFlux 的支持。WebFlux 是 Spring Framework 5.0 中引入的新的响应式框架。与 Spring MVC 不同，它不需要 Servlet API，是完全异步.

**Spring Test**

Spring 团队提倡测试驱动开发（TDD）。有了控制反转 (IoC)的帮助，单元测试和集成测试变得更简单。

Spring 的测试模块对 JUnit（单元测试框架）、TestNG（类似 JUnit）、Mockito（主要用来 Mock 对象）、PowerMock（解决 Mockito 的问题比如无法模拟 final, static， private 方法）等等常用的测试框架支持的都比较好。

### 1.4. Spring 框架中都用到了哪些设计模式？

1. 工厂模式：BeanFactory就是简单工厂模式的体现，用来创建对象的实例；
2. 单例模式：Bean默认为单例模式。
3. 代理模式：Spring的AOP功能用到了JDK的动态代理和CGLIB字节码生成技术；
4. 模板方法：用来解决代码重复的问题。比如. RestTemplate, JmsTemplate, JpaTemplate。
5. 观察者模式：定义对象键一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都会得到通知被制动更新，如Spring中listener的实现–ApplicationListener。

## 二、Spring控制反转(IOC)

### 2.1. 什么是Spring IOC 容器？

控制反转即IoC (Inversion of Control)，它**把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理**。所谓的“控制反转”概念就是对组件对象控制权的转移，从程序代码本身转移到了外部容器。

Spring IOC 负责创建对象，管理对象（通过依赖注入（DI），装配对象，配置对象，并且管理这些对象的整个生命周期。

### 2.2. 控制反转(IoC)有什么作用

- 将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 IoC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。
- 在实际项目中一个 Service 类可能依赖了很多其他的类，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把人逼疯。如果利用 IoC 的话，你只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度。

### 2.3. Spring IoC 的实现机制

Spring 中的 IoC 的实现原理就是工厂模式加反射机制。

```java
interface Fruit {
   public abstract void eat();
 }

class Apple implements Fruit {
    public void eat(){
        System.out.println("Apple");
    }
}

class Orange implements Fruit {
    public void eat(){
        System.out.println("Orange");
    }
}

class Factory {
    public static Fruit getInstance(String ClassName) {
        Fruit f=null;
        try {
            f=(Fruit)Class.forName(ClassName).newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return f;
    }
}

class Client {
    public static void main(String[] a) {
        Fruit f=Factory.getInstance("io.github.dunwu.spring.Apple");
        if(f!=null){
            f.eat();
        }
    }
}
```

### 2.4. Spring 的 IoC支持哪些功能

Spring 的 IoC 设计支持以下功能：

- 依赖注入
- 依赖检查

- 自动装配
- 支持集合

- 指定初始化方法和销毁方法
- 支持回调某些方法（但是需要实现 Spring 接口，略有侵入）

其中，最重要的就是依赖注入，从 XML 的配置上说，即 ref 标签。对应 Spring RuntimeBeanReference 对象。

对于 IoC 来说，最重要的就是容器。容器管理着 Bean 的生命周期，控制着 Bean 的依赖注入。

### 2.5. Spring 如何设计容器的，BeanFactory和ApplicationContext的关系详解

#### 2.5.1 ApplicationContext

我们从创建Spring容器的代码：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
```

可以看到，Spring容器就是`ApplicationContext`，它是一个接口，有很多实现类，这里我们选择`ClassPathXmlApplicationContext`，表示它会自动从classpath中查找指定的XML配置文件。

获得了`ApplicationContext`的实例，就获得了IoC容器的引用。从`ApplicationContext`中我们可以根据Bean的ID获取Bean，但更多的时候我们根据Bean的类型获取Bean的引用：

```java
UserService userService = context.getBean(UserService.class);
```

#### 2.5.2 BeanFactory

Spring还提供另一种IoC容器叫`BeanFactory`，使用方式和`ApplicationContext`类似：

```java
BeanFactory factory = new XmlBeanFactory(new ClassPathResource("application.xml"));
MailService mailService = factory.getBean(MailService.class);
```

#### 2.5.3 二者区别

`BeanFactory`和`ApplicationContext`的区别在于：

`BeanFactory`的实现是按需创建，即第一次获取Bean时才创建这个Bean，而`ApplicationContext`会一次性创建所有的Bean。

实际上，`ApplicationContext`接口是从`BeanFactory`接口继承而来的，

并且，`ApplicationContext`提供了一些额外的功能，包括国际化支持、事件和通知机制等。

通常情况下，我们总是使用`ApplicationContext`，很少会考虑使用`BeanFactory`。

### 2.6. 什么是Spring的依赖注入？

控制反转IoC是一个很大的概念，可以用不同的方式来实现。其主要实现方式有两种：依赖注入和依赖查找

依赖注入：相对于IoC而言，依赖注入(DI)更加准确地描述了IoC的设计理念。所谓**依赖注入**（Dependency Injection），即**组件之间的依赖关系由容器在应用系统运行期来决定，也就是由容器动态地将某种依赖关系的目标对象实例注入到应用系统中的各个关联的组件之中**。组件不做定位查询，只提供普通的Java方法让容器去决定依赖关系。

### 2.7. 依赖注入的基本原则

依赖注入的基本原则是：应用组件不应该负责查找资源或者其他依赖的协作对象。配置对象的工作应该由IoC容器负责，“查找资源”的逻辑应该从应用组件的代码中抽取出来，交给IoC容器负责。容器全权负责组件的装配，它会把符合依赖关系的对象通过属性（JavaBean中的setter）或者是构造器传递给需要的对象。

### 2.8. 有哪些不同类型的依赖注入实现方式？

依赖注入是时下最流行的IoC实现方式，依赖注入分为接口注入（Interface Injection），Setter方法注入（Setter Injection）和构造器注入（Constructor Injection）三种方式。其中接口注入由于在灵活性和易用性比较差，现在从Spring4开始已被废弃。

**构造器依赖注入：**构造器依赖注入通过容器触发一个类的构造器来实现的，该类有一系列参数，每个参数代表一个对其他类的依赖。

**Setter方法注入：**Setter方法注入是容器通过调用无参构造器或无参static工厂 方法实例化bean之后，调用该bean的setter方法，即实现了基于setter的依赖注入。

## 三、Spring bean

### 3.1. 什么是 bean？

简单来说，**bean 代指的就是那些被 IoC 容器所管理的对象。**

我们需要告诉 IoC 容器帮助我们管理哪些对象，这个是通过配置元数据来定义的。配置元数据可以是 XML 文件、注解或者 Java 配置类。

```xml
<!-- Constructor-arg with 'value' attribute -->
<bean id="..." class="...">
   <constructor-arg value="..."/>
</bean>
```

### 3.2. bean 的作用域有哪些?

Spring 中 Bean 的作用域通常有下面几种：

- **singleton** : 唯一 bean 实例，Spring 中的 bean 默认都是单例的，对单例设计模式的应用。
- **prototype** : 每次请求都会创建一个新的 bean 实例。

- **request** : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
- **session** : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。

- **global-session** ： 全局 session 作用域，仅仅在基于 portlet 的 web 应用中才有意义，Spring5 已经没有了。Portlet 是能够生成语义代码(例如：HTML)片段的小型 Java Web 插件。它们基于 portlet 容器，可以像 servlet 一样处理 HTTP 请求。但是，与 servlet 不同，每个 portlet 都有不同的会话。

**注意：** 缺省的Spring bean 的作用域是Singleton。使用 prototype 作用域需要慎重的思考，因为频繁创建和销毁 bean 会带来很大的性能开销。

**如何配置 bean 的作用域呢？**

xml 方式：

```xml
<bean id="..." class="..." scope="singleton"></bean>
```

注解方式：

```java
@Bean 
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE) 
public Person personPrototype() {     
    return new Person(); 
}
```

### 3.3. Spring框架中的单例bean是线程安全的吗？

不是，**Spring框架中的单例bean不是线程安全的。**

spring 中的 bean 默认是单例模式，spring 框架并没有对单例 bean 进行多线程的封装处理。

实际上大部分时候 spring bean 无状态的（比如 dao 类），所有某种程度上来说 bean 也是安全的，但如果 bean 有状态的话（比如 view model 对象），那就要开发者自己去保证线程安全了，最简单的就是改变 bean 的作用域，把“singleton”变更为“prototype”，这样请求 bean 相当于 new Bean()了，所以就可以保证线程安全了。

- 有状态就是有数据存储功能。
- 无状态就是不会保存数据。

### 3.4. Spring如何处理线程并发问题？

在一般情况下，只有无状态的Bean才可以在多线程环境下共享，在Spring中，绝大部分Bean都可以声明为singleton作用域，因为**Spring对一些Bean中非线程安全状态采用ThreadLocal进行处理，解决线程安全问题**。

ThreadLocal和线程同步机制都是为了解决多线程中相同变量的访问冲突问题。同步机制采用了“时间换空间”的方式，仅提供一份变量，不同的线程在访问前需要获取锁，没获得锁的线程则需要排队。而ThreadLocal采用了“空间换时间”的方式。

**ThreadLocal会为每一个线程提供一个独立的变量副本，从而隔离了多个线程对数据的访问冲突。**因为每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。ThreadLocal提供了线程安全的共享对象，在编写多线程代码时，可以把不安全的变量封装进ThreadLocal。

### 5. @Component 和 @Bean 的区别是什么？

1. @Component 注解作用于类；@Bean注解作用于方法。
2. @Component通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中（我们可以使用 @ComponentScan 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中）。@Bean 注解通常是我们在标有该注解的方法中定义产生这个 bean,@Bean告诉了 Spring 这是某个类的实例，当我需要用它的时候还给我。
3. @Bean 注解比 @Component 注解的自定义性更强，而且很多地方我们只能通过 @Bean 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 Spring容器时，则只能通过 @Bean来实现。

@Bean注解使用示例：

```java
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }

}
```

上面的代码相当于下面的 xml 配置

```java
<beans>
    <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```

下面这个例子是通过 @Component 无法实现的。

```java
@Bean
public OneService getService(status) {
    case (status)  {
        when 1:
                return new serviceImpl1();
        when 2:
                return new serviceImpl2();
        when 3:
                return new serviceImpl3();
    }
}
```

### 3.6. 将一个类声明为 bean 的注解有哪些?

我们一般使用 @Autowired 注解自动装配 bean，要想把类标识成可用于 @Autowired 注解自动装配的 bean 的类,采用以下注解可实现：

- @Component ：通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用@Component 注解标注。
- @Repository : 对应持久层即 Dao 层，主要用于数据库相关操作。

- @Service : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- @Controller : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。

### 3.7. bean 的生命周期?

1. Bean 容器找到配置文件中 Spring Bean 的定义。
2. Bean 容器利用 Java Reflection API 创建一个 Bean 的实例。
3. 如果涉及到一些属性值 利用 `set()`方法设置一些属性值。
4. 如果 Bean 实现了 `BeanNameAware` 接口，调用 `setBeanName()`方法，传入 Bean 的名字。
5. 如果 Bean 实现了 `BeanClassLoaderAware` 接口，调用 `setBeanClassLoader()`方法，传入 `ClassLoader`对象的实例。
6. 如果 Bean 实现了 `BeanFactoryAware` 接口，调用 `setBeanFactory()`方法，传入 `BeanFactory`对象的实例。
7. 与上面的类似，如果实现了其他 `*.Aware`接口，就调用相应的方法。
8. 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessBeforeInitialization()` 方法
9. 如果 Bean 实现了`InitializingBean`接口，执行`afterPropertiesSet()`方法。
10. 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
11. 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessAfterInitialization()` 方法
12. 当要销毁 Bean 的时候，如果 Bean 实现了 `DisposableBean` 接口，执行 `destroy()` 方法。
13. 当要销毁 Bean 的时候，如果 Bean 在配置文件中的定义包含 destroy-method 属性，执行指定的方法。

<img src="https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211126110955.png" alt="image-20211126110955629" style="zoom:80%;" />

### 3.8. 什么是bean装配？

装配，或bean 装配是指在Spring 容器中把bean组装到一起，前提是容器需要知道bean的依赖关系，如何通过依赖注入来把它们装配到一起。

### 3.9. 什么是bean的自动装配？

在Spring框架中，在配置文件中设定bean的依赖关系是一个很好的机制，Spring 容器能够自动装配相互合作的bean，这意味着容器不需要和配置，能通过Bean工厂自动处理bean之间的协作。这意味着 Spring可以通过向Bean Factory中注入的方式自动搞定bean之间的依赖关系。自动装配可以设置在每个bean上，也可以设定在特定的bean上。

### 3.10. 解释不同方式的自动装配，spring 自动装配 bean 有哪些方式？

在spring中，对象无需自己查找或创建与其关联的其他对象，由容器负责把需要相互协作的对象引用赋予各个对象，使用autowire来配置自动装载模式。

在Spring框架xml配置中共有5种自动装配：

- **no：**默认的方式是不进行自动装配的，通过手工设置ref属性来进行装配bean。
- **byName：**通过bean的名称进行自动装配，如果一个bean的 property 与另一bean 的name 相同，就进行自动装配。

- **byType：**通过参数的数据类型进行自动装配。
- **constructor：**利用构造函数进行装配，并且构造函数的参数通过byType进行装配。

- **autodetect：**自动探测，如果有构造方法，通过 construct的方式自动装配，否则使用 byType的方式自动装配。

### 3.11. 使用@Autowired注解自动装配的过程是怎样的？

使用@Autowired注解来自动装配指定的bean。在使用@Autowired注解之前需要在Spring配置文件进行配置，<context:annotation-config />。

在启动spring IoC时，容器自动装载了一个AutowiredAnnotationBeanPostProcessor后置处理器，当容器扫描到@Autowied、@Resource或@Inject时，就会在IoC容器自动查找需要的bean，并装配给该对象的属性。在使用@Autowired时，首先在容器中查询对应类型的bean：

- 如果查询结果刚好为一个，就将该bean装配给@Autowired指定的数据；
- 如果查询的结果不止一个，那么@Autowired会根据名称来查找；

- 如果上述查找的结果为空，那么会抛出异常。解决方法时，使用required=false。

## 四、Spring事务

### 4.1 Spring 管理事务的方式

- **编程式事务** ： 在代码中硬编码(不推荐使用) : 通过 `TransactionTemplate`或者 `TransactionManager` 手动管理事务，实际应用中很少使用，但是对于你理解 Spring 事务管理原理有帮助。
- **声明式事务** ： 在 XML 配置文件中配置或者直接基于注解（推荐使用） : 实际是通过 AOP 实现（基于`@Transactional` 的全注解方式使用最多）

### 4.2 Spring事务传播行为

**事务传播行为是为了解决业务层方法之间相互调用的事务问题**

​	当事务方式被另一个事务方法调用是，必须指定事务该如何传播。如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。

#### 4.2.1 PROPAGATION_REQUIRED

使用的最多的一个事务传播行为，我们平时经常使用的`@Transactional`注解默认使用就是这个事务传播行为。

**如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务**。

#### 4.2.2 PROPAGATION_REQUIRES_NEW

**创建一个新的事务，如果当前存在事务，则把当前事务挂起。**

也就是说不管外部方法是否开启事务，`Propagation.REQUIRES_NEW`修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。

#### 4.2.3 PROPAGATION_NESTED

**如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于`PROPAGATION_REQUIRED`。**

#### 4.2.4 PROPAGATION_MANDATORY

**如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常**。（mandatory：强制性）

这个使用的很少。

#### 4.2.5 其他事务传播行为

若是错误的配置以下 3 种事务传播行为，事务将不会发生回滚：

- **`TransactionDefinition.PROPAGATION_SUPPORTS`**: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- **`TransactionDefinition.PROPAGATION_NOT_SUPPORTED`**: 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- **`TransactionDefinition.PROPAGATION_NEVER`**: 以非事务方式运行，如果当前存在事务，则抛出异常。

## 五、Spring循环依赖及三级缓存

Spring启动过程大致如下：

1. 创建beanFactory，加载配置文件
2. 解析配置文件转化beanDefination，获取到bean的所有属性、依赖及初始化用到的各类处理器等
3. 刷新beanFactory容器，初始化所有单例bean
4. 注册所有的单例bean并返回可用的容器，一般为扩展的applicationContext

### 5.1 循环依赖

简单来讲，就是有一个 A 对象，创建 A 的时候发现 A 对象依赖 B，然后去创建 B 对象的时候，又发现 B 对象依赖 C，然后去创建 C 对象的时候，又发现 C 对象依赖 A。这就是所谓的循环依赖。

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211206214331.webp)

### 5.2 三级缓存

![image-20211206214452668](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211206214452.png)



| 名称                     | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| singletonObjects()       | 一级缓存，存放完整的bean                                     |
| earlySinglethonObjects() | 二级缓存，存放提前暴露的Bean，Bean是不完整的，未完成属性注入和执行init方法 |
| singletonFactoried()     | 三级缓存，存放的是Bean工厂，主要是生成Bean，存放到二级缓存中 |

Bean 都已经实例化了，为什么还需要一个生产 Bean 的工厂呢？这里实际上是跟 AOP 有关，如果项目中不需要为 Bean 进行代理，那么这个 Bean 工厂就会直接返回一开始实例化的对象，如果需要使用 AOP 进行代理，那么这个工厂就会发挥重要的作用了

### 5.3 解决循环依赖

#### 5.3.1 流程

利用A,B形成的循环依赖来举例：

1. **实例化 A**，此时 A 还未完成属性填充和初始化方法（@PostConstruct）的执行，A 只是一个半成品。
2. 为 A 创建一个 Bean 工厂，并**放入到  singletonFactories** 中。
3. 发现 A 需要注入 B 对象，但是一级、二级、三级缓存均为发现对象 B。
4. **实例化 B**，此时 B 还未完成属性填充和初始化方法（@PostConstruct）的执行，B 只是一个半成品。
5. 为 B 创建一个 Bean 工厂，并**放入到  singletonFactories** 中。
6. 发现 B 需要注入 A 对象，此时在一级、二级未发现对象 A，但是在三级缓存中发现了对象 A，**从三级缓存中得到对象 A，并将对象 A 放入二级缓存中**，同时删除三级缓存中的对象 A。（注意，此时的 A 还是一个半成品，并没有完成属性填充和执行初始化方法）
7. 将**对象 A 注入到对象 B** 中。
8. 对象 **B 完成属性填充，执行初始化方法**，并**放入到一级缓存中，同时删除二级缓存中的对象 B**。（此时对象 B 已经是一个成品）
9. 对象 A 得到对象 B，将**对象 B 注入到对象 A** 中。（对象 A 得到的是一个完整的对象 B）
10. 对象 **A 完成属性填充，执行初始化方法**，并**放入到一级缓存中，同时删除二级缓存中的对象 A**。

#### 5.3.2 如何获取依赖

Spring在注入属性的时候是如何获取依赖的呢？是通过一个`getSingleton()` 方法去获取需要的Bean的

```java
//缓存查找bean  如果1级没有，从2级获取,也没有,从3级创建放入2级
protected Object getSingleton(String beanName, boolean allowEarlyReference) {
    Object singletonObject = this.singletonObjects.get(beanName); //1级
    if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
        synchronized (this.singletonObjects) {
            singletonObject = this.earlySingletonObjects.get(beanName); //2级
            if (singletonObject == null && allowEarlyReference) {
                //3级缓存  在doCreateBean中创建了bean的实例后，封装ObjectFactory放入缓存的
                ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                if (singletonFactory != null) {
                    //创建未赋值的bean
                    singletonObject = singletonFactory.getObject();
                    //放入到二级缓存
                    this.earlySingletonObjects.put(beanName, singletonObject);
                    //从三级缓存删除
                    this.singletonFactories.remove(beanName);
                }
            }
        }
    }
    return singletonObject;
}   
```

当 Spring 为某个 Bean 填充属性的时候，它首先会寻找需要注入对象的名称，然后依次执行 `getSingleton()` 方法得到所需注入的对象，而获取对象的过程就是

- 先从一级缓存中获取，
- 一级缓存中没有就从二级缓存中获取，
- 二级缓存中没有就从三级缓存中获取，
- 如果三级缓存中也没有，那么就会去执行 `doCreateBean()` 方法创建这个 Bean。
- 如果三级缓存中存在，则将Bean放到二级缓存，从三级缓存删除

**Bean创建**

```java
protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, Object[] args) throws BeanCreationException {
    BeanWrapper instanceWrapper = null;
	
    if (instanceWrapper == null) {
        // 1. 实例化对象
        instanceWrapper = this.createBeanInstance(beanName, mbd, args);
    }

    final Object bean = instanceWrapper != null ? instanceWrapper.getWrappedInstance() : null;
    Class<?> beanType = instanceWrapper != null ? instanceWrapper.getWrappedClass() : null;
   
    // 2. 判断是否允许提前暴露对象，如果允许，则直接添加一个 ObjectFactory 到三级缓存
	boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&
				isSingletonCurrentlyInCreation(beanName));
    if (earlySingletonExposure) {
        // 添加三级缓存的方法详情在下方
        addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
    }

    // 3. 填充属性
    this.populateBean(beanName, mbd, instanceWrapper);
    // 4. 执行初始化方法，并创建代理
    exposedObject = initializeBean(beanName, exposedObject, mbd);
   
    return exposedObject;
}
```

添加三级缓存的方法如下：

```java
protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
    Assert.notNull(singletonFactory, "Singleton factory must not be null");
    synchronized (this.singletonObjects) {
        if (!this.singletonObjects.containsKey(beanName)) {
            //一级缓存没有，放入三级缓存
            this.singletonFactories.put(beanName, singletonFactory);
            //从二级缓存删除，确保二级缓存没有该bean
            this.earlySingletonObjects.remove(beanName);
            this.registeredSingletons.add(beanName);
        }
    }
}
```

通过这段代码，我们可以知道 Spring 在实例化对象的之后，就会为其创建一个 Bean 工厂，并将此工厂加入到三级缓存中。

#### 5.3.3 Aop代理问题

Spring 一开始提前暴露的并不是实例化的 Bean，而是将 Bean 包装起来的 ObjectFactory。为什么要这么做呢？

这实际上涉及到 AOP，如果创建的 Bean 是有代理的，那么注入的就应该是代理 Bean，而不是原始的 Bean。但是 Spring 一开始并不知道 Bean 是否会有循环依赖，通常情况下（没有循环依赖的情况下），Spring 都会在完成填充属性，并且执行完初始化方法之后再为其创建代理。但是，如果出现了循环依赖的话，Spring 就不得不为其提前创建代理对象，否则注入的就是一个原始对象，而不是代理对象。因此，这里就涉及到应该在哪里提前创建代理对象？

Spring 的做法就是在 ObjectFactory 中去提前创建代理对象。它会执行 `getObject()` 方法来获取到 Bean。实际上，它真正执行的方法如下

```java
protected Object getEarlyBeanReference(String beanName, RootBeanDefinition mbd, Object bean) {
    Object exposedObject = bean;
    if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {
        for (BeanPostProcessor bp : getBeanPostProcessors()) {
            if (bp instanceof SmartInstantiationAwareBeanPostProcessor) {
                SmartInstantiationAwareBeanPostProcessor ibp = (SmartInstantiationAwareBeanPostProcessor) bp;
                // 如果需要代理，这里会返回代理对象；否则返回原始对象
                exposedObject = ibp.getEarlyBeanReference(exposedObject, beanName);
            }
        }
    }
    return exposedObject;
}
```

因为提前进行了代理，避免对后面重复创建代理对象，会在 `earlyProxyReferences` 中记录已被代理的对象。

```java
public abstract class AbstractAutoProxyCreator extends ProxyProcessorSupport
        implements SmartInstantiationAwareBeanPostProcessor, BeanFactoryAware {
    @Override
    public Object getEarlyBeanReference(Object bean, String beanName) {
        Object cacheKey = getCacheKey(bean.getClass(), beanName);
        // 记录已被代理的对象
        this.earlyProxyReferences.put(cacheKey, bean);
        return wrapIfNecessary(bean, beanName, cacheKey);
    }
}
```

**通过上面的解析，我们可以知道 Spring 需要三级缓存的目的是为了在没有循环依赖的情况下，延迟代理对象的创建，使 Bean 的创建符合 Spring 的设计原则。**