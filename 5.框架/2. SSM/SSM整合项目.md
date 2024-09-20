### 一. 创建 Maven 项目，搭建 web 框架

- 设置Maven版本修改为自己下载的版本
- 创建 webapp-WEB-INF 目录，补全目录



### 二. 设置 tomcat 

### 三. 导入 Maven 依赖

```xml
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.chr.website</groupId>
    <artifactId>website</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging><!--打 war 包-->

    <!--声明版本-->
    <properties>
        <spring.version>6.1.6</spring.version>
    </properties>


    <!--添加依赖-->
    <dependencies>
        <!--spring-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!--springmvc-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- Mybatis核心 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.16</version>
        </dependency>

        <!--mybatis和spring的整合包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>3.0.3</version>
        </dependency>

        <!-- 连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.9</version>
        </dependency>

        <!-- MySQL驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>

        <!--分页插件-->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.3.1</version>
        </dependency>

        <!--java和json相互转换-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.17.2</version>
        </dependency>

        <!--文件上传-->
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>


        <!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.4.12</version>
        </dependency>

        <!-- ServletAPI -->
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>6.0.0</version>
            <scope>provided</scope>
        </dependency>

        <!-- Spring6和Thymeleaf整合包 -->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring6</artifactId>
            <version>3.1.2.RELEASE</version>
        </dependency>

        <!-- 用于直接生成get，set，tostring方法的-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.24</version>
            <scope>compile</scope>
        </dependency>

        <!--测试类-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```



四. 搭建 MVC 项目结构

```tex
├──pom.xml                                      # Maven 项目管理文件
├──src                                          # 根目录
├── main                                        # 项目主要代码
│   ├── java                                   # java源代码目录
│   │   └── com.chr.website                    # 开发者代码主目录
│   │       ├── config                         # 存放配置类
│   │       ├── controller                     # 存放控制层代码
│   │       ├── dao                            # 存放 Dao 层代码
│   │       ├── entity                         # 存放实体类
│   │       ├── exception                      # 存放异常处理类
│   │       ├── interceptor                    # 拦截器
│   │       ├── mapper                         # Mybatis 的 Mapper 接口
│   │       ├── service                        # 业务逻辑层
│   │       └── utils                          # 工具类
│   ├── resources                              # 资源目录，存放配置文件
│   │		└─ mappers                         # Mybatis 的mapper配置文件
│   └── webapp                                 # 存放 WEB 相关资源和配置
│       └── static                             # 存放静态资源（css，js，font）
│       └── WEB-INF
│           └── views                          # 存放视图
│
└── test                                       # 用于测试
```





### 五. 创建配置文件

```java
package com.chr.website.config;

import com.github.pagehelper.PageInterceptor;
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.mapper.MapperScannerConfigurer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.*;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.sql.DataSource;
import java.util.Properties;

/**
 * @Author: 程浩然
 * @Create: 2024/9/8 - 9:11
 * @Description: SpringConfig.java 代替 Spring.xml
 */
// 表明配置类
@Configuration
// 开启扫描，排除 ControllerAdvice 和 Controller 注解的组件
@ComponentScan(basePackages = "com.chr.website", excludeFilters = {@ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {org.springframework.stereotype.Controller.class, org.springframework.web.bind.annotation.ControllerAdvice.class})})
// 告诉资源路径
@PropertySources({@PropertySource("classpath:jdbc.properties")})
// 启用基于注解的事务管理
@EnableTransactionManagement
public class SpringConfig {
    /**
     * 配置数据源
     */
    @Bean
    public DataSource dataSource(@Value("${jdbc.driver}") String driver, @Value("${jdbc.url}") String url, @Value("${jdbc.username}") String username, @Value("${jdbc.password}") String password) {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    /**
     * 配置 SqlSessionFactory bean<br>
     * 在Mybatis-Config配置文件中的设置都可以在这里设置
     */
    @Bean
    public SqlSessionFactory sqlSessionFactory(@Autowired DataSource dataSource) throws Exception {
        // 创建仓库bean
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        // 设置数据源
        sqlSessionFactoryBean.setDataSource(dataSource);
        // 设置 Mybatis 配置文件（如果配置文件中没有东西可以不设置）
        sqlSessionFactoryBean.setConfigLocation(new ClassPathResource("mybatis-config.xml"));
        // 设置类型别名对应的包
        sqlSessionFactoryBean.setTypeAliasesPackage("com.chr.website.entity");
        // 设置设置映射文件的路径，若映射文件所在路径和mapper接口所在路径一致，则不需要设置
        sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources("mappers/*Mapper.xml"));
        // 设置下划线映射为驼峰
        Properties properties = new Properties();
        properties.put("mapUnderscoreToCamelCase", true);
        sqlSessionFactoryBean.setConfigurationProperties(properties);
        // 设置分页插件
        sqlSessionFactoryBean.setPlugins(new PageInterceptor());

        return sqlSessionFactoryBean.getObject();
    }


    /**
     * 配置mapper接口的扫描配置<br>
     * 由mybatis-spring提供，可以将指定包下所有的mapper接口创建动态代理<br>
     * 并将这些动态代理作为IOC容器的bean管理
     */
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer scanner = new MapperScannerConfigurer();
        scanner.setBasePackage("com.chr.website.mapper");
        scanner.setSqlSessionFactoryBeanName("sqlSessionFactory");
        return scanner;
    }
}
```

```java
package com.chr.website.config;

import jakarta.servlet.Filter;
import org.springframework.web.filter.CharacterEncodingFilter;
import org.springframework.web.filter.HiddenHttpMethodFilter;
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

/**
 * @Author: 程浩然
 * @Create: 2024/9/8 - 9:08
 * @Description: webInit.java 代替 web.xml
 */

public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {

    /**
     * 指定 Spring 配置类
     */
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }


    /**
     * 指定 SpringMVC 配置类
     */
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    /**
     * 指定 DispatcherServlet 的映射规则，即 url-pattern
     */
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    /**
     * 添加过滤器
     */
    @Override
    protected Filter[] getServletFilters() {
        // 字符过滤器
        CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
        characterEncodingFilter.setEncoding("UTF-8");
        characterEncodingFilter.setForceResponseEncoding(true);

        // HiddenHttpMethodFilter 过滤器（用于将get和post请求转换为put和delete请求）
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{characterEncodingFilter, hiddenHttpMethodFilter};
    }
}
```

```java
package com.chr.website.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.multipart.MultipartResolver;
import org.springframework.web.multipart.support.StandardServletMultipartResolver;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.*;
import org.thymeleaf.spring6.SpringTemplateEngine;
import org.thymeleaf.spring6.templateresolver.SpringResourceTemplateResolver;
import org.thymeleaf.spring6.view.ThymeleafViewResolver;
import org.thymeleaf.templateresolver.ITemplateResolver;

/**
 * @Author: 程浩然
 * @Create: 2024/9/8 - 9:11
 * @Description: WebConfig.java 代替 SpringMVC.xml
 */
@Configuration
@ComponentScan({"com.chr.website.controller", "com.chr.website.exception"})
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    /**
     * 配置视图解析器
     */
    @Bean
    public ITemplateResolver templateResolver() {
        SpringResourceTemplateResolver templateResolver = new SpringResourceTemplateResolver();
        templateResolver.setPrefix("/WEB-INF/views/");
        templateResolver.setSuffix(".html");
        templateResolver.setTemplateMode("HTML");
        templateResolver.setCharacterEncoding("UTF-8");
        return templateResolver;
    }

    //生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }

    //生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setOrder(1);
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }

    /**
     * 配置文件上传解析器
     */
    @Bean
    public MultipartResolver multipartResolver() {
        return new StandardServletMultipartResolver();
    }

    /**
     * 静态资源配置
     */
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    /**
     * 配置拦截器
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // addInterceptor(): 添加拦截器
        // addPathPatterns(): 添加拦截路径
        // excludePathPatterns(): 移除拦截路径
//        registry.addInterceptor(拦截器).addPathPatterns("/");
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--MyBatis 配置文件-->

<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>


</configuration>
```

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url= jdbc:mysql://localhost:3306/mysql_text_one
jdbc.username=root
jdbc.password=123
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置，ConsoleAppender表示输出到控制台 -->
    <appender name="STDOUT"
              class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!-- 日志输出的格式 -->
            <!-- 按照顺序分别是：时间、日志级别、线程名称、打印日志的类、日志主体内容、换行 -->
            <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger] [%msg]%n</pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>

    <!-- 设置全局日志级别。日志级别按顺序分别是：TRACE、DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。 -->
    <root level="DEBUG">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT"/>
    </root>

    <!-- 根据特殊需求指定局部日志级别，可也是包名或全类名。 -->
    <logger name="com.chr.website" level="DEBUG"/>

</configuration>
```



### 六. 分析表结构

1. **用户表（Users）**：

   - 用户ID
   - 用户名
   - 密码
   - 电子邮件
   - 手机号
   - 注册日期
   - 最后登录日期

   

2. **商家表（Sellers）**：

   - 商家ID
   - 商家名称
   - 商家密码
   - 商家描述
   - email
   - 手机号

   

3. **商品分类表（Categories）**：
   <font color="red">不需要在代码里写，商家和顾客都无权修改，实现写死的</font>

   - 分类ID
   - 分类名称

   

4. **商品表（Products）**：

   - 商品ID

   - 商品名称
   - 商家id

   - 商品描述

   - 价格

   - 库存数量

   - 商品分类ID

   - 商品图片URL

   - 上架日期

   - 商品状态（特价，现售，预售）

   

   

4. **订单表（Orders）**：

   - 订单ID
   - 用户ID
   - 商品ID
   - 商家ID
   - 订单总金额
   - 送达地址
   - 收件人姓名
   - 收件人电话
   - 订单状态
   - 下单时间
   - 支付时间
   - 发货时间
   - 收货时间

   

5. **支付信息表（Payments）**：

   - 支付ID
   - 订单ID
   - 用户ID
   - 支付金额
   - 支付方式
   - 支付状态
   - 支付时间

   

6. **评价表（Reviews）**：

   - 评价ID
   - 商品ID
   - 用户ID
   - 评分
   - 评论内容
   - 评论时间

   

7. **购物车表（Carts）**：

   - 购物车ID
   - 用户ID
   - 商品ID
   - 商品数量
   
   
   
7. **收藏表（stars）**：

   - 收藏ID
   - 用户ID
   - 商品ID
   - 商品数量
     
   
   



### 七.功能分析

#### 用户：

1. 登录（通过账户名密码登录，账户名不可重复）
2. 注册（对用户表 ==增== 操作）
3. 个人信息管理（对用户表进行 ==改== 操作）
4. 主页显示（对商品表进行 ==查== 操作）
5. 搜索商品（对商品表进行 ==查== 操作）
6. 收藏商品（对收藏表进行 ==增== 操作）
7. 对比商品（对商品表进行 ==查== 操作）
8. 对购物车增删改（购物车表增删改查）
9. 创建订单（对订单表进行 ==增== 操作）
10. 取消订单（对订单表表进行 ==删== 操作）
10. 用户查看订单（对订单表表进行 ==查== 操作）
10. 商家查看订单（对订单表表进行 ==查== 操作）
11. 订单支付（对支付信息表进行 ==增== 操作）
11. 取消订单支付（对支付信息表进行 ==删== 操作，订单不会消失）
11. 用户查看订单支付信息（对订单表表进行 ==查== 操作）
12. 对商品进行评价（对评价表进行 ==增== 操作）
12. 由商品查找评价（对评价表进行 ==查== 操作）





#### 商家：

1. 成为商家(需要先登录，成为商家后用户表中数据删除，只有商家表中有数据，商家登录后直接跳转到商家管理页面    商家表 ==增== 操作)
2. 上架商品（商品表 ==增== 操作）
3. 下架商品（商品表 ==删== 操作）
4. 修改商品信息（商品表 ==查== 操作）
5. 查看订单状态（订单表 ==查== 操作）





### 八. 写前端代码

#### 前端页面分析：

1. 主页
2. 登录页
3. 注册页
4. 个人主页
5. 全部订单页
6. 成为商家页



7. 商家主页
8. 商品管理页
9. 订单管理页



10. 商品首页
11. 商品查询页



12. 其他静态页面（版权，介绍等）





### 九. 写主要业务代码

1. 写 mapper 接口
2. 写拦截器
2. 写service层接口和实现类
2. 
