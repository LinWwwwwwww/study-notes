# SpringBoot-Shiro整合

> Shiro：Java安全框架，用于用户权限管理

## 一、前言

> 此篇为学习[SpringBoot与Shiro整合-权限管理实战视频](https://www.bilibili.com/video/av40342174?from=search&seid=9750299841637280667)按照自己所理解的方式编写出来，方便理解，有错请指正。

Shiro是用于权限管理，权限管理又分为认证与授权。那么Shiro就应该有权限管理的设置，所以用到Shiro的过程是：

```
1、通过前端表单发送post请求将用户信息（username,pasword）传输到后端；

2、后端通过Controller扫描接收请求；

3、将请求中的用户信息发送给Shiro的权限管理进行处理并返回处理结果。
```

前两步都显而易见，关键是第三步，在讲第三步的具体过程之前，先看看Shiro是如何设置权限管理的。

Shiro设置权限管理过程：

```
1、设置权限管理；

2、设置默认安全管理器，将权限管理置于默认安全管理器中；

3、设置拦截请求的工厂并开启默认安全管理器。
```

在Shiro中

```
权限管理为Realm；

默认安全管理器为SecurityManager；

工厂为ShiroFilterFactoryBean。
```

再看看Shiro的三个核心API

```
Realm：Shiro校验数据的桥梁
SecurityManager：安全管理器（关联Realm）
Subject：将操作交给安全管理器（SecurityManager）
```

看了三个API应该可以很清楚知道他们的关系：

通过Subject将用户信息交给安全管理器，而安全管理器关联了权限管理并接收用户信息。

所以上边的第三步为：

```
创建Subject并将用户信息封装后调用相应方法，让工厂拦截；
工厂开启的安全管理器会接收Subject并关联权限管理；
让权限管理通过特定逻辑处理用户信息。
```

## 二、代码

>基础数据放在最后：UserInfo类、相应表、相应Service类、相应Mapper类、login.html、test.html，至于连接mysql、mybatis相关配置类参考[SpringBoot学习笔记](https://gitea.xinglian.info/qunzhi/other_project/issues/166)

### 1、导入Shiro整合依赖

```xml
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-spring</artifactId>
	<version>1.4.0</version>
</dependency>
```

### 2、编写Shiro配置类

- 编写步骤：

  - 创建权限管理（Realm），并通过`@Bean`**将该方法返回的Realm对象放置于Spring环境中以便使用**

    自定义Realm类（需继承AuthorizingRealm，实现两个方法，见以下代码）

  - 创建安全管理器（DefaultWebSecurityManager），通过`@Qualifier`**获取Realm对象**

  - 创建工厂（ShiroFilterFactoryBean）

```java
/**
 * shiro配置类
 * @author LW
 */
@Configuration
public class shiroConfig {
    
    /**
     * 创建工厂（ShiroFilterFactoryBean）
     */
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("securityManager") DefaultWebSecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();

        //设置安全管理器
        shiroFilterFactoryBean.setSecurityManager(securityManager);
       
        /*
         * Shiro内置过滤器
         * 常用的过滤器：
         *       anon: 无需认证（登录）可以访问
         *       authc: 必须认证才可以访问
         *       user: 如果使用rememberMe的功能可以直接访问
         *       perms： 该资源必须得到资源权限才可以访问
         *       role: 该资源必须得到角色权限才可以访问
         */
        Map<String,String> filterMap = new LinkedHashMap<>();
	
        filterMap.put("/testThymeleaf","anon");
        filterMap.put("/login","anon");
        //授权过滤器，当授权拦截后，shiro会自动跳转到未授权页面
        filterMap.put("/user/add","perms[user:add]");
        filterMap.put("/user/update","perms[user:update]");
        filterMap.put("/**","authc");
        //设置未验证提示页面
        shiroFilterFactoryBean.setLoginUrl("/toLogin");
        //设置未授权提示页面
        shiroFilterFactoryBean.setUnauthorizedUrl("/unAuth");

        shiroFilterFactoryBean.setFilterChainDefinitionMap(filterMap);
        return shiroFilterFactoryBean;
    }
    
    /**
     * 创建安全管理器（DefaultWebSecurityManager）
     */
    @Bean(name = "securityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm")UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        //关联realm
        securityManager.setRealm(userRealm);
        return securityManager;
    }
    
    /**
     * 创建权限管理（Realm）
     */
    @Bean(name = "userRealm")
    public UserRealm getRealm(){
        return new UserRealm();
    }
}
```

```java
/**
 * 自定义Realm类
 * @author LW
 */
public class UserRealm extends AuthorizingRealm {

    @Autowired
    UserInfoService userInfoService;

    /**
     * 执行授权逻辑，通过了验证后，SimpleAuthenticationInfo(userInfo,userInfo.getPassword(),"")
     * 其中第一个参数可通过以下代码进行获取
     *  Subject subject = SecurityUtils.getSubject();
     *  UserInfo userInfo = (UserInfo)subject.getPrincipal();
     * @param principalCollection
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行授权逻辑");
        //给资源进行授权 区分zation、cation
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        //获取当前登录用户
        Subject subject = SecurityUtils.getSubject();

        UserInfo userInfo = (UserInfo)subject.getPrincipal();

        //添加资源的授权字符串
        info.addStringPermission(userInfo.getPerm());

        return info;
    }

    /**
     * 执行认证逻辑
     * @param authenticationToken
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行认证逻辑");

        UsernamePasswordToken token = (UsernamePasswordToken)authenticationToken;
        //假设数据库的用户名和密码
//        String name = "root";
//        String password = "123456";
        //获取数据库用户信息
        UserInfo userInfo = userInfoService.getUserInfoByName(token.getUsername());

        //编写shiro判断逻辑，判断用户名和密码
        //1、判断用户名
        if(userInfo == null){
            //用户名不存在
            return null;//shiro底层会抛出UnKnownAccountException
        }

        //判断密码
        return new SimpleAuthenticationInfo(userInfo,userInfo.getPassword(),"");
    }
}
```

### 3、编写Controller类

```java

/**
 * 
 * @author LW
 */
@Controller
public class UserController {

	//接收用户信息
    @PostMapping("/login")
    public String login(
            @RequestParam("username") String username,
            @RequestParam("password") String password,
            Model model
    ){
        /**
         * 使用shiro编写认证操作
         */
        //1、获取subject
        Subject subject = SecurityUtils.getSubject();
        //2、封装用户数据
        UsernamePasswordToken token = new UsernamePasswordToken(username, password);
        //3、执行登录方法，被工厂拦截
        try {
            //无异常则登录成功
            subject.login(token);
            return "test";
        }catch (UnknownAccountException e){ //登录失败
            //e.printStackTrace();
            model.addAttribute("msg","用户名不存在");
            return "login";
        }catch (IncorrectCredentialsException e){ //登录失败
            //e.printStackTrace();
            model.addAttribute("msg","密码错误");
            return "login";
        }
    }
    //未验证跳转设置，跳转至登录页面
	@RequestMapping("/toLogin")
    public String toLogin(){
        return "login";
    }
	//未授权跳转设置
    @RequestMapping("/unAuth")
    public String toUnAuth(){
        return "unAuth";
    }
	//实验授权页面
    @RequestMapping("/user/add")
    public String add(){
        return "user/add";
    }
    @RequestMapping("/user/update")
    public String update(){
        return "user/update";
    }
}
```

### 4、其他相关代码

UserInfo类

```java
public class UserInfo {
    private int id;
    private String name;
    private String password;
    private String perm;

    public int getId() {return id; }

    public void setId(int id) { this.id = id; }

    public String getName() { return name; }

    public void setName(String name) { this.name = name; }

    public String getPassword() { return password; }

    public void setPassword(String password) { this.password = password; }

    public String getPerm() { return perm; }

    public void setPerm(String perm) { this.perm = perm; }
}
```

表

```sql
CREATE TABLE `user_info` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `password` varchar(255) DEFAULT NULL,
  `perm` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8mb4;
```

表数据

| id   | name | password | perm        |
| ---- | ---- | -------- | ----------- |
| 1    | 张三 | 123456   | user:add    |
| 2    | 李四 | 123456   | user:update |

Service类

```java
@Service
public class UserInfoService {
    @Autowired
    UserInfoMapper userInfoMapper;

    public UserInfo getUserInfoByName(String name) {
        return userInfoMapper.getUserInfoByName(name);
    }
}
```

Mapper类

```java
@Mapper
public interface UserInfoMapper {
    @Select("select * from user_info where name=#{name}")
    public UserInfo getUserInfoByName(String name);
}
```

login.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录页面</title>
</head>
<body>
<h1>登录页面</h1>
<h3 color="red" th:text="${msg}"></h3>
<form method="post" action="/login">
    <input name="username" type="text" placeholder="username" />
    <input name="password" type="password" placeholder="password"/>
    <input name="submit" type="submit" value="登录" />
</form>
</body>
</html>
```

test.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1 th:text="${name}"></h1>
    <br/>
    <h1><a href="/user/add">用户添加页面</a></h1>
    <br/>
    <h1><a href="/user/update">用户更新页面</a></h1>
</body>
</html>
```

