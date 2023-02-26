---
lang: zh-CN
title: SpringMVC
description: 学习SpringMVC
---

## 简介
### MVC是什么？
MVC 模式，全称为 Model-View-Controller（模型-视图-控制器）模式，它是一种软件架构模式，其目标是将软件的用户界面（即前台页面）和业务逻辑分离，使代码具有更高的可扩展性、可复用性、可维护性以及灵活性。
| 分层 |	描述 |
| --- | --- |
|Model（模型）|	它是应用程序的主体部分，主要由以下 2 部分组成：1.实体类 Bean：专门用来存储业务数据的对象，它们通常与数据库中的某个表对应，例如 User、Student 等。2.业务处理 Bean：指 Service 或 Dao 的对象，专门用于处理业务逻辑、数据库访问。一个模型可以为多个视图（View）提供数据，一套模型（Model）的代码只需写一次就可以被多个视图重用，有效地减少了代码的重复性，增加了代码的可复用性。|
| View（视图）|	指在应用程序中专门用来与浏览器进行交互，展示数据的资源。在 Web 应用中，View 就是我们常说的前台页面，通常由 HTML、JSP、CSS、JavaScript 等组成。|
| Controller（控制器）	| 通常指的是，应用程序的 Servlet。它负责将用户的请求交给模型（Model）层进行处理，并将 Model 层处理完成的数据，返回给视图（View）渲染并展示给用户。在这个过程中，Controller 层不会做任何业务处理，它只是 View（视图）层和 Model （模型）层连接的枢纽，负责调度 View 层和 Model 层，将用户界面和业务逻辑合理的组织在一起，起粘合剂的效果。 |

### 三层架构与MVC
三层架构是由表示层（UI）、业务逻辑层（BLL）和数据访问层（DAL）三个层次构成的，而 MVC 则是由视图（View）层、控制（Controller）层以及模型（Model）层，且它们之间并不是一一对应的。

### MVC优缺点？
MVC 模式具有以下优点：
- 降低代码耦合性
- 有利于分工合作
- 有利于组件的重用
MVC 模式存在以下不足之处：
- 增加了系统结构和实现的复杂性
- 视图与控制器间的联系过于紧密
- 视图对模型数据的低效率访问

### MVC 的工作流程
MVC 的工作流程如下：
1. 用户发送请求到服务器；
2. 在服务器中，请求被控制层（Controller）接收；
3. Controller 调用相应的 Model 层处理请求；
4. Model 层处理完毕将结果返回到 Controller；
5. Controller 再根据 Model 返回的请求处理结果，找到相应的 View 视图；
6. View 视图渲染数据后最终响应给浏览器。

## SpringMVC概述
### 简介
Spring MVC（全称 Spring Web MVC）是 Spring 框架提供的一款基于 MVC 模式的轻量级 Web 开发框架，是 Spring 为表示层（UI）开发提供的一整套完备的解决方案。
Spring MVC 各层的职责如下：
Model：负责对请求进行处理，并将结果返回给 Controller；
View：负责将请求的处理结果进行渲染，展示在客户端浏览器上；
Controller：是 Model 和 View 交互的纽带；主要负责接收用户请求，并调用 Model 对请求处理，然后将 Model 的处理结果传递给 View。

Spring MVC 本质是对 Servlet 的进一步封装，其最核心的组件是 DispatcherServlet，它是 Spring MVC 的前端控制器，主要负责对请求和响应的统一地处理和分发。

### 开发流程
1. 导入 Spring MVC 相关依赖
2. 导入 [Thymeleaf](../frame/nofoo/thymeleaf.md) 相关依赖
3. 配置 DispatcherServlet（前端控制器）

Spring MVC 是基于 Servlet 的，跟所有的 Servlet 一样，DispatcherServlet 也需要在 web.xml 中进行配置，它才能够正常工作，该配置文件的默认命名规则为 {servlet-name}-servlet.xml，例如 springMVC-servlet.xml。示例代码如下：
~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!-- 配置 SpringMVC 的前端控制器，对浏览器发送的请求统一进行处理 -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--配置 DispatcherServlet 的一个初始化参数：spring mvc 配置文件按的位置和名称-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
        <!--作为框架的核心组件，在启动过程中有大量的初始化操作要做
            而这些操作放在第一次请求时才执行会严重影响访问速度
            因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <!--设置springMVC的核心控制器所能处理的请求的请求路径
        /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
        但是/不能匹配.jsp请求路径的请求-->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
~~~

4. 创建 Spring MVC 配置  
在项目的 src 目录下，创建一个名为 springMVC.xml 的配置文件，配置内容如下：
~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!--开启组件扫描-->
    <context:component-scan base-package="net.biancheng.c"></context:component-scan>
    <!-- 配置 Thymeleaf 视图解析器 -->
    <bean id="viewResolver"
          class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
        <property name="order" value="1"/>
        <property name="characterEncoding" value="UTF-8"/>
        <property name="templateEngine">
            <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
                <property name="templateResolver">
                    <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                        <!-- 视图前缀 -->
                        <property name="prefix" value="/WEB-INF/templates/"/>
                        <!-- 视图后缀 -->
                        <property name="suffix" value=".html"/>
                        <property name="templateMode" value="HTML5"/>
                        <property name="characterEncoding" value="UTF-8"/>
                    </bean>
                </property>
            </bean>
        </property>
    </bean>
</beans>
~~~


5. 创建 Controller（控制器）  
在 Spring MVC 中，一个普通的 Java 类只要标注了 @Controller 注解，就会被 Spring MVC 识别成 Controller。Controller 类中的每一个处理请求的方法被称为“控制器方法”。例如，创建一个名为 HelloController 的控制器类：
~~~java
@Controller //声明这是一个控制器
@RequestMapping("/hello")  //访问路径 ，等价于url-pattern
public class HelloController {
	@RequestMapping("/test1")  //访问路径
	public String hello1(){
		System.out.println("hello world");
        //视图名，视图为：视图前缀+index+视图后缀，即 /WEB-INF/template/index.jsp
		return "index"; // 跳转:/index.jsp  
	}
	@RequestMapping("/test2") //访问路径
	public String hello2(){
		System.out.println("hello c9");
		return "views/users";//  跳转:/views/user.jsp
	}
}
~~~
6. 创建 View（视图）

### SpringMVC执行流程
![SpringMVC执行流程](/frameimg/SpringMVC执行流程.png)
**SpringMVC执行流程:**
1. 用户发送请求至前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用处理器映射器HandlerMapping。
3. 处理器映射器根据请求url找到具体的处理器，生成处理器执行链HandlerExecutionChain(包括处理器对象和处理器拦截器)一并返回给DispatcherServlet。
4. DispatcherServlet根据处理器Handler获取处理器适配器HandlerAdapter执行HandlerAdapter处理一系列的操作，如：参数封装，数据格式转换，数据验证等操作
5. 执行处理器Handler(Controller，也叫页面控制器)。
6. Handler执行完成返回ModelAndView
7. HandlerAdapter将Handler执行结果ModelAndView返回到DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9. ViewReslover解析后返回具体View
10. DispatcherServlet对View进行渲染视图（即将模型数据model填充至视图中）。
11. DispatcherServlet响应用户。

### 接收请求参数

#### 基本类型参数

> 请求参数和方法的形参 同名即可

> springMVC默认可以识别的日期字符串格式为： yyyy/MM/dd HH:mm:ss
> 通过@DateTimeFormat可以修改默认日志格式

```java
// id  name gender
// http://localhost:8989/xxx/../test1?id=1&name=zzz&gender=false&birth=2018-12-12 12:20:30
@RequestMapping("/test1")
public String testParam1(Integer id,
                         String name,
                         Boolean gender,
                         @DateTimeFormat(pattern="yyyy-MM-dd HH:mm:ss")Date birth){
    System.out.println("test param1");
    return "index";
}
```

#### 实体收参【`重点`】

> 请求参数和实体的属性 同名即可

```java
public class User {
	private Integer id;
	private String name;
	@DateTimeFormat(pattern="yyyy-MM-dd")
	private Date birth;
	private Boolean gender;
	//set/get ...
}

//http://localhost:8989/.../test2?id=1&name=zzz&gender=false&birth=2018-12-12 12:20:30
@RequestMapping("/test2")
public String testParam2(User user){
    System.out.println("test param2");
    System.out.println("user:"+user);
    return "index";
}
```

#### 数组收参

> 简单类型的 数组

```html
<form>
    ......
    <input type="checkbox" name="hobby" value="fb"/>足球 
    <input type="checkbox" name="hobby" value="bb"/>篮球 
    <input type="checkbox" name="hobby" value="vb"/>排球
    
</form>
```

```java
//http://localhost:8989/.../test3?hobby=football&hobby=basketball
@RequestMapping("/test3")
public String testParam3(String[] hobby){
    for(String h:hobby){
        System.out.print(h+" ");
    }
    return "index";
}
```

#### 集合收参 【了解】

```java
public class UserList {
	//private User[] users;
	private List<User> users;
	//set/get..
}

// <input type="text" name="users[0].id"/>
// post请求：http://...?users[0].id=1&users[0].name=zhangsan&users[0].birth=2018-12-12&users[1].id=2&....
@RequestMapping("/test4")
public String testParam4(UserList userList){
    for(User user:userList.getUsers()){
        System.out.println(user);
    }
    return "index";
}
```

#### 路径参数

```java
// {id} 定义名为id的路径；【/hello/{id}】的匹配能力和【/hello/*】等价
// http://localhost:8989/.../hello/10   {id}匹配到10
@RequestMapping("/hello/{id}")
// @PathVariable将{id}路径匹配到值赋给id参数
// 路径名和参数名相同则@PathVariable("id")可简写为 @PathVariable
public String testParam5(@PathVariable("id") Integer id){
    System.out.println("id:"+id);            
    return "index";
}

// http://localhost:8989/.../hello/tom   {username}匹配到tom
@RequestMapping("/hello/{username}")
public String testParam6(@PathVariable("username") String name){//将{username}路径匹配到的值赋给name参数
    System.out.println("username:"+name);
    return "index";
}
```

#### 中文乱码

> 首先，页面中字符集统一

```jsp
JSP : <%@page  pageEncoding="utf-8" %>
HTML : <meta charset="UTF-8">
```

> 其次，tomcat中字符集设置，对get请求中，中文参数乱码有效

```markdown
Tomcat配置：URIEncoding=utf-8
```

> 最后，设置此filter，对post请求中，中文参数乱码有效

```xml
<!-- 此过滤器会进行：request.setCharactorEncoding("utf-8"); -->
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



### 跳转
#### 转发

```java
@RequestMapping("/forw")
class ForwardController{
    @RequestMapping("/test1")
    public String testForward(){
        System.out.println("test forward1");
        // 转发跳转 /views/users.jsp
        // return "views/users";//和下一行等价
        return "forward:/views/users.jsp";
    }
    @RequestMapping("/test2")
    public String testForward2(){
        System.out.println("test forward2");
        //转发到  /forw/test1
        //return "forward:test1";//相对路径(转发到本类中的test1)
        //转发到  /forw/test1
        return "forward:/forw/test1"; //绝对路径
    }
}
```

#### 重定向

```java
@RequestMapping("/redir")
class RedirectController{
    @RequestMapping("/test1")
    public String testRedirect1(){
        System.out.println("test redirect1");
        //重定向到 /redir/test1
        //return "redirect:test1"; //相对路径(转发到本类中的test1)
        return "redirect:/redir/test1";//绝对路径
    }
    @RequestMapping("/test2")
    public String testRedirect2(){
        System.out.println("test redirect2");
        //重定向到 /views/users.jsp
        return "redirect:/view/user.jsp";
    }
}
```

#### 跳转细节

> * 在增删改之后，为了防止请求重复提交，重定向跳转
>
> * 在查询之后，可以做转发跳转



### 传值

从处理器往页面传值的四种方式：

1. 传统的 HttpServletRequest、HttpSession。
2. 返回逻辑视图名，则可以通过 Model 参数传参。
3. 返回 ModelAndView。
4. SessionAttribute（可以用在重定向的场景下）

---

> C得到数据后，跳转到V，并向V传递数据。进而V中可以渲染数据，让用户看到含有数据的页面
>
> 转发跳转：Request作用域
>
> 重定向跳转：Session作用域

#### Request和Session

```java
//形参中 即可获得 request 和 session对象
@RequestMapping("/test1")
public String testData(HttpSession session,HttpServletRequest req，Integer id){
    session.setAttribute("user",new User());
    req.setAttribute("age", 18);
    req.setAttribute("users",Arrays.asList(new User(),new User()));
    //return "test2";
    return "forward:/WEB-INF/test2.jsp";
}
```

#### JSP中取值

> 建议：重点复习 EL  JSTL

```jsp
//jsp中用EL表达式 取值即可
<fmt:formatDate value="${sessionScope.user.birth}" pattern="yyyy-MM-dd"/> <br/>
${sessionScope.user.birth} <br>
${requestScope.age}
```

#### Model

```java
//model中的数据，会在V渲染之前，将数据复制一份给request
@RequestMapping("/test")
public String testData(Model model){
    model.addAttribute("name", "张三");
    return "index";
}

//jsp中用EL表达式 取值即可
${requestScope.name}
```

#### ModelAndView

```java
//modelandview 可以集中管理 跳转和数据
@RequestMapping("/test")
public ModelAndView testData(){//返回值类型为ModelAndView
    //新建ModelAndView对象
    ModelAndView mv = new ModelAndView();
    // 设置视图名，即如何跳转
    mv.setViewName("forward:/index.jsp");
    // 增加数据
    mv.addObject("age",18);
    return mv;
}

//jsp中用EL表达式 取值即可
${requestScope.age}
```

#### @SessionAttributes

> * @SessionAttributes({"gender","name"})  ：model中的 name和gender 会存入session中
>
> * SessionStatus 移除session

```java
@Controller
@SessionAttributes({"gender","name"}) // model中的 name和gender 会存入session中
public class UserController {

    @RequestMapping("/hello")
    public String hello(Model m){
        m.addAttribute("gender",true); // 会存入session
        mv.addObject("name","zhj"); // 会存入session
        return "index";
    }
    
    @RequestMapping("/hello2")
    public String hello(SessionStatus status){
        // 移除通过SessionAttributes存入的session
        status.setComplete();
        return "index";
    }
}
```



### 静态资源

---

#### 静态资源问题

> 静态资源：html，js文件，css文件，图片文件

> 静态文件没有url-pattern,所以默认是访问不到的，之所以可以访问，是因为，tomcat中有一个全局的servlet：org.apache.catalina.servlets.DefaultServlet，它的url-pattern是 "/",是全局默认的Servlet.  所以每个项目中不能匹配的静态资源的请求，有这个Servlet来处理即可。

> 但，在SpringMVC中DispatcherServlet也采用了 “/” 作为url-pattern, 则项目中不会再使用全局的Serlvet，则静态资源不能完成访问。

#### 解决方案1

> DispathcerServlet采用其他的url-pattern
>
> 此时，所有访问handler的路径都要以 action结尾！！

```xml
<servlet>
  	<servlet-name>mvc9</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>mvc9</servlet-name>
    <url-pattern>*.action</url-pattern>
</servlet-mapping>
```

#### 解决方案2

> DispathcerServlet的url-pattern依然采用 "/",但追加配置

```xml
<!-- 
  额外的增加一个handler，且其requestMapping:  "/**" 可以匹配所有请求，但是优先级最低
  所以如果其他所有的handler都匹配不上，请求会转向 "/**" ,恰好，这个handler就是处理静态资源的
  处理方式：将请求转会到tomcat中名为default的Servlet
  -->
<mvc:default-servlet-handler/>
```

#### 解决方案3

> * mapping是访问路径，location是静态资源存放的路径
> * 将/html/** 中 /**匹配到的内容，拼接到 /hhh/后
>   http://..../html/a.html  访问 /hhh/a.html

```xml
<mvc:resources mapping="/html/**" location="/hhh/"/>
```

### Json处理

---

主流处理方案三种：

- jackson（推荐）
- gson
- fastjson（了解）

SpringMVC 中，默认提供了对 jackson 和 gson 的支持。

#### 导入依赖

```xml
<!-- Jackson springMVC默认的Json解决方案选择是 Jackson，所以只需要导入jackson的jar，即可使用。-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.8</version>
</dependency>
```

#### 使用@ResponseBody

```java
@Controller
public class JsonController{    
	@RequestMapping("/test1")
    @ResponseBody //将handler的返回值，转换成json(jackson),并将json响应给客户端。
    public User hello1(){
        System.out.println("hello world");
        User user = new User();
        return user;
    }
    // @ResponseBody还可以用在handler的返回值上
    @RequestMapping("/test2")
    public @ResponseBody List<User> hello2(){
        System.out.println("hello world");
        List<User> users = Arrays.asList(new User(),new User());
        return users;
    }
    // 如果返回值已经是字符串，则不需要转json，直接将字符串响应给客户端 
    @RequestMapping(value="/test3",produces = "text/html;charset=utf-8") //produces 防止中文乱码
    @ResponseBody 
    public String hello2(){
        System.out.println("hello world");
        return "你好";
    }
}
```

#### 使用@RestController

> Controller类上加了@RestController注解，等价于在类中的每个方法上都加了@ResponseBody

```java
@Controller
@RestController
public class JsonController{
    @RequestMapping("/test1")
    public User hello1(){
        System.out.println("hello world");
        User user = new User();
        return user;
    }
    //@ResponseBody还可以用在handler的返回值上
    @RequestMapping("/test2")
    public List<User> hello2(){
        System.out.println("hello world");
        List<User> users = Arrays.asList(new User(),new User());
        return users;
    }
}
```



#### 使用@RequestBody

> #### **@RequestBody**, 接收Json参数

##### 定义Handler

```java
class User{
    private Integer id;
    private String name;
    private Boolean gender;
    //set get
}
```

```java
@RequestMapping("/users")
public String addUser(@RequestBody User user){//@RequestBody将请求体中的json数据转换为java对象
    System.out.println("cap2");
    System.out.println("Post user :"+user);
    return "index";
}
```

##### Ajax发送json

```js
var xhr = new XMLHttpRequest();
xhr.open("post","${pageContext.request.contextPath}/users?"+new Date().getTime());
xhr.setRequestHeader("content-type","application/json");//设置请求头
xhr.send('{"id":1,"name":"shine","gender":"true"}');//传递json串
```

```javascript
//ajax
var user = {id:1,name:"shine"};
$.ajax({
    url:'${pageContext.request.contextPath}/json2/test4',
    type:'post',
    contentType:"application/json",//声明请求参数类型为 json
    data:JSON.stringify(user),// 转换js对象成json
    success:function(ret){
        console.log(ret);
    }
});
```



#### Jackson常用注解

##### 日期格式化

> @JsonFormat(pattern="yyyy-MM-dd HH:mm:ss",timezone = "GMT+8")

```java
public class User{
	private Integer id;
	private String name;
	@JsonFormat(pattern="yyyy-MM-dd HH:mm:ss",timezone = "GMT+8")
	private Date birth;
    ....
    get/set
}
```

##### 属性名修改

> @JsonProperty("new_name")

```java
public class User{
	@JsonProperty("new_id") //不再使用原属性名，而是 "new_id"
    private Integer id;
	private String name;
    ....
    get/set
}
输出的json：{“new_id”:xx,"name":"xx"}
```

##### 属性忽略

> @JsonIgnore

```java
public class User{
    private Integer id;
    @JsonIgnore // 生成json时，忽略此属性
	private String name;
    ....
    get/set
}
输出json时: {"id":xx}
```

##### null和empty属性排除

> Jackson 默认会输出null值的属性，如果不需要，可以排除。
>
> @JsonInclude(JsonInclude.Include.NON_NULL) //null值 属性不输出
> @JsonInclude(value= JsonInclude.Include.NON_EMPTY) // empty属性不输出( 空串，长度为0的集合，null值)

```java
public class User{
    private Integer id;
    @JsonInclude(JsonInclude.Include.NON_NULL) // 若"name==null" 忽略此属性
	private String name;
    @JsonInclude(value= JsonInclude.Include.NON_EMPTY)  // 若hobby长度为0或==null 忽略此属性
    private List<String> hobby;
    ....
    get/set
}
如果name=null,且 hobby长度为0，则输出json时：{"id":xx}
```

##### 自定义序列化

> @JsonSerialize(using = MySerializer.class) // 使用MySerializer输出某属性

```java
public class User {
    private Integer id;
    private String name;
    @JsonSerialize(using = MySerializer.class)
    private Double salary = 10000.126;//在输出此属性时，使用MySerializer输出
    ....
    get/set
}
则输出json时：{"id":xx,"name":"xxx","salary":10000.13}
```

```java
public class MySerializer extends JsonSerializer<Double> {

    // value即 Double salary的值
    @Override 
    public void serialize(Double value, JsonGenerator gen, SerializerProvider serializers) throws IOException {
        // 将Double salary的值 四舍五入
        String number = BigDecimal.valueOf(value).setScale(2, BigDecimal.ROUND_HALF_UP).toString();
        // 输出 四舍五入后的值
        gen.writeNumber(number);
    }
}
```



#### FastJson

##### 导入依赖

```xml
<!-- FastJson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.54</version>
</dependency>
```

##### 安装FastJson

```xml
<mvc:annotation-driven>
    <!-- 安装FastJson,转换器 -->
    <mvc:message-converters>
        <bean class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter">
            <!-- 声明转换类型:json -->
            <property name="supportedMediaTypes">
                <list>
                    <value>application/json</value>
                </list>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

##### 使用

> @ResponseBody  @RequestBody @RestController 使用方法不变

##### 常用注解

> * 日期格式化：@JSONField(format="yyyy/MM/dd")
> * 属性名修改：@JSONField(name="birth"）
> * 忽略属性：@JSONField(serialize = false)
> * 包含null值：@JSONField(serialzeFeatures = SerializerFeature.WriteMapNullValue)  默认会忽略所有null值,有此注解会输出null
>   * @JSONField(serialzeFeatures = SerializerFeature.WriteNullStringAsEmpty)  null的String输出为""
> * 自定义序列化：@JSONField(serializeUsing = MySerializer2.class)

```java
public class User implements Serializable{
	@JSONField(serialize = false)
    private Integer id;
    @JSONField(name="NAME",serialzeFeatures = SerializerFeature.WriteNullStringAsEmpty)
	private String name;
    @JSONField(serialzeFeatures = SerializerFeature.WriteMapNullValue) 
    private String city;
	@JSONField(format="yyyy/MM/dd")
	private Date birth;
    @JSONField(serializeUsing = MySerializer2.class)
    private Double salary;
	...
}
```

```java
public class MySerializer2 implements ObjectSerializer {
    @Override
    public void write(JSONSerializer serializer, Object object, Object fieldName, Type fieldType,
                      int features) throws IOException {
        Double value = (Double) object; // salary属性值
        String text = value + "元";// 在salary后拼接 “元”
        serializer.write(text); // 输出拼接后的内容
    }
}
```

```java
new User(1，null，null，new Date()，100.5);
// 如上对象，转换json：
{NAME:""，city:null，"birth":"2020/12/12"，"salary":"100.5元"}

```

### 异常解析器

---

两种方式：

- 实现 HandlerExceptionResolver
- 通过 @ExceptionHandler 注解定义
- SimpleMappingExceptionResolver

#### 现有方案，分散处理

> Controller中的每个Handler自己处理异常
>
> 此种处理方案，异常处理逻辑，分散在各个handler中，不利于集中管理

```java
public String xxx(){
    try{
    	...
    }catch(Exception1 e){
    	e.printStackTrace();
        return "redirect:/xx/error1";
    }catch(Exception2 e){
    	e.printStackTrace();
        return "redirect:/xx/error2";
    }
}
```

#### 异常解析器，统一处理

> Controller中的每个Handler不再自己处理异常，而是直接throws所有异常。
>
> 定义一个“异常解析器” 集中捕获处理 所有异常
>
> 此种方案，在集中管理异常方面，更有优势！

```java
public class MyExResolver implements HandlerExceptionResolver{
	/**
	 * 异常解析器：主体逻辑
	 * 执行时刻：当handler中抛出异常时，会执行：捕获异常，并可以跳到错误页面
	 */
	@Override
	public ModelAndView resolveException(HttpServletRequest request,
			HttpServletResponse response, Object handler, Exception ex) {
		ex.printStackTrace();//打印异常栈
		//创建一个ModelAndView
		ModelAndView mv = new ModelAndView();
		//识别异常
		if (ex instanceof Exception1) {
			mv.setViewName("redirect:/xxx/error1");
		}else if(ex instanceof Exception2){
			mv.setViewName("redirect:/xxx/error2");
		}else{
			mv.setViewName("redirect:/xxx/error");
		}
		return mv;
	}
}
```

```xml
<!-- 声明异常解析器 -->	
<bean class="com.baizhi.exception.resolver.MyExResolver"></bean>
```



### 拦截器

和过滤器的区别：

- **执行时机：拦截器晚于过滤器。**
- 拦截器是一种 AOP 风格的过滤器。

---

#### 作用

> 作用：抽取handler中的冗余功能

#### 定义拦截器

> 执行顺序： preHandle--postHandle--afterCompletion

```java
public class MyInter1 implements HandlerInterceptor{
	//主要逻辑：在handler之前执行：抽取handler中的冗余代码
	@Override
	public boolean preHandle(HttpServletRequest request,
			HttpServletResponse response, Object handler) throws Exception {
		System.out.println("pre~~~");
        /*
        response.sendRedirect("/springMVC_day2/index.jsp");//响应
        return false;//中断请求
        */
		return true;//放行，后续的拦截器或handler就会执行
	}
	//在handler之后执行:进一步的响应定制
	@Override
	public void postHandle(HttpServletRequest request,
			HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		System.out.println("post~~");
	}
	//在页面渲染完毕之后，执行：资源回收
	@Override
	public void afterCompletion(HttpServletRequest request,
			HttpServletResponse response, Object handler, Exception ex)
			throws Exception {
		System.out.println("after~~");
	}
}
```

#### 配置拦截路径

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/inter/test1"/>
        <mvc:mapping path="/inter/test2"/>
        <mvc:mapping path="/inter/test*"/> <!-- test开头 -->
        <mvc:mapping path="/inter/**"/> <!-- /** 任意多级任意路径 -->
        <mvc:exclude-mapping path="/inter/a/**"/>   <!--不拦截此路径-->
        <bean class="com.baizhi.interceptor.MyInter1"></bean>   <!--拦截器类-->
    </mvc:interceptor>
</mvc:interceptors>
```



### 上传

---

#### 导入jar

```xml
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.4</version>
</dependency>

<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.3</version>
    <exclusions>
        <exclusion>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

#### 表单

```html
<form action="${pageContext.request.contextPath }/upload/test1" method="post" 
      enctype="multipart/form-data">
  file: <input type="file" name="source"/> <br>
  <input type="submit" value="提交"/>
</form>
```

#### 上传解析器

```xml
<!-- 上传解析器 
	     id必须是：“multipartResolver”
 -->
<bean id="multipartResolver" 
      class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- 最大可上传的文件大小  单位：byte  超出后会抛出MaxUploadSizeExceededException异常，可以异常解析器捕获 -->
    <property name="maxUploadSize" value="1048576"></property>
</bean>
```

#### Handler

```java
@RequestMapping("/test1")
public String hello1(String username,MultipartFile source,HttpSession session) {
    //文件的原始名称
    String filename = source.getOriginalFilename();
    //定制全局唯一的命名
    String unique = UUID.randomUUID().toString();
    //获得文件的后缀
    String ext = FilenameUtils.getExtension(filename);//abc.txt   txt    hello.html  html
    //定制全局唯一的文件名
    String uniqueFileName = unique+"."+ext;
    System.out.println("唯一的文件名:"+uniqueFileName);

    //文件的类型
    String type = source.getContentType();
    System.out.println("filename:"+filename+" type:"+type);

    //获得 upload_file的磁盘路径 ==> 在webapp目录下创建一个目录"upload_file",且此目录初始不要为空，否则编译时被忽略
    String real_path = session.getServletContext().getRealPath("/upload_file");
    System.out.println("real_path:"+real_path);

    //将上传的文件，存入磁盘路径中
    //source.transferTo(new File("d:/xxxx/xxxx/xx.jpg"))
    source.transferTo(new File(real_path+"\\"+uniqueFileName));
    return "index";
}
```



### 下载

---

#### 超链

```html
<a href="${pageContext.request.contextPath}/download/test1?name=Koala.jpg">下载</a>
```

#### Handler

```java
@RequestMapping("/test1")
public void hello1(String name,HttpSession session,HttpServletResponse response){
    System.out.println("name:"+name);
    //获得要下载文件的绝对路径
    String path = session.getServletContext().getRealPath("/upload_file");
    //文件的完整路径
    String real_path = path+"\\"+name;

    //设置响应头  告知浏览器，要以附件的形式保存内容   filename=浏览器显示的下载文件名
    response.setHeader("content-disposition","attachment;filename="+name);

    //读取目标文件，写出给客户端
    IOUtils.copy(new FileInputStream(real_path), response.getOutputStream());

    //上一步，已经是响应了,所以此handler直接是void
}
```



### 验证码

---

#### 作用

> 防止暴力攻击，前端安全保障

#### 导入jar

```xml
<!-- Kaptcha -->
<dependency>
    <groupId>com.github.penggle</groupId>
    <artifactId>kaptcha</artifactId>
    <version>2.3.2</version>
    <exclusions>
        <exclusion>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

#### 声明验证码组件

```xml
<servlet>
    <servlet-name>cap</servlet-name>
    <servlet-class>com.google.code.kaptcha.servlet.KaptchaServlet</servlet-class>
    <init-param>
      <param-name>kaptcha.border</param-name>
      <param-value>no</param-value>
    </init-param>
    <init-param>
      <param-name>kaptcha.textproducer.char.length</param-name>
      <param-value>4</param-value>
    </init-param>
    <init-param>
      <param-name>kaptcha.textproducer.char.string</param-name>
      <param-value>abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789</param-value>
    </init-param>
    <init-param>
      <param-name>kaptcha.background.clear.to</param-name>
      <param-value>211,229,237</param-value>
    </init-param>
    <init-param>
      <!-- session.setAttribute("captcha","验证码") -->
      <param-name>kaptcha.session.key</param-name>
      <param-value>captcha</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>cap</servlet-name>
    <url-pattern>/captcha</url-pattern>
  </servlet-mapping>
```

#### Page

```html
<img src="${pageContext.request.contextPath}/captcha" style="width:85px" id="cap"/>
<script>
    $(function(){
        $("#cap").click(function(){
            //刷新验证码
            path = $(this).attr("src")+"?"+new Date().getTime();
            $(this).attr("src",path);
        });
    });
</script>
```



### REST

---

#### 开发风格

> 是一种开发风格，遵从此风格开发软件，符合REST风格，则RESTful。

> 两个核心要求：
>
> * 每个资源都有唯一的标识(URL)
> * 不同的行为，使用对应的http-method

| 访问标识                                 | 资源            |
| ---------------------------------------- | --------------- |
| http://localhost:8989/xxx/users          | 所有用户        |
| http://localhost:8989/xxx/users/1        | 用户1           |
| http://localhost:8989/xxx/users/1/orders | 用户1的所有订单 |

| 请求方式       | 标识                                     | 意图                        |
| -------------- | ---------------------------------------- | --------------------------- |
| GET（查询）    | http://localhost:8989/xxx/users          | 查询所有用户                |
| POST（添加）   | http://localhost:8989/xxx/users          | 在所有用户中增加一个        |
| PUT（更新）    | http://localhost:8989/xxx/users          | 在所有用户中修改一个        |
| DELETE（删除） | http://localhost:8989/xxx/users/1        | 删除用户1                   |
| GET            | http://localhost:8989/xxx/users/1        | 查询用户1                   |
| GET            | http://localhost:8989/xxx/users/1/orders | 查询用户1的所有订单         |
| POST           | http://localhost:8989/xxx/users/1/orders | 在用户1的所有订单中增加一个 |

#### 优点

> * **输出json：

#### 使用

##### 定义Rest风格的 Controller

> @RequestMapping(value="/users",method = RequestMethod.GET)
>
> 等价
>
> @GetMapping("/users")

```java
@RestController
public class RestController {
    @GetMapping("/users")
    public List<User> queryAllUsers(){
        System.out.println("get");
        List<User> users = ....
        return users;
    }

    @PostMapping("/users")
    public String addUser(@RequestBody User user){
        System.out.println("Post user :"+user);
        return "{status:1}";
    }
    
    @PutMapping("/users")
    public String updateUser(@RequestBody User user){
        System.out.println("Put user" user:"+user);
        return "{status:1}";
    }

    @GetMapping("/users/{id}")
    public String queryOneUser(@PathVariable Integer id){//@PathVariable 接收路径中的值
        System.out.println("Get user id:"+id);
        return "{status:1}";
    }

    @DeleteMapping("/users/{id}")
    public String deleteOneUser(@PathVariable Integer id){//@PathVariable 接收路径中的值
        System.out.println("delete user id:"+id);
        return "{status:1}";
    }
}
```

##### Ajax请求

```html
<script>    
	function putUser(){ // 发送更新请求 （增加请求发送方式也是如此）
        var xhr = new XMLHttpRequest();
    	//定义 put，delete,get,post方式 即可，不用定义_method
        xhr.open("put","${pageContext.request.contextPath}/rest04/users");
    	// 设置请求头
        xhr.setRequestHeader("content-type","application/json")；
        // 设置请求参数
        var user = {id:1，NAME:"shine"，city:"bj"，"birth":"2020/12/12"，"salary":100.5};
        xhr.send(JSON.stringify(user));
        xhr.onreadystatechange=function(){
            if(xhr.readyState==4 && xhr.status==200){
                var ret = xhr.responseText;
                // 解析json，并输出
                console.log(JSON.parse(ret));
            }
        }
    	/*$.ajax({
            url:'${pageContext.request.contextPath}/rest04/users',
            type:'put',
            contentType:"application/json",//声明请求参数类型为 json
            data:JSON.stringify(user),// 转换js对象成json
            success:function(ret){
                console.log(JSON.parse(ret));
            }
        });*/
    }

	function delUser(){  // 发送删除请求
        var xhr = new XMLHttpRequest();
        //定义 put，delete,get,post方式 即可，不用定义_method
        xhr.open("delete","${pageContext.request.contextPath}/rest04/users/1");
        xhr.send();
        xhr.onreadystatechange=function(){
            if(xhr.readyState==4 && xhr.status==200){
                var ret = xhr.responseText;
                console.log(JSON.parse(ret));
            }
        }
    }
</script>
```

### 跨域请求

---

#### 域

> 域：协议+IP（域名）+端口
>
> * http://localhost:8989
>
> * http://localhost:8080
>
> * http://www.baidu.com:80

#### Ajax跨域问题

> * Ajax发送请求时，不允许跨域，以防用户信息泄露。
>
> * 当Ajax跨域请求时，响应会被浏览器拦截(同源策略)，并报错。即浏览器默认不允许ajax跨域得到响应内容。
> * 互相信任的域之间如果需要ajax访问，(比如前后端分离项目中，前端项目和后端项目之间)，则需要额外的设置才可正常请求。

#### 解决方案

> * 允许其他域访问
>
> * 在被访问方的Controller类上，添加注解

```java
@CrossOrigin("http://localhost:8080") //允许此域发请求访问
public class SysUserController {
	....
}
```

> * 携带对方cookie，使得session可用
>
> * 在访问方，ajax中添加属性：withCredentials: true

```javascript
$.ajax({
     type: "POST",
     url: "http://localhost:8989/web/sys/login",
     ...,
     xhrFields: {
       // 跨域携带cookie
       withCredentials: true
     }
});
或
var xhr = new XMLHttpRequest();
// 跨域携带cookie
xhr.withCredentials=true;
```
### Spring整合

---

#### 整合思路

> 此时项目中有两个工厂
>
> * DispatcherServlet 启动的springMVC工厂==负责生产C及springMVC自己的系统组件
> * ContextLoaderListener 启动的spring工厂==负责生产其他所有组件
> * springMVC的工厂会被设置为spring工厂的子工厂，可以随意获取spring工厂中的组件
> * 整合过程，就是累加：代码+依赖+配置。然后将service注入给controller即可

#### 整合技巧

Spring 是一个父容器，SpringMVC 是一个子容器，子容器中可以访问父容器的组件，父容器不可以访问子容器的组件。

例如，我们将 Service、Dao、Component 都注册到 Spring 容器中，将 Controller 注册到 SpringMVC 容器中，则在 Controller 中，可以注入 Service、Dao、Component ；但是 Service、Dao、Component 中无法注入 Controller。



> 两个工厂不能有彼此侵入，即，生产的组件不能有重合。

```xml
<!-- 告知SpringMVC  哪些包中 存在 被注解的类
	use-default-filters=true 凡是被 @Controller @Service  @Repository注解的类，都会被扫描
	use-default-filters=false 默认不扫描包内的任何类, 只扫描include-filter中指定的类
	只扫描被@Controller注解的类
-->
<context:component-scan base-package="com.zhj" use-default-filters="false">
 	<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

```xml
<!-- 告知Spring
     唯独不扫描@Controller注解的类 -->
<context:component-scan base-package="com.zhj" use-default-filters="true">
	<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```
