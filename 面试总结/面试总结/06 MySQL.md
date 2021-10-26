# MySQL

## 常见问题

### 1. 怎么优化一个慢查询

#### 1. 什么是慢查询

**MySQL慢查询就是在日志中记录运行比较慢的SQL语句**，这个功能需要开启才能用。

在MySQL的配置文件my.cnf中写上：

```cpp
long_query_time = 10
log-slow-queries = /var/lib/mysql/mysql-slow.log
```

`long_query_time`是指执行超过多久的SQL会被日志记录下来，这里是10 秒。
`log-slow-queries`设置把日志写在那里（例子中慢查询日志会写到文件/var/lib/mysql/mysql-slow.log中），为空的时候，系统会给慢查询日志赋予主机名，并加上slow.log。如果设置了参数log-long-format ，那么所有没有使用索引的查询也将被记录。

这是一个非常有用的日志。它对于性能的影响不大（假设所有查询都很快），并且强调了那些最需要注意的查询（丢失了索引或索引没有得到最佳应用）。

#### 2. 如何定位慢查询

 直接分析mysql慢查询日志 ,利用explain关键字可以模拟优化器执行SQL查询语句，来分析sql慢查询语句。

#### 3. 如何优化慢查询

1. 对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。
2. 避免索引失效