## 一.  MyBatis 搭建

##### 1. 创建Maven项目，引入依赖

```xml
<dependencies>
   		<!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.4.12</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.24</version>
            <scope>compile</scope>
        </dependency>

        <!--Mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.16</version>
        </dependency>
    
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.1</version>
        <scope>test</scope>
    </dependency>
    
    <!-- MySQL驱动 -->
    <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>
</dependencies>
```



##### 2. 创建MyBatis的配置文件 

```xml
<!-- 
mybatis-config.xml 
src/main/resources/mybatis-config.xml
-->

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置连接数据库的环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mysql_text_one@localhost"/>
                <property name="username" value="root"/>
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入映射文件-->
    <mappers>
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
```



##### 3. 创建表

##### 4. 创建表对应的POJO

```java
package com.chr.MyBatis.POJO;

import lombok.Data;

@Data
public class user {
    private Integer id;
    private String account;
    private String password;
    private String nickname;

    public user() {
    }

    public user(Integer id, String account, String password, String nickname) {
        this.id = id;
        this.account = account;
        this.password = password;
        this.nickname = nickname;
    }
}
```



##### 5. 创建对应 mapper 接口

```java
package com.chr.MyBatis.mapper;

import com.chr.MyBatis.POJO.user;

import java.util.List;

public interface UserMapper {
    
    int insertUser();

    int delete();

    int update();
    
    List<user> select();
}

```



##### 6. 创建MyBatis的映射文件

> 1、映射文件的命名规则：
>
> 表所对应的实体类的类名+Mapper.xml
>
> 例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml
>
> 
>
> 因此一个映射文件对应一个实体类，对应一张表的操作
>
> MyBatis映射文件用于编写SQL，访问以及操作表中的数据
>
> MyBatis映射文件存放的位置是src/main/resources/mappers目录下

> 2、MyBatis中可以面向接口操作数据，要保证两个一致：
>
> ​		a. <font color="red">mapper接口的全类名和映射文件的命名空间（namespace）保持一致</font>
>
> ​		b. <font color="red">mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致</font>



```xml
<!-- 
UserMapper.xml 
src/main/resources/mappers/UserMapper.xml
-->

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="mapper接口的全类名">
    <!--增删改查-->
    <insert id="insertUser">
        insert into t_user values(1,"a1","b1","c1");
    </insert>
</mapper>
```



##### 7. 在配置文件中引入映射信息

```xml
<!--引入映射文件-->
<mappers>
    <mapper resource="mappers/UserMapper.xml"/>
</mappers>
```



##### 8.1 测试：

```java
package com.chr.MyBatis;

import com.chr.MyBatis.mapper.UserMapper;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

/**
 * @Author: 程浩然
 * @Create: 2024/9/5 - 13:48
 * @Description: 入门测试
 */
public class test {
    @Test
    public void UserAddTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder=new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现添加 sql 语句
        int result = mapper.insertUser();
        System.out.println(result);
        // 提交事务
        sqlSession.commit();
    }
}
```



##### 8.2 测试优化：

1. 导入日志jar包

2. 写日志配置信息

 ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
   <log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
       <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
           <param name="Encoding" value="UTF-8" />
           <layout class="org.apache.log4j.PatternLayout">
               <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS}
   %m (%F:%L) \n" />
           </layout>
       </appender>
       <logger name="java.sql">
           <level value="debug" />
       </logger>
       <logger name="org.apache.ibatis">
           <level value="info" />
       </logger>
       <root>
           <level value="debug" />
           <appender-ref ref="STDOUT" />
       </root>
   </log4j:configuration>
 ```

3. 设置自动提交事务

   ```java
   package com.chr.MyBatis;
   
   import com.chr.MyBatis.mapper.UserMapper;
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   import org.junit.Test;
   
   import java.io.IOException;
   import java.io.InputStream;
   
   /**
    * @Author: 程浩然
    * @Create: 2024/9/5 - 13:48
    * @Description: 入门测试
    */
   public class test {
       @Test
       public void UserAddTest() throws IOException {
           // 加载核心配置文件
           InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
           // 获取 SqlSessionFactoryBuilder
           SqlSessionFactoryBuilder sqlSessionFactoryBuilder=new SqlSessionFactoryBuilder();
           // 获取 SqlSessionFactory
           SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
           // 获取 SqlSession,设置自动提交事务
           SqlSession sqlSession = sqlSessionFactory.openSession(true);
           // 获取接口对象
           UserMapper mapper = sqlSession.getMapper(UserMapper.class);
           // 实现添加 sql 语句
           int result = mapper.insertUser();
           System.out.println(result);
       }
   }
   ```

##### 8.3 进一步测试--增删改查：

```java
// 测试类
package com.chr.MyBatis;

import com.chr.MyBatis.POJO.user;
import com.chr.MyBatis.mapper.UserMapper;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

/**
 * @Author: 程浩然
 * @Create: 2024/9/5 - 13:48
 * @Description: 入门测试
 */
public class test {


    @Test
    public void UserInsertTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession,设置自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现添加 sql 语句
        System.out.println(mapper.insertUser());
    }

    @Test
    public void UserDeleteTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession,设置自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现删除 sql 语句
        System.out.println(mapper.delete());
    }

    @Test
    public void UserUpDateTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession,设置自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现修改 sql 语句
        System.out.println(mapper.update());
    }

    @Test
    public void UserSelectTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession,设置自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现查询 sql 语句
        for (user user : mapper.select())
            System.out.println(user);
    }
}
```

```xml
<!--UserMapper.xml-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.chr.MyBatis.mapper.UserMapper">
    <!--增加-->
    <insert id="insertUser">
        insert into t_user values(2,"a2","b2","c2");
    </insert>

    <!--删除-->
    <delete id="delete">
        delete from t_user where id = 1;
    </delete>
    <!--修改-->
    <update id="update">
        update t_user set account = '啊', nickname='d1' where id = 2;
    </update>
    <!--查询-->
    <select id="select" resultType="com.chr.MyBatis.POJO.user">
    select * from t_user;
    </select>
</mapper>
```

```java
// UserMapper 接口
package com.chr.MyBatis.mapper;

import com.chr.MyBatis.POJO.user;

import java.util.List;

/**
 * @Author: 程浩然
 * @Create: 2024/9/5 - 13:17
 * @Description: UserMapper 接口
 */
public interface UserMapper {
    /**
     * 增
     */
    int insertUser();

    /**
     * 删
     */
    int delete();


    /**
     * 改
     */
    int update();

    /**
     * 查
     */
    List<user> select();
}
```

进行查询时必须设置 `resultType` 或 `resultMap` 来让MyBatis知道返回结果类型

`resultType`：设置默认的映射关系（要求类的属性名和表的字段名一致）

`resultMap`： 设置自定义的映射关系（可能有多对一或一对多的情况）



## 二. 核心配置文件详解

核心配置文件中的标签必须按照固定的顺序：

properties?,settings?,typeAliases?,typeHandlers?,objectFactory?,objectWrapperFactory?,reflectorF

actory?,plugins?,environments?,databaseIdProvider?,mappers?

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//MyBatis.org//DTD Config 3.0//EN"
"http://MyBatis.org/dtd/MyBatis-3-config.dtd">
<configuration>
    
<!--引入properties文件，此时就可以${属性名}的方式访问属性值-->
<properties resource="jdbc.properties"></properties>

<settings>
<!--将表中字段的下划线自动转换为驼峰-->
<setting name="mapUnderscoreToCamelCase" value="true"/>
    
<!--开启延迟加载-->
<setting name="lazyLoadingEnabled" value="true"/>
</settings>

    
<typeAliases>
<!--
typeAlias：设置某个具体的类型的别名
属性：
type：需要设置别名的类型的全类名
alias：设置此类型的别名，若不设置此属性，该类型拥有默认的别名，即类名且不区分大小写
	   若设置此属性，此时该类型的别名只能使用alias所设置的值
-->

<!--<typeAlias type="com.atguigu.mybatis.bean.User"></typeAlias>-->

<!--<typeAlias type="com.atguigu.mybatis.bean.User" alias="abc">

</typeAlias>-->

<!--以包为单位，设置改包下所有的类型都拥有默认的别名，即类名且不区分大小写-->
<package name="com.atguigu.mybatis.bean"/>
</typeAliases>

<!--
environments：设置多个连接数据库的环境
属性：
default：设置默认使用的环境的id
-->

<environments default="mysql_test">
<!--
environment：设置具体的连接数据库的环境信息
属性：id：设置环境的唯一标识，可通过environments标签中的default设置某一个环境的id，表示默认使用的环境

-->
<environment id="mysql_test">
<!--

transactionManager：设置事务管理方式
属性：type：设置事务管理方式，type="JDBC|MANAGED"
type="JDBC"：设置当前环境的事务管理都必须手动处理
type="MANAGED"：设置事务被管理，例如spring中的AOP

-->
<transactionManager type="JDBC"/>

    
    
<!--

dataSource：设置数据源

属性：
type：设置数据源的类型，type="POOLED|UNPOOLED|JNDI"
type="POOLED"：使用数据库连接池，即会将创建的连接进行缓存，下次使用可以从缓存中直接获取，不需要重新创建

type="UNPOOLED"：不使用数据库连接池，即每次使用连接都需要重新创建
type="JNDI"：调用上下文中的数据源

-->

<dataSource type="POOLED">

<!--设置驱动类的全类名-->

<property name="driver" value="${jdbc.driver}"/>

<!--设置连接数据库的连接地址-->

<property name="url" value="${jdbc.url}"/>

<!--设置连接数据库的用户名-->

<property name="username" value="${jdbc.username}"/>

<!--设置连接数据库的密码-->

<property name="password" value="${jdbc.password}"/>

</dataSource>

</environment>

</environments>

    
    
<!--引入映射文件-->

<mappers>

<mapper resource="UserMapper.xml"/>

<!--

以包为单位，将包下所有的映射文件引入核心配置文件

注意：此方式必须保证mapper接口和mapper映射文件必须在相同的包名下

-->

<package name="com.atguigu.mybatis.mapper"/>

</mappers>

</configuration>
```



## 三. MyBatis 获取参数值的两种方式





## 四. MyBatis的各种查询功能





## 五. 特殊sql的执行

