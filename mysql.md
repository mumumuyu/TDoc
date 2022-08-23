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

MYSQL中处理插入过程主键或唯一重复值的解决办法：

1.IGNORE:有则忽略，无则插入

2.REPLACE：有则删除再插入，无则插入

3.ON DUPLIACATE KEY UPDATE:有则更新，无则插入

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

##### 左连接 右连接 内连接

表1 id 123  表2 id 124

左连接 ：以左表为基础关联右表	123

右连接 ：以右表为基础关联左表	124

内连接：关联两表里都有的字段 	12

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

- 未提交读(Read Uncommitted)：允许脏读，也就是可能读取到其他会话中未提交事务修改的数据。

- 提交读(Read Committed)：只能读取到已经提交的数据。Oracle等多数数据库默认都是该级别 (不重复读)。

- 可重复读(Repeated Read)：可重复读。在同一个事务内的查询都是事务开始时刻一致的，InnoDB默认级别。在SQL标准中，该隔离级别消除了不可重复读，但是还存在幻象读。

- 串行读(Serializable)：完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞。

关于脏读，不可重复读，幻读

- 脏读：一个事务读取到了未提交的数据，而前面那个事务回滚了，就出现了脏读
- 不可重复读：同一事务，同一个select得到不同结果(数据变化)
- 幻读：用户读取一个范围数据时插入数据，用户再次读取同一个范围时多出一条“幻影”数据(行数变化)

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

为什么树查询快?

从根节点开始，沿路径查找，中序遍历得到结果是递增排序的结点序列,二叉树一般为二叉查找树，当先后插入完全有序下退化为单只树。树的查询效率与高度相关，为例高度尽量低，左右子树尽量平衡，出来了平衡二叉树，查询复杂度在O（log N），但是删除时必须检查从删除节点到根节点路径上所有平衡因子，代价为O(2log N),那为了 提升插入与删除效率，可以牺牲一点平衡度，不需要那么严格的平衡，所以出来了红黑树，红黑树与完全平衡二叉树差不多查询性能，但是插入最多两次，删除最多三次旋转，代价优于完全平衡。

但是红黑树也可能出现在数据量过大情况下也可能出现，层数过多情况，所以引申出B-树，B+树

### 回表

例如(当查询列不存在于索引中)使用非主键索引的查询，在搜索完非主键索引树后得到主键值，还需要到主键索引树再去搜索一次，所以虽然用了索引，但底层进行了两次索引查询，这就是回表。

### 覆盖索引

使用索引覆盖需要寻找的值

假设有个表

| 表   | info     |
| ---- | -------- |
| 主键 | id       |
| 名称 | name     |
| 值   | value    |
| 别名 | realname |

对于info表，现在有（name,value）联合索引

需要查询realname，1.不查询realname 2.将realname加入一个索引，这就是索引覆盖

### MVCC

Multi Version Concurrency Control，多版本并发控制

乐观锁的体现，最大优势：读不加锁，读写不冲突

InnoDB的MVCC是通过在每行记录后面保存两个隐藏的列来实现的，分别保存这条行的**创建时间**和**行的删除时间**。这里的时间并不是实际的时间值，而是**系统版本号（事务的ID）**。事务开始时刻的系统版本号会作为事务的ID，每次执行一个新事务，系统版本号就会自动递增。随之引出了**当前读**和**快照读**。

**当前读**：select xxx lock in share mode(共享锁)，读取的是记录的最新版本，读取时保证其他事务不修改当前记录，会对读取记录进行加锁

**快照读**：前提：隔离级别不是串行级别，串行级别下快照读会退化为当前读，快照读避免加锁，所以可能不一定是最新版本如select xxx，不加锁的简单select都属于快照读

MVCC只在隔离级别为可重复读和提交读级别下工作

## MVCC的实现原理 

 每一行记录有三个隐藏键，分别为DATA_TRX_ID、DATA_ROLL_PTR、DB_ROW_ID，其中： 

 **DATA_TRX_ID**
 记录最近更新这条行记录的事务 ID，大小为 6 个字节 

 **DATA_ROLL_PTR**
 表示指向该行回滚段（rollback segment）的指针，大小为 7 个字节，InnoDB 便是通过这个指针找到之前版本的[数据]()。该行记录上所有旧版本，在 undo 中都通过[链表]()的形式组织。 

 **DB_ROW_ID**
 行标识（隐藏单调自增 ID），大小为 6 字节，如果表没有主键，InnoDB 会自动生成一个隐藏主键，即此列。 

 为了让大家更直观地理解 MVCC 的实现原理，这里举一个“事务对某行记录更新的过程”的案例来讲解 MVCC 中多版本的实现。 

 假设 F1～F6 是表中字段的名字，1～6 是其对应的[数据]()。后面三个隐含字段分别对应该行的隐含ID、事务号和回滚指针，如下图所示。 

![img](https://uploadfiles.nowcoder.com/images/20210804/821556076_1628054801124/9865DE751FD52158E3DDD815F6077BD8)

 具体的更新过程，简单描述如下。 

 首先，假如这条[数据]()是刚 INSERT 的，可以认为 ID 为 1，其他两个字段为空。 

 然后，当事务 1 更改该行的[数据]()值时，会进行如下操作，如下图所示。 

​     ![img](https://uploadfiles.nowcoder.com/images/20210804/821556076_1628054809278/BD385DC6F10BEC922D9881F512286671)    

​     用排他锁锁定该行；记录 Redo log；    

 把该行修改前的值复制到 Undo log，即图中下面的行； 

 修改当前行的值，填写事务编号，使回滚指针指向 Undo log 中修改前的行。 

 接下来，与事务 1 相同，此时 Undo log 中有两行记录，并且通过回滚指针连在一起。因此，如果 Undo log 一直不删除，则会通过当前记录的回滚指针回溯到该行创建时的初始内容，所幸的是在 InnoDB 中存在 purge 线程，它会查询那些比现在最老的活动事务还早的 Undo log，并删除它们，从而保证 Undo log 文件不会无限增长，如下图所示。 

![img](https://uploadfiles.nowcoder.com/images/20210804/821556076_1628054821818/97F9C71CAEB8A53591CFD5FB4FCF9191)

 如果此时事务回滚，那么可以根据回滚指针，对[数据]()依次进行回滚并找到undo log中[数据]()行为当前事务id的一列，进行[数据]()恢复。

## 引擎

### innodb与myisam

innobo与MyISAM都采用B+Tree结构，一个节点可以存放多个子树，data都存放在叶子节点，非叶子节点存放冗余(索引)且数据可以串联，从左到右有顺序地排列，最左原则

myisam:支持表级锁，不支持事务，他有全局索引，其data与index分开存储于MYD与MYI所以是非聚集索引，其查询与增删改快于innodb，所以比较适合小型应用比如日志。

innodb：支持事务，行级锁。5.6+后有全局索引，data与index一起存放于ibd类型文件中，它推荐有主键且整数自增，在innodb表的B+结构组建时，没有主键的话他会去找有没有哪个列的元素都是唯一不重复的，没有的话就会增加一个隐藏列。这样增加了mysql的负担，磁盘IO速度比较有限，所以mysql资源很宝贵，尽量不要让mysql去做额外事。



先说说sql最小存储单元为”页“，一页有8KB

假设一个表数据8000KB(1000页)，索引80KB(10页)(为一个字段)

若无索引，需要查询1000页，而有索引只需要10页+根据索引结果搜索对应数据(由于磁盘IO速度有限，所以不用索引明显会慢很多)

**那么我们如何设置索引呢**？

一般，

最常见为**单列索引**，主键和外键以及where 条件用到的字段可以设置

而**联合索引**在B+树为复合数据结构(多属性下如name,phone时，name为有序,phone为无序，在name相等时,phone为有序)

当使用where name = ‘zhangsan’ and phone='1320xx’去查询数据的时候，会先去根据name来判断往左还是往右，在得到name相同后再比较phone，name,phone建联合索引等价于建立了phone索引

但如果where条件没有name，那引擎就不知道先查询哪个节点

如果创建三个字段的索引index(a,b,c）相当于创建三个索引：index(a),index(a,b),index(a,b,c)。用where b=?和where b=? and c=? 和where a = ? and c = ?是不能使用到索引。因为不能不用第一个字段，也不能中断。这也就是**最左前缀原则**

索引失效的情况

- 使用函数，表达式，计算，因为值改变后无法与索引中的值匹配
- 使用了范围查找，因为二叉树的查找是=
- 使用like %
- 字符串不加''
- 少用or
- is null, is not null不可使用

当一个sql十分慢的时候

```sql
-- 使用explain plan for查看sql执行计划
-- 对于大表，执行较久的部分，表的Operation尽量使用INDEX,而不是TABLE ACCESS FULL,到对应的table进行添加INDEX,原则如上
```

![image-20220822180437899](C:\Users\L\Desktop\文档\photo\image-20220822180437899.png)

最后来看

**聚簇索引为什么比非聚式索引慢呢？**

假设有一8000条记录的表，表中每条记录在磁盘上占用1000字节，如果在一个10字节长的字段上建立非 聚簇索引主键，需要二叉树节点16000个(这16000个节点中有8000个叶节点，每个页节点都指向一个数据记录)，这样数据将占用8000条 ×1000字节/8K字节=1000个页面；索引将占用16000个节点×10字节/8K字节=20个页面，共计1020个页面。

同样一张表，如果我们在对应字段上建立聚簇索引主键，由于聚簇索引的页节点就是数据节点，所以索引节点仅有8000个，占用10个页面，数据仍然占有1000个页面。

下面我们看看在执行插入操作时，非聚簇索引的主键为什么比聚簇索引主键要快。主键约束要求主键不能出现重复，那么SQL Server是怎么知道不出现重复的呢？唯一的方法就是检索。对于非聚簇索引，只需要检索20个页面中的16000个节点就知道是否有重复，因为所有主键 键值在这16000个索引节点中都包含了。但对于聚簇索引，索引节点仅仅包含了8000个中间节点，至于会不会出现重复必须检索另外1000个页数据节点 才知道，那么相当于检索10+1000=1010个页面才知道是否有重复。所以聚簇索引主键的插入速度要比非聚簇索引主键的插入速度慢很多。

让我们再来看看数据检索的效率，如果对上述两表进行检索，在使用索引的情况下(有些时候SQL Server执行计划会选择不使用索引，不过我们这里姑且假设一定使用索引)，对于聚簇索引检索，我们可能会访问10个索引页面外加1000个数据页面得 到结果(实际情况要比这个好)，而对于非聚簇索引，系统会从20个页面中找到符合条件的节点，再映射到1000个数据页面上(这也是最糟糕的情况)，比较 一下，一个访问了1010个页面而另一个访问了1020个页面，可见检索效率差异并不是很大。所以不管非聚簇索引也好还是聚簇索引也好，都适合排序，聚簇 索引仅仅比非聚簇索引快一点。

### 索引的优化

索引就是有排序的数据结构

- 单索引5个以内
- 禁止3个表以上的join
- 重复和冗余的索引不可以存在
- 字段数<=30
- 索引必须要有default值，不可为空
- verchar索引要制定其长度
- 对于行数超过500w与容量超过2G的表要进行分表

## 一些平时使用sql用法

**问题：在进行oracle数据操作时，某条语句update很久不出。**

这种只有update无法执行其他语句可以执行的其实是因为记录锁导致的，在oracle中，执行了update或者insert语句后，都会要求commit，如果不commit却强制关闭连接，oracle就会将这条提交的记录锁住。由于我的java程序中加了事务，之前debug到一半的时候我强制把工程终止了，这样就导致没有执行事务提交，所以oracle将代码中update那一条的记录锁了。可通过下面两步解决：

**1.首先查询锁定记录**

```
SELECT s.sid, s.serial# FROM v$locked_object lo, dba_objects ao, v$session s WHERE ao.object_id = lo.object_id AND lo.session_id = s.sid;
```

**2.然后删除之**

```
ALTER system KILL session 'SID,serial#'
```



**关于COUNT(1)与COUNT(*)区别**

一、count情况

1、count(1)：可以统计表中所有数据，不统计所有的列，用1代表代码行，在统计结果中包含列字段为null的数据；其实就是计算一共有多少符合条件的行。1并不是表示第一个字段，而是表示一个固定值。其实就可以想成表中有这么一个字段，这个字段就是固定值1，count(1)，就是计算一共有多少个1。

2、count(字段)：只包含列名的列，统计表中出现该字段的次数，并且不统计字段为null的情况；如果列为主键，count(列名)效率优于count，如果列不为主键，count(1)效率优于count(列名)，如果表中存在主键，count(主键列名)效率最优 ，如果表中只有一列，则count(*)效率最优，如果表有多列，且不存在主键，则count(1)效率优于c。

3、count(*)：统计所有的列，相当于行数，统计结果中会包含字段值为null的列；执行时会把星号翻译成字段的具体名字，效果也是一样的，不过多了一个翻译的动作，比固定值的方式效率稍微低一些。

二、count执行效率

列名为主键，count(列名)比count(1)快；列名不为主键，count(1)会比count(列名)快；

如果表中多个列并且没有主键，则count(1)的执行效率优于count(*)；

如果有主键，则select count(主键)的执行效率是最优的；如果表中只有一个字段，则select  count(*)最优。

阿里sql规范：

- 代码编写充分考虑执行速度最优的原则。
- 代码中需要添加必要的注释，以增强代码的可读性。
- 规范要求并非强制性约束开发人员的代码编写行为。实际应用中，在不违反常规要求的前提下，允许存在可以理解的偏差。
- SQL代码中应用到的所有SQL关键字、保留字都需使用全大写或小写，例如select/SELECT、from/FROM、where/WHERE、and/AND、or/OR、union/UNION、insert/INSERT、delete/DELETE、group/GROUP、having/HAVING和count/COUNT等。不能使用大小写混合的方式，例如Select或seLECT等方式。
- 4个空格为1个缩进量，所有的缩进均为1个缩进量的整数倍，按照代码层次对齐。
- 禁止使用select *操作，所有操作必须明确指定列名。
- 对应的括号要求在同一列的位置上。

## **Oracle**

以下因为我实习用的Oracle，所以基于oracle相关用法如下

```sql
-- merge into 范例
merge into STATIS T1 using dual on ((select count(*) from XXX where ID = '10001') > 0)
when matched then
	update set T1.AVERAGE = '20000' , T1.STD='2' , T1.INSERT_TIME = TO_DATE( TO_CHAR( SYSDATE, 'YYYY-MM-DD' ), 'YYYY-MM-DD HH24:MI:SS' ) WHERE CAMP_ID='10001'
when not matched then
	Insert (ID,AVERAGE, STD,INSERT_TIME) VALUES('10001','10000','0',TO_DATE( TO_CHAR( SYSDATE, 'YYYY-MM-DD' ), 'YYYY-MM-DD HH24:MI:SS' ));
	
-- CASE WHEN 范例
(
	CASE	
		WHEN T1.SEND_COUNT_TO_JYJ <= ( T2.AVERAGE + T2.STD ) 
			AND T1.SEND_COUNT_TO_JYJ >= ( T2.AVERAGE - T2.STD ) 
    	THEN
			'1' ELSE '2' 
	END 
) AS CAMP_TRUE_TYPE

-- UNION ALL 与 UNION区别也说一下吧，union all会去掉重复的数据

```

创建索引

# 创建索引

**单列索引：**

单列索引是基于单个列所建立的索引，比如：

create index 索引名 on 表名(列名);

**复合索引：**

复合索引是基于两列或是多列的索引。在同一张表上可以有多个索引，但是要求列的组合必须不同，比如：

create index emp_idx1 on emp (ename, job);

create index emp_idx1 on emp (job, ename);

查询表索引

select * from all_indexes where table_name = 'BMA_RT_CAMP_HOUR_SUM' 

