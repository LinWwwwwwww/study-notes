# SpringBoot学习笔记

> **version**
>
> SpringBoot：2.1.6.RELEASE
>
> Java：1.8

## 开发技巧

> 快捷键、小技巧

### 1、快捷键

####  IDEA快捷键

| 操作         | 效果         |
| ------------ | ------------ |
| Ctrl+F       | 页内匹配字段 |
| Ctrl+H       | 全局匹配字段 |
| Ctrl+Shift+R | 全局匹配文件 |

#### chrome快捷键

| 操作            | 效果                                     |
| --------------- | ---------------------------------------- |
| Ctrl+Tab        | 从当前向右切换标签页                     |
| Ctrl+Shift+Tab  | 从当前向左切换标签页                     |
| Ctrl+W          | 关闭当前标签页                           |
| Ctrl+F4         | 关闭窗口                                 |
| Ctrl+T          | 新建标签页                               |
| Ctrl+N          | 新建窗口                                 |
| Ctrl+1 ~ Ctrl+8 | 切换到标签栏中指定地位编号所对应的标签页 |
| Ctrl+9          | 切换到最后一个标签页                     |



### 2、小技巧

####  开发期间模板引擎页面修改后，要实时生效

```properties
#禁用缓存
spring.thymeleaf.cache=false
#页面修改完成后ctrl+F9:重新编译
#刷新网页即可看到页面修改
```



## 配置文件

### 1、 配置文件值注入

* @PropertySource & @ImportResource

  @PropertySource：加载指定的配置文件

  ``` java
  @PropertySource(value = {"classpath:person.properties"})
  ```

  @ImportResource：导入Spring的配置文件，让配置文件里面的内容生效

  * 创建beans.xml的Spring配置文件

  * 在启动类添加注解：

    ``` java
    @ImportResource(locations = "classpath:beans.xml")
    ```

  SpringBoot推荐给容器中添加组件的方式：使用全注解的方式

  1. 配置类 ==== Spring配置文件
  2. 使用@Bean给容器中添加组件

  ```java
  @Configuration
  public class MyAppConfig {
      /**
       * 将方法的返回值添加到容器中，容器中这个组件默认的id就是方法名
       * @return
       */
      @Bean
      public HelloService helloService02(){
          System.out.println("注解了...");
          return new HelloService();
      }
  }
  ```

## 日志

### 1、 市面上的日志框架

JUL、JCL、Jboss-logging、logback、log4j、log4j2、slf4j....

| 日志门面  （日志的抽象层）                                   | 日志实现                                             |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| ~~JCL（Jakarta  Commons Logging）~~    SLF4j（Simple  Logging Facade for Java）    **~~jboss-logging~~** | Log4j  JUL（java.util.logging）  Log4j2  **Logback** |

左边选一个门面（抽象层）、右边来选一个实现；

日志门面：  SLF4J；

日志实现：Logback；

### 2、 SpringBoot日志关系

SpringBoot能自动适配所有日志，而且底层使用slf4j+logback的方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉；

### 3、 日志的使用

SpringBoot默认配置好了日志：

```java
//记录器
Logger logger = LoggerFactory.getLogger(getClass());

@Test
public void contextLoads() {
    //日志的级别：
    //由低到高：trace<debug<info<warn<error
    //可以调整输出的日志级别：日志就会在这个级别以以后的高级别生效
    logger.trace("这是trace日志...");
    logger.debug("这是debug日志...");
    //SpringBoot默认info级别
    logger.info("这是info日志...");
    logger.warn("这是warn日志...");
    logger.error("这是error日志...");
}
```

```
日志输出格式：
%d：日期时间
%thread：线程名
%-5level：级别从左显示5个字符宽度
%logger{50}：logger名字最长50个字符，否则按照句点分割
%msg：日志消息
%n：换行符
```

SpringBoot修改日志的默认配置

```properties
logging.level.com.lw=trace

#loggin.paths
#不指定路径时，在当前项目下生成springboot.log日志
#可指定完整的路径
#logging.file=D:/springboot.log
#在当前磁盘的根路径下创建spring/log文件夹并使用spring.log作为默认文件
logging.path=/spring/log

#在控制台输出的日志的格式
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
logging.pattern.file=%d{yyyy-MM-dd} === [%thread] === %-5level %logger{50} === %msg%n
```

| logging.file | logging.path | Example  | Description                        |
| ------------ | ------------ | -------- | ---------------------------------- |
| (none)       | (none)       |          | 只在控制台输出                     |
| 指定文件名   | (none)       | my.log   | 输出日志到my.log文件               |
| (none)       | 指定目录     | /var/log | 输出到指定目录的 spring.log 文件中 |

### 4、切换日志框架

按照slf4j的日志适配图，进行相关的切换

![日志适配图](https://www.slf4j.org/images/legacy.png)

slf4j+logback-->slf4j+log4j的方式：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <artifactId>spring-boot-starter-logging</artifactId>
            <groupId>org.springframework.boot</groupId>
        </exclusion>
    </exclusions>
    <exclusions>
        <exclusion>
            <artifactId>logback-classic</artifactId>
            <groupId>ch.qos.logback</groupId>
        </exclusion>
        <exclusion>
            <artifactId>log4j-over-slf4j</artifactId>
            <groupId>org.slf4j</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
</dependency>
```

切换为log4j2

```xml
   <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>spring-boot-starter-logging</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
        </dependency>

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

## Web开发

### 1、 SpringBoot对静态资源的映射规则

* 所有 /webjars/** ，都去 classpath:/META-INF/resources/webjars/ 找资源；

  - localhost:8080/webjars/jquery/3.3.1/jquery.js

```xml
  <!--引入jquery-webjar-->在访问的时候只需要写webjars下面资源的名称即可
  <dependency>
      <groupId>org.webjars</groupId>
      <artifactId>jquery</artifactId>
      <version>3.3.1</version>
  </dependency>
```
* "/**" 访问当前项目的任何资源，都去（静态资源的文件夹）找映射
```
"classpath:/META-INF/resources/", 
"classpath:/resources/",
"classpath:/static/", 
"classpath:/public/" 
"/"：当前项目的根路径
```
* 欢迎页； 静态资源文件夹下的所有index.html页面；被"/**"映射；
localhost:8080/   找index页面

> localhost:8080/abc ==>  去静态资源文件夹里面找abc

* 所有的 **/favicon.ico  都是在静态资源文件下找

### 2、 SpringBoot整合Thymeleaf

1. 修改pom文件添加坐标

```xml
<!--SpringBoot启动器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!--web启动器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
2. 创建存放视图的目录
```java
//默认前缀后缀，只要放在classpath:/templates/,thymeleaf就能自动渲染
public static final String DEFAULT_PREFIX = "classpath:/templates/";
public static final String DEFAULT_SUFFIX = ".html";
```

>templates:该目录是安全的，意味着该目录下的内容不允许外界直接访问。

3. 编写Controller
```java
/**
 * Thymeleaf入门案例
 * @author LinWei
 * @create 2019-08-02 13:39
 */
@Controller
public class DemoController {

    @RequestMapping("/show")
    public String showInfo(Model model){
        model.addAttribute("msg","Thymeleaf第一个案例");
        return "index";
    }
}
```
4. 编写视图
* 在templates目录下创建index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Thymeleaf入门</title>
</head>
<body>
    <span th:text="Hello"></span>
    <br/>
    <span th:text="${msg}"></span>
</body>
</html>
```
5. 编写启动类
* 在Controller的上一级包下创建
```java
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class,args);
    }
}
```
6. 启动 

### 3、 默认访问首页

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry){
        WebMvcConfigurer.super.addViewControllers(registry);
        registry.addViewController("/").setViewName("login");
        registry.addViewController("/index").setViewName("login");
    }
}
```

### 4、 国际化

1. 编写国际化配置文件，抽取页面需要显示的国际化学习

2. Spring自动配置好了管理国际化资源文件的组件，配置文件可以直接放在类路径下叫messages.properties；

   若自己创建i18n/login.properties文件，则在application.properties中指定

```properties
spring.messages.basename=i18n.login
```

3. 页面设置国际化的值
```html
<label class="sr-only" th:text="#{login.password}"></label>
<input type="password" class="form-control"th:placeholder="#{login.password}" required="">
<div class="checkbox mb-3">
    <label>
        <input type="checkbox" value="remember-me">[[#{login.remember}]]
    </label>
</div>
```
>效果：根据浏览器语言设置的信息切换了国际化；

4. 点击链接切换国际化

```html
<a class="btn btn-sm" th:href="@{/login(l='zh_CN')}">中文</a>
<a class="btn btn-sm" th:href="@{/login(l='en_US')}">English</a>
```

```java
package com.lw.springboot.component;

import org.springframework.util.StringUtils;
import org.springframework.web.servlet.LocaleResolver;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Locale;

/**
* 创建处理的组件
* @author LinWei
* @create 2019-08-05 17:46
*/
public class MyLocaleResolver implements LocaleResolver {
    @Override
    public Locale resolveLocale(HttpServletRequest httpServletRequest) {
        String l = httpServletRequest.getParameter("l");
        Locale locale = Locale.getDefault();
        if(!StringUtils.isEmpty(l)){
            String[] s = l.split("_");
            locale = new Locale(s[0],s[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {

    }
}
```

```java
//在配置文件中声明
@Bean
public LocaleResolver localeResolver(){
    return new MyLocaleResolver();
}
```

### 5、 拦截器进行登录检查

1. 拦截器

```java
public class LoginHandlerInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        Object user = request.getSession().getAttribute("loginUser");
        if(user == null){
            request.setAttribute("msg","没权限，先登录");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return false;
        }
        return true;
    }
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
    }
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
    }
}
   
```
2. 注册拦截器
```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry){
        WebMvcConfigurer.super.addViewControllers(registry);
        registry.addViewController("/").setViewName("login");
        registry.addViewController("/login").setViewName("login");
        registry.addViewController("/index.html").setViewName("login");
        registry.addViewController("/main.html").setViewName("dashboard");
    }

    /**
     * 注册拦截器
     * @param registry
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**")
                .excludePathPatterns("/","/login","/index.html");
    }
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }
}
```

### 6、 RestfulCRUD

> URI：  /资源名称/资源标识       HTTP请求方式区分对资源CRUD操作

|      | 普通CRUD（uri来区分操作） | RestfulCRUD       |
| ---- | ------------------------- | ----------------- |
| 查询 | getEmp                    | emp---GET         |
| 添加 | addEmp?xxx                | emp---POST        |
| 修改 | updateEmp?id=xxx&xxx=xx   | emp/{id}---PUT    |
| 删除 | deleteEmp?id=1            | emp/{id}---DELETE |

## SpringBoot与数据访问

> https://blog.csdn.net/qq_36505948/article/details/82056017

### 1、 JDBC

pom.xml配置

 ```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<scope>runtime</scope>
</dependency>
 ```

application.xml配置

```
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/jdbc
    driver-class-name: com.mysql.cj.jdbc.Driver
```

> 默认用`com.zaxxer.hikari.HikariDataSource`作为数据源
>
> 以前（如1.5版本）默认是用`org.apache.tomcat.jdbc.pool.DataSource`作为数据源；

### 2、 整合Druid数据源
* 导入Druid数据源
```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.1.18</version>
</dependency>
```
* 配置Druid
```yaml
#   数据源基本配置
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ssm_crud
    type: com.alibaba.druid.pool.DruidDataSource
#   数据源其他配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
#   配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙  
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true  
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

* Druid配置类
```java
@Configuration
public class DruidConfig {
    //绑定配置属性
    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druid(){
        return new DruidDataSource();
    }
    //配置Druid的监控
    //1、配置管理后台的Servlet
    @Bean
    public ServletRegistrationBean statViewServlet(){
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(),"/druid/*");
        Map<String,String> initParams = new HashMap<>();

        initParams.put("loginUsername","admin");
        initParams.put("loginPassword","123456");
        initParams.put("allow","");//默认允许所有访问
        initParams.put("deny","192.168.15.21");//阻止访问设置
        bean.setInitParameters(initParams);
        return bean;
    }
    //2、配置web监控的filter
    @Bean
    public FilterRegistrationBean webStatFilter(){
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new WebStatFilter());
        Map<String,String> initParams = new HashMap<>();
        initParams.put("exclusions","*.js,*.css,/druid/*");

        bean.setInitParameters(initParams);

        bean.setUrlPatterns(Arrays.asList("/*"));

        return bean;
    }
}
```

### 3、整合mybatis

```xml
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>1.3.1</version>
</dependency>
```

步骤：


1. 配置数据源相关属性（见上一节Druid）

2. 给数据库建表

3. 创建JavaBean

4. 使用

   ①注解版

```java
/**
 * 指定这是一个操作数据库的mapper
 * @author LinWei
 * @create 2019-08-06 16:08
 */
@Mapper
public interface DepartmentMapper {

    @Select("select * from tbl_dept where id=#{id}")
    public Department getDeptById(Integer id);

    @Delete("delete from tbl_dept where id=#{id}")
    public int deleteDeptById(Integer id);

    @Options(useGeneratedKeys = true,keyProperty = "id")
    @Insert("Insert into tbl_dept(deptName) values(#{deptName})")
    public int insertDept(Department department);

    @Update("update tbl_dept set deptName=#{deptName} where id=#{id}")
    public int updateDept(Department department);
}
```

* 自定义MyBatis的配置规则：开启驼峰命名法，以便DB的字段（下划线命名）和JavaBean中的字段（驼峰命名）对应

```java
@Configuration
public class MybatisConfig {
    @Bean
    public ConfigurationCustomizer configurationCustomizer(){
        return new ConfigurationCustomizer() {
            @Override
            public void customize(org.apache.ibatis.session.Configuration configuration) {
                //开启驼峰命名法
                configuration.setMapUnderscoreToCamelCase(true);
            }
        };
    }
}
```
* 使用MapperScan批量扫描指定包下的Mapper接口

```
@MapperScan(value = "com.lw.springboot.mapper")@SpringBootApplicationpublic class SpringBoot06DataMybatisApplication {    public static void main(String[] args) {        SpringApplication.run(SpringBoot06DataMybatisApplication.class, args);    }}
```

   ②配置文件版





### 4、整合SpringData JPA

### SpringBoot整合持久层技术

1. 修改pom文件
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.6.RELEASE</version>
</parent>
<dependencies>
    <!--SpringBoot启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--web启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>1.1.1</version>
    </dependency>
    <!--mysql数据库驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.41</version>
    </dependency>
    <!--druid数据库连接池-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.0.9</version>
    </dependency>
</dependencies>
```
2. 添加 application.properties 全局配置文件
* 文件位于classpath下创建：src/main/resources/application.properties
```properties
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/ssm_crud
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
```

## 所遇问题及解决方式

> ### xxx问题
>
> * 情景
> * 所遇问题
> * 解决方式
> * 原因

### 1、 mysql返回时间问题

* 情景

  在application.xml做出如下配置

  ```
  spring:
    datasource:
      username: root
      password: 123456
      url: jdbc:mysql://localhost:3306/jdbc
      driver-class-name: com.mysql.cj.jdbc.Driver
  ```
  
  在test类做出如下测试
  
  ```
  @RunWith(SpringRunner.class)
  @SpringBootTest
  public class SpringBoot06DataJdbcApplicationTests {
  
      @Autowired
      DataSource dataSource;
      @Test
      public void contextLoads() throws SQLException {
          System.out.println(dataSource.getClass());
          Connection connection = dataSource.getConnection();
          System.out.println(connection);
          connection.close();
      }
  }
  ```
  
* 所遇问题

  ```
  Caused by: com.mysql.cj.exceptions.InvalidConnectionAttributeException: The server time zone value '�й���׼ʱ��' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.
  ```

* 解决方式

  application.xml的url添加` ?serverTimezone=Asia/Shanghai`，如下：

  ```
  spring:
    datasource:
      username: root
      password: 123456
      url: jdbc:mysql://localhost:3306/jdbc?serverTimezone=Asia/Shanghai
      driver-class-name: com.mysql.cj.jdbc.Driver
  ```
  
* 原因

   mysql返回的时间有问题，比实际时间要早8小时。
