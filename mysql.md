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