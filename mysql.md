# mysql

### 分页与排序

###### limit 



###### order by

升序ASC , 降序 DESC

##### 一次性插入百万级数量示例

set global log_bin_trust_function_creators=TRUE;



DELIMITER $$
-- 写函数之前必须要写，标志
CREATE FUNCTION mock_data2 ()
RETURNS INT
BEGIN
DECLARE num INT DEFAULT 1000;
DECLARE i INT DEFAULT 0;
WHILE i<num DO
INSERT INTO `app_user`(`name`,`email`,`phone`,`gender`)VALUES(CONCAT('用户',i),'1017276522@qq.com','123456789',FLOOR(RAND()*2));
SET i=i+1;
END WHILE;
RETURN i;
END;
SELECT mock_data2() -- 执行此函数 生成一百万条数据

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
