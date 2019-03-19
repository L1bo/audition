[TOC]

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
