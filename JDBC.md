# JDBC

### 	基础经典增删查改(jsp)

##### 		1.增

```java
Class.forName("com.mysql.cj.jdbc.Driver");
        System.out.println("Driver loaded");
        Connection connection = DriverManager.getConnection
                ("jdbc:mysql://localhost:3306/javaweb-test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT", "root", "1328910");
        Statement stmt = connection.createStatement();    //Create a statement
        String sql = "insert into student(id, name, sex) values (?,?,?);";    //insert
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setString(1, "003");
        preparedStatement.setString(2, "lgd");
        preparedStatement.setString(3, "男");
        int i = preparedStatement.executeUpdate();
        if (i > 0) {
            out.println("成功添加" + i + "行");
        }
        preparedStatement.close();
        stmt.close();
        connection.close();}
    catch (SQLException e){
            e.printStackTrace();
        }
```



##### 2.删

```java
try {
        Class.forName("com.mysql.cj.jdbc.Driver");
        System.out.println("Driver loaded");
        Connection connection = DriverManager.getConnection
                ("jdbc:mysql://localhost:3306/javaweb-test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT", "root", "1328910");
        Statement stmt = connection.createStatement();    //Create a statement
        String sql = "delete from student where id = '003';";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        int i = preparedStatement.executeUpdate();
        if (i > 0) {
            out.println("成功删除" + i + "行");
        }
        preparedStatement.close();
        stmt.close();
        connection.close();}
    catch (SQLException e){
        e.printStackTrace();
    }
```



##### 3.查

```java
try {
    Class.forName("com.mysql.cj.jdbc.Driver");
    System.out.println("Driver loaded");
    Connection connection = DriverManager.getConnection
            ("jdbc:mysql://localhost:3306/javaweb-test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT", "root", "1328910");
    System.out.println("Database connected");//Create a statement
    Statement stmt = connection.createStatement();
    String sql = "select id,name from student where sex = '女'";
        ResultSet rset = stmt.executeQuery(sql);
        while (rset.next()){
            out.println("id=" + rset.getString(1));
            out.println("name=" + rset.getString(2));
            out.println("sex=" + rset.getString(3));
            String id = rset.getString("id");
            String name = rset.getString("name");
            out.println(id + " " + name + "<br>");
        }
        rset.close();
        stmt.close();
        connection.close();}
    catch (SQLException e){
    e.printStackTrace();
    }
```



##### 4.改

```java
try {
        Class.forName("com.mysql.cj.jdbc.Driver");
        System.out.println("Driver loaded");
        Connection connection = DriverManager.getConnection
                ("jdbc:mysql://localhost:3306/javaweb-test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT", "root", "1328910");
        String sql = "update student set sex = '女' where id = '002';";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        int i = preparedStatement.executeUpdate();
        if (i > 0) {
            out.println("成功修改" + i + "行");
        }
        preparedStatement.close();
        connection.close();}
    catch (SQLException e){
        e.printStackTrace();
    }
```

##### 5.preparestatment版

```java
public ArrayList getListByCid(String cid) throws SQLException, ClassNotFoundException {
        ArrayList scores = new ArrayList();
        try{
            init();
            String sql = "select score.id,student.name,score.type,score.scorenum from score,student where  score.id = student.id and score.cid = ?";
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setString(1, cid);
//            Statement preparedStatement = connection.createStatement();
            ResultSet rset = preparedStatement.executeQuery();
            while (rset.next()) {
                Score score = new Score();
                score.setId(rset.getString("id"));
                score.setType(rset.getString("type"));
                score.setName(rset.getString("name"));
                score.setScorenum(rset.getString("scorenum"));
                scores.add(score);
            }
            rset.close();
            preparedStatement.close();
            connection.close();
        } catch (SQLException e){
        e.printStackTrace();
        }
        return scores;
    }

    public int updateScoes(String cid,String[] type,String[] id,String[] score) throws SQLException, ClassNotFoundException {
        int num = 0;
        init();
        String sql = "update score set scorenum = ? where id = ? and type = ? and cid = ?;";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        for (int i = 0; i < id.length; i++) {
            preparedStatement.setString(1, score[i]);
            preparedStatement.setString(2, id[i]);
            preparedStatement.setString(3, type[i]);
            preparedStatement.setString(4, cid);
            preparedStatement.executeUpdate();
            num++;
        }
        preparedStatement.close();
        connection.close();
        return num;
    }
```

##### 6.关于空指针问题

很显然nmd注意创建的对象不要自己弄成Null！！！！！

弄成null了还set个鬼！！！

## JDBC基础

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

单索引5个以内

禁止3个表以上的join

重复和冗余的索引不可以存在

字段数<=30

索引必须要有default值，不可为空

verchar索引要制定其长度

对于行数超过500w与容量超过2G的表要进行分表

