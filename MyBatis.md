# MyBatis  

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
