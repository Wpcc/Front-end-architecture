# MySQL



## 数据类型

| MySQL 类型      | 用途                       | Java 常用类型               |
| --------------- | -------------------------- | --------------------------- |
| `TINYINT`       | 很小的整数、布尔值         | `Byte` / `Boolean`          |
| `INT`           | 普通整数，如年龄、数量     | `Integer`                   |
| `BIGINT`        | 大整数，如用户 id、订单 id | `Long`                      |
| `DECIMAL(10,2)` | 精确小数，如金额           | `BigDecimal`                |
| `DOUBLE`        | 近似小数，如统计值         | `Double`                    |
| `VARCHAR(50)`   | 可变长度短文本，如用户名   | `String`                    |
| `TEXT`          | 较长文本，如文章内容       | `String`                    |
| `DATE`          | 日期，如生日               | `LocalDate`                 |
| `DATETIME`      | 日期和时间，如创建时间     | `LocalDateTime`             |
| `TIMESTAMP`     | 时间戳，常用于记录时间     | `Instant` / `LocalDateTime` |
| `BOOLEAN`       | 真/假                      | `Boolean`                   |
| `JSON`          | JSON 文档数据              | `String` / `JsonNode`       |
| `BLOB`          | 二进制文件                 | `byte[]`                    |

用户表：

```java
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL,
    age INT,
    balance DECIMAL(10, 2) NOT NULL DEFAULT 0.00,
    birthday DATE,
    created_at DATETIME NOT NULL
);
```

对应Java概念大致是：

```java
public class User {
    private Long id;
    private String username;
    private Integer age;
    private BigDecimal balance;
    private LocalDate birthday;
    private LocalDateTime createdAt;
}
```

几个重要规则：

- 金额不要用 `DOUBLE`，要用 `DECIMAL` + Java 的 `BigDecimal`。
  `DOUBLE` 是近似值，可能出现 `0.1 + 0.2 != 0.3` 的精度问题。
- 数据库中的 `NULL` 对应 Java 中的 `null`。
  所以可能为空的字段通常用包装类，如 `Long`、`Integer`，不要用 `long`、`int`。
- `VARCHAR(50)` 是最多 50 个字符的可变长度文本，适合用户名、邮箱等。
- `CHAR(50)` 是固定长度文本，较少用；短且长度固定的数据才考虑，例如固定格式编码。
- `AUTO_INCREMENT` 不是类型，而是规则：插入数据时 MySQL 自动生成递增 id。
- `PRIMARY KEY`、`NOT NULL`、`DEFAULT` 也不是类型，而是字段约束：
  - `PRIMARY KEY`：唯一标识一行数据；
  - `NOT NULL`：不允许为空；
  - `DEFAULT`：未传值时使用默认值。

还有一个容易误会的点：

```
INT(11)
```

并不表示“最多 11 位数字”。`INT` 的范围是固定的；`(11)` 曾是显示宽度，在现代 MySQL 中基本没有实际意义。通常直接写：

```
age INT
```

就够了。



## 关系模型

### 主键

### 外键

```java
ALTER TABLE students
ADD CONSTRAINT fk_class_id // 添加一个外键约束，这个名叫fk_class_id
FOREIGN KEY (class_id)  // 在students对应的id叫class_id
REFERENCES classes (id);  // 在classes中对应的id叫id

```



### 索引

按照某个字段的有序排序，主要用来提高搜索效率。

```java
// 单列索引
ALTER TABLE students
ADD INDEX idx_score (score); // 对score字段创建一个idx_score的索引

// 多列索引-联合索引
ALTER TABLE students
ADD INDEX idx_name_score (name, score); // 对name、score字段创建一个idx_name_score的索引

// 唯一索引（字段不能重复）
ALTER TABLE students
ADD UNIQUE INDEX uni_name (name);

// 添加唯一约束而不创建索引
ALTER TABLE students
ADD CONSTRAINT uni_name UNIQUE (name);
```



联合索引的**最左前缀**原则指：一个联合索引必须从定义时最左边的字段开始连续使用，才能充分生效。

例如有索引：

```java
// 在orders表上，创建一个联合索引
CREATE INDEX idx_user_status_time
ON orders(user_id, status, created_at);
```

可以把它理解为按下面顺序排序的目录：

```java
先按 user_id
再在同一个 user_id 下按 status
最后再按 created_at
```



#### 索引创建原则

- 数据量较大，且查询比较频繁的表（10万+数据） --- 重要
- 常作为查询条件、排序、分组字段 --- 重要
- 字段内容区分度高
- 内容较长，使用前缀索引
- 尽量联合索引 --- 重要
- 要控制索引的数量 --- 重要
- 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它



#### 索引失效

- 违反最左前缀法则
- 范围查询最右边的列，不能使用索引
- 不要再索引列上进行运算操作，索引失效
- 字符串不加单引号，造成索引失效。（类型转换）
- 以%开头的Like模糊查询，索引失效



#### 数据结构

**索引使用的数据结构是B+树。**



二叉树：每个节点需要有两个子节点

搜索二叉树：在二叉树的基础上按照左小右大的原则

红黑树：降低数的层级

B树：多叉路衡查找树，每个节点有多个数据

B+树：只有叶子节点保留数据，并且数据使用双向链表（指针）进行连接。



#### 回表查询

聚簇索引（聚集索引）：数据和索引放在一块，B+树的叶子节点保存了整行数据（一个表中有且只有一个）

- 人话就是叶子节点保存整行数据

非聚簇索引（二级索引）：数据与索取分开存储，B+树的叶子节点保存对应的主键（一个表中，可以有多个）

- 人话就是保存聚簇索引的key

回表查询：

那么通过非聚簇索引查找到的index，最终需要获取数据，就需要通过聚簇索引去查询一遍，这种查询就叫做回表。



#### MySQL超大分页

比如需要获取100万数据之后的10条数据：

- **覆盖索引 + 子查询**：前面的记录仍要扫，但尽量只读索引，最后才取 20 条完整数据。

- **游标分页**：利用上页位置定位，避免大 `OFFSET` 带来的大量跳过操作。

  

覆盖索引：使用索引，一次查询获取数据的查询（也就是一次性查到数据，不回表，如果是聚簇索引就只查index和当前key）

```sql
// 覆盖索引
select * from user where id = 1;

select id,name from user where name = "arm";

// 非覆盖索引，需要回表
select id,name,gender from user where name = "arm";
```

**游标分页适合连续翻页；必须支持任意跳页时，通常仍要使用偏移量分页，或者额外维护页码到游标的对应关系。**



## sql优化

- 表的设计优化

  - 阿里开发手册《嵩山版》

- 索引优化

- SQL语句优化

  - SELECT语句务必指名字段名称（避免直接使用select *）
  - SQL语句要避免造成索引失效的写法
  - 尽量使用union all代理union，union会过滤重复数据，效率低
  - 避免在where子句中对字段进行表达式操作
  - join优化，以小表驱动，小表在循环外面，大表在里面

- 主从复制、读写分离

- 分库分表

  

### 主从同步

MySQL主从同步原理？

MySQL主从复制的核心就是二进制日志binlog（DDL（数据定义语言））和DML（数据操纵语言）语句。

- 主库在事务提交时，会把数据变更记录在二进制日志文件Binlog中（包含执行位置）
- 从库读取主库的二进制日志文件Binlog，写入到从库的中继日志 Relay Log
- 从库重做中继日志中的事件，将改变反映它自己的数据



## 分库分表

单表1000万或超过20G的时候，需要进行分库分表，主要IO读写缓慢，CPU压力较大。

具体拆分策略：

- 垂直：
  - 垂直分库：将不同的表，按业务拆分到不同的库，高并发下提高磁盘IO和网络连接数
  - 垂直分表：将字段根据冷热数据分离，多表互不影响

- 水平
  - 水平分库：将一个库的数据拆分到多个库中，解决海量数据存储和高并发问题
  - 水平分表，将一个表的数据拆分到多个表中，解决单表存储和性能问题

分库之后的问题：

- 分布式事务一致性问题
- 跨节点关联查询
- 跨节点分页、排序函数
- 主键避重

解决方法：

使用中间件（shading-sphere、mycat）



## 查询数据



## 修改数据



## 中间表和临时表

### 中间表

**用来表示表与表之间的关系**

中间表通常是**长期存在的正式业务表**，最常见于多对多关系。

例如：一个学生可以选多门课，一门课也能被多个学生选。

```
students        student_courses        courses
--------        ---------------        -------
id              student_id             id
name            course_id              name
                selected_at
```

其中 `student_courses` 就是中间表。

```
CREATE TABLE student_courses (
    student_id BIGINT NOT NULL,
    course_id BIGINT NOT NULL,
    selected_at DATETIME NOT NULL,
    PRIMARY KEY (student_id, course_id)
);
```

它不仅连接两个表，还可以记录关系本身的信息，例如选课时间、成绩、状态。

订单系统中更常见的例子是订单项表：

```
orders          order_items           products
------          -----------           --------
id              order_id              id
user_id         product_id            name
                quantity
                product_price
```

`order_items` 连接订单和商品，但它还保存数量、成交价等信息，所以更准确地说，它是“关联实体”或“订单明细表”，不只是纯中间表。

### 临时表

**临时保存计算或处理过程中的数据**

临时表通常只在当前数据库连接期间存在，连接关闭后自动删除。

```
CREATE TEMPORARY TABLE temp_sales AS
SELECT
    product_id,
    SUM(quantity) AS total_quantity
FROM order_items
GROUP BY product_id;
```

随后可以查询它：

```
SELECT * FROM temp_sales;
```

关闭当前 Navicat 查询连接后，`temp_sales` 会自动消失。

临时表常用于：

- 复杂统计的中间结果；
- 批量导入前的数据清洗；
- 多步骤数据处理；
- 临时避免重复计算。

------

| 对比项               | 中间表                      | 临时表                 |
| -------------------- | --------------------------- | ---------------------- |
| 是否长期保存         | 是                          | 否，连接关闭后通常删除 |
| 是否属于正式业务模型 | 是                          | 通常不是               |
| 主要用途             | 表与表之间建立关系          | 临时计算、数据处理     |
| 例子                 | `order_items`、`user_roles` | `temp_sales`           |



## modify

`MODIFY COLUMN` 是 MySQL 中“修改已有字段定义”的语法。

你这段：

```
ALTER TABLE orders
    MODIFY COLUMN product_name VARCHAR(128) NOT NULL,
    MODIFY COLUMN product_price DECIMAL(10, 2) NOT NULL;
```

含义是：

```
orders 表中已经有 product_name、product_price 字段，
现在修改它们的类型/长度/是否允许为空等规则。
```

这里的目标是：历史订单的数据已经补齐后，把字段从“允许为空”改成“不允许为空”。

流程通常是：

```
1. 新增字段，允许 NULL
2. 补齐旧数据
3. MODIFY COLUMN 改为 NOT NULL
```

对比：

```
ADD COLUMN       -- 新增一个字段
MODIFY COLUMN    -- 修改已有字段定义，不改字段名
CHANGE COLUMN    -- 可以同时改字段名和字段定义
DROP COLUMN      -- 删除字段
```

例如：

```
ALTER TABLE users
    MODIFY COLUMN username VARCHAR(100) NOT NULL;
```

表示把已有的 `username` 字段改成最大 100 字符且不能为空。

注意：执行 `MODIFY COLUMN ... NOT NULL` 前，如果表中仍有 `NULL` 数据，SQL 会失败。可以先检查：

```
SELECT *
FROM orders
WHERE product_name IS NULL
   OR product_price IS NULL;
```

另外，`MODIFY` 时要写完整的目标字段定义；如果原字段有 `DEFAULT`、`COMMENT` 等设置，修改时也应一并写上，避免意外丢失这些属性。



## join

**`JOIN` 就是把两张表按关联条件连接起来，在一个查询里拿到两边的数据。**

最常见的是下面两种连接：

| 写法                          | 含义                                          |
| ----------------------------- | --------------------------------------------- |
| `INNER JOIN`，可简写为 `JOIN` | 只保留两边匹配成功的记录                      |
| `LEFT JOIN`                   | 保留左表所有记录，右表匹配不到的字段填 `NULL` |

例如，想查出**所有用户，包括没下过订单的用户**：

```
SELECT
    u.name AS 用户名,
    o.id AS 订单编号,
    o.amount AS 金额
FROM users AS u
LEFT JOIN orders AS o ON u.id = o.user_id;
```

结果：

| 用户名 | 订单编号 | 金额 |
| ------ | -------- | ---- |
| 张三   | 101      | 100  |
| 张三   | 102      | 200  |
| 李四   | 103      | 80   |
| 王五   | NULL     | NULL |

这里的“左表”就是 `LEFT JOIN` 左边的 `users`。因为王五没有订单，订单字段显示为 `NULL`，但王五这条用户记录仍然保留。

你可以这样记：**`JOIN` 用来关联数据，`ON` 指定如何匹配；普通 `JOIN` 只留匹配成功的，`LEFT JOIN` 保留左表全部记录。**



## 日志文件

MySQL中存在缓存池（内存）和数据页（磁盘），为了保存数据的一致性和持久化，有两份日志。

redo log：记录修改的内存结构，当服务宕机时，知道哪些数据还没有从缓存池同步到数据页中。日志文件也是从内存同步到磁盘，比较小所以容易同步

undo log：记录修改的sql语句，通常是相反的逻辑语句，以便回滚和MVCC（多版本控制）。



## 锁

**排他锁（Exclusive Lock，简称 X 锁）就是：事务修改数据时，阻止其他事务同时以冲突的方式操作这份数据。** 也叫“独占锁”或“写锁”。这期间期间允许其他事务读取



**共享锁（Shared Lock，简称 S 锁）就是：允许多个事务一起锁定读取同一份数据，但阻止其他事务修改它。** 也叫“读锁”。





## MVCC

主要由下面组成：

隐藏字段：

- trx_id(事务id)：记录每一次操作的事务id，是自增的
- roll_pointer(回滚指针)，指向上一个版本的事务版本记录地址

undo log：

- 回滚日志，存储老版本数据
- 版本链：多个事务并行操作某一行记录，记录不同事务修改数据的版本，通过roll_pointer指针形成一个链表

readView：解决一个事务查询选择版本的问题

- 根据readView的匹配规则和当前的一些事务id判断该访问那个版本的数据
- 不同的隔离级别快照读是不一样的，最终的访问结果不一样
  - RC：每一次执行快照读生成ReadView（RC 每次查询刷新视图）
  - RR：仅在事务中第一次执行快照时生成ReadView，后续复用（RR 同一事务复用视图）



### RC和RR区别

**RC 和 RR 的核心区别是：RC 每次快照读都生成新的 Read View；RR 在同一个事务里通常复用首次快照读生成的 Read View。**

它们是两个事务隔离级别：

- **RC（Read Committed）**：读已提交。
- **RR（Repeatable Read）**：可重复读。

假设账户余额最初是 **100 元**，按下面顺序执行：

| 执行顺序                 | RC 下的结果 | RR 下的结果 |
| ------------------------ | ----------- | ----------- |
| A 开启事务               | —           | —           |
| A 第一次普通查询余额     | 100         | 100         |
| B 把余额改为 200，并提交 | —           | —           |
| A 再次普通查询余额       | **200**     | **100**     |

**RC 为什么读到 200？**

第二次查询会创建新的 Read View。此时 B 已经提交，所以 B 修改的 200 对这次读取可见。

```
第一次查询 → Read View 1 → 100
B 提交修改
第二次查询 → Read View 2 → 200
```

所以 RC 能避免脏读，但可能出现**不可重复读**。

**RR 为什么仍读到 100？**

第二次查询复用第一次的 Read View。B 的修改对这个旧读视图不可见，所以通过 Undo 找到旧版本 100。

```
第一次查询 → Read View 1 → 100
B 提交修改
第二次查询 → 仍用 Read View 1 → 旧版本 100
```

**这里有两个容易混淆的地方：**

1. **RR 通常在首次快照读时创建 Read View，不是在 `BEGIN` 时。** 如果 A 执行 `BEGIN` 后还没查询，B 就已提交，那么 A 第一次查询能看到 B 的修改。
2. **这个区别针对普通快照读。** `SELECT ... FOR UPDATE` 等锁定读不按这个旧快照读取；而且事务始终可以看到自己的修改，不能理解成“RR 里任何查询结果都不变”。



## JDBC

JDBC是Java DataBase Connectivity的缩写，它是Java程序访问数据库的标准接口。



**安装驱动：**

```java
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
    <scope>runtime</scope>
</dependency>
```



**Connection：**

Connection代表一个JDBC连接，它相当于Java程序到数据库的连接（通常是TCP连接）。

```java
// JDBC连接的URL, 不同数据库有不同的格式:
String JDBC_URL = "jdbc:mysql://localhost:3306/test";
String JDBC_USER = "root";
String JDBC_PASSWORD = "password";
// 获取连接:
Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD);
// TODO: 访问数据库...
// 关闭连接:
conn.close();
```



**JDBC查询：**

第一步，通过`Connection`提供的`createStatement()`方法创建一个`Statement`对象，用于执行一个查询；

第二步，执行`Statement`对象提供的`executeQuery("SELECT * FROM students")`并传入SQL语句，执行查询并获得返回的结果集，使用`ResultSet`来引用这个结果集；

第三步，反复调用`ResultSet`的`next()`方法并读取每一行结果。

```java
try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
    try (Statement stmt = conn.createStatement()) {
        try (ResultSet rs = stmt.executeQuery("SELECT id, grade, name, gender FROM students WHERE gender=1")) {
            while (rs.next()) {
                long id = rs.getLong(1); // 注意：索引从1开始
                long grade = rs.getLong(2);
                String name = rs.getString(3);
                int gender = rs.getInt(4);
            }
        }
    }
}
```



**避免sql注入：**

PreparedStatement创建占位符数据

```java
User login(String name, String pass) {
    ...
    String sql = "SELECT * FROM user WHERE login=? AND pass=?";
    PreparedStatement ps = conn.prepareStatement(sql);
    ps.setObject(1, name);
    ps.setObject(2, pass);
    ...
}

```

```java
// 完整案列：
try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
    try (PreparedStatement ps = conn.prepareStatement("SELECT id, grade, name, gender FROM students WHERE gender=? AND grade=?")) {
        ps.setObject(1, "M"); // 注意：索引从1开始
        ps.setObject(2, 3);
        try (ResultSet rs = ps.executeQuery()) {
            while (rs.next()) {
                long id = rs.getLong("id");
                long grade = rs.getLong("grade");
                String name = rs.getString("name");
                String gender = rs.getString("gender");
            }
        }
    }
}

```



**`try-with-resources` 是 Java 自动关闭资源的语法。**

```java
try (Connection connection = DriverManager.getConnection(url, username, password);
     PreparedStatement statement = connection.prepareStatement(sql);
     ResultSet resultSet = statement.executeQuery()) {

    // 使用数据库资源
}
```

代码离开这个 `try` 块时，无论是正常结束还是中途抛异常，Java 都会自动调用它们的 `close()`。

关闭顺序是**声明顺序的反方向**：

```
ResultSet → PreparedStatement → Connection
```



## Mybatis

解决JDBC频繁创建数据库连接、处理数据库数据转换问题



**1、声明前端传递参数，使用record**

```java
// 通过构造函数进行数据校验
package com.wpcc.userorderservice.order.mapper;

import java.util.Objects;

public record OrderPageRequest(
    int page,
    int size,
    OrderSortField sortField,
    SortDirection direction) {
  public OrderPageRequest {
    if (page < 1) {
      throw new IllegalArgumentException("page 必须大于等于 1");
    }

    if (size < 1 || size > 100) {
      throw new IllegalArgumentException("size 必须在 1 到 100 之间");
    }

    Objects.requireNonNull(sortField, "sortField 不能为空");
    Objects.requireNonNull(direction, "direction 不能为空");
  }

  public int offset() {
    return (page - 1) * size;
  }
}

```



**2、对于`order by`、和排序方式使用枚举避免数据注入**

```java
// 枚举 order
package com.wpcc.userorderservice.order.mapper;

public enum OrderSortField {
  ID,
  TOTAL_AMOUNT,
  CREATED_AT
}

// 枚举 direction
package com.wpcc.userorderservice.order.mapper;

public enum SortDirection {
  ASC,
  DESC
}
```



**3、声明返回数据对象类**

```java
package com.wpcc.userorderservice.order.mapper;

import java.math.BigDecimal;

public record DatabaseOrder(
    Long id,
    Long userId,
    BigDecimal totalAmount,
    OrderStatus status) {

}

```



**4、使用动态sql进行mapper查询**

```java
package com.wpcc.userorderservice.order.mapper;

import java.util.List;
import java.util.Optional;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;

@Mapper
public interface OrderMapper {
  @Select("""
      SELECT id,user_id,total_amount,status
      FROM orders
      WHERE id = #{id}
      """)
  Optional<DatabaseOrder> findById(@Param("id") long id);

  @Select("""
      SELECT id,order_id,product_id,product_name,product_price,quantity,subtotal_amount
      FROM order_items
      WHERE order_id = #{orderId}
      ORDER BY id
      """)
  List<DatabaseOrderItem> findItemsByOrderId(@Param("orderId") long orderId);

  @Select("""
      <script>
        SELECT id,user_id,total_amount,status
        FROM orders
        <where>
          <if test="userId != null">
            AND user_id = #{userId}
          </if>
          <if test="status != null">
            AND status = #{status}
          </if>
        </where>
        ORDER BY id DESC
      </script>
      """)
  List<DatabaseOrder> findByCondition(@Param("userId") Long userId, @Param("status") OrderStatus status);

  @Select("""
      <script>
        SELECT id,user_id,total_amount,status
        FROM orders
        ORDER BY
        <choose>
          <when test="sortField.name() == 'ID'">
            id
          </when>
          <when test="sortField.name() == 'TOTAL_AMOUNT'">
            total_amount
          </when>
          <when test="sortField.name() == 'CREATED_AT'">
            created_at
          </when>
          <otherwise>
            id
          </otherwise>
        </choose>
        <choose>
          <when test="direction.name() == 'ASC'">
              ASC
          </when>
          <otherwise>
              DESC
          </otherwise>
        </choose>
        LIMIT #{limit} OFFSET #{offset}
      </script>
      """)
  List<DatabaseOrder> findPage(
      @Param("limit") int limit,
      @Param("offset") int offset,
      @Param("sortField") OrderSortField sortField,
      @Param("direction") SortDirection direction);
}

```



## 动态sql

```java
// 表面是动态语句
<script></script>
// 条件
<where></where> 
// if判断
<if test=""></if>
// 选择判断
<choose>
	<when test=""></when>
    <otherwise></otherwise>
</choose>
```



## 事务操作

为了解决并发问题，将一系列操作合并成一套操作，要么都成功要么都失败。

```java
// 使用Transactional注解，通常出现在service层

```

ACID 最容易混淆的是：

- 原子性（Atomicity）：关注“操作能否拆开”。例如“扣库存 + 创建订单”要么都成功，要么都撤销。
- 一致性（Consitency）：关注“结果是否仍符合规则”。例如库存不能小于 0、订单项必须属于已有订单。事务不能自动保证规则正确，SQL 和业务代码也必须写对。
- 隔离性（Isolcation）：关注并发事务是否互相干扰。
- 持久性（Durability）：关注提交后的数据不会因应用或服务器异常退出而丢失；数据库重启后，已提交的订单和库存变更仍存在。

另外，`@Transactional` 要生效通常需要：它所在类是 Spring Bean、方法通过 Spring 代理从外部调用、方法是 `public`，并且应用配置了事务管理器。类内部直接 `this.xxx()` 调用带注解的方法，通常不会触发事务代理。



### 下单事务流程

- 查询用户商品，计算订单预览

  - 校验用户与商品
  - 计算金额

- 条件扣库存

  - 扣除库存的时候，判断库存是否大于订单量

- 创建订单、订单详情

  - 插入订单
  - 回写订单项
  - 插入订单详情




### 并发事务

并发事务带来的问题：

- 脏读：读到了别人还没提交的数据
-  不可重复读：同一条记录，前后读到的值不同（关注某条记录的值变了）
- 幻读：相同条件查询，前后匹配的记录集合不同（关注符合条件的记录集合变了，例如多了一条或少了一条。）
- 丢失更新：一个人的修改覆盖了另一个人的修改

mysql的隔离级别（从上到下，效率越来越低）

- READ UNCOMMINTED 未提交读（未解决：脏读、不可重复读、幻读）
- READ COMMINETD 读已提交（未解决：不可重复读、幻读）
- REPEATABLE READ 可重复读（未解决：幻读）
- SERIALIZABLE 串行化



## 慢查询

**哪些原因造成慢查询？**

- 聚合查询
- 多表查询
- 表数据量过大查询
- 深度分页查询

**如何定位慢查询？**

方案一：

调试工具：Arthas

运维工具：prometheus、Skywalking

方案二：

在MySQL的配置文件（/etc/my.cnf）中配置如下信息：

```conf
# 开启MySQL慢日志查询开关
slow_query_log=1
# 设置慢日志的时间为2秒，SQL语句执行时间超过2秒，就会被视为慢查询，记录慢查询日志
long_query_time=2
```

**如何分析？**

可以采用MySQL自带的分析工具EXPLAIN

- 通过key和key_len检查是否命中了索引（索引本身存在是否有失效的情况）
- 通过type字段查看sql是否有进一步的优化空间，是否存在全索引扫描或全盘扫描
  - index（索引数扫描）、all（全盘扫描）——需要优化
  - system（查询系统中的表）、const（根据主键查询）、eq_ref（主键索引查询或唯一索引查询）、ref（索引查询）、range（范围查询）
- 通过extra建议判断，是否出现了回表的情况。如果出现了，可以尝试添加索引或修改返回字段来修复





## redis



### 基础操作

  ```java
// 执行redis cli 进入到redis容器中
docker exec -it seckill-redis redis-cli

// 验证redis是否启动：需要在redis容器中执行
PING

// 创建key-value 获取value
SET seckill:day106:name alice
GET seckill:day106:name

// 创建过期时间
EXPIRE seckill:day106:name 60
TTL seckill:day106:name

// HSET-写Hash字段、HGET-读取一个字段、HGETALL读取全部字段
// H - hash的缩写
HSET seckill:day106:user:1 username alice role user
HGET seckill:day106:user:1 username
HGETALL seckill:day106:user:1

// DEL 删除整个 key
DEL seckill:day106:name
DEL seckill:day106:user:1
  ```

```
多个请求同时执行 INCR
→ Redis 不会让它们都读到同一个旧值
→ 每个请求都会得到准确递增后的不同结果

// 自增：用来限定用户请求次数
INCR seckill:rate-limit:1:5:29300000
```



### 缓存操作

主要了解两个对象：

- StringRedisTemplate：读写 Redis 字符串
- JsonMapper：Java 对象和 JSON 字符串互相转换

```java
private final StringRedisTemplate stringRedisTemplate;
private final JsonMapper jsonMapper;

public ProductCacheService(
    StringRedisTemplate stringRedisTemplate,
    JsonMapper jsonMapper) {
    this.stringRedisTemplate = stringRedisTemplate;
    this.jsonMapper = jsonMapper;
}
```

### 缓存问题

先区分三个概念：

```
缓存穿透：请求的数据本来就不存在，每次都穿过缓存访问数据库
缓存击穿：一个热点 Key 过期，大量请求同时去查数据库
缓存雪崩：大量 Key 在相近时间同时过期，数据库瞬时被压垮
```

#### 缓存穿透

缓存空数据，查询返回的数据为空，仍把这个空结果进行缓存

- 优点：简单

- 缺点：消耗内存，可能发送不一致问题。MySQL和redis没有同步

布隆过滤器：首先查询布隆过滤器，不存在直接返回

缓存预热时，预热布隆过滤器，使用bitmap（位图），布隆过滤器存在误判问题，一般设置在5%可以接受。



#### 缓存击穿

解决方案一：互斥锁（分布式锁）

- 一个线程查询缓存未命中（redis过期时间直接删除），先获取锁，然后查询数据库重建缓存，其余线程休眠重试

- 特点：强一致，性能差，涉及到钱的业务使用该方案

解决方案二：逻辑过期，也就是保存的字段里面包含过期时间 

- 一个线程查询缓存，发现字段过期，获取互斥锁成功，开启新线程，新线程查询数据库写入缓存数据，重置过期时间
- 其余线程，如果获取互斥锁失败，返回过期数据。如果获取缓存数据没有过期，则直接返回
- 特点：高可用、性能优

也就是逻辑过期，返回的数据不是一定是一致的



#### 缓存雪崩

问题一：大量的key同时过期

解决方案：给不同的Key的TTL（time to live 距离过期还剩多少秒）添加随机值。

问题二：redis宕机

解决方案：

- 利用redis集群提高服务的可用性：哨兵模式，集群模式
- 给缓存业务添加降级限流策略：ngxin或spring cloud gateway
- 给业务添加多级缓存：guava或caffeine



#### 双写一致性

redis作为缓存，mysql数据如何与redis进行同步呢？（双写一致性）

两者情况：

先删除缓存 ---延时---再修改数据库

- 这个好理解，线程在延时期间去读取之前的数据库值，从而出现缓存脏数据

先修改数据库---延时---再删除缓存

- 这个的问题，不是出现在延时阶段，而是在修改数据库之前缓存过期了，这个时候A线程读取了原本数据库的值，然后B修改了数据库，并删除了过期缓存，A线程把之前的值写入到缓存中。（线程是交替执行，这里的交替不是绝对交替）

**延时双删**

删除缓存 - 修改数据库 - 延时一会儿再删除缓存

从这里可以看出来，这个延时很重要，要在数据库写入完成的时候，再删除读取，保证读取的是最新值。



**强一致性 - 分布式锁**

保证双写**强一致性**，分布式锁（redission）本质让一个线程完成所有操作。为了提高效率，可以分别使用两把锁。

- 共享锁：读锁readLock，加锁之后，其他线程可以共享读操作
- 排他锁：独占锁writeLock也叫，加锁之后，阻塞其他线程读写操作



**短暂延迟 - 异步通知**

修改数据的时候 - 发布消息给MQ - 写入mysql

缓存服务监听MQ消息 - 更新缓存



**短暂延迟 - 使用canal异步通知**

canal基于mysql的主从同步来实现，监听mysql的binlog，也就是mysql数据库变化后，通知缓存进行更新。



### 数据持久化

#### RDB

RDB全称Redis Database Backup file（Redis数据备份文件），也叫Redis数据快照，简单来说就是把内存中的所有数据都记录到磁盘中。当Redis实例故障重启后，从磁盘读取快照文件，恢复数据。

```java
// redis 操作
# redis-cli
# save // 由redis主进程来执行RDB，会阻塞所有命令
# bgsave // backrround save 开启子进程执行RDB，避免主进程受到影响
```

Redis内部有触发RDB机制，可以在redis.conf文件中找到，格式如下：

```
# 900秒内，如果至少有1个key被修改，则执行bgsave
save 900 1
save 300 10
save 60 10000
```



#### AOF

AOF全称为Append Only File（追加文件）。Redis处理的每一个写命令都会记录到AOF文件，可以看做是命令日志文件。

AOF默认是关闭的，需要修改redis.conf配置文件来开启AOF：

```java
// 是否开启AOF功能，默认是no
appendonly yes
// AOF文件的名称
appendfilename "appendonly.aof"
```

AOF的命令记录的频率也可以通过redis.conf文件来配置：

```java
// 表示每执行一次写命令，立即记录到AOF文件
appendfsync always
// 写命令执行完先放入AOF缓冲区，然后表示每隔1秒将缓存区数据写到AOF文件，是默认方案
appendfsync everysec
// 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写回磁盘
appendfsync no
```

优化：由于AOF会记录对同一个key的多次写操作，但只有最后一次写操作才有意义，通过执行bgrewriteaof命令，可以让AOF文件执行重新功能。

redis也会在触发阙值时自动重新AOF文件。阙值也可以在redis.conf中配置：

```java
// 以下两个条件同时满足：
// AOF文件比上次文件，增长超过多少百分比则触发重新
auto-aof-rewrite-percentage 100
// AOF文件体积最小多大以上才触发重写
auto-aof-rewrite-min-size 64mb
```





### 数据过期策略

惰性删除：设置该key过期时间后，我们不去管它，当需要该key时，我们检查其是否过期，如果过期，我们就删除它，反之返回该key。

定期删除：每隔一段时间，我们就对一些key进行检查，删除里面过期的key（从一定数量的数据库中取出一定数量的随机key进行检查，并删除其中的过期key）。（slow模式和fast模式）

**Redis的过期删除策略：惰性删除 + 定期删除 两种策略进行配合使用。**





### 数据淘汰策略

当Redis中的内存不够用时，此时再向Redis中添加新的key，那么Redis就会按照某一种规则将内存中的数据删除掉，这种数据的删除规则别称之为内存的淘汰策略。

默认模式：noeviction - 不淘汰任何key，但是内存满时，不允许写入新数据。

LFU：（least frequently used），最少频率使用。

LRU：（least recently used），最近最少使用，需要设置时间，比如3秒内。









### 限流

简单对比：

| 方案     | 核心逻辑                 | 特点                       |
| -------- | ------------------------ | -------------------------- |
| 固定窗口 | 每分钟重新计数           | 最容易实现，但有边界突刺   |
| 滑动窗口 | 统计过去 N 秒            | 更精确，实现较复杂         |
| 令牌桶   | 请求消耗令牌，按速度补充 | 允许短时突发，工程中很常用 |

令牌桶则像一个装令牌的桶：

```
桶最大容纳 5 个令牌
每秒补充 1 个令牌
每次请求消耗 1 个令牌
没有令牌时拒绝请求
```

它的特点是：允许“短时突发”。比如用户一开始可以立刻请求 5 次；之后要等系统按固定速度补令牌。它很适合 API 网关、登录接口、秒杀入口等需要“既允许少量突发、又限制长期频率”的场景。

### lua

redis中有lua解析器，通常使用lua脚本，是为了让redis操作具有原子性。



### 分布式锁



分布式锁是：**让多台服务器上的多个程序实例，共用同一把锁**。

普通 `synchronized` 只能锁住当前 JVM 内的线程：

```
服务器 A 的 JVM：有自己的锁
服务器 B 的 JVM：有自己的锁
```

两台服务器各自都能拿到锁，所以它们仍可能同时修改同一份库存。

分布式锁会把“锁状态”放在所有服务器都能访问的地方，例如 Redis：

```
服务器 A ─┐
服务器 B ─┼──→ Redis 中同一个 lock Key
服务器 C ─┘
```

例如抢商品缓存重建锁：

```
seckill:lock:product:1
```

流程：

```
请求 A：Redis 中没有锁 Key → 创建成功 → 获得锁
请求 B：Redis 中已有锁 Key → 创建失败 → 没拿到锁
请求 A：查 MySQL、重建缓存
请求 A：删除锁 Key → 释放锁
```

Redis 常用写法：

```
SET seckill:lock:product:1 随机值 NX EX 10
```

含义：

```
SET
│
├── key   → seckill:lock:product:1
│           锁的名字
│
├── value → uuid-A
│           谁持有这把锁
│
├── NX    → Not eXists
│           key 不存在时才 SET
│
└── EX 10 → EXpire 10 seconds
            10 秒后自动过期
```

它的适用场景：

- 缓存失效时，只让一个请求查数据库并重建缓存，防止缓存击穿。
- 多台服务同时执行“只能进行一次”的后台任务。
- 分布式环境下协调某些低频、短时间的临界操作。

```java
// Redis 分布式互斥锁 SETNX

缓存失效
→ 第一个请求抢到锁，查询 MySQL 并重建缓存
→ 其他请求未抢到锁，等待短暂时间后再次查询缓存
→ 缓存已重建，直接返回
```

秒杀库存的最终正确性不要只依赖分布式锁，仍应有：

```
UPDATE seckill_activities
SET seckill_stock = seckill_stock - 1
WHERE id = #{activityId}
  AND seckill_stock > 0;
```

因为锁可能过期、服务可能异常、代码可能漏加锁；数据库条件更新才是防止超卖的最后防线。



### redission

Redisson 是一个 Java 的 Redis 客户端框架。它在 Spring 项目里把 Redis 的很多高级用法封装好了，尤其是**分布式锁**。

你现在使用的是 Spring 的：

```
StringRedisTemplate
```

它比较接近 Redis 原生命令：

```
stringRedisTemplate.opsForValue().setIfAbsent(...)
stringRedisTemplate.delete(...)
```

如果自己写分布式锁，还要处理：

- 锁 Key 不存在时才创建；
- 锁自动过期；
- 每个请求生成唯一 `lockOwner`；
- 只能释放自己的锁；
- “比较锁主人 + 删除锁”要用 Lua 保证原子性；
- 任务执行太久时，锁如何续期。

Redisson 把这些封装成更接近 Java 锁的 API：

```java
RLock lock = redissonClient.getLock("seckill:lock:product:1");

try {
	// 获取锁最大等待时间（期间会重连） 锁自动释放时间（设置后没有watchdog）  时间单位
    boolean locked = lock.tryLock(0, 10, TimeUnit.SECONDS);

    if (!locked) {
        // 没拿到锁
        return;
    }

    // 临界区：查 MySQL、重建 Redis 缓存
} finally {
    if (lock.isHeldByCurrentThread()) {
        lock.unlock();
    }
}
```

它通常能帮你完成：

```
tryLock：原子抢锁
unlock：只释放当前调用者拥有的锁
watchdog：未指定固定租约时，可自动续期
Lua：内部处理比较、删除等原子操作
```

你可以把它理解为：

```
StringRedisTemplate：直接使用 Redis 基础能力
Redisson：在 Redis 基础上提供 Java 风格的分布式对象和锁
```

除了 `RLock`，Redisson 还提供：

```
RMap        分布式 Map
RSet        分布式 Set
RQueue      分布式队列
RSemaphore  分布式信号量
RRateLimiter 分布式限流器
```

对你当前秒杀项目而言：

```
缓存重建锁：可以用 Redisson 的 RLock
Lua 秒杀扣库存：仍适合直接用 Redis Lua
最终防超卖：仍应依赖 MySQL 条件更新
```

也就是说，Redisson 能减少你手写分布式锁出错的概率，但不是替代数据库事务和唯一索引。

#### watch dog

**① 显式设置租期：到期释放，不启动 watchdog**

```
boolean locked = lock.tryLock(0, 10, TimeUnit.SECONDS);
```

参数含义是：

- `0`：获取不到锁就不等待。
- `10`：获取锁成功后，锁的有效期是 10 秒。
- `SECONDS`：前两个参数的时间单位。

如果没有提前 `unlock()`，锁会在租期到达后过期，**即使业务还没执行完，也不会自动续期**。[Redisson 官方说明](https://redisson.pro/docs/data-and-services/locks-and-synchronizers/index.html)

**② 不设置租期：使用 watchdog 自动续期**

```
boolean locked = lock.tryLock(0, TimeUnit.SECONDS);
```

这里的 `0` 仍然是获取锁的最大等待时间，但没有指定 `leaseTime`。此时使用配置：

```
config.setLockWatchdogTimeout(30000); // 默认 30 秒
```

因此：

| 项目         | 默认行为                     |
| ------------ | ---------------------------- |
| 锁初始有效期 | 30 秒                        |
| 续期调度间隔 | 约 30 ÷ 3 = 10 秒            |
| 每次续期成功 | 将剩余有效期重新设置为 30 秒 |

**除以 3 的是 `lockWatchdogTimeout`，而不是你显式传入的 `leaseTime=10`。** 这个配置用于未指定租期的锁，续期任务按它的三分之一进行调度。[配置说明](https://redisson.pro/docs/configuration/) · [续期源码](https://github.com/redisson/redisson/blob/master/redisson/src/main/java/org/redisson/renewal/RenewalTask.java)

正常续期的过程可以理解为：

```
第 0 秒：加锁，预计第 30 秒过期
第 10 秒：续期，预计第 40 秒过期
第 20 秒：续期，预计第 50 秒过期
……
业务完成并 unlock：释放锁，停止续期
```

另外，你注释里的“期间会重连”更准确应写成**“期间等待并重试获取锁”**；锁被别人占用，不等于 Redis 连接断了。



### redis集群

主从复制是“备一份”，哨兵模式是“故障后自动换人”，分片集群是“多人分工，每人还有替补”。

#### 主从复制

**1. 主从复制：给数据增加副本**

```
                应用
                 │
                 ▼
              主节点
             读写数据
             /      \
        复制数据    复制数据
          ↓            ↓
       从节点 1      从节点 2
       数据副本      数据副本
```

主节点接收写入，再把数据复制给从节点。应用也可以把部分读请求交给从节点，但可能读到稍旧的数据。

它解决的是：

- 一份数据有多个副本。
- 可以通过读写分离分担读取压力。

局限是：**单靠主从复制，主节点宕机后不会自动选出新主节点**，通常需要人工或其他机制处理。另外，各节点保存相同的数据，不能靠增加从节点扩大可存储的数据总量。

##### 全量同步

整个同步分两部分，rdb文件，和后续新增写入（日志文件）。

1、从节点请求主节点同步数据：replication id（判断是否第一次请求），offset（日志偏移量）

2、主节点判断是否是第一次请求，是第一次就与从节点同步版本信息（replication id、offset）

3、主节点执行bgsave，生成rdb文件后，发送给从节点去执行

4、在rdb生成执行期间，主节点会以命令的方式记录到缓冲区（一个日志文件）

5、把生成之后的命令日志文件发送给从节点进行同步

##### 增量同步

1、从节点请求主节点同步数据，主节点判断不是第一次请求，不是第一次就获取从节点的offset值

2、主节点从命令日志中获取offset值之后的数据，发送给从节点进行数据同步



#### 哨兵模式

**2. 哨兵模式：在主从复制上增加自动故障转移**

```
       哨兵 1 ←→ 哨兵 2 ←→ 哨兵 3
          \         │         /
            监控、判断故障
                    ↓
                  主节点
                 /      \
              复制      复制
               ↓          ↓
            从节点 1    从节点 2
```

**哨兵是额外运行的监控进程，不负责保存业务数据。**

如果主节点宕机：

```
哨兵共同判断主节点故障
          ↓
选一个从节点提升为新主节点
          ↓
让其他从节点复制新主节点
          ↓
支持哨兵的客户端发现新主节点并连接
```

它主要解决：**主节点故障后，自动恢复服务，减少人工介入。**

但它仍然是一组主从，数据没有分片：**一份完整数据仍然要能放进单个主节点，写入压力也主要由这个主节点承担。**



#### 分片集群

**3. 分片集群：把数据分给多个主节点**

```
                  应用
           /       │       \
          ↓        ↓        ↓
        主 A      主 B      主 C
       数据①     数据②     数据③
          │        │        │
         复制     复制      复制
          ↓        ↓        ↓
        从 A1     从 B1     从 C1
```

和前两种的关键区别是：

**A、B、C 保存不同部分的数据，合起来才是完整数据。**

Redis Cluster 使用 16384 个槽分配数据，每个主节点负责一部分槽。客户端根据 key 所属的槽访问对应节点。

它主要解决：

- **容量扩展**：数据太多，一台机器放不下。
- **负载分担**：不同 key 的读写可以分散到不同主节点。
- **自动故障转移**：有合适副本且满足选举条件时，从节点接替故障主节点，**不需要另外部署 Sentinel**。

代价是使用起来更复杂，例如某些多 key 操作要求这些 key 位于同一个槽。



## 消息队列

消息队列（Message Queue，MQ）可以理解为一个“可靠的任务中转站”。

它把“发起任务”和“执行任务”分开：

```
生产者 → 消息队列 → 消费者
```

总关系：

```
生产者：发送任务
消费者：执行任务
ACK：告诉 MQ“这条任务已成功完成”
重试：暂时失败后再处理
死信：多次失败后隔离，等待排查
积压：任务来得太快，处理速度跟不上
```



### 生产者与消费者

```
生产者 Producer
    │
    │ 发送消息 publish
    ▼
RabbitMQ
    │
    ▼
Queue 队列
    │
    │ 投递消息 deliver
    ▼
消费者 Consumer
    │
    ▼
创建 MySQL 订单
```



### 确认：ACK

消费者拿到消息后，不能马上认为任务完成（只能认为收到了消息）。

```
Queue 中有：

消息A
消息B
消息C

Consumer 正在监听

RabbitMQ
   │
   ├──投递 A──→ Consumer
   │
   │            ↓
   │          处理订单
   │            ↓
   │           ACK
   │
   ├──投递 B──→ Consumer
   ...
```

所以才需要我们刚才讲的 **ACK（Acknowledgement，确认）**：

```
RabbitMQ
   │
   │ 投递消息
   ▼
Consumer
   │
   │ 创建订单
   ▼
MySQL
   │
   │ 成功
   ▼
Consumer
   │
   │ ACK
   ▼
RabbitMQ
   │
   └── 删除这条消息
```



如果消费者刚拿到消息就宕机，没有来得及 ACK（acknowlegment）：

```
RabbitMQ 不会删除消息
→ 消息会重新投递给其他消费者
```

这就是 MQ 可靠性的基础之一。



### 重试

如果消费者处理失败，例如 MySQL 短暂连接失败：

```
消费者处理消息失败
→ 不确认 ACK
→ 消息按策略重新投递
→ 消费者再次尝试处理
```

但重试不能无限进行：

```
一直失败 → 无限重试 → 队列被阻塞
```

通常设置最多重试次数，例如 3 次。



### 死信队列：DLQ

一直处理失败的消息会进入死信队列（Dead Letter Queue，DLQ）：

```
正常订单队列
  ↓ 多次处理失败
死信队列
  ↓
人工排查 / 后台补偿 / 修复后重新投递
```

秒杀订单消息进入死信队列，可能代表：

```
数据库故障
订单数据不合法
活动数据异常
代码存在 Bug
```

它不能静默丢失，否则用户可能“显示抢购成功，却没有订单”。



### 消息积压

积压表示：生产消息的速度超过消费者处理消息的速度。

```
生产者：每秒 10,000 条消息
消费者：每秒只能处理 1,000 条消息
```

队列中的消息越来越多：

```
第 1 秒：积压 9,000
第 10 秒：积压 90,000
```

后果：

- 用户等待订单创建的时间越来越长；
- RabbitMQ 内存、磁盘压力变大；
- 极端情况下消息过期或服务故障。

常见处理方式：

```
增加消费者实例 / 提高并发消费数量
优化数据库写入和订单处理逻辑
限流，减少进入队列的请求
监控队列长度、消费速率、失败次数
```

总关系：

```
生产者：发送任务
消费者：执行任务
ACK：告诉 MQ“这条任务已成功完成”
重试：暂时失败后再处理
死信：多次失败后隔离，等待排查
积压：任务来得太快，处理速度跟不上
```

秒杀中，一个很重要的补充是：**消费者必须幂等**。

因为 MQ 可能重复投递同一条消息，所以消费者即使收到两次相同订单任务，也只能真正创建一份订单。你的数据库唯一约束：

```
UNIQUE(activity_id, user_id)
```

正好可以作为这层最终兜底。



## rabbitMQ

主要涉及到交换机 - 路由 - 队列

- 为啥需要路由，可以将一个消息分配到多个队列中

```java
生产者发送：
交换机 + 路由键 + 消息
        ↓
交换机根据路由规则匹配队列
        ↓
消息进入匹配到的队列 ---
        ↓                 队列有消息时，RabbitMQ 推送给消费者处理
消费者监听该队列 -------
```

