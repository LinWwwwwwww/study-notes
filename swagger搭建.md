# swagger框架搭建

>前后端分离开发的API接口框架

## 1、 Maven-pom.xml文件

> pom.xml

```xml
<!--swagger-API 依赖开始-->
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.8</version>
</dependency>
<!--swagger-API 依赖结束-->
```

## 2、 Swagger配置

> config/Swagger2.java

```java
@Configuration
@EnableSwagger2
@EnableWebMvc
//扫描API Controller包
@ComponentScan(basePackages = {"com.lw.testswagger02.controller"})
public class Swagger2 {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo());
    }

    private ApiInfo apiInfo(){
        return new ApiInfoBuilder()
                //页面标题
                .title("SpringBoot 测试使用 Swagger构建RestFul API")
                //创建人
                .contact(new Contact("piglet","http://www.baidu.com",""))
                //版本号
                .version("1.0")
                //描述
                .description("API 描述")
                .build();
    }
}
```

## 3、 WebMvc配置

> config/WebMvcConfig.java

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    public void addResourceHandlers(ResourceHandlerRegistry registry){
        registry.addResourceHandler("swagger-ui.html")
                .addResourceLocations("classpath:/META-INF/resources/");

        registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/");
    }
}
```

## 4、 控制类

> controller/UserController.java
>
> 数据库连接、mybatis整合过程见[SpringBoot学习笔记](https://gitea.xinglian.info/qunzhi/other_project/issues/166)

``` java
//对类进行描述
@Api(value = "用户模块", description = "用户模块的接口信息")
@RestController
public class UserController {

    @Autowired
    UserMapper userMapper;

    //对方法进行描述
    @ApiOperation(value = "获取用户列表", notes = "获取所有用户的信息")
    @GetMapping("/users")
    public Object users(){
        Map<String, Object> map = new HashMap<>();
        map.put("users",userMapper.getUsers());
        return map;
    }

    @ApiOperation(value = "通过id获取用户", notes = "通过路径上的id获取当个用户的信息")
    @ApiImplicitParam(value = "用户的id", paramType = "path")
    @GetMapping("/user/{id}")
    public User getUserById(@PathVariable("id")Integer id){
        return userMapper.getUser(id);
    }

    @ApiOperation(value = "添加用户", notes = "通过添加姓名与年龄，id为自增")
    @ApiImplicitParam(value = "用户对象", paramType = "query")
    @PostMapping("/user")
    public User addUser(User user){
        userMapper.addUser(user);
        return user;
    }

    @ApiOperation(value = "删除用户", notes = "通过用户id删除对应的用户信息")
    @ApiImplicitParam(value = "用户id", paramType = "path")
    @DeleteMapping("/user/{id}")
    public User deleteUserById(@PathVariable("id")Integer id){
        User user = userMapper.getUser(id);
        userMapper.deleteUser(id);
        return user;
    }
}
```

## 5、 启动类

> TestSwagger02Application.java

```java
@MapperScan("com.lw.testswagger02.mapper")
@SpringBootApplication
@EnableWebMvc
public class TestSwagger02Application {

    public static void main(String[] args) {
        SpringApplication.run(TestSwagger02Application.class, args);
    }

}
```

## 6、 访问[swagger-ui.html](http://localhost:8080/swagger-ui.html)