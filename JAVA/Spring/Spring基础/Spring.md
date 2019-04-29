[TOC]
# Spring

## 为什么要使用 spring？
spring 提供 ioc 技术，容器会帮你管理依赖的对象，从而不需要自己创建和管理依赖对象了，更轻松的实现了程序的解耦。
spring 提供了事务支持，使得事务操作变的更加方便。
spring 提供了面向切片编程，这样可以更方便的处理某一类的问题。
更方便的框架集成，spring 可以很方便的集成其他框架，比如 MyBatis、hibernate 等。

## 解释一下什么是 aop？
aop 是面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。

简单来说就是统一处理某一“切面”（类）的问题的编程思想，比如统一处理日志、异常等。

## 解释一下什么是 ioc？
ioc：Inversionof Control（中文：控制反转）是 spring 的核心，对于 spring 框架来说，就是由 spring 来负责控制对象的生命周期和对象间的关系。

简单来说，控制指的是当前对象对内部成员的控制权；控制反转指的是，这种控制权不由当前对象管理了，由其他（类,第三方容器）来管理。

## spring 有哪些主要模块？
spring core：框架的最基础部分，提供 ioc 和依赖注入特性。
spring context：构建于 core 封装包基础上的 context 封装包，提供了一种框架式的对象访问方法。
spring dao：Data Access Object 提供了JDBC的抽象层。
spring aop：提供了面向切面的编程实现，让你可以自定义拦截器、切点等。
spring Web：提供了针对 Web 开发的集成特性，例如文件上传，利用 servlet listeners 进行 ioc 容器初始化和针对 Web 的 ApplicationContext。
spring Web mvc：spring 中的 mvc 封装包提供了 Web 应用的 Model-View-Controller（MVC）的实现。

## spring 常用的注入方式有哪些？
setter 属性注入
构造方法注入
注解方式注入

## spring 中的 bean 是线程安全的吗？
spring 中的 bean 默认是单例模式，spring 框架并没有对单例 bean 进行多线程的封装处理。

实际上大部分时候 spring bean 无状态的（比如 dao 类），所有某种程度上来说 bean 也是安全的，但如果 bean 有状态的话（比如 view model 对象），那就要开发者自己去保证线程安全了，最简单的就是改变 bean 的作用域，把“singleton”变更为“prototype”，这样请求 bean 相当于 new Bean()了，所以就可以保证线程安全了。

有状态就是有数据存储功能。
无状态就是不会保存数据。

## spring 支持几种 bean 的作用域？
spring 支持 5 种作用域，如下：

singleton：spring ioc 容器中只存在一个 bean 实例，bean 以单例模式存在，是系统默认值；
prototype：每次从容器调用 bean 时都会创建一个新的示例，既每次 getBean()相当于执行 new Bean()操作；
Web 环境下的作用域：
request：每次 http 请求都会创建一个 bean；
session：同一个 http session 共享一个 bean 实例；
global-session：用于 portlet 容器，因为每个 portlet 有单独的 session，globalsession 提供一个全局性的 http session。
注意： 使用 prototype 作用域需要慎重的思考，因为频繁创建和销毁 bean 会带来很大的性能开销。

## spring 自动装配 bean 有哪些方式？
no：默认值，表示没有自动装配，应使用显式 bean 引用进行装配。
byName：它根据 bean 的名称注入对象依赖项。
byType：它根据类型注入对象依赖项。
构造函数：通过构造函数来注入依赖项，需要设置大量的参数。
autodetect：容器首先通过构造函数使用 autowire 装配，如果不能，则通过 byType 自动装配。

## @Autowired 的作用是什么？
@Autowired 它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作，通过@Autowired 的使用来消除 set/get 方法。

Spring Boot/Spring Cloud
104. 什么是 spring boot？
spring boot 是为 spring 服务的，是用来简化新 spring 应用的初始搭建以及开发过程的。

# SpringMVC

## 什么是 SpringMVC？
SpringMVC 是 spring 的一个模块，基于 MVC 的一个框架，无需中间整合层来整

## Spring MVC 的优点
1. 它是基于组件技术的.全部的应用对象,无论控制器和视图,还是业务对象之类的都是 组件.并且和 Spring 提供的其他基础结构紧密集成.
2. 不依赖于 Servlet API(目标虽是如此,但是在实现的时候确实是依赖于 Servlet 的)3）可以任意使用各种视图技术,而不仅仅局限于 JSP4）支持各种请求资源的映射策略5）它应是易于扩展的

## SpringMVC 工作原理？
1. 客户端发送请求到 DispatcherServlet
2. DispatcherServlet 查询 handlerMapping 找到处理请求的 Controller
3. Controller 调用业务逻辑后，返回 ModelAndView
4. DispatcherServlet 查询 ModelAndView，找到指定视图
5. 视图将结果返回到客户端

## SpringMVC 流程？
1. 用户发送请求至前端控制器 DispatcherServlet。
2. DispatcherServlet 收到请求调用 HandlerMapping 处理器映射器。
3. 处理器映射器找到具体的处理器(可以根据 xml 配置、注解进行查找)，生成处理器及处理器拦截器(如果有则生成)一并返回给 DispatcherServlet。
4. DispatcherServlet 调用 HandlerAdapter 处理器适配器。
5. HandlerAdapter 经过适配调用具体的处理器(Controller，也叫后端控制器)
6. Controller 执行完成返回 ModelAndView。
7. HandlerAdapter 将 controller 执行结果 ModelAndView 返回给 DispatcherServlet。8）DispatcherServlet 将 ModelAndView 传给 ViewReslover 视图解析器。
9. ViewReslover 解析后返回具体 View。
10. DispatcherServlet 根据 View 进行渲染视图（即将模型数据填充至视图中）。
11. DispatcherServlet 响应用户。

## SpringMVC 的控制器是不是单例模式,如果是,有什么问题,怎么解决？
是单例模式,所以在多线程访问的时候有线程安全问题,不要用同步,会影响性能的,解方案是在控制器里面不能写字段。

## 如果你也用过 struts2.简单介绍下 springMVC 和 struts2 的区别有哪些?
1. springmvc 的入口是一个 servlet 即前端控制器，而 struts2 入口是一个 filter 过虑器
2. springmvc 是基于方法开发(一个 url 对应一个方法)，请求参数传递到方法的形参，设计为单例或多例(建议单例)，struts2 是基于类开发，传递参数是通过类的属性，只能计为多例。
3. Struts 采用值栈存储请求和响应的数据，通过 OGNL 存取数据，springmvc 通过参析器是将 request 请求内容解析，并给方法形参赋值，将数据和视图封装成 ModelAnd对象，最后又将 ModelAndView 中的模型数据通过 reques 域传输到页面。Jsp 视图解析认使用 jstl。

## SpingMVC 中的控制器的注解一般用那个,有没有别的注解可以替代
一般用@Conntroller 注解,表示是表现层,不能用用别的注解代替

## @RequestMapping 注解用在类上面有什么作用？
是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类有响应请求的方法都是以该地址作为父路径。

## 怎么样把某个请求映射到特定的方法上面？
直接在方法上面加上注解@RequestMapping,并且在这个注解里面写上要拦截的路径

## 如果在拦截请求中,我想拦截 get 方式提交的方法,怎么配置？
可以在@RequestMapping 注解里面加上 method=RequestMethod.GET

## 怎么样在方法里面得到 Request,或者 Session？
直接在方法的形参中声明 request,SpringMvc 就自动把 request 对象传

## 我想在拦截的方法里面得到从前台传入的参数,怎么得到？
直接在形参里面声明这个参数就可以,但必须名字和传过来的参数一样

## 如果前台有很多个参数传入,并且这些参数都是一个对象的,那么怎么样快速得到这象？
直接在方法中声明这个对象,SpringMVC 就自动会把属性赋值到这个对象里面。

## SpringMVC 中函数的返回值是什么？
返回值可以有很多类型,有 String, ModelAndView,当一般用 String 比较好

## SpringMVC 怎么样设定重定向和转发的？
在返回值前面加"forward:"就可以让结果转发,譬如"forward:user.do?name=method4返回值前面加"redirect:"就可以让返回值重定向,譬如"redirect:百度一下，你就知道"

## SpringMVC 用什么对象从后台向前台传递数据的？
通过 ModelMap 对象,可以在这个对象里面用 put 方法,把对象加到里面,前台就可以过 el 表达式拿到。

## SpringMvc 中有个类把视图和数据都合并的一起的,叫什么？
ModelAndView。

## 怎么样把 ModelMap 里面的数据放入 Session 里面？
可以在类上面加上@SessionAttributes 注解,里面包含的字符串就是要放入 session 的 key

## SpringMVC 怎么和 AJAX 相互调用的？
通过 Jackson 框架就可以把 Java 里面的对象直接转化成 Js 可以识别的 Json 对象具体步骤如下 ：
1. 加入 Jackson.jar
2. 在配置文件中配置 json 的映射
3. 在接受 Ajax 方法里面可以直接返回 Object,List 等,但方法前面要加上@ResponseBody注解

## 当一个方法向 AJAX 返回特殊对象,譬如 Object,List 等,需要做什么处理？
要加上@ResponseBody 注解

## SpringMvc 里面拦截器是怎么写的？
有两种写法,一种是实现接口,另外一种是继承适配器类,然后在 SpringMvc 的配置文配置拦截器即可：mvc:interceptors
```xml
<mvc:mapping path="/modelMap.do" />
</mvc:interceptor></mvc:interceptors>
```

## 说一下 spring mvc 运行流程？
spring mvc 先将请求发送给 DispatcherServlet。
DispatcherServlet 查询一个或多个 HandlerMapping，找到处理请求的 Controller。
DispatcherServlet 再把请求提交到对应的 Controller。
Controller 进行业务逻辑处理后，会返回一个ModelAndView。
Dispathcher 查询一个或多个 ViewResolver 视图解析器，找到 ModelAndView 对象指定的视图对象。
视图对象负责渲染返回给客户端。

## spring mvc 有哪些组件？
前置控制器 DispatcherServlet。
映射控制器 HandlerMapping。
处理器 Controller。
模型和视图 ModelAndView。
视图解析器 ViewResolver。

## @RequestMapping 的作用是什么？
将 http 请求映射到相应的类/方法上。

## spring mvc中常用注解有哪些，分别什么作用？
- @Controller
标识这个类是一个控制器
- @RequestMapping
给控制器方法绑定一个uri
- @ResponseBody
将java对象转成json，并且发送给客户端
- @RequestBody
将客户端请求过来的json转成java对象
- @RequestParam
当表单参数和方法形参名字不一致时，做一个名字映射
- @PathVarible
用于获取uri中的参数,比如user/1中1的值

### Rest风格的新api
- @RestController
组合注解 @Controller + @ResponseBody
- @GetMapping
- @DeleteMapping
- @PostMapping
- @PutMapping

### 其他注解
- @SessionAttribute
声明将什么模型数据存入session
- @CookieValue
获取cookie值
- @ModelAttribute
将方法返回值存入model中
- @HeaderValue
获取请求头中的值

## springmvc和strus2的区别？
1. 入口不同:
springmvc 入口是Servlet。struts2入口是filter。
2. 生命周期不同:
spring mvc Controller是单例的。所以不能使用成员变量获取参数。所以效率高。
struts action是多例的。所以可以使用成员变量获取参数。所以效率低。

# 事务

## REQUIRES_NEW  NESTED
REQUIRES_NEW 始终启动一个新事务
两个事务没有关联

NESTED 在原事务内启动一个内嵌事务
两个事务有关联
外部事务回滚内部事务也会回滚

## spring 事务实现方式有哪些？
声明式事务：声明式事务也有两种实现方式，基于 xml 配置文件的方式和注解方式（在类上添加 @Transaction 注解）。
编码方式：提供编码的形式管理和维护事务。

## 说一下 spring 的事务隔离？
spring 有五大隔离级别，默认值为 ISOLATION_DEFAULT（使用数据库的设置），其他四个隔离级别和数据库的隔离级别一致：

1. ISOLATION_DEFAULT：用底层数据库的设置隔离级别，数据库设置的是什么我就用什么；
2. ISOLATIONREADUNCOMMITTED：未提交读，最低隔离级别、事务未提交前，就可被其他事务读取（会出现幻读、脏读、不可重复读）；
3. ISOLATIONREADCOMMITTED：提交读，一个事务提交后才能被其他事务读取到（会造成幻读、不可重复读），SQL server 的默认级别；
4. ISOLATIONREPEATABLEREAD：可重复读，保证多次读取同一个数据时，其值都和事务开始时候的内容是一致，禁止读取到别的事务未提交的数据（会造成幻读），MySQL 的默认级别；
5. ISOLATION_SERIALIZABLE：序列化，代价最高最可靠的隔离级别，该隔离级别能防止脏读、不可重复读、幻读。

- 脏读 ：表示一个事务能够读取另一个事务中还未提交的数据。比如，某个事务尝试插入记录 A，此时该事务还未提交，然后另一个事务尝试读取到了记录 A。
- 不可重复读 ：是指在一个事务内，多次读同一数据。
- 幻读 ：指同一个事务内多次查询返回的结果集不一样。比如同一个事务 A 第一次查询时候有 n 条记录，但是第二次同等条件下查询却有 n+1 条记录，这就好像产生了幻觉。发生幻读的原因也是另外一个事务新增或者删除或者修改了第一个事务结果集里面的数据，同一个记录的数据内容被修改了，所有数据行的记录就变多或者变少了。
