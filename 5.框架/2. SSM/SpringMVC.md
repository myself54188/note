## 一. SpringMVC 简介

### 1. 什么是 MVC

MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分

M：Model，模型层，指工程中的JavaBean，作用是处理数据

>JavaBean分为两类：
>
>- 一类称为实体类Bean：专门存储业务数据的，如 Student、User 等
>- 一类称为业务处理 Bean：指 Service 或 Dao 对象，专门用于处理业务逻辑和数据访问。

V：View，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据

C：Controller，控制层，指工程中的servlet，作用是接收请求和响应浏览器

MVC的工作流程：
用户通过视图层发送请求到服务器，在服务器中请求被控制层接收，控制层调用相应的模型层处理请求，处理完毕将结果返回到控制层，控制层再根据请求处理的结果找到相应的视图，渲染数据后最终响应给浏览器

> 注：三层架构分为表述层（或表示层）、业务逻辑层、数据访问层，表述层表示前台页面和后台servlet



### 2. SpringMVC 的特点

- **Spring 家族原生产品**，与 IOC 容器等基础设施无缝对接
- **基于原生的Servlet**，通过了功能强大的**前端控制器DispatcherServlet**，对请求和响应进行统一处理
- 表述层各细分领域需要解决的问题**全方位覆盖**，提供**全面解决方案**
- **代码清新简洁**，大幅度提升开发效率
- 内部组件化程度高，可插拔式组件**即插即用**，想要什么功能配置相应组件即可
- **性能卓著**，尤其适合现代大型、超大型互联网项目要求



## 二. HelloWord

##### 1. 创建 Maven 文件，设置 web 框架

##### 2. 导入 jar 包

```xml
<!-- SpringMVC -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>6.1.6</version>
        </dependency>

        <!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
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
```

##### 3. 配置 **前端控制器 DispatcherServlet**

> 前端控制器实际上是一个servlet程序，用于处理所以请求，需要对它在web.xml中注册。在注册时配置了SpringMVC的配置文件的位置和名称，以及前端控制器的初始化时间提前到服务器启动时

```xml
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>

<!--
		设置springMVC的核心控制器所能处理的请求的请求路径
		/所匹配的请求可以是/login或.html或.js或.css方式的请求路径
		但是/不能匹配.jsp请求路径的请求
 -->
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

##### 4. 配置 SpringMVC 的配置文件的位置和名称

1. 情况一：默认配置方式
   SpringMVC 的配置文件位于 WEB-INF 下，默认名称为`<servlet-name>`(的值) -servlet.xml。

   

2. 情况二：拓展配置方式
   通过`<init-param>`标签设置SpringMVC配置文件的位置和名称，通过`<load-on-startup>`标签设置SpringMVC前端控制器DispatcherServlet 的初始化时间。

   ```xml
   <servlet>
       <servlet-name>SpringMVC</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--配置SpringMVC配置文件的位置和名称-->
       <init-param>
           <!--配置SpringMVC配置文件的位置和名称-->
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springMVC.xml</param-value>
       </init-param>
       <!--将前端控制器 DispatcherServlet 初始化时间提前到服务器启动时-->
       <load-on-startup>1</load-on-startup>
   </servlet>
   <!--
       设置springMVC的核心控制器所能处理的请求的请求路径
       /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
       但是/不能匹配.jsp请求路径的请求
   -->
   <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>
   ```



##### 5.  创建请求控制器

前端控制器对于浏览器的请求进行了统一的处理，但是具体的请求有不同的处理过程，一次需要创建处理具体请求的类，即请求控制器。

请求控制器中每一个处理请求的方法称为控制器方法。

应为SpringMVC的控制器由一个POJO(普通的java类)担任，因此需要通过==@Controller==注解将其标识为一个控制层组件，交给Spring的IOC容器管理，才能识别出控制器的存在。

注意：使用注解要开启组件扫描功能

```java
@Controller
public class HelloController {

}
```

```xml
<context:component-scan base-package="com.chr.SpringMVC.controller"/>
```

开启视图解析器

```xml
<!-- 配置Thymeleaf视图解析器 -->
    <bean id="viewResolver"
          class="org.thymeleaf.spring6.view.ThymeleafViewResolver">
        <property name="order" value="1"/>
        <property name="characterEncoding" value="UTF-8"/>
        <property name="templateEngine">
            <bean class="org.thymeleaf.spring6.SpringTemplateEngine">
                <property name="templateResolver">
                    <bean class="org.thymeleaf.spring6.templateresolver.SpringResourceTemplateResolver">
                        <!-- 视图前缀 -->
                        <property name="prefix" value="/WEB-INF/templates/"/>
                        <!-- 视图后缀 -->
                        <property name="suffix" value=".html"/>
                        <property name="templateMode" value="HTML"/>
                        <property name="characterEncoding" value="UTF-8"/>
                    </bean>
                </property>
            </bean>
        </property>
    </bean>
```



编写首页的请求映射：

> @RequestMapping(value = "/") 注解用于进行请求映射

```java
@Controller
public class HelloController {
    @RequestMapping(value = "/")
    public String index() {
        // 返回视图名称
        return "index";
    }
}
```

##### 6. 测试

![image-20240813153712344](../../0.笔记图片/image-20240813153712344.png)

添加一个跳转功能

```html
<!--通过 thymeleaf 来将相对路径转换为绝对路径-->
<a th:href="@{/target}">方位目标页面 target.html</a>
```

```java
@RequestMapping(value = "/target")
public String toTarget() {
    return "target";
}
```

![image-20240813154323944](../../0.笔记图片/image-20240813154323944.png)

##### 7. 总结

1. 浏览器发送请求。
2. 若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet 处理。
3. 前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中==@RequestMapping==注解的value属性值进行匹配。
4. 若匹配成功，该注解所标识的控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径。
5. 之后通过Thymeleaf对视图进行渲染，最终<font color="red">转发</font>到视图所对应页面。



## 三. @RequestMapping 注解

### 1.@RequestMapping 注解的功能

==@RequestMapping== 注解的作用就是将请求和处理请求的控制器方法关联起来，建立映射关系。

SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的控制器方法来处理这个请求。

> 同一个初始信息的类中的控制器所对应的匹配路径是唯一的（即value的值唯一，不能重复）



### 2. @RequestMapping 注解的位置

@RequestMapping标识一个类：设置映射请求的请求路径的初始信息

@RequestMapping标识一个方法：设置映射请求请求路径的具体信息

```java
// 这时候需要访问 http://localhost:8080/SpringMVC_demo2/hello/testRequestMapping
@Controller
@RequestMapping(value = "/hello")
public class RequestMappingController {
    @RequestMapping(value = "/testRequestMapping")
    public String success() {
        return "success";
    }
}
```

> 通常会将同一个模块的功能放在一个类中，在类上进行标识，以便区分。
>
> 比如用户和管理员会创建两个类，分别写两套控制器，在类上会写不同的 RequestMapping，但在两个类中，RequestMapping 值可能相同。



### 3. @RequestMapping 注解的value属性

value属性通过请求的<font color="red">请求地址</font>匹配请求映射

value属性是一个**字符串类型**的数组，表示该请求映射能够匹配多个请求地址所对应的请求，只要满足一个请求地址即可

==@RequestMapping== 注解的 value 属性必须设置

```java
@RequestMapping(value = {"/testRequestMapping","/test"})
public String success() {
    return "success";
}
```

```html
<a th:href="@{/testRequestMapping}">超链接1</a><br>
<a th:href="@{/test}">超链接2</a><br>
```



### 4. @RequestMapping 注解的 method 属性

method 属性通过请求的<font color="red">请求方式</font>（get或post）匹配请求映射

method属性是一个RequestMethod类型的 **数组**，表示该请求映射能够匹配多种请求方式的请求，只要满足一个请求方式即可

若当前请求的请求地址满足请求映射的value属性，但是请求方式不满足method属性，则浏览器报错405：Request method 'POST' not supported

> ==@RequestMapping== 默认是匹配 get 和 post 都满足

```java
@RequestMapping(value = {"/testRequestMapping", "/test"}, method = {RequestMethod.GET})
public String success() {
    return "success";
}
```

```html
<form th:action="@{/test}" method="post">
    <input type="submit" value="post 提交">
</form>
<br>
<form th:action="@{/test}" method="get">
    <input type="submit" value="get 提交">
</form>
```

> 注：
>
> 1、对于处理指定请求方式的控制器方法，SpringMVC中提供了@RequestMapping的派生注解
>
> 处理get请求的映射-->@GetMapping
>
> 处理post请求的映射-->@PostMapping
>
> 处理put请求的映射-->@PutMapping
>
> 处理delete请求的映射-->@DeleteMapping
>
> 2、常用的请求方式有get，post，put，delete
>
> 但是目前浏览器只支持get和post，若在form表单提交时，为method设置了其他请求方式的字符串（put或delete），则按照默认的请求方式get处理
>
> 若要发送put和delete请求，则需要通过spring提供的过滤器HiddenHttpMethodFilter，在RESTful 部分会讲到



### 5. @RequestMapping 注解的 params 属性

params 属性是通过请求的<font color="red">请求参数</font>匹配请求映射，必须同时满足所有 param 条件

params 属性是一个字符串类型的数组，可以通过四种表达式设置参数和请求映射的匹配关系：

- `"param"`：要求请求映射所匹配的请求必须携带 param 请求参数
- `"!param"`：要求请求映射所匹配的请求必须不能携带 param 请求参数
- `"param=value"`：要求请求映射所匹配的请求必须携带 param 请求参数且 param=value
- `"param!=value"`：要求请求映射所匹配的请求必须携带 param 请求参数但是param!=value

```java
 @RequestMapping(value = "/test",
            params = {"username",
//                    "!username",
//                    "username=1",
//                    "username!=1"
            }
    )
    public String test() {
        System.out.println("params");
        return "success";
    }
```

```html
<form th:action="@{/test}" method="get">
    <input type="text" name="username">
    <input type="submit" value="提交">
</form>
```

> 注：
>
> 若当前请求满足@RequestMapping注解的value和method属性，但是不满足params属性，此时页面回报错400：Parameter conditions "username, password!=123456" not met for actual request parameters: username={admin}, password={123456}



### 6. @RequestMapping 注解的 headers 属性

headers 属性通过请求的<font color="red">请求头信息</font>匹配请求映射

headers属性是一个字符串数组，可以通过四种表达式设置请求头信息和请求映射的匹配关系

- `"header"`：要求请求映射所匹配的请求必须携带header请求头信息

- `"!header"`：要求请求映射所匹配的请求必须不能携带header请求头信息

- `"header=value"`：要求请求映射所匹配的请求必须携带header请求头信息且header=value

- `"header!=val"`：要求请求映射所匹配的请求必须携带header请求头信息且header != val

```java
 @RequestMapping(value = "/test",
            headers = {"Host",
//                    "!Host",
//                    "Host=localhoot:8080",
//                    "Host!=localhoot:8080"
            }
    )
    public String test() {
        System.out.println("headers");
        return "success";
    }
```

```html
<form th:action="@{/test}" method="get">
    <input type="text" name="username">
    <input type="submit" value="提交">
</form>
```



>若当前请求满足@RequestMapping注解的value和method属性，但是不满足headers属性，此时页面显示404错误，即资源未找到



### 7. SpringMVC 支持 ant 风格(模糊匹配)的路径

-  `？`：表示任意的单个字符

- `*`：表示任意的0个或多个字符

### 8. SpringMVC 支持路径中的占位符

原始方式：/deleteUser?id=1

rest方式：/deleteUser/1(不采用 key = val 的形式)

SpringMVC路径中的占位符常用于RESTful风格中，当请求路径中将某些数据通过路径的方式传输到服务器中，就可以在相应的 @RequestMapping 注解的value属性中通过占位符 {xxx} 表示传输的数据，在通过==@PathVariable==注解，将占位符所表示的数据赋值给控制器方法的形参

```html
<a th:href="@{/testRest/1/admin}">测试路径中的占位符-->/testRest</a><br>
```

```java
@RequestMapping("/testRest/{id}/{username}")
public String testRest(@PathVariable("id") String id, @PathVariable("username") String username){
    System.out.println("id:"+id+",username:"+username);
    return "success";
}
//最终输出的内容为-->id:1,username:admin
```



## 四. SpringMVC获取请求参数

