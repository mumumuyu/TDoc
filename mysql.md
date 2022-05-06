# mysql

## **基本用法**

```sql
select xxx,count(distinct(xxx)) xxx,xxx from xxx x,xxxx xx where (xxx) or (xxx) group by xxx order by xxx (DESC) having xxx
```

### **逻辑判断**

```sql
(CASE
    when age<20 then '20岁以下'
    when age between 20 and 24 then '20-24岁'
    when age>=25 then '25岁及以上'
    else '其他' end ) as age_cut
```

### **日期**

```sql
SELECT 1 '月份', count(id) '个数'
from `order`
where month(create_time) = '1' and year(create_time) = '2022'
union all
SELECT 2 '月份', count(id) '个数'
from `order`
where month(create_time) = '2' and year(create_time) = '2022'
union all
SELECT 3 '月份', count(id) '个数'
from `order`
where month(create_time) = '3' and year(create_time) = '2022'
```

### **切分字符**

```sql
substring_index(profile,',',-1) //获取，号分开的某个字段的最后一部分
								//1，就是获取第一部分，2就是获取第一部分加第二部分
```

### **窗口函数**

join

```sql
select a.device_id,a.university,a.gpa
from user_profile a join 
(
    select university,min(gpa) gpa
    from user_profile 
    group by university
) as b on a.university = b.university and a.gpa = b.gpa
order by university
```

##### Union和Union All

Union在整理的过程中会将一些重复的选项筛选，并且针对所产生的结果进行排列顺序之后运算。所删除的记录会再次返回到结果当中。

Union all操作过程中会针对两个结果直接合并之后就会返回。

##### **Date和DateTime**

Date

显示格式：YYYY-MM-DD

DateTime

显示格式：YYYY-MM-DD HH:mm:ss

复合索引最左原则

- 最左优先就是说组合索引的第一个字段必须出现在查询组句中，这个索引才会被用到。只要组合索引最左边第一个字段出现在Where中，那么不管后面的字段出现与否或者出现顺序如何，MySQL引擎都会自动调用索引来优化查询效率。
- 根据最左匹配原则可以知道B-Tree建立索引的过程，比如假设有一个3列索引(col1,col2,col3),那么MySQL只会会建立三个索引(col1),(col1,col2),(col1,col2,col3)。

##### 一次性插入百万级数量示例

set global log_bin_trust_function_creators=TRUE;

```sql
DELIMITER $$
-- 写函数之前必须要写，标志
CREATE FUNCTION mock_data2 ()
RETURNS INT
BEGIN
DECLARE num INT DEFAULT 10000;
DECLARE i INT DEFAULT 0;
WHILE i<num DO
INSERT INTO `app_user`(`name`,`email`,`phone`,`gender`)VALUES(CONCAT('用户',i),'1017276522@qq.com','123456789',FLOOR(RAND()*2));
SET i=i+1;
END WHILE;
RETURN i;
END;
SELECT mock_data2() -- 执行此函数 生成一万条数据
```

## 数据库基本知识及mysql

### 数据连接池

j2ee的server启动后会建立一定数量的池连接，并维持这个数目。当Browser需要连接后会返回一个未使用的池连接并进行标记。若没有空闲连接就会创建一定数量的连接（由配置参数决定）当池子连接使用完毕就会标记为空闲。

### 继承映射

1.按继承结构，多少个子类都一张

- 单表策略，无需表连接，查询快，适合多态查询

2.子类一张，公共信息一张，特有信息一张

- 多表查询，需要连接，数据紧凑

3.具体类一张，多少个子类多少张表

### 数据库的优化

- 为了查询更快，表中字段宽度尽可能小，查询时，需要什么数据查什么数据，不要直接*
- 使用连接(join)代替子查询
- 使用union来代替手动创建临时表，用Union将多个select语句连接就可以，不过select的字段数要相同
- 当数据特别多时，可以通过横向分表（分列）或者纵向（分数据量）

### left / right join

left join : 以左表位基准，将左表数据全部表示出来，而右表显示符合条件的记录

### 主从复制

主服务器对数据库修改记录二进制日志，从服务器通过二进制日志执行自动更新

### ACID

- 原子性：事务是一个不可分割的工作单位，事务要么都完成，要么都不完成
- 一致性：事务前后数据的完整性必须保持一致
- 隔离性：多个用户并发访问数据库时，一个用户的事务不可以被其它用户干扰，不同事物之间数据要相互隔离
- 持久性：一个事务一旦提交就是永久性改变。

### 范式

1. 第一范式

   列原子性，不可以再分成别的列，一个表要有主键且其余列必须完全依赖主键

2. 2NF

   非主属性间不可存在依赖，且非主键必须直接依赖于主键

3. 3NF

### 锁

- 悲观锁：很悲观，每次拿数据都觉得别人会修改，所以拿数据时候会给数据上锁别人想拿就会被Block直到拿到锁。可以避免发生并发冲突，如synchronized
- 乐观锁：很乐观，每次拿数据都不上锁。适用于读多写少，可以提高吞吐量。如无并发冲突，只在提交时检查数据完整性
  - 数据版本记录机制：（数据版本：表字段加一个version。读取时将version一块取出。每更新一次，version+1）当我们提交更新时，将当前数据库表version信息与第一次取时进行对比，一致就更新，否则认为过期
  - 时间戳(timestamp)，增加一个字段（名字无所谓，类型一定要是timestamp），与version类似，在提交更新时讲当前表timestamp与取到时进行对比，一致就更新，否则版本冲突

### 隔离级别

| 隔离级别                     | 脏读（Dirty Read） | 不可重复读（NonRepeatable Read） | 幻读（Phantom Read） |
| ---------------------------- | ------------------ | -------------------------------- | -------------------- |
| 未提交读（Read uncommitted） | 可能               | 可能                             | 可能                 |
| 已提交读（Read committed）   | 不可能             | 可能                             | 可能                 |
| 可重复读（Repeatable read）  | 不可能             | 不可能                           | 可能                 |
| 可串行化（Serializable ）    | 不可能             | 不可能                           | 不可能               |

未提交读(Read Uncommitted)：允许脏读，也就是可能读取到其他会话中未提交事务修改的数据。

提交读(Read Committed)：只能读取到已经提交的数据。Oracle等多数数据库默认都是该级别 (不重复读)。

可重复读(Repeated Read)：可重复读。在同一个事务内的查询都是事务开始时刻一致的，InnoDB默认级别。在SQL标准中，该隔离级别消除了不可重复读，但是还存在幻象读。

串行读(Serializable)：完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞。

### 索引底层实现

B+树，数据仅存储于叶子节点，且从左到右可以串联便于范围查询，导致索引符合最左原则

### 引擎

InnoDB是聚集索引，支持事务，行级锁

面向在线事务处理

MyISAM非聚集，不支持事务，支持行级锁

### MyISAM和InnoDB的区别

①InnoDB支持事务与外键和行级锁,MyISAM不支持(最主要的差别)

②MyISAM读性能要优于InnoDB,除了针对索引的update操作,MyISAM的写性能可能低于InnoDB,其他操作MyISAM的写性能也是优于InnoDB的,而且可以通过分库分表来提高MyISAM写操作的速度

③MyISAM的索引和数据是分开的,而且索引是压缩的,而InnoDB的索引和数据是紧密捆绑的,没有使用压缩,所以InnoDB的体积比MyISAM庞大

MyISAM引擎索引结构的叶子节点的数据域，存放的并不是实际的数据记录，而是数据记录的地址。索引文件与数据文件分离，这样的索引称为“非聚簇索引”。其检索算法：先按照B+Tree的检索算法检索，找到指定关键字，则取出对应数据域的值，作为地址取出数据记录。

InnoDB引擎索引结构的叶子节点的数据域，存放的就是实际的数据记录。这样的索引被称为“聚簇索引”，一个表只能有一个聚簇索引。

④InnoDB 中不保存表的具体行数，也就是说，执行select count(*) from table时，InnoDB要扫描一遍整个表来计算有多少行，但是MyISAM只要简单的读出保存好的行数即可。注意的是，当count(*)语句包含 where条件时，两种表的操作是一样的。

⑤DELETE FROM table时，InnoDB不会重新建立表，而是一行一行的删除。

⑥InnoDB表的行锁也不是绝对的，假如在执行一个SQL语句时MySQL不能确定要扫描的范围，InnoDB表同样会锁全表，例如update table set num=1 where name like “%aaa%”

在where条件没有主键时,InnoDB照样会锁全表

 

选择哪种搜索引擎,应视具体应用而定

①如果是读多写少的项目,可以考虑使用MyISAM,MYISAM索引和数据是分开的，而且其索引是压缩的，可以更好地利用内存。所以它的查询性能明显优于INNODB。压缩后的索引也能节约一些磁盘空间。MYISAM拥有全文索引的功能，这可以极大地优化LIKE查询的效率。

②如果你的应用程序一定要使用事务，毫无疑问你要选择INNODB引擎

③如果是用MyISAM的话，merge引擎可以大大加快应用部门的开发速度，他们只要对这个merge表做一些select count(*)操作，非常适合大项目总量约几亿的rows某一类型(如日志，调查统计)的业务表。

### 切分

垂直切分：表按模块划分到不同数据库，（列）表数据拆分到不同表

水平切分：单表大数据量，行数据拆分到不同表

### JDBC事务处理

connection调用setAutoCommit设置是否手动提交事务

完成事务commit();否则rollback()

savapoint，可以设置保存点让事务回滚到指定保存点

关于事务，在面试中被问到的概率是很高的，可以问的问题也是很多的。首先需要知道的是，只有存在并发数据访问时才需要事务。当多个事务访问同一数据时，可能会存在5类问题，包括3类数据读取问题（脏读、不可重复读和幻读）和2类数据更新问题（第1类丢失更新和第2类丢失更新）。

事物的ACID:

Atomic：要么全做，要不全不做

Consistent:结束前后系统状态一致

Isolated:事务彼此无法看到对方状态

Durable:改动会持久化

### 性能问题

statement And PrepareStatement

prepareStatement:可以防止sql注入，增加安全性，自动增加''，可以带参数，数据库可以缓存编译优化后的sql，批处理时有明显的性能优势。

JDBC的反射及作用

通过反射com.mysql.jdbc.Driver类，实例化该类的时候会执行该类内部的静态代码块，该代码块会在Java实现的DriverManager类中注册自己,DriverManager管理所有已经注册的驱动类，当调用DriverManager.geConnection方法时会遍历这些驱动类，并尝试去连接数据库，只要有一个能连接成功，就返回Connection对象，否则则报异常。

```java

```

## 引擎

innodb与myisam

innobo与MyISAM都采用B+Tree结构，一个节点可以存放多个子树，data都存放在叶子节点，非叶子节点存放冗余(索引)且数据可以串联，从左到右有顺序地排列，最左原则

myisam:支持表级锁，不支持事务，他有全局索引，其data与index分开存储于MYD与MYI所以是非聚集索引，其查询与增删改快于innodb，所以比较适合小型应用比如日志。

innodb：支持事务，行级锁。5.6+后有全局索引，data与index一起存放于ibd类型文件中，它推荐有主键且整数自增，在innodb表的B+结构组建时，没有主键的话他会去找有没有哪个列的元素都是唯一不重复的，没有的话就会增加一个隐藏列。这样增加了mysql的负担，磁盘IO速度比较有限，所以mysql资源很宝贵，尽量不要让mysql去做额外事。

### 索引的优化

索引就是有排序的数据结构

- 单索引5个以内

- 禁止3个表以上的join

- 重复和冗余的索引不可以存在

- 字段数<=30

- 索引必须要有default值，不可为空

- verchar索引要制定其长度

- 对于行数超过500w与容量超过2G的表要进行分表

