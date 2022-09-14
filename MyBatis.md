# MyBatis  

## 实际使用

一般，对于一个类 or 表的CRUD

```XML
<resultMap >
	< column = "DB_column" property = "VOProperty"/>
    ...
</resultMap>

<sql>
	all column
</sql>
<select>
	select 
    <include refid = "xxx"/>
    from cc
    where id in 
    <foreach item="item" index="index" collection="ids"
                 open="(" separator="," close=")">
            #{item}
    </foreach>
</select>

//按条件查询
<select>
	select
    <include refid = "xxx"/>
    from cc
    where is_delete = 0
    <if test="id != null">
     AND id = #{id,jdbcType = BIGINT}
    </if>
    ...一路if过去
    order by xxx DESC
</select>

<insert>
	insert into
    <trim prefix="(" suffix=")" suffixOverrides=","
          <if 不为空>
    		xx,
		  </if>
		  ...
	</trim>
    <trim prefix="values(" suffix=")" suffixOverrides=","
          <if 不为空>
    		xx,
		  </if>
		  ...
	</trim>      
</insert>

//批量操作也可以用ExecutorType.BATCH


//删除也不是真删除，可以设置一个字段is_delete
<update>
	update xx
    set is_delete='-1'
    where id = #{id}
</update>
```



1.Generator

config.xml的配置文件

```xml 
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd" >
<generatorConfiguration>
    <!-- 数据库驱动:选择你的本地硬盘上面的数据库驱动包-->
    <classPathEntry  location="C:\Program Files\Java\javafx-sdk-11.0.2\lib\other\mysql-connector-java-8.0.25.jar"/>
    <context id="DB2Tables"  targetRuntime="MyBatis3">
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!--数据库链接URL，用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver" connectionURL="jdbc:mysql://localhost:3306/lgdtest?useUnicode=true"
                        userId="root" password="1328910">
        </jdbcConnection>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成模型的包名和位置-->
        <javaModelGenerator targetPackage="com.example._002springboottest.web.entity" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!-- 生成映射文件的包名和位置-->
        <sqlMapGenerator targetPackage="com.mapper.xml" targetProject="src/main/resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>

        <!-- 生成DAO的包名和位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.example._002springboottest.web.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <!-- 要生成的表 tableName是数据库中的表名或视图名 domainObjectName是实体类名-->
        <table tableName="boke" domainObjectName="Boke" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"/>
    </context>
</generatorConfiguration>
```

环境：

- JDK1.8
- Mysql 8.0
- maven 3.6.1
- IDEA

回顾：

- JDBC
- Mysql
- javase
- Maven
- Junit

SSM框架：配置文件 推荐看官网文档

## 简介

### 1.1什么是MyBatis?

- 优秀的持久层框架
- 定制化sql，避免jdbc代码，手动设置参数
- 可以用简单的XML，注解来映射
- 2013已迁移到Github

如何获得Mybatis？

- maven仓库

  ```xml
  <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.4.6</version>
  </dependency>
  ```

  

- Github https://github.com/mybatis/mybatis-3

### 1.2持久层

数据持久化

持久化就是将程序的数据在持久状态和瞬时状态转化的过程

瞬时状态：由于内存**断电即失** 

### 1.3持久层

Dao , Service , Controller

- 完成持久化工作的代码块
- 层界限十分明显

### 1.4为什么用Mybatis

- 方便
- jdbc太麻烦了，所以要简化框架
- 把数据存入database
- 不用mybatis也行，不过更容易上手，技术没高低之分，人水平有高低之分
- 特性：只要俩jar文件和几个sql映射文件
- 把sql和代码分离，更好维护
- 最关键！！！用的人多，哈哈哈哈哈哈哈哈哈！

Spring SpringMVC SpringBoot

### 1.5 第一个Mybatis程序

搭环境-->导入Mybatis-->编写代码-->测试Junit

```sql
create DATABASE `mybatis`;

use `mybatis`;

create table `users`(
	`id` int(20) not null PRIMARY KEY,
	`name` varchar(30) default null,
	`pwd` VARCHAR(30) default null
)engine=innodb default charset = utf8;

insert into `users`(`id`,`name`,`pwd`) values
(1,'lgd','123456'),
(2,'dgg','123456'),
(3,'djj','123456')
```

#### maven依赖

```xml
<!--mysql-->
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.25</version>
        </dependency>

        <!--mybatis-->
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>

        <!--junit-->
        <!-- https://mvnrepository.com/artifact/junit/junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
```

#### 从 XML 文件中构建 SqlSessionFactory 的实例

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

#### utils

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {
    static {
        String resource = "mybatis-config.xml";
        try {
            InputStream inputStream = Resources.getResourceAsStream(resource);
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 测试示例

```java
@Test
public void test() {
    SqlSession sqlSession = MybatisUtils.getsqlSession();

    StudentMapper studentMapper = sqlSession.getMapper(StudentMapper.class);
    Student student = studentMapper.selectByPrimaryKey("001");
    System.out.println(student);
    sqlSession.close();
}
```

statement-->SqlSession

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
}
```

 错误：忘记写POJO实体的toString方法了

总体步骤

1. 配置工具类Utils
2. resource里配置文件
3. 实体类
4. 接口
5. mapper.xml
6. test

## 第一组CRUD ----2021.10.10

### 查

```xml
<select id="getUserById" parameterType="int" resultType="com.lgd.pojo.User">
    select * from mybatis.user where id = #{id};
</select>
```

### 增

```xml
<insert id="addUser" parameterType="com.lgd.pojo.User">
    insert into mybatis.user(id,name ,pwd) value (#{id},#{name},#{pwd})
</insert>
```

### 改

```xml
<update id="updateUser" parameterType="com.lgd.pojo.User">
    update mybatis.user set name = #{name}, pwd=#{pwd} where id = #{id};
</update>
```

### 删

```xml
<delete id="deleteUser" parameterType="int" >
    delete from mybatis.user where id = #{id};
</delete>
```

### Map作为参数的查询

Hashmap 里插入多个参数一并传入

### 模糊查询

like %xxx%

## 配置解析

### 具体配置大全

#### mybatis-config.xml

- ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
  
      <properties resource="db.properties"/>
  
      <typeAliases>
          <package name="com.lgd.pojo"/>
      </typeAliases>
  
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="${driver}"/>
                  <property name="url" value="${url}"/>
                  <property name="username" value="${username}"/>
                  <property name="password" value="${password}"/>
              </dataSource>
          </environment>
      </environments>
  
      <mappers>
          <mapper resource="com/lgd/dao/UserMapper.xml" />
      </mappers>
  </configuration>
  ```

#### db.properties

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=utf8&serverTimezone=GMT
username=root
password=1328910
```

#### utils--MybatisUtils

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;

    //获取sqlSessionFactory对象
    static {
        String resource = "mybatis-config.xml";
        try {
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //创建sqlSession实例
    public static SqlSession getsqlSession(){
        return sqlSessionFactory.openSession();
    }
}
```

#### 实体偷懒写法

- aliases type---aliases

- ```xml
  <typeAliases>
      <package name="com.lgd.pojo"/>
  </typeAliases>
  ```

- 注解pojo类@Aliases("xxxx")

#### 字段属性不一致的话

起别名

pwd as password

#### 结果集映射

resultMap 

​	result  column--字段 property----属性 

## 日志

log4j

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```



stdout_logging 标准日志输出

## 分页查询

可以减少数据的处理量，使用Limit分页

selet * *from user limit 0,2(每页显示2个从第0个开始查)

​											4，-1（从第四个开始查到最后一个）bug，已经被修复

### 通过分页插件

pagehelper  -- ---学Spring时候学

## 注解

### 简单增删查改如下

```java
@Select("select * from mybatis.user where id = #{id}")
User getUserById2(@Param("id") int id);

@Insert("insert into user(id,name,pwd) values (#{id},#{name},#{password})")
int addUser2(User user);

@Update("update user set name=#{name},pwd=#{password} where id=#{id}")
int updateUser2(User user);

@Delete("delete from user where id = #{id}")
int deleteUser2(@Param("id") int id);
```

### lombok

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
```

不用写getter setter and equals

## mybatis-generator

### generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd" >
<generatorConfiguration>
    <!-- 数据库驱动:选择你的本地硬盘上面的数据库驱动包-->
    <classPathEntry  location="C:\Program Files\Java\javafx-sdk-11.0.2\lib\other\mysql-connector-java-8.0.25.jar"/>
    <context id="DB2Tables"  targetRuntime="MyBatis3">
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!--数据库链接URL，用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver" connectionURL="jdbc:mysql://localhost:3306/javaweb-test?useUnicode=true"
                        userId="root" password="1328910">
        </jdbcConnection>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成模型的包名和位置-->
        <javaModelGenerator targetPackage="com.lgd.pojo" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!-- 生成映射文件的包名和位置-->
        <sqlMapGenerator targetPackage="com.lgd.dao" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>

        <!-- 生成DAO的包名和位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.lgd.dao" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <!-- 要生成的表 tableName是数据库中的表名或视图名 domainObjectName是实体类名-->
        <table tableName="student" domainObjectName="Student" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"/>
    </context>
</generatorConfiguration>
```

### generator.properties

```properties
jdbc.driverClass=com.mysql.cj.jdbc.Driver
jdbc.connectionURL=jdbc:mysql://localhost:3306/javaweb-test
jdbc.userId=root
jdbc.password=1328910

#c3p0
jdbc.maxPoolSize=50
jdbc.minPoolSize=10
jdbc.maxStatements=100
jdbc.testConnection=true

# 通用Mapper配置
mapper.plugin = tk.mybatis.mapper.generator.MapperPlugin
mapper.Mapper = tk.mybatis.mapper.common.Mapper
```

### pom依赖

```xml
<build>
<!--这条在springboot框架里别写，不然框架找不到配置-->    
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
        <plugins>
<!--            <plugin>-->
<!--                &lt;!&ndash; 打包插件 &ndash;&gt;-->
<!--                <groupId>org.springframework.boot</groupId>-->
<!--                <artifactId>spring-boot-maven-plugin</artifactId>-->
<!--                <version>2.5.3</version>-->
<!--            </plugin>-->
<!--            <plugin>-->
<!--                <groupId>org.apache.maven.plugins</groupId>-->
<!--                <artifactId>maven-compiler-plugin</artifactId>-->
<!--                <version>3.8.1</version>-->
<!--                <configuration>-->
<!--                    <verbose>true</verbose>-->
<!--                </configuration>-->
<!--            </plugin>-->
            <plugin>
                <!--Mybatis-generator插件,用于自动生成Mapper和POJO-->
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.2</version>
                <configuration>
                    <!--配置文件的位置-->
                    <configurationFile>src/main/resources/mybatis/generatorConfig.xml</configurationFile>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
                <executions>
                    <execution>
                        <id>Generate MyBatis Artifacts</id>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>org.mybatis.generator</groupId>
                        <artifactId>mybatis-generator-core</artifactId>
                        <version>1.3.2</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
```

## 复杂查询

### 多对一：

学生多对一

如每个Student 都有一个teacher

```
方式一：通过子查询进行查询
思路：
    1.查询所有的学生信息
    2.根据查询出来的学生的tid，寻找对应的老师！
复杂的属性，我们需要单独处理
     对象：association
     集合：collection
```

```xml
<select id="getAllStudent" resultMap="StudentAndTeacher">
        select * from student;
    </select>
    <resultMap id="StudentAndTeacher" type="Student">
        <association property="teacher" column="tid" javaType="Teacher" select="getTeacherById"/>
    </resultMap>
    <select id="getTeacherById" resultType="Teacher">
        select * from teacher where id = #{tid};
    </select>
<!--方式二：按照结果嵌套处理-->
    <resultMap id="StudentTeacher2" type="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <association property="teacher" javaType="Teacher">
            <result property="name" column="tname"/>
        </association>
    </resultMap>
<!--    <resultMap id="StudentTeacher2" type="Student">-->
<!--        <result property="id" column="sid"/>-->
<!--        <result property="name" column="sname"/>-->
<!--        <association property="teacher" column="tid" javaType="Teacher" select="getTeacherById"/>-->
<!--    </resultMap>-->
```

### 一对多：

如一个Teacher里有LIst<Student>

方法一：嵌套查询

```xml
<select id="getTeacherStudents2" resultMap="TeacherDeStudents2">
    select  * from teacher where id = #{tid};
</select>

<resultMap id="TeacherDeStudents2" type="Teacher">
    <result property="id" column="id"/>
    <collection property="students" javaType="ArrayList" ofType="Student" column="id" select="getStudentByTid"/>
</resultMap>

<select id="getStudentByTid" resultType="Student">
    select * from student where tid=#{id};
</select>
```

方法二：结果查询,更快

```xml
<select id="getTeacherStudents" resultMap="TeacherDeStudents">
    select s.id sid,s.name sname,s.tid tid,t.name tname from student s,teacher t where s.tid=#{tid} and t.id=s.tid;
</select>

<resultMap id="TeacherDeStudents" type="Teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <collection property="students" ofType="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
    </collection>
</resultMap>
```

association:对象 

<association property="teacher" column="tid" javaType="Teacher" select="getTeacherById"/>

javaType实体类的类型

collection集合 List

<collection property="students" javaType="ArrayList" ofType="Student" column="id" select="getStudentByTid"/>

ofType:集合中的类型List<这里>

面试必问：

- Mysql引擎
- InnoDB底层原理
- 索引
- 索引优化

## 动态SQL

动态sql根据不同条件生成不同的sql语句

### 搭建环境

UUID，不推荐用

```java
package com.lgd.utils;

import java.util.UUID;

@SuppressWarnings("all")
public class IdUtils {
    public static String getId() {
        return UUID.randomUUID().toString().replace("-","");
    }
}
```

### IF

```xml
<select id="getBlogIF" parameterType="map" resultType="blog">
    select * from blog where 1 = 1
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="views!= null">
        and views = #{views}
    </if>
</select>
```

Map中put了啥加啥，没有东西就全查

其中if的模块可以用sql Id拼接

```xml
<include refid="if-title-author"></include>

<sql id="if-title-author">
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="views!= null">
        and views = #{views}
    </if>
</sql>
```

### Choose , when , otherwise

类似于java中的switch语句，选择一个执行

```xml
<select id="getBlogChoose" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <choose>
            <when test="title != null">
                title = #{title}
            </when>
            <when test="views!=null">
                views = #{views}
            </when>
            <otherwise>
                1 = 1
            </otherwise>
        </choose>
    </where>
</select>
```

### updata之SET

它可以帮你删除一些会造成sql语句错误的“，”

### trim



### Foreach

```xml
<select id="getBlogForeach" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <foreach collection="ids" item="id" open="(" close=")" separator="or">
            id = #{id}
        </foreach>
    </where>
</select>
```

## 缓存

### 什么是缓存？

- 存在于内存中的经常访问但基本不改变的数据
- 将用户经常访问的数据放在其中，这样访问的时候关系型数据库就不用到磁盘里去读而是到内存中读，增加查询效率，高并发

Mybatis可以定制和配置缓存，它有两级缓存，一级和二级

- 默认情况下就SqlSession级别的缓存，（本地缓存，一级缓存）
- 二级缓存需要手动开启，基于namespace级别，一个mapper
- 提高扩展性，配置Cache

### 清除策略：

LRU-最近最少使用

FIFO-先进先出

SOFT-软引用，基于垃圾回收器状态

Weak-弱引用，更积极基于垃圾收集器状态

默认LRU 

### 缓存失效：

- 增删改完会刷新缓存，因为增删改可能会改变原来的数据，为了数据一致性，刷新缓存
- 查询不同的东西
- 通过不同的Mapper.xml查询
- 手动清除缓存sqlSession.clearCache();

小结：一级缓存默认开启，只在一次SQLSESSION中有效，只在一次开启到关闭中有用

比如你用户一直刷新一个东西要取的时候可以用到

### 开启二级缓存

- 开启全局变量，cacheEnabled=True
- <cache

​	eviction="FIFO"

​	flushInterval="60000"

​	size="512"

​	readOnly="true"/>

### 二级缓存机制：

- 二级缓存作用域为namespace,一个mapper
- 当一个会话关闭，这个会话的一级缓存就没了，但是我们想要的数据会被存到二级缓存中
- 新会话查询可以到二级缓存中获取内容
- 不同mapper查到的数据会放到自己的对应缓存map中



# MyBatis-Plus

虽然用过，稍微补一下吧

JPD tk-mapper MyBatisPlus,自动生成CRUD代码

特性：

- 强大CRUD：通过内置通用Mapper，通用Service，实现单表大部分CRUD操作
- Lambda形式调用
- 主键自动生成
- 内置分页插件
- 内置性能分析插件
- 内置全局拦截插件

使用

注意启动类加上类似@MapperScan("com.lgd.mapper")

```xml
		<dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.3.0</version>
        </dependency>
```

```java
public interface UserMapper extends BaseMapper<User> {

}
```

相关注解：

- ```java
  @TableName("sys_user")//表名
  @TableId//主键
  @TableField("nickname")//数据库对应字段名
  ```

### 简单进行crud

```java
	@Test
    public void testSelect() {
        System.out.println(("----- selectAll method test ------"));
        List<User> userList = userMapper.selectList(null);
        userList.forEach(System.out::println);
    }

    @Test
    public void testInsert() {
        System.out.println(("----- selectAll method test ------"));
        User user = new User(11L,"lgd",17,"lgd@lgd.com");
        int insert = userMapper.insert(user);
        System.out.println(insert);
    }

    @Test
    public void testUpdate() {
        System.out.println(("----- selectAll method test ------"));

        User user = new User(11L,"lgd",18,"lgd@lgd.com");
        int insert = userMapper.updateById(user);
        System.out.println(insert);
    }

    @Test
    public void testDelete() {
        System.out.println(("----- selectAll method test ------"));

        User user = new User(11L,"lgd",18,"lgd@lgd.com");
        Map<String, Object> map = new HashMap<>();
        map.put("id",11l);

        int insert = userMapper.deleteByMap(map);
        System.out.println(insert);
    }
```

配置日志

```yml
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

查询扩展

```java
//    查询操作
    @Test
    public void TestSelectById(){
        User user = mapper.selectById(1);
        System.out.println(user);
    }
//    测试批量查询
    public void testSelectByBatchId(){
        List<Integer> idList = Arrays.asList(1, 2, 3);
        List<User> users = mapper.selectBatchIds(idList);
        users.forEach(System.out::println);
    }
//    条件查询
    @Test
    public void testSelectByBatchIds(){
        HashMap<String,Object> map=new HashMap<>();
 
        map.put("name","狂徒张三");
        map.put("age",13);
 
        List<User> users = mapper.selectByMap(map);
        users.forEach(System.out::println);
    }
//    测试分页查询
    @Test
    public void testPage(){
        Page<User> page=new Page<>(1,10);
        mapper.selectPage(page,null);
 
        page.getRecords().forEach(System.out::println);
        System.out.println(page.getTotal());
    }
```

总的Configuration配置

```java
@Configuration
@EnableTransactionManagement
@MapperScan("com.lgd.mapper")
public class MyBatisPlusConfig {

/*    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {

        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        // 添加分页插件
        PaginationInnerInterceptor pageInterceptor = new PaginationInnerInterceptor();
        // 设置请求的页面大于最大页后操作，true调回到首页，false继续请求。默认false
        pageInterceptor.setOverflow(false);
        // 单页分页条数限制，默认无限制
        pageInterceptor.setMaxLimit(500L);
        // 设置数据库类型
        pageInterceptor.setDbType(DbType.MYSQL);

        interceptor.addInnerInterceptor(pageInterceptor);
        return interceptor;
    }*/
    //    性能分析插件 3.0.5有
    @Bean
    @Profile({"dev","test"})
    public PerformanceInterceptor performanceInterceptor(){
        PerformanceInterceptor performanceInterceptor=new PerformanceInterceptor();
        performanceInterceptor.setMaxTime(100);
        performanceInterceptor.setFormat(true);
        return performanceInterceptor;
    }

    // 乐观锁,字段+@Version
    @Bean
    public OptimisticLockerInterceptor optimisticLockerInterceptor(){
        return new OptimisticLockerInterceptor();
    }

    //    分页插件
    @Bean
    public PaginationInterceptor paginationInterceptor(){
        return new PaginationInterceptor();
    }
}
```

### 乐观锁

乐观锁的实现方式：

1.取出记录时，获取当前 version

2.更新时，带上这个version

3.执行更新时， set version = newVersion where version = oldVersion

4.如果 version 不对，就更新失败
1、给数据库中增加version字段


 2、我们实体类加对应的字段,并添加version注解

    @Version
    private Integer version;
3.到config类中去注册组件

#注册乐观锁插件

```java
@Bean
public OptimisticLockerInterceptor optimisticLockerInterceptor(){
    return new OptimisticLockerInterceptor();
}
```

4.使用修改操作进行测试

### 分页查询插件

```java
//1.到config类中注册插件

//    分页插件
    @Bean
    public PaginationInterceptor paginationInterceptor(){
        return new PaginationInterceptor();
    }
//2.直接使用Page对象

//    测试分页查询
    @Test
    public void testPage(){
        Page<User> page=new Page<>(1,10);
        mapper.selectPage(page,null);
    page.getRecords().forEach(System.out::println);
    System.out.println(page.getTotal());
	}
```
### 性能插件



### wrapper条件改造器

```java
	@Test
    void test1(){
//        查询name不为空的用户，并且邮箱不为空的用户，年龄大于等于13
        QueryWrapper<User> wrapper=new QueryWrapper<>();
        wrapper
                .isNotNull("name")
                .isNotNull("email")
                .ge("age",13);
        mapper.selectList(wrapper).forEach(System.out::println);
    }
    @Test
    void test2(){
//      查询名字李云
        QueryWrapper<User> wrapper=new QueryWrapper<>();
        wrapper.eq("name","李云");
        User user = mapper.selectOne(wrapper);
        System.out.println(user);
    }
    @Test
    void test3(){
        // 查询年龄在 20 ~ 30 岁之间的用户
        QueryWrapper<User> wrapper=new QueryWrapper<>();
        wrapper.between("age",20,30);
        Integer user = mapper.selectCount(wrapper);
        System.out.println(user);
    }
    @Test
    void test4(){
//        模糊查询
        QueryWrapper<User> wrapper=new QueryWrapper<>();
        wrapper
                .notLike("name","e")
                .likeRight("email","qq");
        List<Map<String, Object>> user = mapper.selectMaps(wrapper);
//        Integer user = mapper.selectCount(wrapper);
        System.out.println(user);
    }
    @Test
    void test5(){
        QueryWrapper<User> wrapper=new QueryWrapper<>();
//        id在子查询中查出来
       wrapper.inSql("id","select id from user where id<3");
//        Integer user = mapper.selectCount(wrapper);
        List<Object> objects = mapper.selectObjs(wrapper);
        System.out.println(objects);
    }
    @Test
    void test6(){
//        通过id进行排序
        QueryWrapper<User> wrapper=new QueryWrapper<>();
        wrapper.orderByAsc("id");
        List<User> users = mapper.selectList(wrapper);
        users.forEach(System.out::println);
    }
```

### 代码生成器

```java
	@Test
    public void testAuto() {
    //构建一个代码自动生成器对象
    AutoGenerator mpg = new AutoGenerator();
    //配置策略
    //1、全局配置
    GlobalConfig gc = new GlobalConfig();
    String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath+"/src/main/java");
        gc.setAuthor("LGD");
        gc.setOpen(false);
        gc.setFileOverride(false);
        gc.setServiceName("%sService");
        gc.setIdType(IdType.ID_WORKER);
        mpg.setGlobalConfig(gc);
    // 数据源配置
    DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/mb_p_test?charset=utf8mb4&useSSL=false");
            dsc.setDriverName("com.mysql.cj.jdbc.Driver") ;
        dsc.setUsername("root");
        dsc.setPassword("1328910");
        dsc.setDbType(DbType.MYSQL);
        mpg.setDataSource(dsc);
    // 包配置
    PackageConfig pc = new PackageConfig();
        pc.setModuleName("User");
        pc.setParent("com.lgd");
        pc.setEntity("pojo");
        pc.setMapper("mapper");
        pc.setService("service");
        pc.setController("controller");
        mpg.setPackageInfo(pc);
    //策略配置
    StrategyConfig strategy = new StrategyConfig();
        strategy.setInclude("user");//设置映射的表名
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        strategy.setEntityLombokModel(true);
        mpg.setStrategy(strategy);
    //自动填充
        mpg.execute();
    }
```

![image-20220824095657544](C:\Users\L\Desktop\文档\photo\image-20220824095657544.png)

自动生成成功,确实啊，确实比Mybatis-generator好用一万倍

作为偷懒工具，就先看到这了

