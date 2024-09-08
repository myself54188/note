### 一. 创建 Maven 项目，搭建 web 框架

- 设置Maven版本修改为自己下载的版本
- 创建 webapp-WEB-INF 目录，补全目录



### 二. 设置 tomcat 

### 三. 导入 Maven 依赖

```xml
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
            <version>2.0.6</version>
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
│   │       ├── config                         # 存放配置文件
│   │       ├── controller                     # 存放控制层代码
│   │       ├── dao                            # 存放 Dao 层代码
│   │       ├── entity                         # 存放实体类
│   │       ├── exception                      # 存放异常处理类
│   │       ├── interceptor                    # 拦截器
│   │       ├── mapper                         # Mybatis 的 Mapper 接口
│   │       ├── service                        # 业务逻辑层
│   │       └── utils                          # 工具类
│   ├── resources                              # 资源目录，存放配置文件
│   └── webapp                                 # 存放 WEB 相关资源和配置
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
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.transaction.annotation.EnableTransactionManagement;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.servlet.mvc.Controller;

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
@ComponentScan(basePackages = "com.chr.website", excludeFilters = {@ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {Controller.class, ControllerAdvice.class})})
// 告诉资源路径
@PropertySources({@PropertySource("classpath:jdbc.properties")})
// 启用基于注解的事务管理
@EnableTransactionManagement
public class SpringConfig {

    /**
     * 配置数据源
     */
    @Bean
    public DataSource dataSource(
            @Value("${jdbc.driver}") String driver,
            @Value("${jdbc.url}") String url,
            @Value("${jdbc.username}") String username,
            @Value("${jdbc.password}") String password
    ) {
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
    public SqlSessionFactory sqlSessionFactory(
            @Autowired DataSource dataSource
    ) throws Exception {
        // 创建仓库bean
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        // 设置 Mybatis 配置文件（如果配置文件中没有东西可以不设置）
        sqlSessionFactoryBean.setConfigLocation(new ClassPathResource("mybatis-config.xml"));
        // 设置数据源
        sqlSessionFactoryBean.setDataSource(dataSource);
        // 设置类型别名对应的包
        sqlSessionFactoryBean.setTypeAliasesPackage("com.chr.website.entity");
        // 设置设置映射文件的路径，若映射文件所在路径和mapper接口所在路径一致，则不需要设置
        sqlSessionFactoryBean.setMapperLocations(new ClassPathResource("mappers/*.xml"));
        // 设置下划线映射为驼峰
        Properties properties = new Properties();
        properties.put("mapUnderscoreToCamelCase", true);
        sqlSessionFactoryBean.setConfigurationProperties(properties);
        // 设置分页插件
        sqlSessionFactoryBean.setPlugins(new PageInterceptor());

        return sqlSessionFactoryBean.getObject();
    }

    // 配置mapper接口的扫描配置
    // 由mybatis-spring提供，可以将指定包下所有的mapper接口创建动态代理
    // 并将这些动态代理作为IOC容器的bean管理
    @Bean(name = "mapperScannerConfigurer")
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer scanner = new MapperScannerConfigurer();
        scanner.setBasePackage("com.chr.website.mapper");
        scanner.setSqlSessionFactoryBeanName("SqlSessionFactory");
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
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
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
@ComponentScan({"com.chr.website.controller","com.chr.website.exception"})
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



### 六. 写主要业务代码

