[TOC]

## 导包
```xml
<!-- swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.8.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.8.0</version>
</dependency>
```

## SwaggerConfig
```java
package com.dayou.jiuwei.config;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
public class SwaggerConfig {

    @Value(value = "${swagger.enable}")
    private Boolean swaggerEnable;

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2).apiInfo(setApiInfo())
                // 是否开启
                .enable(this.swaggerEnable).select()
                // 扫描的路径包
                .apis(RequestHandlerSelectors.basePackage("com.dayou.jiuwei.controller"))
                // 指定路径处理 PathSelectors.any()代表所有的路径
                .paths(PathSelectors.any()).build().pathMapping("/");
    }

    private ApiInfo setApiInfo() {
        return new ApiInfoBuilder().title("风控输出系统")
                .description("风控输出系统")
                .version("3.0.0").build();
    }
}


```

## 注解整体说明

### 用于controller类上
| 注解 | 说明       |
| ---- | ---------- |
| @Api | 协议集描述 |
### 用于方法上
| 注解               | 说明                             |
| ------------------ | -------------------------------- |
| @ApiOperation      | -                                |
| @ApiImplicitParams | 方法上                           |
| @ApiImplicitParam  | 用在@ApiImplicitParams的方法里边 |
| @ApiResponses      | 方法上                           |
| @ApiResponse       | 用在 @ApiResponses里边           |
| @ApiParam          | 参数上                           |

### 返回对象类
注解 | 说明
@ApiModel | 用在返回对象类上
@ApiModelProperty | 用在出入参数对象的字段上

## 注解使用详情

### @Api：请求类的说明
@Api：放在 请求的类上，与@Controller并列，说明的请求类的用下，如用户登录类，订单类等。
	tags="说明该类的作用"
	value="该参数没什么意义，所以不需要配置"
```java
@Api(tags="APP登录授权")
@Controller
public class ApiLoginController {

}
```
@Api属性配置：
与Controller注解并列使用。

| 属性名称       | 备注                                      |
| -------------- | ----------------------------------------- |
| value          | url的路径值                               |
| tags           | 如果设置这个值、value的值会被覆盖         |
| description    | 对api资源的描述                           |
| basePath       | 基本路径                                  |
| position       | 如果配置多个Api 想改变显示的顺序位置      |
| produces       | 如, “application/json, application/xml” |
| consumes       | 如, “application/json, application/xml” |
| protocols      | 协议类型，如: http, https, ws, wss.       |
| authorizations | 高级特性认证时配置                        |
| hidden         | 配置为true ，将在文档中隐藏               |

### @ApiOperation：方法的说明
@ApiOperation："用在请求的方法上，说明方法的作用"
	value="说明方法的作用"
	notes="方法的备注说明"
```java
@ApiOperation(value = "商户校验测试接口")
```

### @ApiImplicitParams、@ApiImplicitParam：方法参数的说明
- @ApiImplicitParams：用在请求的方法上，包含一组参数说明
- @ApiImplicitParam：对单个参数的说明	    
    - name：参数名
    - value：参数的汉字说明、解释
    - required：参数是否必须传
    - paramType：参数放在哪个地方
        - header --> 请求参数的获取：@RequestHeader
        - query --> 请求参数的获取：@RequestParam
        - path（用于restful接口）--> 请求参数的获取：@PathVariable
        - body（请求体）-->  @RequestBody User user
        - form（不常用）	   
    - dataType：参数类型，默认String，其它值dataType="Integer"	   
    - defaultValue：参数的默认值

```java
@ApiOperation(value="用户登录",notes="手机号、密码都是必输项，年龄随便填，但必须是数字")
@ApiImplicitParams({
    @ApiImplicitParam(name="mobile",value="手机号",required=true,paramType="form"),
    @ApiImplicitParam(name="password",value="密码",required=true,paramType="form"),
    @ApiImplicitParam(name="age",value="年龄",required=true,paramType="form",dataType="int")
})
@PostMapping("/login")
public JsonResult login(@RequestParam String mobile, @RequestParam String password,
@RequestParam Integer age){
...
    return JsonResult.ok(map);
}
```

### @ApiResponses、@ApiResponse：方法返回值的说明
- @ApiResponses：方法返回对象的说明
- @ApiResponse：每个参数的说明
    - code：数字，例如400
    - message：信息，例如"请求参数没填好"
    - response：抛出异常的类

```java
@ApiOperation("获取用户信息")
@ApiImplicitParams({
	@ApiImplicitParam(paramType = "query", name = "userId", dataType = "String", required = true, value = "用户Id")
}) 
@ApiResponses({
	@ApiResponse(code = 400, message = "请求参数没填好"),
	@ApiResponse(code = 404, message = "请求路径没有或页面跳转路径不对")
}) 
@ResponseBody
@RequestMapping("/list")
public JsonResult list(@RequestParam String userId) {
	...
	return JsonResult.ok().put("page", pageUtil);
}
```

### @ApiModel、@ApiModelProperty：用于响应类上，表示一个返回响应数据的信息
@ApiModel：用于响应类上，表示一个返回响应数据的信息
			（这种一般用在post创建的时候，使用@RequestBody这样的场景，
			请求参数无法使用 @ApiImplicitParam 注解进行描述的时候）
	@ApiModelProperty：用在属性上，描述响应类的属性

```java
@ApiModel(description= "返回响应数据")
public class RestMessage implements Serializable{

	@ApiModelProperty(value = "是否成功")
	private boolean success=true;
	@ApiModelProperty(value = "返回对象")
	private Object data;
	@ApiModelProperty(value = "错误编号")
	private Integer errCode;
	@ApiModelProperty(value = "错误信息")
	private String message;
		
	/* getter/setter */
}
```

```java
package com.dayou.jiuwei.config;

import com.dayou.jiuwei.interceptor.MerchantVerifyInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

@Configuration
public class WebMvcConfig extends WebMvcConfigurationSupport {

    @Bean
    public MerchantVerifyInterceptor getMerchantVerifyInterceptor() {
        return new MerchantVerifyInterceptor();
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(getMerchantVerifyInterceptor()).addPathPatterns("/**")
                .excludePathPatterns("/swagger-ui.html", "/webjars/**", "/swagger-resources/**");
    }

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/**").addResourceLocations("classpath:/META-INF/resources/").setCachePeriod(0);
    }

}

```