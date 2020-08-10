EXAMPLE.md
zhihu-database
====

### 简介
这个项目的创建来源于 [T15355](http://ph.in.zhihu.com/T15355) 

主要是为了让数据库的变更变的有流程可依，变更纪录可以回溯（如果放在各个项目中，这个维护成本就会很高）

##### 数据库上线流程

 `fork zhihu-database` -> `本地定义数据库schema` -> `Pull Request` -> `review`  -> `发布数据库` -> `accept PR`


##### 数据变更流程

`fork zhihu-database` -> `修改数据库 schema` -> `Pull Request` -> `review` -> `变更数据库结构` -> `accept PR`

这样所有数据库变更可以保证有人 review，而且变更记录可以方便查询到。另外需要线上数据库配置的人，可以方便的从这个 repo 来拿

### 使用说明

每个项目有一个单独的目录进行维护，每个目录下有两个文件

* schema.sql  创建表的语句。
* README.md 维护数据库更详细的信息，包括数据库的设计思路，表的设计思路，以及使用数据库的逻辑（Query Example）和对应的解释说明
* history.md 保存变更历史，使用 commit 可能不会那么灵活，所以添加了这个文件

##### 规范
* 命名规范
	* 命名应使用富有意义的英文词汇，多个单词组成的，中间以下划线分割。
	* 命名全部采用小写，并且名称前后不能加引号
	* 同一个模块的表尽可能使用相同的前缀，表名尽可能表达含义，例如：member_group和member_notice（服务化以后这里可能不再适用，酌情处理吧）
	* 字段命名应尽可能使用表达实际含义的英文单词或缩写，如，公司 ID，不要使用 corporation_id, 而用 corp_id 即可
	* 布尔值类型的字段命名为 `is+` 描述。如 member 表上表示是否为 enabled 的会员的字段命名为 `is_enabled`
	* 出现引用别的表数据情况的时候，`外键处` 的名称要使用 `column_name_id`, 比如引用 `member`，则该列名称为`member_id`
* 建表的规范
	* 所有的表必须有主键，尽量为自增类型，推荐使用整型(INT/BIGINT)
	* 最小列原则，比如类型字段一般 `TINYINT` 就可以满足; 存储 `hash` 字段一般 `CHAR(32)` 就可以满足。不要滥用可变长度的字段类型，但同时也不要强制使用固定长度而忽略存储空间的浪费问题。
	* 优先使用内置类型，比如存储IP地址，比起字符串来推荐使用整型
	* 一般的整型存储，最好加上 `UNSIGNED`, 毕竟我们目前使用 `有符号` 的还是比较少的。
	* 不推荐使用 `NULL`（有业务意义时，使用默认值优于 NULL。比如用户的简介，可以用 '' 来代替 NULL，注意的是，TEXT类型不能设置默认值，需慎重使用）。 主要是出于业务查询方便的考虑。
	* 所存储的数据类型务必与 `column` 的类型一致，否则可能出现查询结果错误的情况。（常见的就是用`char`存储`int`） 
	* 每个 `column` 后面必须加上 `comment` 解释其意义，如果特别长，可以放在README.md
* 索引规范（引用一个评分系统）
	* 命名应体现该索引的类型，比如唯一键约束可以命名为 unq_mid_tid，普通索引为 idx_mid_tid
	* 对于有组合主键需求的场景，可以建一个 UNIQUE 的联合索引，再单独使用一个自增长列来作为主键。因为联合主键插入随机，不是按顺序连续插入，对数据插入性能比较有影响
	* 选择 `联合索引` 还是 `单独索引` 要考虑自己的业务模式
	* 对于出现在 ORDER BY 和 GROUP BY 中的列，也应该有索引
	* 强烈不推荐建立过多的索引，禁止冗余的索引、不使用的索引需要及时删除。推荐扩展现有索引，而不是建立新的索引。索引较多，可能代表着逻辑太复杂，最好能通过别的方式来解决。
	* 下面是一个针对索引的评分系统（Three Star Index）, 至少要两星。
		* Rows referenced by your query are grouped together in the index
		* Rows referenced by your query are ordered in the index the way you want them
		* The index contains all columns referenced by you query (Covering index)

* SQL 语句规范

	* 关键字 均使用大写 `SELECT` `FROM` `WHERE` `LIMIT` `UPDATE` `ORDER BY` `DESC`
	* 字段名称 以及 查询条件使用小写 `user`
	* 查询值如果是字符串则使用 `''` 单引号扩起来
	* 查询值如果是数值则禁止使用 `''` 单引号
	* 禁用 `SELECT * `
	* `COUNT` 查询的时候，只允许使用 `*`
	* 做范围查询的时候，强制使用 `LIMIT` 参数，后面的值酌情而定。
	* 不推荐使用 `DELETE`
	* 线上禁止使用 `JOIN` 操作
	* 同查询列多值的时候使用`IN` 不要用 `OR`

### EXAMPLE.md
每个目录下的 `README.md`，可以按照该文件的格式进行内容填充
