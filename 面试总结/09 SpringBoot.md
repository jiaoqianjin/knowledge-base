# SpringBoot

1. 什么是 SpringBoot 自动装配？
2. SpringBoot 是如何实现自动装配的？如何实现按需加载？
3. 如何实现一个 Starter？

## 1. 说一下 Spring Boot 自动装配原理？ 

​		没有 Spring Boot 的情况下，如果我们需要引入第三方依赖，需要手动配置，非常麻烦。但是，Spring Boot 中，我们直接引入一个 starter 即可。比如你想要在项目中使用 redis 的话，直接在项目中引入对应的 starter 即可。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

​	引入 starter 之后，我们通过少量注解和一些简单的配置就能使用第三方组件提供的功能了。

自动装配可以简单理解为：**通过注解或者一些简单的配置就能在 Spring Boot 的帮助下实现某块功能。**

### 1. 什么是 SpringBoot 自动装配？

SpringBoot 定义了一套接口规范，这套规范规定：SpringBoot 在启动时会扫描外部引用 jar 包中的`META-INF/spring.factories`文件，将文件中配置的类型信息加载到 Spring 容器（此处涉及到 JVM 类加载机制与 Spring 的容器知识），并执行类中定义的各种操作。对于外部 jar 来说，只需要按照 SpringBoot 定义的标准，就能将自己的功能装置进 SpringBoot。

### 2. SpringBoot 是如何实现自动装配的？ 

#### @EnableAutoConfiguration:实现自动装配的核心注解

EnableAutoConfiguration 只是一个简单地注解，自动装配核心功能的实现实际是通过 AutoConfigurationImportSelector类。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage //作用：将main包下的所欲组件注册到容器中
@Import({AutoConfigurationImportSelector.class}) //加载自动装配类 xxxAutoconfiguration
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```

`AutoConfigurationImportSelector` 类到底做了什么？

#### AutoConfigurationImportSelector: 加载自动装配类

`AutoConfigurationImportSelector` 类实现了 `ImportSelector`接口，也就实现了这个接口中的 `selectImports`方法，该方法主要用于**获取所有符合条件的类的全限定类名，这些类需要被加载到 IoC 容器中**。

![image-20211018204602160](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211018204603.png)

```java
private static final String[] NO_IMPORTS = new String[0];

public String[] selectImports(AnnotationMetadata annotationMetadata) {
        // <1>.判断自动装配开关是否打开
        if (!this.isEnabled(annotationMetadata)) {
            return NO_IMPORTS;
        } else {
          //<2>.获取所有需要装配的bean
            AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
            AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata);
            return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
        }
    }
```

这里我们需要重点关注一下`getAutoConfigurationEntry()`方法，这个方法主要负责加载自动配置类的。

该方法调用链如下：

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211207095035.png)

### 3. 如何实现一个 Starter

#### 第一步，创建`threadpool-spring-boot-starter`工程

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211207095259.png) 

#### 第二步，引入 Spring Boot 相关依赖

<img src="https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211207095303.png" alt="img" style="zoom: 80%;" /> 

#### 第三步，创建`ThreadPoolAutoConfiguration`

<img src="https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211207095307.png" alt="img" style="zoom: 50%;" /> 

#### 第四步，在`threadpool-spring-boot-starter`工程的 resources 包下创建`META-INF/spring.factories`文件

<img src="https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211207095310.png" alt="img" style="zoom:80%;" /> 

#### 最后新建工程引入`threadpool-spring-boot-starter`

<img src="https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211207095313.png" alt="img" style="zoom:50%;" /> 

#### 测试通过！！！

<img src="https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211207095316.png" alt="img" style="zoom:50%;" /> 

### 4. 总结

Spring Boot 通过@EnableAutoConfiguration开启自动装配，通过 SpringFactoriesLoader 最终加载META-INF/spring.factories中的自动配置类实现自动装配，自动配置类其实就是通过@Conditional按需加载的配置类，想要其生效必须引入spring-boot-starter-xxx包实现起步依赖



**springboot的自动装配就是通过自定义实现ImportSelector接口，从而导致项目启动时会自动将所有项目META-INF/spring.factories路径下的配置类注入到spring容器中，从而实现了自动装配。**

## 2. SpringBoot常用注解

### 2.1 @SpringBootApplication

`@SpringBootApplication`看作是 `@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan` 注解的集合。

- `@EnableAutoConfiguration`：启用 SpringBoot 的自动配置机制
- `@ComponentScan`： 扫描被`@Component` (`@Service`,`@Controller`)注解的 bean，注解默认会扫描该类所在的包下所有的类。
- `@Configuration`：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类

### 2.2 Spring Bean 相关

#### 2.2.1 @Autowired

自动导入对象到类中，被注入进的类同样要被 Spring 容器管理比如：Service 类注入到 Controller 类中。

**如何注入同一接口的不同实现类**

1. @Autowired 注解和@Qualifier(value="xxx")注解组合指定注入的具体类名称即可

```java
    @Autowired
    @Qualifier(value = "defaultUserService")
    private UserService userService1;
    @Autowired
    @Qualifier(value = "englishUserService")
    private UserService userService2;
```

2. 使用@Resource（name="xxx"）注解指定注入的特定实现类

```java
//    @Autowired
//    @Qualifier(value = "defaultUserService")
    @Resource(name = "defaultUserService")
    private UserService userService1;
//    @Autowired
//    @Qualifier(value = "englishUserService")
    @Resource(name = "englishUserService")
    private UserService userService2;
```

3. 使用@Autowired 时，属性名称使用实现类中交由spring container管理的bean名称，也可实现相同的功能。

```java
    @Autowired
//    @Qualifier(value = "defaultUserService")
//    @Resource(name = "defaultUserService")
    private UserService defaultUserService;
    @Autowired
//    @Qualifier(value = "englishUserService")
//    @Resource(name = "englishUserService")
    private UserService englishUserService;
```



#### 2.2.2 @Component,@Repository,@Service, @Controller

我们一般使用 `@Autowired` 注解让 Spring 容器帮我们自动装配 bean。要想把类标识成可用于 `@Autowired` 注解自动装配的 bean 的类,可以采用以下注解实现：

- `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面。@RestController

#### 2.2.3 @RestController

`@RestController`注解是`@Controller`和`@ResponseBody`的合集,表示这是个控制器 bean,并且是将函数的返回值直接填入 HTTP 响应体中,是 REST 风格的控制器。

单独使用 `@Controller` 不加 `@ResponseBody`的话一般是用在要返回一个视图的情况，这种情况属于比较传统的 Spring MVC 的应用，对应于前后端不分离的情况。`@Controller` +`@ResponseBody` 返回 JSON 或 XML 形式数据

#### 2.2.4 @Scope

声明 Spring Bean 的作用域，使用方法:

```java
@Bean
@Scope("singleton")
public Person personSingleton() {
    return new Person();
}Copy to clipboardErrorCopied
```

**四种常见的 Spring Bean 的作用域：**

- singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
- prototype : 每次请求都会创建一个新的 bean 实例。
- request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
- session : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。

#### 2.2.5 @Configuration

一般用来声明配置类，可以使用 `@Component`注解替代，不过使用`@Configuration`注解声明配置类更加语义化。

### 2.3 前后端传值

#### 2.3.1 @PathVariable 和 @RequestParam

`@PathVariable`用于获取路径参数，`@RequestParam`用于获取查询参数。

#### 2.3.2 @RequestBody

用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分并且**Content-Type 为 application/json** 格式的数据，接收到数据之后会自动将数据绑定到 Java 对象上去。系统会使用`HttpMessageConverter`或者自定义的`HttpMessageConverter`将请求的 body 中的 json 字符串转换为 java 对象。

### 2.4 读取配置信息

#### 2.4.1 @Value(常用)

使用 `@Value("${property}")` 读取比较简单的配置信息：

#### 2.4.1 @ConfigurationProperties(常用)

通过`@ConfigurationProperties`读取配置信息并与 bean 绑定。

### 2.5 字段校验

- `@NotEmpty` 被注释的字符串的不能为 null 也不能为空
- `@NotBlank` 被注释的字符串非 null，并且必须包含一个非空白字符
- `@Null` 被注释的元素必须为 null
- `@NotNull` 被注释的元素必须不为 null
- `@AssertTrue` 被注释的元素必须为 true
- `@AssertFalse` 被注释的元素必须为 false
- `@Pattern(regex=,flag=)`被注释的元素必须符合指定的正则表达式
- `@Email` 被注释的元素必须是 Email 格式。
- `@Min(value)`被注释的元素必须是一个数字，其值必须大于等于指定的最小值
- `@Max(value)`被注释的元素必须是一个数字，其值必须小于等于指定的最大值
- `@DecimalMin(value)`被注释的元素必须是一个数字，其值必须大于等于指定的最小值
- `@DecimalMax(value)` 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
- `@Size(max=, min=)`被注释的元素的大小必须在指定的范围内
- `@Digits(integer, fraction)`被注释的元素必须是一个数字，其值必须在可接受的范围内
- `@Past`被注释的元素必须是一个过去的日期
- `@Future` 被注释的元素必须是一个将来的日期

### 2.6  json 数据处理

#### 2.6.1 过滤 json 数据

**`@JsonIgnoreProperties` 作用在类上用于过滤掉特定字段不返回或者不解析。**

```java
//生成json时将userRoles属性过滤
@JsonIgnoreProperties({"userRoles"})
public class User {

    private String userName;
    private String fullName;
    private String password;
    private List<UserRole> userRoles = new ArrayList<>();
}
```

**`@JsonIgnore`一般用于类的属性上，作用和上面的`@JsonIgnoreProperties` 一样。**

```java

public class User {

    private String userName;
    private String fullName;
    private String password;
   //生成json时将userRoles属性过滤
    @JsonIgnore
    private List<UserRole> userRoles = new ArrayList<>();
}
```

#### 2.6.2 格式化 json 数据

`@JsonFormat`一般用来格式化 json 数据。

比如：

```java
@JsonFormat(shape=JsonFormat.Shape.STRING, pattern="yyyy-MM-dd'T'HH:mm:ss.SSS'Z'", timezone="GMT")
private Date date;
```

#### 2.6.3 扁平化对象

```java
Getter
@Setter
@ToString
public class Account {
    private Location location;
    private PersonInfo personInfo;

  @Getter
  @Setter
  @ToString
  public static class Location {
     private String provinceName;
     private String countyName;
  }
  @Getter
  @Setter
  @ToString
  public static class PersonInfo {
    private String userName;
    private String fullName;
  }
}
```

未扁平化之前：

```json
{
    "location": {
        "provinceName":"湖北",
        "countyName":"武汉"
    },
    "personInfo": {
        "userName": "coder1234",
        "fullName": "shaungkou"
    }
}
```

使用`@JsonUnwrapped` 扁平对象之后：

```java
@Getter
@Setter
@ToString
public class Account {
    @JsonUnwrapped
    private Location location;
    @JsonUnwrapped
    private PersonInfo personInfo;
    ......
}
```

```java
{
  "provinceName":"湖北",
  "countyName":"武汉",
  "userName": "coder1234",
  "fullName": "shaungkou"
}
```

