# MyBatis

1. 了解MyBatis底层实现原理吗
2. MyBatis有哪些动态标签
3. Mybatis的一级缓存，二级缓存了解吗

[MyBatis面试题（2020最新版）](https://thinkwon.blog.csdn.net/article/details/101292950)

[ORM框架 - MyBatis知识体系详解](https://www.pdai.tech/md/framework/orm-mybatis/mybatis-y-arch.html)

## 一、MyBatis 简介

### 1.1 MyBatis是什么

MyBatis 是一款优秀的持久层框架，一个半 ORM（对象关系映射）框架，它**支持定制化 SQL、存储过程以及高级映射**。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以**使用简单的 XML 或注解来配置和映射**原生类型、接口和 Java 的 POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

### 1.2 ORM是什么

ORM（Object Relational Mapping），对象关系映射，是一种为了**解决关系型数据库数据与简单Java对象（POJO）的映射关系的技术**。简单的说，ORM是通过使用描述对象和数据库之间映射的元数据，将程序中的对象自动持久化到关系型数据库中。

### 1.3 半自动ORM映射与全自动的区别？

`Hibernate`属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以**根据对象关系模型直接获取**，所以它是**全自动**的。

`Mybatis`在查询关联对象或关联集合对象时，需要**手动编写sql来完成**，所以，称之为**半自动ORM映射**工具。

### 1.4 解决了什么问题

1. 数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库连接池可解决此问题。

   解决：在mybatis-config.xml中配置数据链接池，使用连接池管理数据库连接。

2. Sql语句写在代码中造成代码不易维护，实际应用sql变化的可能较大，sql变动需要改变java代码。

   解决：将Sql语句配置在XXXXmapper.xml文件中与java代码分离。

3. 向sql语句传参数麻烦，因为sql语句的where条件不一定，可能多也可能少，占位符需要和参数一一对应。

   解决： Mybatis自动将java对象映射至sql语句。

4. 对结果集解析麻烦，sql变化导致解析代码变化，且解析前需要遍历，如果能将数据库记录封装成pojo对象解析比较方便。

   解决：Mybatis自动将sql执行结果映射至java对象。
   

## 二、MyBatis解析和运行原理

### 1.1 MyBatis编程步骤

1. 创建SqlSessionFactory
2. 通过SqlSessionFactory创建SqlSession
3. 通过sqlsession执行数据库操作
4. 调用session.commit()提交事务
5. 调用session.close()关闭会话

### 1.2 MyBatis工作原理

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211122155853.png)

1）**读取 MyBatis 配置文件**：mybatis-config.xml 为 MyBatis 的全局配置文件，配置了 MyBatis 的运行环境等信息，例如数据库连接信息。

2）**加载映射文件**：映射文件即 SQL 映射文件，该文件中配置了操作数据库的 SQL 语句，需要在 MyBatis 配置文件 mybatis-config.xml 中加载。mybatis-config.xml 文件可以加载多个映射文件，每个文件对应数据库中的一张表。

3）**构造会话工厂**：通过 MyBatis 的环境等配置信息构建会话工厂 SqlSessionFactory。

4）**创建会话对象**：由会话工厂创建 SqlSession 对象，该对象中包含了执行 SQL 语句的所有方法。

5）**Executor 执行器**：MyBatis 底层定义了一个 Executor 接口来操作数据库，它将根据 SqlSession 传递的参数动态地生成需要执行的 SQL 语句，同时负责查询缓存的维护。

6）**MappedStatement** 对象：在 Executor 接口的执行方法中有一个 MappedStatement 类型的参数，该参数是对映射信息的封装，用于存储要映射的 SQL 语句的 id、参数等信息。

7）**输入参数映射**：输入参数类型可以是 Map、List 等集合类型，也可以是基本数据类型和 POJO 类型。输入参数映射过程类似于 JDBC 对 preparedStatement 对象设置参数的过程。

8）**输出结果映射**：输出结果类型可以是 Map、 List 等集合类型，也可以是基本数据类型和 POJO 类型。输出结果映射过程类似于 JDBC 对结果集的解析过程。

### 1.3 MyBatis架构设计

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211122160044.png)

Mybatis的功能架构分为三层：

1. **API接口层**：提供给外部使用的接口API，开发人员通过这些本地API来操纵数据库。接口层一接收到调用请求就会调用数据处理层来完成具体的数据处理。
2. **数据处理层**：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等。它主要的目的是根据调用的请求完成一次数据库操作。
3. **基础支撑层**：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理，这些都是共用的东西，将他们抽取出来作为最基础的组件。为上层的数据处理层提供最基础的支撑。

### 1.4 主要构件及其相互关系

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20211122170030.png)

主要的核心部件解释如下：

- `SqlSession` 作为MyBatis工作的主要顶层API，表示和数据库交互的会话，完成必要数据库增删改查功能
- `Executor` MyBatis执行器，是MyBatis 调度的核心，负责SQL语句的生成和查询缓存的维护
- `StatementHandler` 封装了JDBC Statement操作，负责对JDBC statement 的操作，如设置参数、将Statement结果集转换成List集合。
- `ParameterHandler` 负责对用户传递的参数转换成JDBC Statement 所需要的参数，
- `ResultSetHandler` 负责将JDBC返回的ResultSet结果集对象转换成List类型的集合；
- `TypeHandler` 负责java数据类型和jdbc数据类型之间的映射和转换
- `MappedStatement` MappedStatement维护了一条`<select|update|delete|insert>`节点的封装，
- `SqlSource` 负责根据用户传递的parameterObject，动态地生成SQL语句，将信息封装到BoundSql对象中，并返回
- `BoundSql` 表示动态生成的SQL语句以及相应的参数信息
- `Configuration` MyBatis所有的配置信息都维持在Configuration对象之中。

### 1.5  MyBatis 延迟加载原理

**延迟加载的好处**

先从单表查询、需要时再从关联表去关联查询，大大提高数据库性能，因为查询单表要比关联查询多张表速度要快。

**如何启动延迟加载**

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载`lazyLoadingEnabled=true|false`。

**延迟加载原理**

使用**CGLIB创建目标对象的代理对象**，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器intercept()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

## 三、映射器

### 3.1 #{}与${}的区别

- \#{}是占位符，预编译处理；${}是拼接符，字符串替换，没有预编译处理。

- Mybatis在处理#{}时，#{}传入参数是以字符串传入，会将SQL中的#{}替换为?号，调用PreparedStatement的set方法来赋值。
- \#{} 可以有效的防止SQL注入，提高系统安全性；${} 不能防止SQL 注入

## 四、动态SQL

### 4.1 Mybatis动态sql是做什么的？

Mybatis动态sql可以让我们在Xml映射文件内，以标签的形式编写动态sql，完成逻辑判断和动态拼接sql的功能

### 4.2 都有哪些动态sql

Mybatis提供了9种动态sql标签trim|where|set|foreach|if|choose|when|otherwise|bind。

### 4.3 动态sql的执行原理

使用OGNL从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能。

### 4.4 动态sql使用

#### 4.4.1 If

使用动态 SQL 最常见情景是根据条件包含 where 子句的一部分。比如：

```xml
<select id="findActiveBlogWithTitleLike" resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

#### 4.4.2 choose、when、otherwise

有时候，我们不想使用所有的条件，而只是想从多个条件中选择一个使用。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

还是上面的例子，但是策略变为：传入了 “title” 就按 “title” 查找，传入了 “author” 就按 “author” 查找的情形。若两者都没有传入，就返回标记为 featured 的 BLOG

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

#### 4.4.3 trim、where、set

前面几个例子已经合宜地解决了一个臭名昭著的动态 SQL 问题。现在回到之前的 “if” 示例，这次我们将 “state = ‘ACTIVE’” 设置成动态条件，看看会发生什么。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE
  <if test="state != null">
    state = #{state}
  </if>
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>   
```

如果没有匹配的条件会怎么样？最终这条 SQL 会变成这样：

```java
SELECT * FROM BLOG WHERE   
```

这会导致查询失败。如果匹配的只是第二个条件又会怎样？这条 SQL 会是这样:

```java
SELECT * FROM BLOG
WHERE
AND title like ‘someTitle’
```

这个查询也会失败。这个问题不能简单地用条件元素来解决。这个问题是如此的难以解决，以至于解决过的人不会再想碰到这种问题。

MyBatis 有一个简单且适合大多数场景的解决办法。而在其他场景中，可以对其进行自定义以符合需求。而这，只需要一处简单的改动：

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

**where 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，where 元素也会将它们去除。**

如果 where 元素与你期望的不太一样，你也可以通过**自定义 trim 元素来定制 where 元素的功能**。比如，和 where 元素等价的自定义 trim 元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim> 
```

prefixOverrides 属性会忽略通过管道符分隔的文本序列（注意此例中的空格是必要的）。上述例子会移除所有 prefixOverrides 属性中指定的内容，并且插入 prefix 属性中指定的内容。

用于动态更新语句的类似解决方案叫做 set。set 元素可以用于动态包含需要更新的列，忽略其它不更新的列。比如：

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

这个例子中，set 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）。

来看看与 set 元素等价的自定义 trim 元素吧：

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

#### 4.4.4 foreach

动态 SQL 的另一个常见使用场景是对集合进行遍历（尤其是在构建 IN 条件语句的时候）。比如：

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT * FROM POST P WHERE ID in
  <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
      #{item}
  </foreach>
</select> 
```

foreach 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符，看它多智能！

> 提示 你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象作为集合参数传递给 foreach。当使用可迭代对象或者数组时，index 是当前迭代的序号，item 的值是本次迭代获取到的元素。当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。

#### 4.4.5 script

要在带注解的映射器接口类中使用动态 SQL，可以使用 script 元素。比如:

```java
    @Update({"<script>",
      "update Author",
      "  <set>",
      "    <if test='username != null'>username=#{username},</if>",
      "    <if test='password != null'>password=#{password},</if>",
      "    <if test='email != null'>email=#{email},</if>",
      "    <if test='bio != null'>bio=#{bio}</if>",
      "  </set>",
      "where id=#{id}",
      "</script>"})
    void updateAuthorValues(Author author);
```

#### 4.4.6 bind

bind 元素允许你在 OGNL 表达式以外创建一个变量，并将其绑定到当前的上下文。比如：

```xml
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
  SELECT * FROM BLOG WHERE title LIKE #{pattern}
</select>
```

## 五、缓存

### 5.1 MyBatis一级缓存

基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。

### 5.2 MyBatis二级缓存

二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap 存储；

不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置<cache/> 

### 5.3 缓存机制特点

对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear。