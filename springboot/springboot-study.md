# Spring Boot

## 1. HelloWorld

### 1.1 创建SpringBoot项目

![img](.\img\0d6397ddd746f839c711824c5833e663_1007x805.png)

一路next，到这一步骤，在这里可以选择我们需要依赖的第三方软件类库，包括spring-boot-web，mysql驱动，mybatis等。

![img](.\img\0c916c976285c284a5beab8c3b1aae1e_1041x804.png)

继续一路next...

![img](.\img\bbb4a9677cb98dad7af57f17b74a9567_465x616.png)

**将application.properties改成application.yml。yml文件和properties配置文件具有同样的功能。**

```yaml
server:
  port: 8888   # web应用服务端口
```

引入spring-boot-starter-web依赖（不需要加版本号）

```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**所有的Spring Boot项目默认使用spring-boot-starter-parent作为应用程序的父项目。**

```xml
<parent>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-parent</artifactId>
 <version>2.0.0.RELEASE</version>
</parent>
```

继承父项目的好处在于： **统一java版本配置和其他的一些依赖类库的版本。**也就是说，你引入的第三方类库不要加版本号，父项目替你管理版本，而且是经过兼容性测试的。比你自己随便引入一个版本兼容性更好。

### 1.2 test

```java
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String hello(String name) {
        return "hello world, " +name;
    }
}
```

![img](.\img\0a937bcb7006e7a59c6d1f8e84b7da0e_743x536.png)

### 1.3 项目结构目录结构简介

![img](.\img\562529f745a76a6e668b15914d5d72ea_474x372.png)

项目结构目录整体上符合maven规范要求：

| 目录位置                                  | 功能                                                         |
| :---------------------------------------- | :----------------------------------------------------------- |
| src/main/java                             | 项目java文件存放位置，初始化包含主程序入口 XxxApplication，可以通过直接运行该类来 启动 Spring Boot应用 |
| src/main/resources                        | 存放静态资源，图片、CSS、JavaScript、web页面模板文件等       |
| src/test                                  | 单元测试代码目录                                             |
| .gitignore                                | git版本管理排除文件                                          |
| target文件夹                              | 项目代码构建打包结果文件存放位置，不需要人为维护             |
| pom.xml                                   | maven项目配置文件                                            |
| application.properties（application.yml） | 用于存放程序的各种依赖模块的配置信息，比如服务端口，数据库连接配置等 |

- src/main/resources/static主要用来存放css、图片、js等开发用静态文件
- src/main/resources/public用来存放可以直接用于访问的html文件
- src/main/resources/templates用来存放web开发模板文件



## 2. Lombok插件

在pom.xml里面加上如下依赖

```xml
 <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
</dependency>
```

### 2.1 使用lombok注解简化开发

#### 1. Data注解

在java类上使用@Data注解，将为我们在编译期自动生成

- 成员变量的get和set方法
- equals方法
- canEqual方法
- hashCode方法
- toString方法

#### 2. Slf4j注解

将在编译期自动帮我们引入Logger日志常量，我们在代码中就直接使用log.info或log.debug打印日志即可。下图中红色代码就用Slf4j注解代替就可以了。

![img](.\img\d24f55de1112606f804e9bf6280b3be0_750x441.png)

#### 3. Builder注解

在Java类上使用Builder注解之后，我们可以使用如下代码为对象属性赋值

```java
LombokPOJO lombokPOJO = LombokPOJO.builder()
        .name("kobe")
        .age(39)
        .build();
```

![img](.\img\bda328a18d6acb36be7a3673f2f4122c_1384x862.png)

#### 4.  AllArgsConstructor注解

AllArgsConstructor注解将为我们在编译期自动生成：全参构造函数。

![img](.\img\f94f6763804e87144618e7c9cde0fd5a_1202x466.png)

有全参构造函数注解，自然就有无参构造函数注解：NoArgsConstructor注解。



## 3. RESTful风格接口

### 3.1 RESTFul风格API的好处

RESTful是基于http方法的API设计风格，而不是一种新的技术，url和http的method和statuscode都是很有历史性的技术。

1. 看Url就知道要什么资源
2. 看http method就知道针对资源干什么
3. 看http status code就知道结果如何

规范了程序员的代码开发，为前端后端交互减少了接口交流的口舌成本，是"约定大于配置"的体现。通过下面的设计，大家来理解一下这三句话。

### 3.2 RESTful风格API的设计风格

#### 1. REST 是面向资源的（名词）

**REST 通过 URI 暴露资源时，会强调不要在 URI 中出现动词。比如：**

| 不符合REST的接口URI      | 符合REST接口URI       | 功能           |
| :----------------------- | :-------------------- | :------------- |
| GET /api/getDogs         | GET /api/dogs/{id}    | 获取一个小狗狗 |
| GET /api/getDogs         | GET /api/dogs         | 获取所有小狗狗 |
| GET /api/addDogs         | POST /api/dogs        | 添加一个小狗狗 |
| GET /api/editDogs/{id}   | PUT /api/dogs/{id}    | 修改一个小狗狗 |
| GET /api/deleteDogs/{id} | DELETE /api/dogs/{id} | 删除一个小狗狗 |

#### 2. 用HTTP方法体现对资源的操作（动词）

**用HTTP方法体现对资源的操作（动词）**

GET ： 获取资源
POST ： 添加资源
PUT ： 修改资源
DELETE ： 删除资源

实际上，**这四个动词实际上就对应着增删改查四个操作**，这就利用了HTTP动词来表示对资源的操作。

#### 3. HTTP状态码

通过HTTP状态码体现动作的结果,不要自定义

```
200 OK 
400 Bad Request 
500 Internal Server Error
```

在 APP 与 API 的交互当中，其结果逃不出这三种状态：

- 所有事情都按预期正确执行完毕 - 成功
- APP 发生了一些错误 – 客户端错误（如：校验用户输入身份证，结果输入的是军官证，就是客户端错误）
- API 发生了一些错误 – 服务器端错误（各种编码bug或服务内部自己导致的异常）

#### 4. Get方法和查询参数不应该改变数据

改变数据的事交给POST、PUT、DELETE

#### 5. 使用复数名词

/dogs 而不是 /dog

#### 6. 复杂资源关系的表达

`GET /cars/711/drivers/` 返回 使用car 711的所有司机
`GET /cars/711/drivers/4` 返回 使用car 711的4号司机

#### 7.  高级用法:HATEOAS

**H**ypermedia **a**s **t**he **E**ngine **o**f **A**pplication **S**tate 超媒体作为应用状态的引擎
RESTful API最好做到Hypermedia,或HATEOAS，**即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么**。比如，当用户向api.example.com的根目录发出请求，会得到这样一个文档。

```json
{"link": {
  "rel":   "collection https://www.example.com/zoos",
  "href":  "https://api.example.com/zoos",
  "title": "List of zoos",
  "type":  "application/vnd.yourformat+json"
}}
```

#### 8. 为集合提供过滤 排序 选择和分页等功能

**Filtering过滤**:

使用唯一的查询参数进行过滤：

GET /cars?color=red 返回红色的cars
GET /cars?seats<=2 返回小于两座位的cars集合

**Sorting**排序:

允许针对多个字段排序

GET /cars?sort=-manufactorer,+model

这是返回根据生产者降序和模型升序排列的car集合

**Field selection**

移动端能够显示其中一些字段，它们其实不需要一个资源的所有字段，给API消费者一个选择字段的能力，这会降低网络流量，提高API可用性。

GET /cars?fields=manufacturer,model,id,color

**Paging分页**

使用 limit 和offset.实现分页，缺省limit=20 和offset=0；

GET /cars?offset=10&limit=5

#### 9. 版本化你的API

使得API版本变得强制性，不要发布无版本的API。
/api/**v1**/blog
**面向扩展开放，面向修改关闭。**



### 3.3 开发一个RESTful接口

```java
package com.jjcc.bootlaunch.controller;

import com.jjcc.bootlaunch.config.exception.AjaxResponse;
import com.jjcc.bootlaunch.model.Article;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;

/**
 * @author Administrator
 * @version 1.0.0
 * @description
 * @className ArticleRestController.java
 * @createTime 2019年10月05日 10:38:00
 */
@Slf4j
@RestController
@RequestMapping(value = "/rest", produces = "application/json")
public class ArticleRestController {

    /**
     * 增加
     * @title saveArticle
     * @description
     * @author Jjcc
     * @param article
     * @return com.jjcc.bootlaunch.config.exception.AjaxResponse
     * @createTime 2019/10/5 0005 10:58
     * @throws
     */
    @PostMapping("/article")
    public AjaxResponse saveArticle(@RequestBody Article article) {

        log.info("saveArticle：{}", article);

        return AjaxResponse.success(article);
    }

    /**
     * 删除
     * @title deleteArticle
     * @description
     * @author Jjcc
     * @param id
     * @return com.jjcc.bootlaunch.config.exception.AjaxResponse
     * @createTime 2019/10/5 0005 10:59
     * @throws
     */
    @DeleteMapping("/article/{id}")
    public AjaxResponse deleteArticle(@PathVariable Long id) {

        log.info("deleteArticle：{}", id);

        return AjaxResponse.success(id);
    }

    @PutMapping("/article/{id}")
    public AjaxResponse updateArticle(@PathVariable Long id, @RequestBody Article article) {
        article.setId(id);

        log.info("updateArticle：{}",article);
        return AjaxResponse.success(article);
    }

    @GetMapping("/article/{id}")
    public AjaxResponse getArticle(@PathVariable Long id) {

        return AjaxResponse.success(id);
    }

    @GetMapping("/article")
    public AjaxResponse getArticle() {


        Article.ArticleBuilder jjcc = Article.builder().id(1L).author("jjcc").content("asdasdasd");

        return AjaxResponse.success(jjcc);
    }
}

```

#### 1 REST接口开发常用的注解

- @RestController与@Controller
  - @RestController相当于 @Controller和@ResponseBody结合。它有两层含义：一是作为控制器注入到Spring上下文环境，二是请求响应为数据序列化（默认序列化方式是JSON），而不是跳转到html或模板页面。
- @RequestMapping 与@GetMapping、@PutMapping、@PostMapping、@DeleteMapping
  - @RequestMapping(value = “/article”, method = RequestMethod.GET)
    新方法可以简写为： @GetMapping("/article")，其他同理。
- @RequestBody与@ResponseBody
  - 用于接收和响应序列化数据（JSON），可以支持嵌套JSON数据结构。
- @PathVariable 与@RequestParam
  - PathVariable用于URI上的{参数}
    RequestParam用于接收普通方式提交的参数

### 3.4 使用Swagger2构建API文档

#### 1. 整合swagger2

**pom.xml**

```xml
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>2.6.1</version>
</dependency>

<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>2.6.1</version>
</dependency>
```

**java代码**

`@Profile({"dev","test"})`：  生产环境关闭API文档

`@EnableSwagger2`： 注解表示开启Swagger

```java
package com.jjcc.bootlaunch.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;


@Profile({"dev","test"}) 
@Configuration
@EnableSwagger2
public class Swagger1 {

	@Bean
	public Docket createRestApi() {
		return new Docket(DocumentationType.SWAGGER_2)
				.apiInfo(apiInfo())
				.select()
			    .apis(RequestHandlerSelectors.basePackage("com.jjcc.bootlaunch"))
				.paths(PathSelectors.regex("/rest/.*"))
				.build();
	}
	
	private ApiInfo apiInfo() {
		return new ApiInfoBuilder()
				.title("springboot利用swagger构建api文档")
				.description("简单优雅的restful风格")
				.termsOfServiceUrl("")
				.version("1.0")
				.build();
	}
}
```

#### 2. 常用注解及其功能

```
@Api：用在请求的类上，表示对类的说明
    tags="说明该类的作用，可以在UI界面上看到的注解"
    value="该参数没什么意义，在UI界面上也看到，所以不需要配置"

@ApiOperation：用在请求的方法上，说明方法的用途、作用
    value="说明方法的用途、作用"
    notes="方法的备注说明"

@ApiImplicitParams：用在请求的方法上，表示一组参数说明
    @ApiImplicitParam：用在@ApiImplicitParams注解中，指定一个请求参数的各个方面
        name：参数名
        value：参数的汉字说明、解释
        required：参数是否必须传
        paramType：参数放在哪个地方
            · header --> 请求参数的获取：@RequestHeader
            · query --> 请求参数的获取：@RequestParam
            · path（用于restful接口）--> 请求参数的获取：@PathVariable
            · body（不常用）
            · form（不常用）    
        dataType：参数类型，默认String，其它值dataType="Integer"       
        defaultValue：参数的默认值

@ApiResponses：用在请求的方法上，表示一组响应
    @ApiResponse：用在@ApiResponses中，一般用于表达一个错误的响应信息
        code：数字，例如400
        message：信息，例如"请求参数没填好"
        response：抛出异常的类

@ApiModel：用于响应类上，表示一个返回响应数据的信息
            （这种一般用在post创建的时候，使用@RequestBody这样的场景，
            请求参数无法使用@ApiImplicitParam注解进行描述的时候）
    @ApiModelProperty：用在属性上，描述响应类的属性
```

#### 3. 实例

```java
package com.jjcc.bootlaunch.controller;

import com.jjcc.bootlaunch.config.exception.AjaxResponse;
import com.jjcc.bootlaunch.model.Article;
import io.swagger.annotations.*;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;

/**
 * @author Administrator
 * @version 1.0.0
 * @description
 * @className ArticleRestController.java
 * @createTime 2019年10月05日 10:38:00
 */
@Api("restful风格接口")
@Slf4j
@RestController
@RequestMapping(value = "/rest", produces = "application/json")
public class ArticleRestController {

    /**
     * 增加
     * @title saveArticle
     * @description
     * @author Jjcc
     * @param article asd
     * @return com.jjcc.bootlaunch.config.exception.AjaxResponse
     * @createTime 2019/10/5 0005 10:58
     * @throws
     */
    @ApiOperation(value = "添加信息方法", notes = "添加信息方法是post请求")
    @PostMapping("/article")
    public AjaxResponse saveArticle(@RequestBody(required = false) Article article) {

        log.info("saveArticle：{}", article);

        return AjaxResponse.success(article);
    }

    /**
     * 删除
     * @title deleteArticle
     * @description
     * @author Jjcc
     * @param id
     * @return com.jjcc.bootlaunch.config.exception.AjaxResponse
     * @createTime 2019/10/5 0005 10:59
     * @throws
     */
    @ApiOperation(value = "信息删除", notes = "信息删除接口是delete请求")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "id", value = "信息id", required = true,
                    dataType = "Long", paramType = "query")
    })
    @ApiResponses({
            @ApiResponse(code = 200, message = "成功", response = AjaxResponse.class),
            @ApiResponse(code = 400, message = "用户输入失败", response = AjaxResponse.class),
            @ApiResponse(code = 500, message = "服务器错误", response = AjaxResponse.class)
    })
    @DeleteMapping("/article/{id}")
    public AjaxResponse deleteArticle(@PathVariable Long id) {

        log.info("deleteArticle：{}", id);

        return AjaxResponse.success(id);
    }

    @ApiOperation("信息修改方法")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "id", value = "id：信息主键id，article：信息实体类")
    })
    @PutMapping("/article/{id}")
    public AjaxResponse updateArticle(@PathVariable Long id, @RequestBody(required = false) Article article) {
        article.setId(id);

        log.info("updateArticle：{}",article);
        return AjaxResponse.success(article);
    }

    @GetMapping("/article/{id}")
    public AjaxResponse getArticle(@PathVariable Long id) {

        return AjaxResponse.success(id);
    }

    @GetMapping("/article")
    public AjaxResponse getArticle() {


        Article.ArticleBuilder jjcc = Article.builder().id(1L).author("jjcc").content("asdasdasd");

        return AjaxResponse.success(jjcc);
    }
}
```

#### 4. 生产环境下如何禁用swagger2

- 禁用方法1：使用注解`@Profile({"dev","test"})` 表示在开发或测试环境开启，而在生产关闭。（推荐使用）
- 禁用方法2：使用注解`@ConditionalOnProperty(name = "swagger.enable", havingValue = "true")` 然后在测试配置或者开发配置中 添加 swagger.enable = true 即可开启，生产环境不填则默认关闭Swagger.



## 4. SpringBoot配置原理

```yml
#    1. 一个家庭有爸爸、妈妈、孩子。
#    2. 这个家庭有一个名字（family-name）叫做“happy family”
#    3. 爸爸有名字(name)和年龄（age）两个属性
#    4. 妈妈有两个别名
#    5. 孩子除了名字(name)和年龄（age）两个属性，还有一个friends的集合
#    6. 每个friend有两个属性：hobby(爱好)和性别(sex)

family:
  family-name: "happy family"
  father:
    name: zimug
    age: 18
  mother:
    alias:
      - lovely
      - ailice
  child:
    name: zimug
    age: 5
    friends:
      - hobby: football
        sex:  male
      - hobby: basketball
        sex: female
```

### 4.1 获取自定义配置的两种实现方法

**使用@Value获取配置值**

```java
@Data
@Component
public class Family {

    @Value("${family.family-name}")
    private String familyName;

}
```

**使用@ConfigurationProperties获取配置值**

```
@Data
@Component
@ConfigurationProperties(prefix = "family")
public class Family {

    //@Value("${family.family-name}")
    private String familyName;
    private Father father;
    private Mother mother;
    private Child child;

}
```

**两者区别**

|                          | @ConfigurationProperties | @Value             |
| :----------------------- | :----------------------- | ------------------ |
| 功能                     | 批量注入属性到java类     | 一个个属性指定注入 |
| 松散语法绑定             | 支持                     | 不支持             |
| SpEL                     | 不支持                   | 支持               |
| 复杂数据类型(对象、数组) | 支持                     | 不支持             |
| JSR303数据校验           | 支持                     | 不支持             |

> 使用`@Value`不支持嵌套注入等等；

### 4.2 配置文件注入值数据校验

#### 1. 如何对注入的属性进行校验

在需要校验的属性装配类上加@Validated注解

```java
@Data
@Component
@Validated
@ConfigurationProperties(prefix = "family")
public class Family {
```

在需要校验的属性装配类上加@Validated注解,只有加上才会触发数据校验
@Validated注解的在此处的作用是配合@ConfigurationProperties进行属性校验。

#### 2. 数据校验注解列表（部分常用）

| 限制                      | 说明                                                         |
| :------------------------ | :----------------------------------------------------------- |
| @Null                     | 限制只能为null                                               |
| @NotNull                  | 限制必须不为null                                             |
| @AssertFalse              | 限制必须为false                                              |
| @AssertTrue               | 限制必须为true                                               |
| @DecimalMax(value)        | 限制必须为一个不大于指定值的数字                             |
| @DecimalMin(value)        | 限制必须为一个不小于指定值的数字                             |
| @Digits(integer,fraction) | 限制必须为一个小数，且整数部分的位数不能超过integer，小数部分的位数不能超过fraction |
| @Future                   | 限制必须是一个将来的日期                                     |
| @Max(value)               | 限制必须为一个不大于指定值的数字                             |
| @Min(value)               | 限制必须为一个不小于指定值的数字                             |
| @Past                     | 限制必须是一个过去的日期                                     |
| @Pattern(value)           | 限制必须符合指定的正则表达式                                 |
| @Size(max,min)            | 限制字符长度必须在min到max之间                               |
| @Past                     | 验证注解的元素值（日期类型）比当前时间早                     |
| @NotEmpty                 | 验证注解的元素值不为null且不为空（字符串长度不为0、集合大小不为0） |
| @NotBlank                 | 验证注解的元素值不为空（不为null、去除首位空格后长度为0），不同于@NotEmpty，@NotBlank只应用于字符串且在比较时会去除字符串的空格 |
| @Email                    | 验证注解的元素值是Email，也可以通过正则表达式和flag指定自定义的email格式 |

- @size (min=6, max=20, message="密码长度只能在6-20之间")
- @pattern (regexp="[a-za-z0-9._%+-]+@[a-za-z0-9.-]+\.[a-za-z]{2,4}", message="请输入正确的邮件格式")
- @Length(min = 5, max = 20, message = "用户名长度必须位于5到20之间")
- @Email(message = "请输入正确的邮箱")
- @NotNull(message = "用户名称不能为空")
- @Max(value = 100, message = "年龄不能大于100岁")
- @Min(value= 18 ,message= "必须年满18岁！" )
- @AssertTrue(message = "bln4 must is true")
- @AssertFalse(message = "blnf must is falase")
- @DecimalMax(value="100",message="decim最大值是100")
- @DecimalMin(value="100",message="decim最小值是100")
- @NotNull(message = "身份证不能为空")
- @Pattern(regexp="^(\d{18,18}|\d{15,15}|(\d{17,17}[x|X]))$", message="身份证格式错误")

### 4.3. profile不同环境使用不同配置

#### 1. 配置文件规划

Spring Boot 默认的配置文件是 application.properties(或yml)。

一个比较好的实践是为不同的环境定义不同的配置文件，如下所示：

<img src=".\img\08ca13216aa4a6160fdccd6507598b1b_329x163.png" alt="img" style="zoom:120%;" />

> 全局配置文件:application.yml
> 开发环境配置文件：application-dev.yml
> 测试环境配置文件：application-test.yml
> 生产环境配置文件：application-prod.yml
>
> **外部大于内部，特指大于泛指**

#### 2. 切换环境的方式

**1. 通过application.yml配置**

application.yml是默认使用的配置文件，在其中通过spring.profiles.active设置使用哪一个配置文件，下面代码表示使用application-prod.yml配置，如果application-prod.yml和application.yml配置了相同的配置，比如都配置了运行端口，那application-prod.yml的优先级更高

```yaml
#需要使用的配置文件
spring:
  profiles:
    active: prod
```

**2.VM options、Program arguments、Active Profile**

> VM options设置启动参数 -Dspring.profiles.active=prod
>
> Program arguments设置 --spring.profiles.active=prod
>
> Active Profile 设置 prod

**这三个参数不要一起设置，会引起冲突，选一种即可**

![img](.\img\2e05093186863f8664983a7b4a247dd8_921x617.png)

**3. 命令行方式**

将项目打成jar包，在jar包的目录下打开命令行，使用如下命令启动：

```sh
java -jar spring-boot-profile.jar --spring.profiles.active=prod
```

### 4.4 加载其它配置文件的几种方式 

有一些老的项目的jar包并未主动的去与spring boot 融合，如果使用这些jar包就得使用他们自己的配置文件。如果我们的应用也需要这些配置，就不要同样的配置写两份了（一份放在application.yml，一份放在自定义的配置文件），这种还不如多个配置文件就好了。所以我们需要学习一下如何加载外部自定义配置文件。

Springboot默认加载`application.yml/application.yaml`文件，但除此之外的文件不会加载；

**1. 使用@PropertySource加载自定义yml或properties文件**

新建一个配置文件family.yml，把之前的用的YAML数据结构放里面；

```yaml
#    1. 一个家庭有爸爸、妈妈、孩子。
#    2. 这个家庭有一个名字（family-name）叫做“happy family”
#    3. 爸爸有名字(name)和年龄（age）两个属性
#    4. 妈妈有两个别名
#    5. 孩子除了名字(name)和年龄（age）两个属性，还有一个friends的集合
#    6. 每个friend有两个属性：hobby(爱好)和性别(sex)

family:
  family-name: "happy family"
  father:
    name: zimug
    age: 18
  mother:
    alias:
      - lovely
      - ailice
  child:
    name: zimug2
    age: 5
    friends:
      - hobby: football
        sex:  male
      - hobby: basketball
        sex: female

```

因为**@PropertySource默认不支持读取YAML格式外部配置文件**，所以我们继承DefaultPropertySourceFactory ，然后对它的createPropertySource进行一下修改

```java
public class MixPropertySourceFactory extends DefaultPropertySourceFactory {

  @Override
  public PropertySource<?> createPropertySource(String name, EncodedResource resource) throws IOException {
    String sourceName = name != null ? name : resource.getResource().getFilename();
    if (!resource.getResource().exists()) {
      return new PropertiesPropertySource(sourceName, new Properties());
    } else if (sourceName.endsWith(".yml") || sourceName.endsWith(".yaml")) {
      Properties propertiesFromYaml = loadYml(resource);
      return new PropertiesPropertySource(sourceName, propertiesFromYaml);
    } else {
      return super.createPropertySource(name, resource);
    }
  }

  private Properties loadYml(EncodedResource resource) throws IOException {
    YamlPropertiesFactoryBean factory = new YamlPropertiesFactoryBean();
    factory.setResources(resource.getResource());
    factory.afterPropertiesSet();
    return factory.getObject();
  }
}
```

**在Family类的上面加上如下注解即可。**

```java
@Data
@Component
@PropertySource(value = "classpath:application-family.yml", factory = MixPropertySourceFactory.class)
@ConfigurationProperties(prefix = "family")
public class Family {

    private String familyName;

    private Father father;

    private Mother mother;

    private Child child;
}
```

**2. 使用@ImportResource加载Spring的xml配置文件**

在没有注解的时代，spring的相关配置都是通过xml来完成的，如：beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="testBeanService" class="com.zimug.bootlaunch.service.TestBeanService"></bean>
</beans>
```

测试用例:

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ImportResourceTests {

    @Autowired
    private ConfigurableApplicationContext  ioc;

    @Test
    public void testHelloService() {
        //测试Spring上下文环境中是否有testBeanService这样一个bean，有的话表示xml配置文件生效
        boolean testBeanService= ioc.containsBean("testBeanService");
        System.out.println(testBeanService);
    }
}
```

因为此时还没使用@ImportResource加载beans.xml，所以输出false.**在启动类上加**

```java
@ImportResource(locations = {"classpath:beans.xml"})
```

**3. yml文件中spring:profiles:include**

引入yml文件，include中可以用逗号隔开引入过个yml文件；

注意：引入的文件前缀必须是`application-`

```yaml
spring:
  profiles:
    include: family
```

### 4.5 配置文件敏感字段加密

#### 1. Jasypt

[Jasypt](http://jasypt.org/)是一个Java库，允许开发人员以很简单的方式添加基本加密功能，而无需深入研究加密原理。利用它可以实现高安全性的，基于标准的加密技术，无论是单向和双向加密。加密密码，文本，数字，二进制文件。

1. 高安全性的，基于标准的加密技术，无论是单向和双向加密。加密密码，文本，数字，二进制文件…
2. 集成Hibernate的。
3. 可集成到Spring应用程序中，与Spring Security集成。
4. 集成的能力，用于加密的应用程序（即数据源）的配置。
5. 特定功能的高性能加密的multi-processor/multi-core系统。
6. 与任何JCE提供者使用开放的API

#### 2. 与spring boot整合

```xml
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>1.18</version>
</dependency>
```

设置盐值和修改相应需要加密的配置参数

```yaml
family:
  # 需要解密的地方，使用ENC()进行包裹处理
  family-name: ENC(QwxV2ZpwDG4GBZkHu4yp+Q==)
  
# 设置盐值（加密解密密钥），我们配置在这里只是为了测试方便
# 生产环境中，切记不要这样直接进行设置，可通过环境变量、命令行等形式进行设置。下面会讲
jasypt:
  encryptor:
    password: 123456
```

简单来说，就是在需要加密的值使用`ENC(`和`)`进行包裹，即：`ENC(密文)`。之后想往常一样使用`@Value("${}")`获取该配置即可，获取的是解密之后的值。

#### 3. 使用bat脚本生成加密串和盐值（密钥）

```bat
@echo off
set/p input=待加密的明文字符串：
set/p password=加密密钥(盐值)：
echo 加密中......
java -cp jasypt-1.9.2.jar org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI  input=%input% password=%password% algorithm=PBEWithMD5AndDES
pause
```

**注意：jasypt-1.9.2.jar 文件需要和bat脚本放在相同目录下。此包可直接在示例项目中直接下载。**

<img src=".\img\1570585852266.png" alt="1570585852266" style="zoom:150%;" />

**注意：相同的盐值(密钥)，每次加密的结果是不同的。**

#### 4. 如何存储盐值(密钥)更安全

> **本身加解密过程都是通过`盐值`进行处理的**，所以正常情况下`盐值`和`加密串`是分开存储的。**盐值应该放在系统属性、命令行或是环境变量来使用，而不是放在同一个配置文件里面。**

**1. 命令行存储方式示例**

```sh
java -jar xxx.jar --jasypt.encryptor.password=xxx &;
```

**2. 环境变量存储方式示例**

设置环境变量(linux)：

```properties
# 打开/etc/profile文件
vim /etc/profile
# 文件末尾插入
export JASYPT_PASSWORD = xxxx
```

启动命令：

```sh
java -jar xxx.jar --jasypt.encryptor.password=${JASYPT_PASSWORD} &;
```

#### 5. webjar与静态资源

WebJars是将这些通用的Web前端资源打包成Java的Jar包，然后借助Maven工具对其管理，保证这些Web资源版本唯一性，升级也比较容易。关于webjars资源，有一个专门的网站https://www.webjars.org/，我们可以到这个网站上找到自己需要的资源，在自己的工程中添加入maven依赖，即可直接使用这些资源了。

**1.pom中引入依赖**

```xml
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>jquery</artifactId>
    <version>1.12.4</version>
</dependency>
```

加入 webjars-locator 组件，就不需要在 html 添加依赖的时候填写版本。

```xml
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>webjars-locator</artifactId>
    <version>0.34</version>
</dependency>
```

引入 webjars-locator 值后可以省略版本号:

```html
<script src="/webjars/jquery/jquery.min.js"></script>
```



## 5. 常用Web开发框架

### 5.1 Mybatis框架

#### 1. 整合Mybatis

**1. 引入Maven依赖包**

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.0</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

**2. 保证application.yml里面有数据库连接的配置**

```yaml
server:
  port: 8888
spring:
  http:
    encoding:
      charset: utf-8
      force: false
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
  profiles:
    include: family
  datasource:
    url: jdbc:mysql://localhost:3306/mydb?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=UTC
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver

mybatis:
  mapper-locations: classpath:mapping/*/*Mapper.xml
  type-aliases-package: com.jjcc.bootlaunch.model
  #  config-location: classpath:classpath:mapping/*/*Mapper.xml
  configuration:
    map-underscore-to-camel-case: true
```

- `mapper-locations` 指定 mapper 文件的位置。如果在项目中你的 mapper 文件是按目录来放置，那么对应的配置就变成：`mapper-locations: classpath:mapper/*/*.xml`
- `config-locations` 是配置`mybatis-confg.xml`文件的位置。指定 MyBatis 主配置文件的位置
- `type-aliases-package` 指定实例类的别名
- `configuration:map-underscore-to-camel-case` 开启mybatis的驼峰命名转换跟entity的别名。

**3. Service层**



**4. DAO层**

定义这个 mapper 的作用是用来跟数据库进行交互的。

```java
package com.jjcc.bootlaunch.generator;

import com.jjcc.bootlaunch.model.User;

import java.util.List;

/**
 * @author Jjcc
 * @version 1.0.0
 * @description
 * @className UserMapper.java
 * @createTime 2019年10月09日 14:58:00
 */
//@Mapper
public interface UserMapper {

    /**
     * 获取所有信息
     * @title getUserAll
     * @description
     * @author Jjcc
     * @return java.util.List<com.jjcc.bootlaunch.model.User>
     * @createTime 2019/10/9 17:26
     * @throws
     */
     List<User> getUserAll();

    /**
     * 获取指定数据
     * @title getUserInfo
     * @author Jjcc
     * @param id
     * @return com.jjcc.bootlaunch.model.User
     * @createTime 2019/10/9 21:45
     */
     User getUserInfo(Integer id);

     /**
      * 删除指定数据
      * @title deleteUserInfo
      * @author Jjcc
      * @param id
      * @return int
      * @createTime 2019/10/9 22:23
      */
     int deleteUserInfo(Integer id);
    
     @Select("select id, username, password from user")
     @Results(
        @Result(property = "userId", column = "user_id"),
        @Result(property = "userName", column = "user_name")
     )
     User getUserInfoMethod(Integer id);
    
}
```

- `@Mapper`：可以用于注解的方式操作数据库
- `@Results` Results对应mapper.xml文件中的`resultMap`
- `@Result`  Result对应mapper.xml文件中的`result`

**5.  配置Mybatis的Mapper的扫描路径**

`@MapperScan(包路径)`：**用于给出需要扫描的mapper文件路径，并完成自动注入**

```java
@SpringBootApplication
@EnableSwagger2
@MapperScan("com.jjcc.bootlaunch.generator")
public class BootLaunchApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootLaunchApplication.class, args);
    }

}
```

#### 2. MySQL多数据源

**1. yml配置文件添加多个数据源**

```yaml
spring:
  http:
    encoding:
      charset: utf-8
      force: false
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
  profiles:
    include: family
  datasource:
    primary:
      jdbc-url: jdbc:mysql://localhost:3306/mydb?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=UTC
      username: root
      password: ENC(QwxV2ZpwDG4GBZkHu4yp+Q==)
      driver-class-name: com.mysql.jdbc.Driver

    secondary:
      jdbc-url: jdbc:mysql://localhost:3306/project_01?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=UTC
      username: root
      password: ENC(QwxV2ZpwDG4GBZkHu4yp+Q==)
      driver-class-name: com.mysql.jdbc.Driver
```

**2. 定义多个数据源类**

> - `@Configuration`：被@Configuration修饰的类被定义为一个Spring容器（应用上下文）；@Configuration就相当于Spring配置文件中的<beans />标签，里面可以配置bean。
>
> - `@Bean`：相当于Spring配置文件中的<bean />标签可以在Spring容器中注入一个bean；相当于实例化一个TestBean并交给Spring容器管理
>   - @Bean注解在返回实例的方法上，如果未通过@Bean指定bean的名称，则默认与方法名相
>   - @Bean注解默认作用域为单例singleton作用域，可通过@Scope(“prototype”)设置为多例
>
> - `@Primary`：相当于Spring 的xml配置文件中bean标签里的 primary属性。它表示在需要自动注入一个**单值**依赖的地方，却有**多个**候选依赖，那么这个注解会指定一个类作为**preference（偏好）**选择。
>   - 把@Primary注解标记在任意一个类上面，在使用@Autowired注入的时候，如果不特殊指明（如何特殊指明请看@Qualifier的讲解），那么默认就注入被@Primary标记的类。
> - `@Qualifier`：这个注解用在属性上，或参数上，在自动注入的时候作为多个候选bean的限定。也可以使用在其他的随后会被用于限定bean的定制注解上。
>   - 使用@Qualifier注入bean的时候，它所选取的类的别名需要与具体的类的别名一致，否则会报无法找到指定类型的错误。

***注意：去掉主入口上的@MapperScan***

```java
/**
 * 加载主数据源
 * @author Jjcc
 * @version 1.0.0
 * @className PrimaryDataSourceConfig.java
 * @createTime 2019年10月10日 10:48:00
 */
@Configuration
@MapperScan(basePackages = "com.jjcc.bootlaunch.generator.test1",
        sqlSessionTemplateRef = "primarySqlSessionTemplate")
public class PrimaryDataSourceConfig {

    @Bean(name = "primaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.primary")
    @Primary
    public DataSource testDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "primarySqlSessionFactory")
    @Primary
    public SqlSessionFactory testSqlSessionFactory(@Qualifier("primaryDataSource") DataSource dataSource) throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        //扫描mapper.xml路径
        bean.setMapperLocations(new PathMatchingResourcePatternResolver().
                getResources("classpath:mybatis/mapping/*.xml"));
        //增加驼峰配置
        org.apache.ibatis.session.Configuration configuration = new org.apache.ibatis.session.Configuration();
        configuration.setMapUnderscoreToCamelCase(true);
        bean.setConfiguration(configuration);
        return bean.getObject();
    }

    @Bean(name = "primaryTransactionManager")
    @Primary
    public DataSourceTransactionManager testTransactionManager(@Qualifier("primaryDataSource") DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }

    @Bean(name = "primarySqlSessionTemplate")
    @Primary
    public SqlSessionTemplate testSqlSessionTemplate(@Qualifier("primarySqlSessionFactory") SqlSessionFactory sqlSessionFactory) throws Exception {
        return new SqlSessionTemplate(sqlSessionFactory);
    }

}
```

```java
/**
 * 加载次数据源
 * @author Jjcc
 * @version 1.0.0
 * @className SecondaryDataSourceConfig.java
 * @createTime 2019年10月10日 11:09:00
 */
@Configuration
@MapperScan(basePackages = "com.jjcc.bootlaunch.generator.test2",
        sqlSessionTemplateRef = "secondarySqlSessionTemplate")
public class SecondaryDataSourceConfig {

    @Bean(name = "secondaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.secondary")
    public DataSource testDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "secondarySqlSessionFactory")
    public SqlSessionFactory testSqlSessionFactory(@Qualifier("secondaryDataSource") DataSource dataSource) throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        //扫描mapper.xml路径
        bean.setMapperLocations(new PathMatchingResourcePatternResolver().
                getResources("classpath:mybatis/mapping/*.xml"));
        //增加驼峰配置
        org.apache.ibatis.session.Configuration configuration = new org.apache.ibatis.session.Configuration();
        configuration.setMapUnderscoreToCamelCase(true);
        bean.setConfiguration(configuration);
        return bean.getObject();
    }

    @Bean(name = "secondaryTransactionManager")
    public DataSourceTransactionManager testTransactionManager(@Qualifier("secondaryDataSource") DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }

    @Bean(name = "secondarySqlSessionTemplate")
    public SqlSessionTemplate testSqlSessionTemplate(@Qualifier("secondarySqlSessionFactory") SqlSessionFactory sqlSessionFactory) throws Exception {
        return new SqlSessionTemplate(sqlSessionFactory);
    }
}
```

**3. 注意不同的数据源的mapper类一定不能放在一个包下,否则@mapperScan只扫描一个数据源,即只会使用一个数据源,里一个不会加载**

<img src=".\img\1570691803584.png" alt="1570691803584"  />

#### 3. mybatis-plus

[mp官方文档连接]: https://mp.baomidou.com

**1. Maven依赖**

将`mybatis-spring-boot-starter`依赖删除。

```xml
		<dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.1.0</version>
        </dependency>
        <!-- mybatis plus 代码生成器依赖 -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.1.0</version>
        </dependency>
        <!-- 代码生成器模板 -->
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.0</version>
            <scope>test</scope>
        </dependency>
```

**2. application.yml配置文件配置相关的属性值**

> 配置了mybatis-plus后，mybatis就不需要配置了

```yaml
# mybatis-plus
mybatis-plus:
  mapper-locations: classpath:mybatis/mapping/*Mapper.xml
  type-aliases-package: com.jjcc.bootlaunch.model
  configuration:
    # 驼峰下划线转换
    map-underscore-to-camel-case: true
    cache-enabled: false
    call-setters-on-nulls: true
    # 这个配置会将执行的sql打印出来，在开发或测试的时候可以用
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    # 刷新mapper 调试神器
    refresh: true
    banner: false
    #序列接口实现类配置
    #key-generator: com.baomidou.springboot.xxx
    # 数据库相关配置
    db-config:
      db-type: mysql
      # 主键类型  AUTO:"数据库ID自增", INPUT:"用户输入ID",ID_WORKER:"全局唯一ID (数字类型唯一ID)", UUID:"全局唯一ID UUID";
      id-type: id_worker
      # 字段策略 IGNORED:"忽略判断",NOT_NULL:"非 NULL 判断"),NOT_EMPTY:"非空判断"
      field-strategy: not_empty
      #驼峰下划线转换
#      column-underline: true
      #数据库大写下划线转换
      capital-mode: true
      #逻辑删除配置
      logic-delete-value: 1
      logic-not-delete-value: 0
```

- `config.mybatis` mybatis的配置
- `config.mybatis.scanner` 扩展mybatis的mapper scanner
- `controller` 接口控制器
- `db` 访问数据库相关的对象
- `db.entity` 实体
- `db.mapper` mybatis mapper
- `exception` 例外处理器，用来处理controller里的例外
- `repository` 仓库
- `resources.mapper` mybatis mapper的xml文件
- `resources.META-INF` 向spring注册组件

**3. SqlSessionFactory**

> 如果是多数据源，需要将`SqlSessionFactory`中的`SqlSessionFactoryBean`改为`MybatisSqlSessionFactoryBean`，否则baseMapper自带的方法不能访问，但能访问*Mapper.xml中定义的方法。**并且多数据源情况下，需要将配置文件中的信息手动注入到`MybatisConfiguration`和`GlobalConfig`中**
>

```java
    @Bean(name = "primarySqlSessionFactory")
    @Primary
    public SqlSessionFactory testSqlSessionFactory(@Qualifier("primaryDataSource") DataSource dataSource,
                                                   @Qualifier("mybatisConfiguration") MybatisConfiguration configuration,
                                                   @Qualifier("globalConfig") GlobalConfig globalConfig) throws Exception {
        MybatisSqlSessionFactoryBean bean = new MybatisSqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        //指定mapper.xml路径
        bean.setMapperLocations(new PathMatchingResourcePatternResolver().
                getResources(mapperLocations));

        bean.setTypeAliasesPackage(typeAliasesPackage);

        bean.setConfiguration(configuration);

        bean.setGlobalConfig(globalConfig);

        return bean.getObject();
    }
```

**mybatis-plus配置类**

> 注册成bena的方式开启插件在多数据源情况下是无效的；需要手动加载至Configuration对象中
>
> `mybatisConfiguration.addInterceptor(paginationInterceptor);`

`MybatisConfiguration`Bean创建时使用`@Scope`指定为**多例模式创建**；**原因是多数据源集成mybatis-plus时，调用自带接口时，无法区分哪个数据源**

```java
/**
     * mybatis-plus的分页插件
     * @title paginationInterceptor
     * @author Jjcc
     * @return com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor
     * @createTime 2019/10/20 17:48
     */
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        return new PaginationInterceptor();
    }

    /**
     * SQL执行效率插件
     * 设置 dev test 环境开启
     * @title performanceInterceptor
     * @author Jjcc
     * @return com.baomidou.mybatisplus.extension.plugins.PerformanceInterceptor
     * @createTime 2019/10/20 17:49
     */
    @Bean
    @Profile({"test", "dev"})
    public PerformanceInterceptor performanceInterceptor() {
        return new PerformanceInterceptor();
    }

    /**
     * 逻辑删除用，3.1.1之后的版本可不需要配置该bean，但项目这里用的是3.1.1的
     * @title sqlInjector
     * @author Jjcc
     * @return com.baomidou.mybatisplus.core.injector.ISqlInjector
     * @createTime 2019/10/20 17:53
     */
    @Bean
    public ISqlInjector sqlInjector() {
        return new LogicSqlInjector();
    }

    /**
     * 加载配置文件中mybatis-plus的配置信息，配置多数据源所需要
     * 这里获取bean的方式为多例，原因是多数据源集成mybatis-plus时，调用自带接口时，无法区分哪个数据源（MybatisConfiguration方法）；
     * @title mybatisConfiguration
     * @author Jjcc
     * @param paginationInterceptor 分页插件
     * @param performanceInterceptor SQL执行效率插件
     * @return com.baomidou.mybatisplus.core.MybatisConfiguration
     * @createTime 2019/10/22 9:23
     */
    @Scope("prototype")
    @Bean("mybatisConfiguration")
    @ConfigurationProperties(prefix = "mybatis-plus.configuration")
    public MybatisConfiguration mybatisConfiguration(@Qualifier("paginationInterceptor") PaginationInterceptor paginationInterceptor,
                                                      @Qualifier("performanceInterceptor") PerformanceInterceptor performanceInterceptor) {
        MybatisConfiguration mybatisConfiguration = new MybatisConfiguration();
        mybatisConfiguration.addInterceptor(paginationInterceptor);
        mybatisConfiguration.addInterceptor(performanceInterceptor);
        return mybatisConfiguration;
    }

    @Scope("prototype")
    @Bean("globalConfig")
    @ConfigurationProperties(prefix = "mybatis-plus.global-config")
    public GlobalConfig globalConfig() {
        return new GlobalConfig();
    }
```

**4. 代码生成工具与各层次类的结构**

[代码生成工具类]: .\img\MysqlGenerator.java

生成后Bean如下：

```java
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
public class MpUser extends Model<MpUser> {

    private static final long serialVersionUID = 1L;

    @TableId(value = "id", type = IdType.AUTO)
    private Long id;

    private String username;

    private String address;

    private String openid;

    private LocalDateTime gmtCreate;

    private LocalDateTime gmtModified;

    @TableLogic
    private Integer deleted;

    @Override
    protected Serializable pkVal() {
        return this.id;
    }

}

```

生成的mapper.java和service和serviceImpl分别如下：

```java
@Repository
public interface MpUserMapper extends BaseMapper<MpUser> {
    
}
```

```java
public interface MpUserService extends IService<MpUser> {
}
```

```java
@Service
public class MpUserServiceImpl extends ServiceImpl<MpUserMapper, MpUser> implements MpUserService {
    
}
```



### 5.2 整合Druid数据源

**1. Maven依赖**

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.17</version>
</dependency>
```

**2.  applicatioin.ym配置文件中配置相关的属性值(多数据源)**

```yaml
server:
  port: 8888

spring:
  http:
    encoding:
      charset: utf-8
      force: false
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
  profiles:
    include: family
  datasource:
    primary:
      type: com.alibaba.druid.pool.DruidDataSource
      url: jdbc:mysql://localhost:3306/mydb?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=UTC
      username: root
      password: ENC(QwxV2ZpwDG4GBZkHu4yp+Q==)
      driver-class-name: com.mysql.jdbc.Driver
      initial-size: 8
      min-idle: 1
      max-active: 20
      max-wait: 60000
      time-between-eviction-runsMillis: 60000
      min-evictable-idle-timeMillis: 300000
      validation-query: select 'x' FROM DUAL
      test-while-idle: true
      test-on-borrow: false
      test-on-return: false
      pool-prepared-statements: true
      max-open-prepared-statements: 20
      max-pool-prepared-statement-per-connection-size: 20
      filters: stat
      connection-properties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
      use-global-data-source-stat: true

    secondary:
      type: com.alibaba.druid.pool.DruidDataSource
      url: jdbc:mysql://localhost:3306/project_01?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=UTC
      username: root
      password: ENC(QwxV2ZpwDG4GBZkHu4yp+Q==)
      driver-class-name: com.mysql.jdbc.Driver
      initial-size: 8
      min-idle: 1
      max-active: 20
      max-wait: 60000
      time-between-eviction-runsMillis: 60000
      min-evictable-idle-timeMillis: 300000
      validation-query: select 'x' FROM DUAL
      test-while-idle: true
      test-on-borrow: false
      test-on-return: false
      pool-prepared-statements: true
      max-open-prepared-statements: 20
      max-pool-prepared-statement-per-connection-size: 20
      filters: stat
      connection-properties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
      use-global-data-source-stat: true

#    druid:
#      initial-size: 8
#      min-idle: 1
#      max-active: 20
#      max-wait: 60000
#      time-between-eviction-runsMillis: 60000
#      min-evictable-idle-timeMillis: 300000
#      validation-query: select 'x' FROM DUAL
#      test-while-idle: true
#      test-on-borrow: false
#      test-on-return: false
#      pool-prepared-statements: true
#      max-open-prepared-statements: 20
#      max-pool-prepared-statement-per-connection-size: 20
#      filters: stat
#      connection-properties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
#      use-global-data-source-stat: true

mybatis:
  mapper-locations: classpath:mapping/*Mapper.xml
  type-aliases-package: com.jjcc.bootlaunch.model
#  config-location: classpath:classpath:mapping/*/*Mapper.xml
  configuration:
    map-underscore-to-camel-case: true
```

**单数据源**

```yaml
datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    url: jdbc:mysql://localhost:3306/mydb?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver
    druid:
        # 下面为连接池的补充设置，应用到上面所有数据源中
        # 初始化大小，最小，最大
        initialSize: 5
        minIdle: 5
        maxActive: 20
        # 配置获取连接等待超时的时间
        maxWait: 60000
        # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
        timeBetweenEvictionRunsMillis: 60000
        # 配置一个连接在池中最小生存的时间，单位是毫秒
        minEvictableIdleTimeMillis: 30
        validationQuery: SELECT 1
        validationQueryTimeout: 10000
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        # 打开PSCache，并且指定每个连接上PSCache的大小
        poolPreparedStatements: true
        maxPoolPreparedStatementPerConnectionSize: 20
        filters: stat,wall
        # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
        connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
        # 合并多个DruidDataSource的监控数据
        useGlobalDataSourceStat: true
```

**3. 更改数据源类**

返回`DataSource`的方法修改为返回`DruidDataSource`类型

```java
@Bean(name = "primaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.primary")
    @Primary
    public DataSource testDataSource() {
        DruidDataSource dds = DruidDataSourceBuilder.create().build();
        return dds;
    }
```

**4. Druid的SQL监视功能**

```java
/**
 * druid的sql监视功能
 * @author Jjcc
 * @version 1.0.0
 * @className DruidConfig.java
 * @createTime 2019年10月11日 15:23:00
 */
@Configuration
public class DruidConfig {

    /**
     * 配置Druid监控
     * 后台管理Servlet
     * @title druidServlet
     * @author Jjcc 
     * @return org.springframework.boot.web.servlet.ServletRegistrationBean
     * @createTime 2019/10/11 15:33
     */
    @Bean
    @Primary
    public ServletRegistrationBean druidServlet() {
        ServletRegistrationBean reg = new ServletRegistrationBean();
        reg.setServlet(new StatViewServlet());
        reg.addUrlMappings("/druid/*");
        //白名单
        Map<String,String> initParameters = new HashMap<>(16);
        //禁用HTML页面上的“REST ALL”功能
        initParameters.put("resetEnable","false");
        //IP白名单（没有配置或者为空，则允许所有访问）
        initParameters.put("/druid/*","");
        //监控页面登录用户名
        initParameters.put("loginUsername","admin");
        //监控页面登录用户密码
        initParameters.put("loginPassword", "admin");
        //ip黑名单
        initParameters.put("deny","");
        reg.setInitParameters(initParameters);
        return reg;
    }


    /**
     * 配置web监控的filter
     * @title filterRegistrationBean
     * @author Jjcc
     * @return org.springframework.boot.web.servlet.FilterRegistrationBean
     * @createTime 2019/10/11 15:33
     */
    @Bean
    @Primary
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
        filterRegistrationBean.setFilter(new WebStatFilter());
        filterRegistrationBean.addUrlPatterns("/*");
        filterRegistrationBean.addInitParameter("exclusions", "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
        filterRegistrationBean.addInitParameter("profileEnable", "true");
        filterRegistrationBean.addInitParameter("principalCookieName","USER_COOKIE");
        filterRegistrationBean.addInitParameter("principalSessionName","USER_SESSION");
        filterRegistrationBean.addInitParameter("DruidWebStatFilter","/*");
        return filterRegistrationBean;
    }
}
```

## 6. Spring事务与分布式事务

### 6.1 事务并发执行会出现的问题

- 更新丢失（问题严重）
  - **当有多个并发事务的执行，更新同一行数据，那个有可能一个操作把另一个操作的更新覆盖**
- 脏读（问题严重）

  - **一个事务读到另一个尚未提交的事务，即读到了事务的处理过程中的数据，而不是结果数据。该数据可能会被回滚从而失效。**
- 不可重复读（可以接受）

  - 不可重复读的含义：**一个事务对同一行数据读了两次，却得到了不同的结果。**具体分为以下两种情况
  - 虚读：在事务1读取同一记录的过程中，事务2对该事务进行了修改，从而事务1第二次读到了不一样的数据	
  
- 幻读
  - 事务1在两次查询的过程中，事务2对该表进行了插入，删除操作。从而事务1第二次查询的结果数量发生了变化。

> 脏读与不可重复度的区别：
>
> 脏读是一个事务读取到了另一个未提交事务的数据，而不是结果数据。而不可重复读读取到的是事务已经提交的数据，只不过在两次读的过程中数据被另一个事务改变了。

### 6.2 如何解决并发过程中事务问题（事务隔离）

**数据库一共有如下四种隔离级别：**

- Read uncommitted 读未提交
  在该级别下，一个事务对一行数据修改的过程中**，不允许另一个事务对该行数据进行修改，但允许另一个事务对该行数据读。**
  因此本级别下，不会出现更新丢失，但会出现脏读、不可重复读。
- Read committed 读提交 （oracle、sqlserver默认的隔离级别）
  在该级别下，未提交的写事务不允许其他事务访问该行，因此**不会出现脏读**；**但是读取数据的事务允许其他事务的访问该行数据，因此会出现不可重复读的情况。**
- Repeatable read 重复读 （mysql的默认隔离级别）
  简单说就是：一个事务开始读或写数据时，不允许其他事务对该数据进行修改。在该级别下，读事务禁止写事务，但允许读事务，因此不会出现同一事务两次读到不同的数据的情况（不可重复读），且写事务禁止其他一切事务。**这个级别无法解决幻读问题**。
- Serializable 序列化
  该级别要求所有事务都必须串行执行，因此能避免一切因并发引起的问题，但效率很低。

![img](E:\软件开发资料\Spring全家桶\springboot\img\d1f1334fce5a9b832eafb79085333c26_844x334.png)

隔离级别越高，越能保证数据的完整性和一致性，但是对并发性能的影响也越大。对于多数应用程序，可以优先考虑把数据库系统的隔离级别设为Read Committed。它能够避免脏读取，而且具有较好的并发性能。尽管它会导致不可重复读、幻读这些并发问题，应该由应用程序员采用悲观锁或乐观锁来控制。

- **共享锁（Shared Lock，也叫S锁）**：又称读锁，允许一个事务去读一行，**多个事务可以同时为一个对象加共享锁**（如果试衣间的门还没被锁上，顾客都能够同时进去参观），**阻止其他事物获得相同数据集的排他锁**。
  - 若事务T对数据对象A加上S锁，则事务T可以读A但不能修改A，其他事务只能再对A加S锁，而不能加X锁，直到T释放A上的S锁。这保证了其他事务可以读A，但在T释放A上的S锁之前不能对A做任何修改。
  - 产生共享锁的sql：`select * from ad_plan lock in share mode;`
- **排他锁（Exclusive Lock，也叫X锁）**：又称写锁。如果一个事务对象加了排他锁，**其他事务就不能再给它加任何锁了**。(某个顾客把试衣间从里面反锁了，其他顾客想要使用这个试衣间，就只有等待锁从里面给打开了)
  - 允许获取排他锁的事务更新数据，阻止其他事务取得相同的数据集共享读锁和排他写锁。若事务T对数据对象A加上X锁，事务T可以读A也可以修改A，其他事务不能再对A加任何锁，直到T释放A上的锁。
  - mysql InnoDB引擎默认的修改数据语句：**update,delete,insert都会自动给涉及到的数据加上排他锁，select语句默认不会加任何锁类型，加过排他锁的数据行在其他事务种是不能修改数据的，也不能通过for update和lock in share mode锁的方式查询数据，但可以直接通过select …from…查询数据，因为普通查询没有任何锁机制。**
  - 产生排他锁的sql： `select * from ad_plan for update;`

### 6.3 事务传播行为

事务传播行为用来描述由某一个事务传播行为修饰的方法被嵌套进另一个方法的时事务如何传播。

用伪代码说明：

```java
ServiceA {
           
         void methodA() {
             ServiceB.methodB();
         }

}
      
ServiceB {
           
         void methodB() {
         }
           
}
```

代码中`methodA()`方法嵌套调用了`methodB()`方法，`methodB()`的事务传播行为由`@Transaction(Propagation=XXX)`设置决定。

**Spring中七种事务传播行为**

| 事务传播行为类型          | 说明                                                         |
| :------------------------ | :----------------------------------------------------------- |
| PROPAGATION_REQUIRED      | 如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是最常见的选择。 |
| PROPAGATION_SUPPORTS      | 支持当前事务，如果当前没有事务，就以非事务方式执行。         |
| PROPAGATION_MANDATORY     | 使用当前的事务，如果当前没有事务，就抛出异常。               |
| PROPAGATION_REQUIRES_NEW  | 新建事务，如果当前存在事务，把当前事务挂起。                 |
| PROPAGATION_NOT_SUPPORTED | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。   |
| PROPAGATION_NEVER         | 以非事务方式执行，如果当前存在事务，则抛出异常。             |
| PROPAGATION_NESTED        | 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。 |

### 6.4 @Transactional 注解

| 属性名           | 说明                                                         |
| :--------------- | :----------------------------------------------------------- |
| value            | 当在配置文件中有多个 TransactionManager , 可以用该属性指定选择哪个事务管理器。 |
| propagation      | 事务的传播行为，默认值为 REQUIRED。                          |
| isolation        | 事务的隔离度，默认值采用 DEFAULT。                           |
| timeout          | 事务的超时时间，默认值为-1。如果超过该时间限制但事务还没有完成，则自动回滚事务。 |
| read-only        | 指定事务是否为只读事务，默认值为 false；为了忽略那些不需要事务的方法，比如读取数据，可以设置 read-only 为 true。 |
| rollback-for     | 用于指定能够触发事务回滚的异常类型，如果有多个异常类型需要指定，各类型之间可以通过逗号分隔。 |
| no-rollback- for | 抛出 no-rollback-for 指定的异常类型，不回滚事务。            |

### 6.5 spring事务的实现

spring事务本质上是依赖于数据库事务

![img](.\img\83b7902520146de4b8468a6c12210d56_955x538.png)

Spring事务本质上是依赖于第三方的实现

![img](.\img\312679a3bf90c280b9f4fed1f77a8e37_640x369.png)

### 6.6 分布式事务

**使用JTA处理分布式事务**
Spring Boot通过Atomikos或Bitronix的内嵌事务管理器支持跨多个XA资源的分布式JTA事务，当部署到恰当的J2EE应用服务器时也会支持JTA事务。

当发现JTA环境时，Spring Boot将使用Spring的 JtaTransactionManager 来管理事务。自动配置的JMS，DataSource和JPA　beans将被升级以支持XA事务。可以使用标准的Spring idioms，比如 @Transactional ，来参与到一个分布式事务中。如果处于JTA环境，但仍想使用本地事务，你可以将 `spring.jta.enabled` 属性设置为 false 来禁用JTA自动配置功能。

**1. 整合jta-atomikos**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jta-atomikos</artifactId>
</dependency>
```

**2. 更改application.yml中数据源配置**

```yaml
spring:
  datasource:
    type: com.alibaba.druid.pool.xa.DruidXADataSource
    druid:
      systemDB:
        name: systemDB
        url: jdbc:mysql://localhost:3306/mydb?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
        username: root
        password: 123456
        driver-class-name: com.mysql.jdbc.Driver
        # 下面为连接池的补充设置，应用到上面所有数据源中
        # 初始化大小，最小，最大
        initialSize: 5
        minIdle: 5
        maxActive: 20
        # 配置获取连接等待超时的时间
        maxWait: 60000
        # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
        timeBetweenEvictionRunsMillis: 60000
        # 配置一个连接在池中最小生存的时间，单位是毫秒
        minEvictableIdleTimeMillis: 30
        validationQuery: SELECT 1
        validationQueryTimeout: 10000
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        # 打开PSCache，并且指定每个连接上PSCache的大小
        poolPreparedStatements: true
        maxPoolPreparedStatementPerConnectionSize: 20
        filters: stat,wall
        # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
        connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
        # 合并多个DruidDataSource的监控数据
        useGlobalDataSourceStat: true

      businessDB:
        name: businessDB
        url: jdbc:mysql://localhost:3306/project_01?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
        username: root
        password: 123456
        driver-class-name: com.mysql.jdbc.Driver
        # 下面为连接池的补充设置，应用到上面所有数据源中
        # 初始化大小，最小，最大
        initialSize: 5
        minIdle: 5
        maxActive: 20
        # 配置获取连接等待超时的时间
        maxWait: 60000
        # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
        timeBetweenEvictionRunsMillis: 60000
        # 配置一个连接在池中最小生存的时间，单位是毫秒
        minEvictableIdleTimeMillis: 30
        validationQuery: SELECT 1
        validationQueryTimeout: 10000
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        # 打开PSCache，并且指定每个连接上PSCache的大小
        poolPreparedStatements: true
        maxPoolPreparedStatementPerConnectionSize: 20
        filters: stat,wall
        # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
        connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
        # 合并多个DruidDataSource的监控数据
        useGlobalDataSourceStat: true
```

**3. 定义两个数据源，返回AtomikosDataSourceBean类型对象 **

```java
@Configuration
public class DataSourceConfig {


    /**
     * 主数据源
     * 其次DataSource里用的是DruidXADataSource ，而后注册到AtomikosDataSourceBean并且返回
     * @title masterDataSource
     * @author Jjcc
     * @return javax.sql.DataSource
     * @createTime 2019/10/13 15:51
     */
    @Bean(name = "primaryDataSource")
    @Primary
    @Autowired
    public DataSource systemDataSource(Environment env) {
        AtomikosDataSourceBean ds = new AtomikosDataSourceBean();
        Properties prop = build(env, "spring.datasource.druid.systemDB.");
        ds.setXaDataSourceClassName("com.alibaba.druid.pool.xa.DruidXADataSource");
        ds.setUniqueResourceName("systemDB");
        ds.setPoolSize(5);
        ds.setXaProperties(prop);
        return ds;

    }

    /**
     * 次数据源
     * 其次DataSource里用的是DruidXADataSource ，而后注册到AtomikosDataSourceBean并且返回
     * @title testDataSource
     * @author Jjcc
     * @return javax.sql.DataSource
     * @createTime 2019/10/13 15:51
     */
    @Autowired
    @Bean(name = "secondaryDataSource")
    public DataSource businessDataSource(Environment env) {

        AtomikosDataSourceBean ds = new AtomikosDataSourceBean();
        Properties prop = build(env, "spring.datasource.druid.businessDB.");
        ds.setXaDataSourceClassName("com.alibaba.druid.pool.xa.DruidXADataSource");
        ds.setUniqueResourceName("businessDB");
        ds.setPoolSize(5);
        ds.setXaProperties(prop);

        return ds;
    }

    private Properties build(Environment env, String prefix) {
        Properties prop = new Properties();
        prop.put("url", env.getProperty(prefix + "url"));
        prop.put("username", env.getProperty(prefix + "username"));
        prop.put("password", env.getProperty(prefix + "password"));
        prop.put("driverClassName", env.getProperty(prefix + "driverClassName", ""));
        prop.put("initialSize", env.getProperty(prefix + "initialSize", Integer.class));
        prop.put("maxActive", env.getProperty(prefix + "maxActive", Integer.class));
        prop.put("minIdle", env.getProperty(prefix + "minIdle", Integer.class));
        prop.put("maxWait", env.getProperty(prefix + "maxWait", Integer.class));
        prop.put("poolPreparedStatements", env.getProperty(prefix + "poolPreparedStatements", Boolean.class));

        prop.put("maxPoolPreparedStatementPerConnectionSize",
                env.getProperty(prefix + "maxPoolPreparedStatementPerConnectionSize", Integer.class));

        prop.put("maxPoolPreparedStatementPerConnectionSize",
                env.getProperty(prefix + "maxPoolPreparedStatementPerConnectionSize", Integer.class));
        prop.put("validationQuery", env.getProperty(prefix + "validationQuery"));
        prop.put("validationQueryTimeout", env.getProperty(prefix + "validationQueryTimeout", Integer.class));
        prop.put("testOnBorrow", env.getProperty(prefix + "testOnBorrow", Boolean.class));
        prop.put("testOnReturn", env.getProperty(prefix + "testOnReturn", Boolean.class));
        prop.put("testWhileIdle", env.getProperty(prefix + "testWhileIdle", Boolean.class));
        prop.put("timeBetweenEvictionRunsMillis",
                env.getProperty(prefix + "timeBetweenEvictionRunsMillis", Integer.class));
        prop.put("minEvictableIdleTimeMillis", env.getProperty(prefix + "minEvictableIdleTimeMillis", Integer.class));
        prop.put("filters", env.getProperty(prefix + "filters"));

        return prop;
    }

}
```

**4. 配置事务管理器**

```java
/**
 *分布式事务使用JTA管理，不管有多少个数据源只要配置一个 JtaTransactionManager
 * @author baibing
 * @project: springboot-mybatis
 * @package: com.sailing.springbootmybatis.config
 * @Description: 多数据源事务管理器配置类
 * @date 2018/11/9 11:45
 */
@Configuration
public class TransactionManagerConfig {

    @Bean
    public JtaTransactionManager transactionManager(){
        UserTransactionManager userTransactionManager = new UserTransactionManager();
        UserTransaction userTransaction = new UserTransactionImp();
        return new JtaTransactionManager(userTransaction, userTransactionManager);
    }
}
```

**注意：注释掉原有的两个数据源事物，**

**5. 测试**

```java
    @Override
    @Transactional(value = "transactionManager", rollbackFor = {ArithmeticException.class, Error.class})
    public int saveUseInfo(User user) {
            userMapper1.saveUserInfo(user);
            user2Mapper.saveUserInfo(user);
        int s = 5 / 0;
       return 0;
    }
```



## 7. 生命周期内的拦截，过滤与监听

### 7.1 servlet与filter与listener的实现

> 在SpringBootApplication上使用@ServletComponentScan注解后，Servlet、Filter、Listener可以直接通过@WebServlet、@WebFilter、@WebListener注解自动注册，无需其他代码。

#### 1. 监听器

Servlet 监听器是 Servlet 规范中定义的一种特殊类，**用于监听 ServletContext、HttpSession 和 ServletRequest 等域对象的创建与销毁事件，以及监听这些域对象中属性发生修改的事件**。监听器是观察者模式的应用，它关注特定事物，并伺机而动，所以监听器具有**异步**的特性。

Servlet Listener 监听三大域对象的创建和销毁事件，三大对象分别是：

1. ServletContext：application 级别，整个应用只存在一个，可以进行全局应用配置。
2. HttpSession：session 级别，针对每一个对话，如统计会话总数。
3. ServletRequest：request 级别，针对每一个客户请求，

##### 使用场景

Servlet 规范设计监听器的作用是在事件发生前、发生后进行一些处理，一般可以用来统计在线人数和在线用户、统计网站访问量、系统启动时初始化信息等。我们可以在容器启动时初始化 Log4j 信息，添加自己对容器状态的监控，初始化 Spring 组件等。

##### 监听器的实现

创建一个ServletRequest监听器(其他监听器类似创建)

```java
@WebListener
@Slf4j
public class Customlister implements ServletRequestListener{

    @Override
    public void requestDestroyed(ServletRequestEvent sre) {
        log.info(" request监听器：销毁");
    }

    @Override
    public void requestInitialized(ServletRequestEvent sre) {
        log.info(" request监听器：可以在这里记录访问次数哦");
    }

}
```

> **在启动类中加入@ServletComponentScan进行自动注册即可。**

#### 2 过滤器

##### 定义

过滤器是一种可重用的代码，可以转换 HTTP 请求、响应和头信息，通俗来说就是过滤器可以在请求到达服务器之前，对请求头进行预先处理，在响应内容到达客户端之前，对服务器做出的响应进行后置处理。
根据这个定义，我们就不难理解为什么它是位于 Listener 的下游，Servlet 的上游了？过滤器必须是在容器启动之后，接收到请求后开始处理，所以它是在 Listener 执行之后；在请求到达 Servlet 之前进行预处理，所以它又处于 Servlet 之前的位置。
从命名上理解，它就是对**请求和响应的数据**内容进行过滤处理的，并非前端传过来的数据都全单接收，而是有原则地进行过滤处理，以保证后端服务器业务的安全。

##### 使用场景

Servlet 3.1 中定义了几种常见的过滤器组件：

| 过滤器                                        | 作用                         |
| :-------------------------------------------- | :--------------------------- |
| Authentication filters：                      | 授权类，如用户登陆会话校验； |
| Logging and auditing filters：                | 日志和安全审计类；           |
| Image conversion filters：                    | 图片转换；                   |
| Data compression filters：                    | 数据压缩；                   |
| Encryption filters：                          | 加密、解密类；               |
| Tokenizing filters：                          | 词法类；                     |
| Filters that trigger resource access events： | 触发资源访问事件类；         |
| XSL/T filters that transform XML content：    | XML文件转换类；              |
| MIME-type chain filters：                     | MIME文件；                   |
| Caching filters：                             | 缓存类；                     |

或者我们社交应用经常需要的敏感词过滤，都可以使用过滤器。过滤器主要的特点在于，它能够改变请求内容。

##### 过滤器的实现

过滤器Filter，是Servlet的的一个实用技术了。可通过过滤器，对请求进行拦截，比如读取session判断用户是否登录、判断访问的请求URL是否有访问权限(黑白名单)等。主要还是可对请求进行预处理。接下来介绍下，在springboot如何实现过滤器功能。

**1 实现方式一:利用WebFilter注解配置**

`@WebFilter`时Servlet3.0新增的注解，原先实现过滤器，需要在web.xml中进行配置，而现在通过此注解，启动启动时会自动扫描自动注册。

编写Filter类：

```java
//注册器名称为customFilter,拦截的url为所有
@WebFilter(filterName="customFilter",urlPatterns={"/*"})
@Slf4j
public class CustomFilter implements Filter{

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("filter 初始化");
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        log.info("customFilter 请求处理之前");
        //对request、response进行一些预处理
        // 比如设置请求编码
        // request.setCharacterEncoding("UTF-8");
        // response.setCharacterEncoding("UTF-8");

        //链路 直接传给下一个过滤器
        chain.doFilter(request, response);

        log.info("customFilter 请求处理之后");
    }

    @Override
    public void destroy() {
        log.info("filter 销毁");
    }
}
```

> **然后在启动类加入@ServletComponentScan注解即可。**

使用这种方法，当注册多个过滤器时，无法指定执行顺序的，原本使用web.xml配置过滤器时，是可指定执行顺序的，但使用`@WebFilte`r时，没有这个配置属性的(需要配合`@Order`进行)，所以接下来介绍下通过FilterRegistrationBean进行过滤器的注册。

`@Order：`

1. order的值越小，类加载器加载优先级越高
2. order如果不标注数字，默认最低优先级，因为其默认值是int最大值
3. 该注解等同于实现Ordered接口getOrder方法，并返回数字。

**2. FilterRegistrationBean方式**

FilterRegistrationBean是springboot提供的，此类提供setOrder方法，可以为filter设置排序值，让spring在注册web filter之前排序后再依次注册。
首先要改写`filter`, 其实就除了@webFilter注解即可。其他的都没有变化。启动类中利用@bean注册FilterRegistrationBean

```java
@Configuration
public class FilterRegistration {
    @Bean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        //当过滤器有注入其他bean类时，可直接通过@bean的方式进行实体类过滤器，这样不可自动注入过滤器使用的其他bean类。
        //当然，若无其他bean需要获取时，可直接new CustomFilter()，也可使用getBean的方式。
        registration.setFilter(customFilter());
        //过滤器名称
        registration.setName("customFilter");
        //拦截路径
        registration.addUrlPatterns("/*");
        //设置顺序
        registration.setOrder(10);
        return registration;
    }

    @Bean
    public Filter customFilter() {
        return new CustomFilter();
    }
}
```

注册多个时，就注册多个FilterRegistrationBean即可,启动后，效果和第一种是一样的。

#### 3. servlet

##### 定义

在java程序员10年以前做web开发的时候，所有的请求都是由servlet来接受并响应的。每一个请求，就要写一个servlet。这种方式很麻烦，大家就想能不能根据请求的路径以及参数不同，映射到不同的方法上去执行，这样就可以在一个servlet类里面处理多个请求，每个请求就是一个方法。这个思想后来就主键发展为structs、SpringMVC。

##### 使用场景

目前来看，servlet使用的场景已经被springMVC架构全面覆盖。但是不排除，老项目向spring boot项目迁移，需要支持servlet的情况。

##### 实现

下面我们就看一下，在spring boot里面如何实现servlet。

```java
@WebServlet(name = "firstServlet", urlPatterns = "/firstServlet") //标记为servlet，以便启动器扫描。
public class FirstServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().append("firstServlet");
    }

}
```

然后在启动类加入`@ServletComponentScan`注解即可。

### 7.2 spring拦截器及请求链路说明

#### 1. 定义

在 Servlet 规范中并没有拦截器的概念，它**是面向切面编程的一种应用**：在需要对方法进行增强的场景下，例如在方法调用前执行一段代码，或者在方法完成后额外执行一段操作，**拦截器的一种实现方式就是动态代理。**
把过滤器和拦截器放在一起比较，我觉得是没有意义的，本质上是不同概念，并没有可比性，它位于过滤器的下游，是面向 Servlet 方法的。

#### 2. 使用场景

AOP 编程思想面对的是横向的切面，而非纵向的业务。举个简单的例子，每个方法处理过程中，除了业务逻辑外，我们都会有一些相同的操作：参数校验，日志打印等，虽然这些处理代码并不多，但是每个方法都要写这类东西，工作量就不小了。
能否使用程序来统一加入这类操作，而不用程序员自己手写呢？这就是切面编程思想的应用，利用 Java 的代理，在调用真正的方法之前或者之后，添加一些额外的增强功能。

#### 3. 拦截器的实现

以上的过滤器、监听器都属于Servlet的api，我们在开发中处理利用以上的进行过滤web请求时，还可以使用Spring提供的拦截器(HandlerInterceptor)进行更加精细的控制。

**源码：**

```java
// HandlerInterceptor.java

/**
 * 拦截处理器，在 {@link HandlerAdapter#handle(HttpServletRequest, HttpServletResponse, Object)} 执行之前
 */
default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
		throws Exception {
	return true;
}

/**
 * 拦截处理器，在 {@link HandlerAdapter#handle(HttpServletRequest, HttpServletResponse, Object)} 执行成功之后
 */
default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
		@Nullable ModelAndView modelAndView) throws Exception {
}

/**
 * 拦截处理器，在 {@link HandlerAdapter#handle(HttpServletRequest, HttpServletResponse, Object)} 执行完之后，无论成功还是失败
 *
 * 并且，只有该处理器 {@link #preHandle(HttpServletRequest, HttpServletResponse, Object)} 执行成功之后，才会被执行
 */
default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
		@Nullable Exception ex) throws Exception {
}

```

- 一共有三个方法，分别为：
  - `#preHandle(...)` 方法，调用 Controller 方法之**前**执行。
  - `#postHandle(...)` 方法，调用 Controller 方法之**后**执行。
  - `#afterCompletion(...)`方法，处理完 Controller 方法返回结果之后执行。
    - 例如，页面渲染后。
    - **当然，要注意，无论调用 Controller 方法是否成功，都会执行**。
- 举个例子：
  - 当俩个拦截器都实现放行操作时，执行顺序为 `preHandle[1] => preHandle[2] => postHandle[2] => postHandle[1] => afterCompletion[2] => afterCompletion[1]` 。
  - 当第一个拦截器 `#preHandle(...)` 方法返回 `false` ，也就是对其进行拦截时，第二个拦截器是完全不执行的，第一个拦截器只执行 `#preHandle(...)` 部分。
  - 当第一个拦截器 `#preHandle(...)` 方法返回 `true` ，第二个拦截器 `#preHandle(...)` 返回 `false` ，执行顺序为 `preHandle[1] => preHandle[2] => afterCompletion[1]` 。
- 总结来说：
  - `#preHandle(...)` 方法，按拦截器定义**顺序**调用。若任一拦截器返回 `false` ，则 Controller 方法不再调用。
  - `#postHandle(...)` 和 `#afterCompletion(...)` 方法，按拦截器定义**逆序**调用。
  - `#postHandler(...)` 方法，在调用 Controller 方法之**后**执行。
  - `#afterCompletion(...)` 方法，只有该拦截器在 `#preHandle(...)` 方法返回 `true` 时，才能够被调用，且一定会被调用。为什么“且一定会被调用”呢？即使 `#afterCompletion(...)` 方法，按拦截器定义**逆序**调用时，前面的拦截器发生异常，后面的拦截器还能够调用，**即无视异常**。

**编写自定义拦截器类**

```java
@Slf4j
public class CustomHandlerInterceptor implements HandlerInterceptor{

 @Override
 public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
   throws Exception {
  log.info("preHandle:Controller执行之前调用");
  //返回 false 则请求中断
  return true;
 }

 @Override
 public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
   ModelAndView modelAndView) throws Exception {
  log.info("postHandle:Controller中逻辑代码执行完之后调用");

 }

 @Override
 public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
   throws Exception {
  log.info("afterCompletion:Controller中Return之后，即在视图渲染完成之后调用");

 }

}
```

通过继承WebMvcConfigurerAdapter注册拦截器。笔者在写作完成后，发现WebMvcConfigurerAdapter类已经被废弃，**请实现WebMvcConfigurer接口完成拦截器的注册。**

```java
@Configuration
//废弃：public class MyWebMvcConfigurer extends WebMvcConfigurerAdapter{
public class MyWebMvcConfigurer implements WebMvcConfigurer 
 @Override
  public void addInterceptors(InterceptorRegistry registry) {
   //注册拦截器 拦截规则
  //多个拦截器时 以此添加 执行顺序按添加顺序
  registry.addInterceptor(getHandlerInterceptor()).addPathPatterns("/**");
  }
	
 @Bean
 public static HandlerInterceptor getHandlerInterceptor() {
  return new CustomHandlerInterceptor();
 }
}
```

#### 4. 请求链路说明

![img](.\img\dc392b1f9ce0145707da6493665ff7f4_535x457.png)

![img](.\img\a8ac4b58376ce29cf0ee7d80af763f40_1391x239.png)

![img](.\img\f663fc3c415b198dd6e4e8a5136a6b59_819x460.png)

## 8. 嵌入式容器的配置与应用

### 8.1 打war包部署到外置tomcat容器

**1. 修改打包方式**

```xml
<packaging>war</packaging>
```

![img](.\img\3e5b09175fb32b2a6ce9afd3cccb76c2_446x154.png)

**2. 排除内置tomcat的依赖**

`exclusions`标签是指除外的意思

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

**如果在代码中有应用到Servlet的api，则可以添加外部`tomcat`的依赖**

`<scope>provided</scope>`是指只有开发，测试环境才有用。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>
</dependency>
```

**3. 新增加一个类继承SpringBootServletInitializer实现configure：**

```java
public class ServletInitializer extends SpringBootServletInitializer { 
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) { 		 //此处的Application.class为带有@SpringBootApplication注解的启动类
        return builder.sources(BootLaunchApplication.class);
    } 
}
```

> 注意事项：
> 使用外部Tomcat部署访问的时候，application.properties(或者application.yml)中配置的
> server.port=
> server.servlet.context-path=
> 将失效，请使用tomcat的端口，tomcat，webapps下项目名进行访问。

**4. build要有finalName标签**

```xml
<build>
    <!-- 应与application.properties(或application.yml)中context-path保持一致 -->
    <finalName>war包名称</finalName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

**5. 打包**

war方式打包，使用外置Tomcat：`mvn clean package -Dmaven.test.skip=true`

<img src=".\img\1571310965238.png" alt="1571310965238" style="zoom:150%;" />



## 9. 统一全局异常处理

### 9.1 设置一个优秀的异常处理机制

![img](.\img\fafd4730aec29c45388c3b0defbafccb_1273x486.png)

**为什么要将系统运行时异常捕获，转换为自定义异常抛出？**

因为用户不认识ConnectionTimeOutException类似这种异常是什么东西，但是转换为自定义异常就要求程序员对运行时异常进行一个翻译，比如：自定义异常里面应该有message字段，后端程序员应该明确的在message字段里面用面向用户的友好语言，说明发生了什么。

**开发规范**

1. Controller、Service、DAO层拦截异常转换为自定义异常，不允许将异常私自截留。必须对外抛出。
2. 统一数据响应代码，使用httpstatusode，不要自定义。自定义不方便记忆。200请求成功，400用户输入错误导致的异常，500系统内部异常，999未知异常。
3. 自定义异常里面有message属性，一定用友好的语言描述异常，并赋值给message.
4. 不允许对父类Excetion统一catch，要分小类catch，这样能够清楚地将异常转换为自定义异常传递给前端。

### 9.2 自定义异常和相关数据结构

#### 1. 数据结构

1. CustomException 自定义异常。核心要素：异常错误编码（200正常,400,500），异常错误信息message。
2. ExceptionTypeEnum 枚举异常分类，**将异常分类固化下来，防止开发人员思维发散**。 核心要素 异常分类编码（200正常,400,500），异常分类描述。
3. AjaxResponse 用于响应Ajax请求。核心要素：是否请求成功 isok；响应code零与非零，零表示成功（200,400,500）；响应成功与否信息描述message；响应成功的数据data。
4. error.html
   另外还需要有一个统一处理CustomException的地方。即@ControllerAdvice和@ExceptionHandler，后文会说明

#### 2. 自定义异常及自定义响应数据结构

**异常分类的枚举，把异常分类固化下来**

```java
public enum CustomExceptionType {
    USER_INPUT_ERROR(400,"用户输入异常"),
    SYSTEM_ERROR (500,"系统服务异常"),
    OTHER_ERROR(999,"其他未知异常");

    CustomExceptionType(int code, String typeDesc) {
        this.code = code;
        this.typeDesc = typeDesc;
    }

    private String typeDesc;//异常类型中文描述

    private int code; //code

    public String getTypeDesc() {
        return typeDesc;
    }

    public int getCode() {
        return code;
    }
}
```

**自定义异常**

```java
public class CustomException extends RuntimeException {
    //异常错误编码
    private int code ;
    //异常信息
    private String message;

    public CustomException(CustomExceptionType exceptionTypeEnum, String message) {
        this.code = exceptionTypeEnum.getCode();
        this.message = message;
    }

    int getCode() {
        return code;
    }

    @Override
    public String getMessage() {
        return message;
    }
}
```

**统一响应前端的数据结构**

```java
@Data
public class AjaxResponse {

    private boolean isok; // ajax请求是否成功
    private int code;        // http status code
    private String message; //请求失败的的提示信息。
    private Object data;     //请求成功时，需要响应给前端的数据

    private AjaxResponse() {

    } 
    //请求出现异常时的响应数据封装
    public static AjaxResponse error(CustomException e) {

        AjaxResponse resultBean = new AjaxResponse();
        resultBean.setIsok(false);
        resultBean.setCode(e.getCode());
        if(e.getCode() == CustomExceptionType.USER_INPUT_ERROR.getCode()){
            resultBean.setMessage(e.getMessage());
        }else if(e.getCode() == CustomExceptionType.SYSTEM_ERROR.getCode()){
            resultBean.setMessage(e.getMessage() + ",系统出现异常，请联系管理员电话：13756108723进行处理!");
        }else{
            resultBean.setMessage("系统出现未知异常，请联系管理员电话：13756108723进行处理!");
        }
        return resultBean;
    }
    //请求成功时的响应数据封装，没有响应数据（比如删除修改成功）
    public static AjaxResponse success() {
        AjaxResponse resultBean = new AjaxResponse();
        resultBean.setIsok(true);
        resultBean.setCode(200);
        resultBean.setMessage("success");
        return resultBean;
    }
    //请求成功时的响应数据封装，有响应数据（比如查询成功）
    public static AjaxResponse success(Object data) {
        AjaxResponse resultBean = new AjaxResponse();
        resultBean.setIsok(true);
        resultBean.setCode(200);
        resultBean.setMessage("success");
        resultBean.setData(data);
        return resultBean;
    }

}
```

#### 3. 无异常情况下，如何使用AjaxResponse

**更新操作，Controller无需返回额外的数据**

```java
return AjaxResponse.success();
```

![img](.\img\160d84fb9387609b015f6415091d27e6_233x116.png)

**查询接口，Controller需返回结果数据(data可以是任何类型数据)**

```java
 return AjaxResponse.success(data);
```

![img](.\img\ebb13a738b0b4ee06dd7037177e97577_437x255.png)

### 9.3 全局异常处理ExceptionHandler

#### 1. 全局异常处理器

`@ControllerAdvice`注解的作用就是监听所有的`Controller`，一旦Controller抛出`CustomException`，就会在`@ExceptionHandler(CustomException.class)`对该异常进行处理。

```java
@ControllerAdvice
public class WebExceptionHandler {

    @ExceptionHandler(CustomException.class)
    @ResponseBody
    public AjaxResponse customerException(CustomException e) {
        if(e.getCode() == CustomExceptionType.SYSTEM_ERROR.getCode()){
                 //400异常不需要持久化，将异常信息以友好的方式告知用户就可以
                //TODO 将500异常信息持久化处理，方便运维人员处理
        }
        return AjaxResponse.error(e);
    }

    @ExceptionHandler(Exception.class)
    @ResponseBody
    public AjaxResponse exception(Exception e) {
        //TODO 将异常信息持久化处理，方便运维人员处理

        //没有被程序员发现，并转换为CustomException的异常，都是其他异常或者未知异常.
        return AjaxResponse.error(new CustomException(CustomExceptionType.OTHER_ERROR,"未知异常"));
    }


}
```

#### 2. 异常校验的规范及常用注解

在web开发时，对于请求参数，一般上都需要进行参数合法性校验的，原先的写法时一个个字段一个个去判断，这种方式太不通用了，所以java的JSR 303: Bean Validation规范就是解决这个问题的。
JSR 303只是个规范，并没有具体的实现，目前通常都是才有hibernate-validator进行统一参数校验。

JSR303定义的校验类型

| Constraint                  | 详细信息                                                 |
| :-------------------------- | :------------------------------------------------------- |
| @Null                       | 被注释的元素必须为 null                                  |
| @NotNull                    | 被注释的元素必须不为 null                                |
| @AssertTrue                 | 被注释的元素必须为 true                                  |
| @AssertFalse                | 被注释的元素必须为 false                                 |
| @Min(value)                 | 被注释的元素必须是一个数字，其值必须大于等于指定的最小值 |
| @Max(value)                 | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值 |
| @DecimalMin(value)          | 被注释的元素必须是一个数字，其值必须大于等于指定的最小值 |
| @DecimalMax(value)          | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值 |
| @Size(max, min)             | 被注释的元素的大小必须在指定的范围内                     |
| @Digits (integer, fraction) | 被注释的元素必须是一个数字，其值必须在可接受的范围内     |
| @Past                       | 被注释的元素必须是一个过去的日期                         |
| @Future                     | 被注释的元素必须是一个将来的日期                         |
| @Pattern(value)             | 被注释的元素必须符合指定的正则表达式                     |

Hibernate Validator 附加的 constraint

| Constraint | 详细信息                               |
| :--------- | :------------------------------------- |
| @Email     | 被注释的元素必须是电子邮箱地址         |
| @Length    | 被注释的字符串的大小必须在指定的范围内 |
| @NotEmpty  | 被注释的字符串的必须非空               |
| @Range     | 被注释的元素必须在合适的范围内         |

**用法:把以上注解加在pojo类的属性字段上，然后在参数校验的方法上加@Valid注解**

```java
@PutMapping("/article/{id}")
public @ResponseBody AjaxResponse updateArticle(
                                 @Valid @RequestBody ArticleVO article) {
        //业务
    return AjaxResponse.success();
}
```

##### 友好的数据校验异常处理（用户输入异常的全局处理）

我们已知当数据校验失败的时候，会抛出异常`BindException`或`MethodArgumentNotValidException`。所以我们对这两种异常做全局处理，防止程序员重复编码带来困扰。

```java
@ExceptionHandler(MethodArgumentNotValidException.class)
@ResponseBody
public AjaxResponse handleBindException(MethodArgumentNotValidException ex) {
    FieldError fieldError = ex.getBindingResult().getFieldError();
    return AjaxResponse.error(new CustomException(CustomExceptionType.USER_INPUT_ERROR,fieldError.getDefaultMessage()));
}


@ExceptionHandler(BindException.class)
@ResponseBody
public AjaxResponse handleBindException(BindException ex) {
    FieldError fieldError = ex.getBindingResult().getFieldError();
    return AjaxResponse.error(new CustomException(CustomExceptionType.USER_INPUT_ERROR,fieldError.getDefaultMessage()));
}
```



## 10. 日志框架与全局日志管理

### 10.1 日志框架的简介与选型

**Spring Boot 默认的日志记录框架使用的是 `Logback`，此外我们还可以选择 Log4j 和 Log4j2**。其中 Log4j 可以认为是一个过时的函数库，已经停止更新，不推荐使用，相比之下，性能和功能也是最差的。

![img](.\img\ef1a0347b2282a671fd0b95f060f62b3_685x363.png)

#### 1. 日志级别

最常见的日志的几个级别：**ERROR, WARN, INFO, DEBUG和TRACE**。像其他的，比如ALL、OFF和FATAL之类的开发过程中应该基本上是不会涉及的。所以以下**从低到高**一次介绍下常见的日志级别。

1. TRACE：追踪。一般上对核心系统进行性能调试或者跟踪问题时有用，此级别很低，一般上是不开启的，开启后日志会很快就打满磁盘的。
2. DEBUG:调试。这个大家应该不陌生了。开发过程中主要是打印记录一些运行信息之类的。
3. INFO:信息。这个是最常见的了，大部分默认就是这个级别的日志。一般上记录了一些交互信息，一些请求参数等等。可方便定位问题，或者还原现场环境的时候使用。此日志相对来说是比较重要的。
4. WARN:警告。这个一般上是记录潜在的可能会引发错误的信息。比如启动时，某某配置文件不存在或者某个参数未设置之类的。
5. ERROR:错误。这个也是比较常见的，一般上是在捕获异常时输出，虽然发生了错误，但不影响系统的正常运行。但可能会导致系统出错或是宕机等。

#### 2. 常见术语概念解析

1. appender：主要控制日志输出到哪里，比如：文件、数据库、控制台打印等
2. logger: 用来设置某一个包或者具体某一个类的日志打印级别、以及指定appender
3. root：也是一个logger，是一个特殊的logger。所有的logger最终都会将输出流交给root，除非设置logger中配置了additivity="false"。
4. rollingPolicy：所有日志都放在一个文件是不好的，所以可以指定滚动策略，按照一定周期或文件大小切割存放日志文件。
5. RolloverStrategy：日志清理策略。通常是指日志保留的时间。
6. 异步日志：单独开一个线程做日志的写操作，达到不阻塞主线程的目的

### 10.2 logback日志框架整合使用

spring boot 用自带的logback打印日志:

多环境打印.

1. 生产环境输出到控制台和文件,一天一个文件,保留30天.
2. 开发环境输出到控制台和打印sql(mybatis)输出.
3. 测试环境输出到控制台.

#### 1. 配置文件

因为logback是spring boot的默认日志框架，所以不需要引入maven依赖，直接上logback-spring.xml放在resources下面

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--引入默认的一些设置-->
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <!--web信息-->
    <logger name="org.springframework.web" level="info"/>

    <!--写入日志到控制台的appender,用默认的,但是要去掉charset,否则windows下tomcat下乱码-->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
        </encoder>
    </appender>

    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_PATH" value="D:/logs/boot-launch"/>
    <!--写入日志到文件的appender-->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件输出的文件名,每天一个文件-->
            <FileNamePattern>${LOG_PATH}.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--日志文件保留天数-->
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
        <!--日志文件最大的大小-->
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <MaxFileSize>10MB</MaxFileSize>
        </triggeringPolicy>
    </appender>

    <!--异步到文件-->
    <appender name="asyncFileAppender" class="ch.qos.logback.classic.AsyncAppender">
        <discardingThreshold>0</discardingThreshold>
        <queueSize>500</queueSize>
        <appender-ref ref="FILE"/>
    </appender>

    <!--生产环境:打印控制台和输出到文件-->
    <springProfile name="prod">
        <root level="info">
            <appender-ref ref="CONSOLE"/>
            <appender-ref ref="asyncFileAppender"/>
        </root>
    </springProfile>

    <!--开发环境:打印控制台-->
    <springProfile name="dev">
        <!-- 打印sql -->
        <logger name="com.zimug.bootlaunch" level="DEBUG"/>
        <root level="DEBUG">
            <appender-ref ref="CONSOLE"/>
        </root>
    </springProfile>

    <!--测试环境:打印控制台-->
    <springProfile name="test">
        <root level="info">
            <appender-ref ref="CONSOLE"/>
        </root>
    </springProfile>
</configuration>
```

### 10.3 log4j2日志框架

#### 1. Maven依赖

Spring Boot默认使用LogBack，但是我们没有看到显示依赖的jar包，其实是因为所在的jar包spring-boot-starter-logging都是作为**spring-boot-starter-web或者spring-boot-starter依赖的一部分**。

如果这里要使用Log4j2，需要从spring-boot-starter-web中去掉spring-boot-starter-logging依赖，同时显示声明使用Log4j2的依赖jar包，具体如下:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

**注意，log4j是之前使用比较广泛的，可能会存在jar包冲突**

#### 2. 配置文件

在resources目录下新建一个log4j2.xml文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <Appenders>
        <Console name="CONSOLE" target="SYSTEM_OUT">
            <PatternLayout charset="UTF-8" pattern="[%-5p] %d %c - %m%n" />
        </Console>

        <RollingFile name="runtimeFile" fileName="./logs/boot-launch.log" filePattern="./logs/boot-launch-%d{yyyy-MM-dd}.log"
                     append="true">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS Z}\t%level\t%class\t%line\t%thread\t%msg%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
            </Policies>
            <!-- 此行以下为自动清理日志的配置 -->
            <DefaultRolloverStrategy>
                <Delete basePath="./logs">
                    <!-- glob 项为需要自动清理日志的pattern -->
                    <IfFileName glob="*.log"/>
                    <!-- 30d 表示自动清理掉30天以前的日志文件 -->
                    <IfLastModified age="30d"/>
                </Delete>
            </DefaultRolloverStrategy>
            <!-- 此行以上为自动清理日志的配置 -->
        </RollingFile>


    </Appenders>

    <Loggers>
        <root level="info">
            <AppenderRef ref="CONSOLE" />
            <AppenderRef ref="runtimeFile" />
        </root>
    </Loggers>
</configuration>
```

**注意：关于log4j2的定时删除如果filePattern的粒度为HH，那么在中如果age=30d则不生效**

#### 3. 修改application.yml配置

```yaml
logging:
    config: classpath:log4j2.xml
```



## 11. 异步请求，异步任务，定时任务

### 11.1 异步请求

#### 1. 何为异步请求

在`Servlet 3.0`之前，`Servlet`采用`Thread-Per-Request`的方式处理请求，**即每一次`Http`请求都由某一个线程从头到尾负责处理**。如果一个请求需要进行IO操作，比如访问数据库、调用第三方服务接口等，那么其所对应的线程将**同步地等待**IO操作完成， 而IO操作是**非常慢**的，所以此时的线程并不能及时地释放回线程池以供后续使用，在并发量越来越大的情况下，这将带来严重的性能问题。其请求流程大致为：

![正常请求](.\img\22906052.jpg)

而在`Servlet3.0`发布后，提供了一个新特性：**异步处理请求**。可以**先释放**容器分配给请求的线程与相关资源，减轻系统负担，释放了容器所分配线程的请求，其**响应将被延后**，可以在耗时处理完成（例如长时间的运算）时再对客户端进行响应。其请求流程为：

![异步请求](.\img\71641059.jpg)

在`Servlet 3.0`后，我们可以从`HttpServletRequest`对象中获得一个**AsyncContext**对象，该对象构成了异步处理的上下文，`Request`和`Response`对象都可从中获取。`AsyncContext`可以从当前线程传给另外的线程，并在新的线程中完成对请求的处理并返回结果给客户端，初始线程便可以还回给容器线程池以处理更多的请求。如此，通过将请求从一个线程传给另一个线程处理的过程便构成了`Servlet 3.0`中的异步处理。

**特点：**

- 可以先释放容器分配给请求的线程与相关资源，减轻系统负担，释放了容器所分配线程的请求，其响应将被延后，可以在耗时处理完成（例如长时间的运算）时再对客户端进行响应。一句话：**增加了服务器对客户端请求的吞吐量**（实际生产上我们用的比较少，如果并发请求量很大的情况下，我们会通过nginx把请求负载到集群服务的各个节点上来分摊请求压力，当然还可以通过消息队列来做请求的缓冲）。
- **异步请求只是提高了服务的吞吐量，提高单位时间内处理的请求数，并不会加快处理效率的**

> **实现异步请求的方式有原生异步请求（Servlet的`HttpServletRequest.getAsyncContext()`），Spring方式实现（Controller返回Callable，返回DeferredResult，返回WebAsyncTask）**

#### 2. Spring实现方式

在`Spring`中，有多种方式实现异步请求，比如`callable`、`DeferredResult`或者`WebAsyncTask`。每个的用法略有不同，可根据不同的业务场景选择不同的方式。以下主要介绍一些常用的用法

##### Callable方式

直接返回的参数包裹一层`callable`即可。**可以继承`WebMvcConfigurer`类来设置默认线程池和超时处理**

**用法**

```java
@ApiOperation("异步请求")
@GetMapping("/student")
public Callable getStudentAll() {
    System.out.println("外部线程1：" + Thread.currentThread().getName());

    Callable<AjaxResponse> callable = () -> {
        List<TableStudent> tableStudents = tableStudentService.selectAll();
        System.out.println("内部线程：" + Thread.currentThread().getName());
        return AjaxResponse.success(tableStudents);
    };
    System.out.println("外部线程2：" + Thread.currentThread().getName());
    return callable;
}
```

```
2018-08-15 23:32:22.317  INFO 15740 --- [nio-8080-exec-2] c.l.l.s.controller.SpringController      : 外部线程：http-nio-8080-exec-2
2018-08-15 23:32:22.323  INFO 15740 --- [      MvcAsync1] c.l.l.s.controller.SpringController      : 内部线程：MvcAsync1

```

**设置默认线程池和超时处理**

```java
/**
 * 异步请求配置
 * @author Jjcc
 * @version 1.0.0
 * @className RequestAsyncConfig.java
 * @createTime 2019年10月23日 22:14:00
 */
@Configuration
public class RequestAsyncConfig implements WebMvcConfigurer {


    /**
     * 配置线程池
     * @return
     */
    @Bean(name = "asyncPoolTaskExecutor")
    public ThreadPoolTaskExecutor getAsyncThreadPoolTaskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(20);
        taskExecutor.setMaxPoolSize(200);
        taskExecutor.setQueueCapacity(50);
        taskExecutor.setKeepAliveSeconds(200);
        taskExecutor.setThreadNamePrefix("callable-");
        // 线程池对拒绝任务（无线程可用）的处理策略，默认为CallerRunsPolicy
        taskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        taskExecutor.initialize();
        return taskExecutor;
    }

    @Override
    public void configureAsyncSupport(AsyncSupportConfigurer configurer) {
        //处理 callable超时
        configurer.setDefaultTimeout(6*1000);
        //线程池
        configurer.setTaskExecutor(getAsyncThreadPoolTaskExecutor());
        //超时处理策略
        configurer.registerCallableInterceptors(new RequestTimeCallableProcessor());
    }
    
    /**
     * 超时策略
     * @title timeoutCallableProcessingInterceptor
     * @author Jjcc 
     * @return com.jjcc.bootlaunch.config.async.RequestTimeCallableProcessor
     * @createTime 2019/10/24 10:22
     */
    @Bean
    public RequestTimeCallableProcessor timeoutCallableProcessingInterceptor() {
        return new RequestTimeCallableProcessor();
    }
}
```

**超时策略**

编写一个类实现`CallableProcessingInterceptor`类

这里返回的是一个自定义异常，在全局异常处理那里进行处理

```java
/**
 * 请求超时策略
 * @author Jjcc
 * @version 1.0.0
 * @className RequestTimeCallableProcessor.java
 * @createTime 2019年10月23日 23:25:00
 */
public class RequestTimeCallableProcessor implements CallableProcessingInterceptor {

    @Override
    public <T> Object handleTimeout(NativeWebRequest request, Callable<T> task) {
        HttpServletRequest nativeRequest = request.getNativeRequest(HttpServletRequest.class);
        String requestURI = nativeRequest.getRequestURI();

        return new CustomAsyncRequestTimeoutException(CustomExceptionType.REQUEST_TIME_OUT.getCode(), requestURI, "请求超时");
    }
}
```

##### DeferredResult

相比于`callable`，`DeferredResult`可以处理一些相对复杂一些的业务逻辑，最主要还是可以在另一个线程里面进行业务处理及返回，即可在两个完全不相干的线程间的通信。

```java
/**
     * 线程池
     */
    public static ExecutorService FIXED_THREAD_POOL = Executors.newFixedThreadPool(30);
    
@RequestMapping("/deferredresult")
    public DeferredResult<String> deferredResult(){
        log.info("外部线程：" + Thread.currentThread().getName());
        //设置超时时间
        DeferredResult<String> result = new DeferredResult<String>(60*1000L);
        //处理超时事件 采用委托机制
        result.onTimeout(new Runnable() {
            
            @Override
            public void run() {
                log.error("DeferredResult超时");
                result.setResult("超时了!");
            }
        });
        result.onCompletion(new Runnable() {
            
            @Override
            public void run() {
                //完成后
                log.info("调用完成");
            }
        });
        FIXED_THREAD_POOL.execute(new Runnable() {
            
            @Override
            public void run() {
                //处理业务逻辑
                log.info("内部线程：" + Thread.currentThread().getName());
                //返回结果
                result.setResult("DeferredResult!!");
            }
        });
        return result;
    }
```

```
2018-08-15 23:52:27.841  INFO 12984 --- [nio-8080-exec-2] c.l.l.s.controller.SpringController      : 外部线程：http-nio-8080-exec-2
2018-08-15 23:52:27.843  INFO 12984 --- [pool-1-thread-1] c.l.l.s.controller.SpringController      : 内部线程：pool-1-thread-1
2018-08-15 23:52:27.872  INFO 12984 --- [nio-8080-exec-2] c.l.l.s.controller.SpringController      : 调用完成
```

**注意：返回结果时记得调用下setResult方法。**

> **利用DeferredResult可实现一些长连接的功能，比如当某个操作是异步时，我们可以保存这个DeferredResult对象，当异步通知回来时，我们在找回这个DeferredResult对象，之后在setResult会结果即可。提高性能。**

##### WebAsyncTask

`WebAsyncTask`是直接返回

```java
@RequestMapping("/webAsyncTask")
   public WebAsyncTask<String> webAsyncTask() {
       log.info("外部线程：" + Thread.currentThread().getName());
       WebAsyncTask<String> result = new WebAsyncTask<String>(60*1000L, new Callable<String>() {

           @Override
           public String call() throws Exception {
               log.info("内部线程：" + Thread.currentThread().getName());
               return "WebAsyncTask!!!";
           }
       });
       result.onTimeout(new Callable<String>() {
           
           @Override
           public String call() throws Exception {
               // TODO Auto-generated method stub
               return "WebAsyncTask超时!!!";
           }
       });
       result.onCompletion(new Runnable() {
           
           @Override
           public void run() {
               //超时后 也会执行此方法
               log.info("WebAsyncTask执行结束");
           }
       });
       return result;
   }
```

```java
2018-08-15 23:55:02.568  INFO 2864 --- [nio-8080-exec-1] c.l.l.s.controller.SpringController      : 外部线程：http-nio-8080-exec-1
2018-08-15 23:55:02.587  INFO 2864 --- [          oKong-1] c.l.l.s.controller.SpringController      : 内部线程：oKong-1
2018-08-15 23:55:02.615  INFO 2864 --- [nio-8080-exec-2] c.l.l.s.controller.SpringController      : WebAsyncTask执行结束
```

### 11.2 异步任务

一般上我们都是`同步调用`，即：程序按定义的顺序依次执行的过程，每一行代码执行过程必须等待上一行代码执行完毕后才执行。而`异步调用`指：程序在执行时，无需等待执行的返回值可继续执行后面的代码。显而易见，同步有依赖相关性，而异步没有，所以异步可`并发`执行，可提高执行效率，在相同的时间做更多的事情。

> 处理`异步`、`同步`外，还有一个叫`回调`。其主要是解决异步方法执行结果的处理方法，比如在希望异步调用结束时返回执行结果，这个时候就可以考虑使用回调机制。
>

#### 1. @Async异步调用

##### 环境准备

**在 `Spring Boot` 入口类上配置 `@EnableAsync` 注解开启异步处理。**

创建任务抽象类 `AbstractTask`，并分别配置三个任务方法 `doTaskOne()`，`doTaskTwo()`，`doTaskThree()`。

```java
public abstract class AbstractTask {
    private static Random random = new Random();

    public void doTaskOne() throws Exception {
        System.out.println("开始做任务一");
        long start = currentTimeMillis();
        sleep(random.nextInt(10000));
        long end = currentTimeMillis();
        System.out.println("完成任务一，耗时：" + (end - start) + "毫秒");
    }

    public void doTaskTwo() throws Exception {
        System.out.println("开始做任务二");
        long start = currentTimeMillis();
        sleep(random.nextInt(10000));
        long end = currentTimeMillis();
        System.out.println("完成任务二，耗时：" + (end - start) + "毫秒");
    }

    public void doTaskThree() throws Exception {
        System.out.println("开始做任务三");
        long start = currentTimeMillis();
        sleep(random.nextInt(10000));
        long end = currentTimeMillis();
        System.out.println("完成任务三，耗时：" + (end - start) + "毫秒");
    }
}
```

##### 异步调用

创建 `AsyncTask`类，分别在方法上配置 `@Async` 注解，将原来的 **同步方法** 变为 **异步方法**。

```java
@Component
public class AsyncTask extends AbstractTask {
    @Async
    public void doTaskOne() throws Exception {
        super.doTaskOne();
    }

    @Async
    public void doTaskTwo() throws Exception {
        super.doTaskTwo();
    }

    @Async
    public void doTaskThree() throws Exception {
        super.doTaskThree();
    }
}
```

在 **单元测试** 用例中，注入 `AsyncTask` 对象，并在测试用例中执行 `doTaskOne()`，`doTaskTwo()`，`doTaskThree()` 三个方法。

```java
@Autowired
private AsyncTask asyncTask;

@Test
public void testAsyncTasks() throws Exception {
    asyncTask.doTaskOne();
    asyncTask.doTaskTwo();
    asyncTask.doTaskThree();
}
```

```
开始做任务三
开始做任务一
开始做任务二
```

如果反复执行单元测试，可能会遇到各种不同的结果，比如：

1. 没有任何任务相关的输出
2. 有部分任务相关的输出
3. 乱序的任务相关的输出

原因是目前 `doTaskOne()`，`doTaskTwo()`，`doTaskThree()` 这三个方法已经 **异步执行** 了。主程序在 **异步调用** 之后，主程序并不会理会这三个函数是否执行完成了，由于没有其他需要执行的内容，所以程序就 **自动结束** 了，导致了 **不完整** 或是 **没有输出任务** 相关内容的情况。

**几种导致@Async异步方法失效的情况**

- 调用的是静态(`static` )方法
- 调用(`private`)私有化方法
- **调用同一个类下注有@Async异步方法**：在spring中像@Async和@Transactional、cache等注解本质使用的是动态代理，其实**Spring容器在初始化的时候Spring容器会将含有AOP注解的类对象“替换”为代理对象**（简单这么理解），那么注解失效的原因就很明显了，就是因为调用方法的是对象本身而不是代理对象，因为没有经过Spring容器，那么解决方法也会沿着这个思路来解决。

**解决办法**

1. **将要异步执行的方法单独抽取成一个类**，原理就是当你把执行异步的方法单独抽取成一个类的时候，这个类肯定是被Spring管理的，其他Spring组件需要调用的时候肯定会注入进去，这时候实际上注入进去的就是代理类了。
2. 其实我们的注入对象都是从Spring容器中给当前Spring组件进行成员变量的赋值，由于某些类使用了AOP注解，那么实际上在Spring容器中实际存在的是它的代理对象。那么我们就可以**通过上下文获取自己的代理对象调用异步方法。**

```java
@Controller
@RequestMapping("/app")
public class EmailController {

    //获取ApplicationContext对象方式有多种,这种最简单,其它的大家自行了解一下
    @Autowired
    private ApplicationContext applicationContext;

    @RequestMapping(value = "/email/asyncCall", method = GET)
    @ResponseBody
    public Map<String, Object> asyncCall () {
        Map<String, Object> resMap = new HashMap<String, Object>();
        try{
            //这样调用同类下的异步方法是不起作用的
            //this.testAsyncTask();
            //通过上下文获取自己的代理对象调用异步方法
            EmailController emailController = (EmailController)applicationContext.getBean(EmailController.class);
            emailController.testAsyncTask();
            resMap.put("code",200);
        }catch (Exception e) {
            resMap.put("code",400);
            logger.error("error!",e);
        }
        return resMap;
    }

    //注意一定是public,且是非static方法
    @Async
    public void testAsyncTask() throws InterruptedException {
        Thread.sleep(10000);
        System.out.println("异步任务执行完成！");
    }
}
```

##### 异步回调

> 为了让 `doTaskOne()`，`doTaskTwo()`，`doTaskThree()` 能正常结束，假设我们需要统计一下三个任务 **并发执行** 共耗时多少，这就需要等到上述三个函数都完成动用之后记录时间，并计算结果。

那么我们如何判断上述三个 **异步调用** 是否已经执行完成呢？我们需要使用 `Future<T>` 来返回 **异步调用** 的 **结果**。

```java
@Async("asyncPoolTaskExecutor")
public Future<String> asyncEvent() throws InterruptedException {
    //休眠1s
    Thread.sleep(1000);
    log.info("异步方法内部线程名称：{}!", Thread.currentThread().getName());
    return new AsyncResult<>("异步方法返回值");
}
```

**其中`AsyncResult`是`Spring`提供的一个`Future`接口的子类。**

在 **单元测试** 用例中，注入 `AsyncCallBackTask` 对象，并在测试用例中执行 `doTaskOneCallback()`，`doTaskTwoCallback()`，`doTaskThreeCallback()` 三个方法。循环调用 `Future` 的 `isDone()` 方法等待三个 **并发任务** 执行完成，记录最终执行时间。

```java
@Autowired
private AsyncCallBackTask asyncCallBackTask;

@Test
public void testAsyncCallbackTask() throws Exception {
    long start = currentTimeMillis();
    Future<String> task1 = asyncCallBackTask.doTaskOneCallback();
    Future<String> task2 = asyncCallBackTask.doTaskTwoCallback();
    Future<String> task3 = asyncCallBackTask.doTaskThreeCallback();

    // 三个任务都调用完成，退出循环等待
    while (!task1.isDone() || !task2.isDone() || !task3.isDone()) {
        sleep(1000);
    }

    long end = currentTimeMillis();
    System.out.println("任务全部完成，总耗时：" + (end - start) + "毫秒");
}
```

- 在测试用例一开始记录开始时间；
- 在调用三个异步函数的时候，返回Future类型的结果对象；
- 在调用完三个异步函数之后，开启一个循环，根据返回的Future对象来判断三个异步函数是否都结束了。若都结束，就结束循环；若没有都结束，就等1秒后再判断。
- 跳出循环之后，根据结束时间 - 开始时间，计算出三个任务并发执行的总耗时。

```
开始做任务三
开始做任务一
开始做任务二
完成任务二，耗时：2572毫秒
完成任务一，耗时：7333毫秒
完成任务三，耗时：7647毫秒
任务全部完成，总耗时：8013毫秒
```

可以看到，通过 **异步调用**，让任务一、任务二、任务三 **并发执行**，有效的 **减少** 了程序的 **运行总时间**。

##### 超时处理

> 对于一些需要异步回调的函数，不能无期限的等待下去，所以一般上需要设置超时时间，超时后可将线程释放，而不至于一直堵塞而占用资源。

对于`Future`配置超时，很简单，通过`get`方法即可，具体如下：

```java
//get方法会一直堵塞，直到等待执行完成才返回
//get(long timeout, TimeUnit unit) 在设置时间类未返回结果，会直接抛除异常TimeoutException，messages为null。就不需要使用while来判断任务是否完成了。
String result = doFutrue.get(60, TimeUnit.SECONDS);//60s
```

超时后，会抛出异常`TimeoutException`类，此时可进行统一异常捕获即可。

<img src=".\img\35438012.jpg" alt="超时异常" style="zoom:150%;" />

### 11.3 线程池

> 系统使用的是默认的`SimpleAsyncTaskExecutor`进行线程创建。所以一般上我们会自定义线程池来进行线程的复用。**默认的线程池不会销毁指定时间内未使用的线程**

#### 1. 线程池的作用

1. 防止资源占用无限的扩张
2. 调用过程省去资源的创建和销毁所占用的时间

#### 2. 定义线程池

创建一个 **线程池配置类**`TaskConfiguration` ，并配置一个 **任务线程池对象**`taskExecutor`。（*这里异步调用与异步请求用的同一个线程池*）

```java
    /**
     * 配置线程池
     * @return
     */
    @Bean(name = "asyncPoolTaskExecutor")
    public ThreadPoolTaskExecutor getAsyncThreadPoolTaskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(20);
        taskExecutor.setMaxPoolSize(200);
        taskExecutor.setQueueCapacity(50);
        taskExecutor.setKeepAliveSeconds(200);
        taskExecutor.setThreadNamePrefix("callable-");
        // 线程池对拒绝任务（无线程可用）的处理策略，默认为CallerRunsPolicy
        taskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        taskExecutor.initialize();
        return taskExecutor;
    }
```

**参数简介**

|         线程池属性         |                          属性的作用                          |    设置初始值    |
| :------------------------: | :----------------------------------------------------------: | :--------------: |
|         核心线程数         |   线程池创建时候初始化的线程数（线程池维护线程的最少数量）   |        10        |
|         最大线程数         | 线程池最大的线程数，只有在缓冲队列满了之后，才会申请超过核心线程数的线程 |        20        |
|          缓冲队列          |                    用来缓冲执行任务的队列                    |       200        |
|     允许线程的空闲时间     |    当超过了核心线程之外的线程，在空闲时间到达之后会被销毁    |       60秒       |
|       线程池名的前缀       |               可以用于定位处理任务所在的线程池               |  taskExecutor-   |
| 线程池对拒绝任务的处理策略 | 线程池对拒绝任务（无线程可用）的处理策略。这里采用了`CallerRunsPolicy`策略，当线程池没有处理能力的时候，该策略会直接在 execute 方法的调用线程中运行被拒绝的任务；如果执行程序已关闭，则会丢弃该任务。还有一个是`AbortPolicy`策略：处理程序遭到拒绝将抛出运行时`RejectedExecutionException`。 | CallerRunsPolicy |

使用的时候就只需要在`@Async`加入线程池名称即可

```java
@Async("asyncPoolTaskExecutor")
public void asyncEvent() throws InterruptedException {
    //休眠1s
    Thread.sleep(1000);
    log.info("异步方法内部线程名称：{}!", Thread.currentThread().getName());
}
```

#### 3. 优雅地关闭线程池

> 由于在应用关闭的时候异步任务还在执行，导致类似 **数据库连接池** 这样的对象一并被 **销毁了**，当 **异步任务** 中对 **数据库** 进行操作就会出错。可以通过简单的配置，进行`优雅的停机`策略配置。关键就是通过`setWaitForTasksToCompleteOnShutdown(true)`和`setAwaitTerminationSeconds(time)`方法。

- **setWaitForTasksToCompleteOnShutdown(true):** 该方法用来设置 **线程池关闭** 的时候 **等待** 所有任务都完成后，再继续 **销毁** 其他的 `Bean`，这样这些 **异步任务** 的 **销毁** 就会先于 **数据库连接池对象** 的销毁。
- **setAwaitTerminationSeconds(60):** 该方法用来设置线程池中 **任务的等待时间**，如果超过这个时间还没有销毁就 **强制销毁**，以确保应用最后能够被关闭，而不是阻塞住。

```java
/**
     * 配置线程池
     * @return
     */
    @Bean(name = "asyncPoolTaskExecutor")
    public ThreadPoolTaskExecutor getAsyncThreadPoolTaskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(20);
        taskExecutor.setMaxPoolSize(200);
        taskExecutor.setQueueCapacity(50);
        taskExecutor.setKeepAliveSeconds(200);
        taskExecutor.setThreadNamePrefix("callable-");
        // 线程池对拒绝任务（无线程可用）的处理策略，默认为CallerRunsPolicy
        taskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        //用以设置线程池关闭的时候是否等待队列中的任务执行完成在关闭线程池。
        //true为等待，false为不等待。默认为false
        taskExecutor.setWaitForTasksToCompleteOnShutdown(true);
        //当waitForToCompleteOnShutdown为true时，设置等待任务完成的时间，超过这个时间队列中的任务还没有完成，就销毁线程池；
        taskExecutor.setAwaitTerminationSeconds(60);

        taskExecutor.initialize();
        return taskExecutor;
    }
```

### 11.4 定时任务

#### 1. 通过@Scheduled实现定时任务

##### 开启定时任务方法

`Scheduled`定时任务是`Springboot`自身提供的功能（`SpingTask`实现定时任务），不需要引入maven依赖包；

在项目入口`main`方法上加入注解即可，使用`@Scheduled`注解即可轻松搞定。

```java
@SpringBootApplication
//开启定时任务
@EnableScheduling
@Slf4j
public class Chapter22Application {

    public static void main(String[] args) {
        SpringApplication.run(Chapter22Application.class, args);
        log.info("Chapter22启动!");
    }
}
```

```java
@Component
@Slf4j
public class ScheduledTask {

    /**
     * 自动扫描，启动时间点之后5秒执行一次
     */
    @Scheduled(fixedRate=5000)
    public void getCurrentDate() {
        log.info("Scheduled定时任务执行：" + new Date());
    }
}
```

##### 不同定时方式的解析

> `@Scheduled`注解中参数可以设置定时任务执行的频率

###### fixedDelay和fixedRate，单位是毫秒

`fixedRate`就是每隔多长时间执行一次。(开始------->X时间------>再开始)。**如果间隔时间小于任务执行完成的时间，上一次任务执行完成下一次任务就立即执行**。如果间隔时间大于任务执行时间，就按照每隔X时间运行一次。

`fixedDelay`是当任务**执行完毕后一段时间再次执行**。（开始--->结束(隔一分钟)开始----->结束）。上一次执行任务未完成，下一次任务不会开始。

###### cron表达式

| 表达式         | 说明                                                        |
| :------------- | :---------------------------------------------------------- |
| 0 0 3 * * ?    | 每天3点执行                                                 |
| 0 5 3 * * ?    | 每天3点5分执行                                              |
| 0 5 3 ? * *    | 每天3点5分执行，与上面作用相同                              |
| 0 5/10 3 * * ? | 每天3点的 5分，15分，25分，35分，45分，55分这几个时间点执行 |
| 0 10 3 ? * 1   | 每周星期天，3点10分 执行，注：1表示星期天                   |
| 0 10 3 ? * 1#3 | 每个月的第三个星期，星期天 执行，#号只能出现在星期的位置    |

- 第一位，表示秒，取值0-59
- 第二位，表示分，取值0-59
- 第三位，表示小时，取值0-23
- 第四位，日期天/日，取值1-31
- 第五位，日期月份，取值1-12
- 第六位，星期，取值1-7，星期一，星期二...，注：不是第1周，第二周的意思，另外：1表示星期天，2表示星期一。
- 第七位，年份，可以留空，取值1970-2099

**cron中，还有一些特殊的符号，含义如下：**

(*)星号：可以理解为每的意思，每秒，每分，每天，每月，每年...*

*(?)问号：问号只能出现在日期和星期这两个位置。*

*(-)减号：表达一个范围，如在小时字段中使用“10-12”，则表示从10到12点，即10,11,12*

*(,)逗号：表达一个列表值，如在星期字段中使用“1,2,4”，则表示星期一，星期二，星期四*

*(/)斜杠：如：x/y，x是开始值，y是步长，比如在第一位（秒） 0/15就是，从0秒开始，每15秒，最后就是0，15，30，45，60 另：*/y，等同于0/y

*(#)：如：1#3，1是星期的哪一天，3是每个月的第几周*

##### 实现定时任务

```java
/**
 * 定时任务类
 * @author Jjcc
 * @version 1.0.0
 * @className ScheduledJobs.java
 * @createTime 2019年10月27日 21:16:00
 */
@Slf4j
@Component
public class ScheduledJobs {

    /**
     * 表示方法执行完成后5秒再执行
     * @title fixedDelayJob
     * @author Jjcc
     * @return void
     * @createTime 2019/10/27 21:18
     */
    @Scheduled(fixedDelay = 5000)
    public void fixedDelayJob() {
        String name = Thread.currentThread().getName();
        log.info(name + "：fixedDelayJob定时任务执行");
        log.info("fixedDelayJob--------------------");
    }

    /**
     * 表示方法每隔5秒执行一次；当指定的时间内任务没有执行完成，任务执行完成后会再次执行
     * @title fixedRateJob
     * @author Jjcc
     * @return void
     * @createTime 2019/10/27 21:20
     */
    @Scheduled(fixedRate = 5000)
    public void  fixedRateJob() {
        String name = Thread.currentThread().getName();
        log.info(name + "：fixedRateJob定时任务执行");
        log.info("fixedRateJob--------------------");
    }

    /**
     * 表示每隔20秒执行一次
     * @title cronJob
     * @author Jjcc
     * @return void
     * @createTime 2019/10/27 21:24
     */
    @Scheduled(cron = "0/20 * * * * ?")
    public void cronJob() {
        String name = Thread.currentThread().getName();
        log.info(name + "：cronJob定时任务执行");
        log.info("cronJob--------------------");
    }
}
```

##### 配置SpringTask定时任务的线程池

```java
/**
 * SpringTask定时任务配置类
 * @author Jjcc
 * @version 1.0.0
 * @className ScheduledAsyncConfig.java
 * @createTime 2019年10月27日 21:33:00
 */
@Configuration
public class ScheduledAsyncConfig implements SchedulingConfigurer {

    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
        //给定时任务添加线程池
        taskRegistrar.setScheduler(scheduledTaskExecutor());
    }

    /**
     * 线程配置类
     * @title scheduledTaskExecutor
     * @author Jjcc
     * @return org.springframework.scheduling.TaskScheduler
     * @createTime 2019/10/27 22:02
     */
    @Bean
    public TaskScheduler scheduledTaskExecutor() {
        ThreadPoolTaskScheduler threadPoolTaskExecutor = new ThreadPoolTaskScheduler();
        threadPoolTaskExecutor.setPoolSize(20);
        threadPoolTaskExecutor.setThreadNamePrefix("oKong-Executor-");
        threadPoolTaskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        //调度器shutdown被调用时等待当前被调度的任务完成
        threadPoolTaskExecutor.setWaitForTasksToCompleteOnShutdown(true);
        //等待时长
        threadPoolTaskExecutor.setAwaitTerminationSeconds(60);
        return threadPoolTaskExecutor;
    }
}
```

**打印结果：**

```
2019-10-27 22:14:00.001  INFO 1300 --- [ong-Executor-18] c.j.bootlaunch.scheduled.ScheduledJobs   : oKong-Executor-18：cronJob定时任务执行
2019-10-27 22:14:00.001  INFO 1300 --- [ong-Executor-18] c.j.bootlaunch.scheduled.ScheduledJobs   : cronJob--------------------
2019-10-27 22:14:01.976  INFO 1300 --- [Kong-Executor-5] c.j.bootlaunch.scheduled.ScheduledJobs   : oKong-Executor-5：fixedRateJob定时任务执行
2019-10-27 22:14:01.976  INFO 1300 --- [Kong-Executor-5] c.j.bootlaunch.scheduled.ScheduledJobs   : fixedRateJob--------------------
2019-10-27 22:14:02.091  INFO 1300 --- [ong-Executor-19] c.j.bootlaunch.scheduled.ScheduledJobs   : oKong-Executor-19：fixedDelayJob定时任务执行
2019-10-27 22:14:02.091  INFO 1300 --- [ong-Executor-19] c.j.bootlaunch.scheduled.ScheduledJobs   : fixedDelayJob--------------------
2019-10-27 22:14:06.976  INFO 1300 --- [ong-Executor-10] c.j.bootlaunch.scheduled.ScheduledJobs   : oKong-Executor-10：fixedRateJob定时任务执行
2019-10-27 22:14:06.976  INFO 1300 --- [ong-Executor-10] c.j.bootlaunch.scheduled.ScheduledJobs   : fixedRateJob--------------------
2019-10-27 22:14:07.093  INFO 1300 --- [ong-Executor-20] c.j.bootlaunch.scheduled.ScheduledJobs   : oKong-Executor-20：fixedDelayJob定时任务执行
2019-10-27 22:14:07.093  INFO 1300 --- [ong-Executor-20] c.j.bootlaunch.scheduled.ScheduledJobs   : fixedDelayJob--------------------
```

#### 2. quartz

**核心结构**

**job**（或task）是实现定时功能的接口：如定时推送消息等。

**trigger**是触发执行定时任务的触发器，基本设置有：指定job的运行时间，时间间隔，运行次数等。

**schedule**是调度指定的trigger去执行指定的job。

![在这里插入图片描述](.\img\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI5MTE5NTgx,size_16,color_FFFFFF,t_70)

##### 任务类

这里定义了两个任务类，一个是simpleTrigger，一个是CronTrigger；

**simpleTrigger**

```java
/**
 * quartz定时任务；任务类
 * @author Jjcc
 * @version 1.0.0
 * @className QuartzSimpleTask.java
 * @createTime 2019年10月30日 10:02:00
 */
@Slf4j
public class QuartzSimpleTask extends QuartzJobBean {

    @Override
    protected void executeInternal(JobExecutionContext jobExecutionContext) {
        log.info("quartz简单的定时任务：" + LocalDateTime.now());
    }
}
```

**CronTrigger**

```java
/**
 * quartz的任务类
 * @author Jjcc
 * @version 1.0.0
 * @className QuartzCronTask.java
 * @createTime 2019年10月30日 21:39:00
 */
@Slf4j
public class QuartzCronTask extends QuartzJobBean {

    @Override
    protected void executeInternal(JobExecutionContext jobExecutionContext) {
        log.info("quartz的cron任务: " + LocalDateTime.now());
    }
}
```

##### 创建Quartz定时配置类

创建一个`QuartzConfig`类，并添加`@Configuration`注解

在配置类中定义`JobDetail`和`SimpleTrigger`。

`simpleTaskTrigger`触发器使用的是固定触发器，`cronTaskTrigger`使用的是cron表达式方式。

```java
/**
 * quartz定时任务配置配
 * @author Jjcc
 * @version 1.0.0
 * @className QuartzConfig.java
 * @createTime 2019年10月30日 10:19:00
 */
@Slf4j
@Configuration
public class QuartzConfig {

    /**
     * JobDetail指定任务类
     * @title testQuartz1
     * @author Jjcc
     * @return org.quartz.JobDetail
     * @createTime 2019/10/30 11:31
     */
    @Bean
    public JobDetail simpleTaskJobDetail() {
        return JobBuilder.newJob(QuartzSimpleTask.class).withIdentity("simpleTaskJobDetail").storeDurably().build();
    }

    /**
     * Simple触发器定义与设置
     * 一个trigger只对应一个job，schedule调度trigger执行对应的job
     * @title simpleTaskTrigger
     * @author Jjcc
     * @return org.quartz.Trigger
     * @createTime 2019/10/30 11:40
     */
    @Bean
    public Trigger simpleTaskTrigger() {
        //Simple类型：可设置时间间隔、是否重复、触发频率（misfire机制）等
        //每隔5秒执行一次
        SimpleScheduleBuilder simpleScheduleBuilder = SimpleScheduleBuilder.simpleSchedule().withIntervalInSeconds(5).repeatForever();

        return TriggerBuilder.newTrigger().forJob(simpleTaskJobDetail()).
                withIdentity("simpleTaskTrigger").withSchedule(simpleScheduleBuilder).build();
    }

    /**
     * JobDetail指定工作类
     * @title cronTaskJobDetail
     * @author Jjcc
     * @return org.quartz.JobDetail
     * @createTime 2019/10/30 21:42
     */
    @Bean
    public JobDetail cronTaskJobDetail() {
        return JobBuilder.newJob(QuartzCronTask.class).withIdentity("cronTaskJobDetail").storeDurably().build();
    }

    /**
     * cron触发器的定义与配置
     * @title cronTaskTrigger
     * @author Jjcc
     * @return org.quartz.Trigger
     * @createTime 2019/10/30 21:53
     */
    @Bean
    public Trigger cronTaskTrigger() {
        //cron类型的触发器
        CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule("0/5 * * * * ?").withMisfireHandlingInstructionDoNothing();

        return TriggerBuilder.newTrigger().forJob(cronTaskJobDetail())
                .withIdentity("cronTaskTrigger")
                .withSchedule(cronScheduleBuilder)
                .withDescription("corn类型的触发器")
                .startNow()
                .build();
    }
}
```



## 12. redis缓存

### 12.1 redis数据结构与应用场景

#### 1. 简介

Redis是开源免费的，key-value内存数据库，主要解决高并发，大数据场景下，热点数据访问的性能问题，提供高性能的数据快速访问。项目中部分数据访问比较频繁，对下游DB造成服务压力，这时候可以使用缓存来提高效率。

Redis的主要特点包括：

- Redis数据存储在内存中，可以提高热点数据的访问效率
- Redis处理支持key-value类型的数据，同时还支持其他多种数据结构
- Redis支持数据持久化，可以将数据存储存储在硬盘中，机器重启数据将从磁盘重新加载数据

 Redis 作为缓存数据库和 MySQL 这种结构化数据库进行对比。 

- 从数据库类型上，Redis是NoSQL半结构化缓存数据库，Mysql是结构化关系型数据库；
- 从读写性能上，Mysql是持久化硬盘存储，读写速度较慢，Redis数据存储读取在内存， 同时也可以持久化到磁盘，读写速度较快； 
- 从使用场景上，Redis一般作为Mysql数据读取性能优化的优化选型，彼此配合使用。

#### 2. Redis 基本数据结构与实战场景

<img src=".\img\89a963bb66e26e44fbcdf0501993a544_918x823.png" alt="img" style="zoom:150%;" /> 

1. redis的数据结构可以理解为`Map`
2. 字 符串类型的数据结构可以理解为`Map<String,String>` 
3. list类型的数据结构可以理解为`Map<String, List<String>>`
4. set类型的数据结构可以理解为`Map<String, Set<String>>`
5. hash类型的数据结构可以理解为`Map<String, Map<String, String>>`
6. zset类型的数据结构可以理解为`Map<String, Map<value, score>>`

#### 3. 场景解析

##### String类型场景使用

 **场景一：商品库存数** 

 从业务上，商品库存数据是热点数据，交易行为会直接影响库存。而 Redis 自身 String 类型提供了： 

```sh
incr key && decr key && incrby key increment && decrby key decrement
```

 set goods_id 10; 设置 id 为 good_id 的商品的库存初始值为 10；
decr goods_id; 当商品被购买时候，库存数据减 1。
依次类推的场景：商品的浏览次数，问题或者回复的点赞次数等。这种计数的场景都可以考虑利用 Redis 来实现。 

 **场景二：时效信息存储** 

Redis 的数据存储具有自动失效能力。也就是存储的 key-value 可以设置过期时间：set(key, value, expireTime)。

比如，用户登录某个 App 需要获取登录验证码， 验证码在 30 秒内有效。那么我们就可以使用 String 类型存储验证码，同时设置 30 秒的失效时间。

```java
keys = redisCli.get(key);
     if(keys != null)
    {
        return false;
    }
    else
    {
        sendMsg()
        redisCli.set(keys,value,expireTime)
    }
```

##### List 类型使用场景

 list 是按照插入顺序排序的字符串链表。可以在头部和尾部插入新的元素（双向链表实现，两端添加元素的时间复杂度为 O(1)） 。 

 **场景一：消息队列实现** 

目前有很多专业的消息队列组件 `Kafka`、`RabbitMQ` 等。 我们在这里仅仅是使用 list 的特征来实现消息队列的要求。在实际技术选型的过程中，大家可以慎重思考。

list 存储就是一个队列的存储形式：

`lpush key value`; 在 key 对应 list 的头部添加字符串元素；
`rpop key`;移除列表的最后一个元素，返回值为移除的元素。

 **场景二：最新上架商品** 

在交易网站首页经常会有新上架产品推荐的模块， 这个模块是存储了最新上架前 100 名。

这时候使用 Redis 的 list 数据结构，来进行 TOP 100 新上架产品的存储。

Redis ltrim 指令对一个列表进行修剪（`trim`），这样 list 就会只包含指定范围的指定元素。

```sh
ltrim key start stop
```

start 和 stop 都是由 0 开始计数的，这里的 0 是列表里的第一个元素（表头），1 是第二个元素。

如下伪代码演示：

```
//把新上架商品添加到链表里
ret = r.lpush(“new:goods", goodsId)
//保持链表 100 位
ret = r.ltrim("new:goods", 0, 99)
//获得前 100 个最新上架的商品 id 列表
newest_goods_list = r.lrange("new:goods", 0, 99)
```

##### set 类型使用场景

set 也是存储了一个集合列表功能。和 list 不同，set 具备去重功能。当需要存储一个列表信息，同时要求列表内的元素不能有重复，这时候使用 set 比较合适。与此同时，set 还提供的交集、并集、差集。

例如，在交易网站，我们会存储用户感兴趣的商品信息，在进行相似用户分析的时候， 可以通过计算两个不同用户之间感兴趣商品的数量来提供一些依据。

```sh
    # userid 为用户 ID ， goodID 为感兴趣的商品信息。 
    sadd “user:userId” goodID； 

    sadd “user:101”, 1
    sadd “user:101”, 2
    sadd “user:102”, 1
    Sadd “user:102”, 3

    sinter “user:101” “user:101”
```

获取到两个用户相似的产品， 然后确定相似产品的类目就可以进行用户分析。

类似的应用场景还有， 社交场景下共同关注好友， 相似兴趣 tag 等场景的支持。

##### Hash 类型使用场景

Redis 在存储对象（例如：用户信息）的时候需要对对象进行序列化转换然后存储。

还有一种形式，就是将对象数据转换为 JSON 结构数据，然后存储 JSON 的字符串到 Redis。

对于一些对象类型，还有一种比较方便的类型，那就是按照 Redis 的 Hash 类型进行存储。

```sh
hset key field value
```

 例如，我们存储一些网站用户的基本信息， 我们可以使用： 

```sh
 hset user101 name “小明”
    hset user101 phone “123456”
    hset user101 sex “男”
```

这样就存储了一个用户基本信息，存储信息有：{name : 小明， phone : “123456”，sex : “男”}

当然这种类似场景还非常多， 比如存储订单的数据，产品的数据，商家基本信息等。大家可以参考来进行存储选型。

##### Sorted Set 类型使用场景

Redis sorted set 的使用场景与 set 类似，区别是 set 不是自动有序的，而 sorted set 可以通过提供一个 score 参数来为存储数据排序，并且是自动排序，插入既有序。

业务中如果需要一个有序且不重复的集合列表，就可以选择 sorted set 这种数据结构。

比如，商品的购买热度可以将购买总量 num 当做商品列表的 score，这样获取最热门的商品时就是可以自动按售卖总量排好序。

sorted set 适合有排序需求的集合存储场景。 大家可以思考一下自己负责的业务服务是否有可以使用的场景。

### 12.2 使用redisTemplate操作数据

#### 1. spring-data-redis简介

Spring Boot 提供了对 Redis 集成的组件包：spring-boot-starter-data-redis，它依赖于 spring-data-redis 和 lettuce。Spring Boot 1.0 默认使用的是 Jedis 客户端，2.0 替换成了 Lettuce，但如果你从 Spring Boot 1.5.X 切换过来，几乎感受不大差异，这是因为 spring-boot-starter-data-redis 为我们隔离了其中的差异性。 

1. Lettuce：**是一个可伸缩线程安全的 Redis 客户端，多个线程可以共享同一个 RedisConnection，它利用优秀 Netty NIO 框架来高效地管理多个连接。**
2. Spring Data：是 Spring 框架中的一个主要项目，目的是为了简化构建基于 Spring 框架应用的数据访问，包括非关系数据库、Map-Reduce 框架、云数据服务等，另外也包含对关系数据库的访问支持。
3. Spring Data Redis：是 Spring Data 项目中的一个主要模块，实现了对 Redis 客户端 API 的高度封装，使对 Redis 的操作更加便捷。

#### 2. 整合spring data redis

 **引入依赖包** 

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

 引入 commons-pool 2 是因为 Lettuce 需要使用 commons-pool 2 创建 Redis 连接池。 

**application 配置**

```yml
spring:
  redis:
    database: 0 # Redis 数据库索引（默认为 0）
    host: 192.168.161.3 # Redis 服务器地址
    port: 6379 # Redis 服务器连接端口
    password: 123456 # Redis 服务器连接密码（默认为空）
    lettuce:
      pool:
        max-active: 8 # 连接池最大连接数（使用负值表示没有限制） 默认 8
        max-wait: -1 # 连接池最大阻塞等待时间（使用负值表示没有限制） 默认 -1
        max-idle: 8 # 连接池中的最大空闲连接 默认 8
        min-idle: 0 # 连接池中的最小空闲连接 默认 0
```

#### 3. redis模板封装类

##### redis模板封装类

```java
redisTemplate.opsForValue();//操作字符串
redisTemplate.opsForHash();//操作 hash
redisTemplate.opsForList();//操作 list
redisTemplate.opsForSet();//操作 set
redisTemplate.opsForZSet();//操作有序 set
```

StringRedisTemplate 与 RedisTemplate 的封装的 Reids 操作要比我们第二节讲的自己调用 Jedis 的 API 的方式更优雅了一步。

##### redis模板封装类的两种注入方式

```java
public class Example {

  // 注入RedisTemplate，更通用
  @Autowired
  private RedisTemplate<String, String> template;

  // 如：注入RedisTemplate的子类ListOperations，只能操作List
  @Resource
  private ListOperations<String, String> listOps;

}
```

#### 4. 代码测试

```java
@Data
public class Person implements Serializable {

  private static final long serialVersionUID = -8985545025228238754L;

  String id;
  String firstname;
  String lastname;
  Address address;

  public Person(String firstname, String lastname) {
    this.firstname = firstname;
    this.lastname = lastname;
  }
}
```

```java
@Data
public class Address implements Serializable {

  private static final long serialVersionUID = -8985545025228238771L;

  String city;
  String country;

  public Address(String city, String country) {
    this.city = city;
    this.country = country;
  }
}
```

**使用redisTemplate**

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class RedisConfigTest {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Autowired
    private RedisTemplate redisTemplate;

    @Resource(name = "redisTemplate")
    private ValueOperations<String,Object> valueOperations;

    @Resource(name = "redisTemplate")
    private HashOperations<String, String, Object> hashOperations;

    @Resource(name = "redisTemplate")
    private ListOperations<String, Object> listOperations;

    @Resource(name = "redisTemplate")
    private SetOperations<String, Object> setOperations;

    @Resource(name = "redisTemplate")
    private ZSetOperations<String, Object> zSetOperations;

    @Test
    public void testValueObj() throws Exception{
        Person person = new Person("boke","byrant");
        person.setAddress(new Address("南京","中国"));
        //ValueOperations<String,Object> operations = redisTemplate.opsForValue();
        valueOperations.set("player:1",person,20,TimeUnit.SECONDS); //10秒之后数据消失

        Person getBack = (Person)valueOperations.get("player:1");
        System.out.println(getBack);
    }

    @Test
    public void testSetOperation() throws Exception{
        Person person = new Person("boke","byrant");
        Person person2 = new Person("curry","stephen");
        setOperations.add("playerset",person,person2);
        Set<Object> result = setOperations.members("playerset");
        System.out.println(result);
    }

    @Test
    public void HashOperations() throws Exception{
        Person person = new Person("boke","byrant");
        hashOperations.put("hash:player","firstname",person.getFirstname());
        hashOperations.put("hash:player","lastname",person.getLastname());
        hashOperations.put("hash:player","address",person.getAddress());
        System.out.println(hashOperations.get("hash:player","firstname"));
    }

    @Test
    public void  ListOperations() throws Exception{

        listOperations.leftPush("list:player",new Person("boke","byrant"));
        listOperations.leftPush("list:player",new Person("Jordan","Mikel"));
        listOperations.leftPush("list:player",new Person("curry","stephen"));

        System.out.println(listOperations.leftPop("list:player"));
    }
}
```

更多的RedisTemplate方法可以，可以参考：

 https://blog.csdn.net/weixin_43835717/article/details/92802040 ；

 https://www.jianshu.com/p/7bf5dc61ca06 ；

#### 5. 序列化

`RedisTemplate`默认使用的是`JdkSerializationRedisSerializer`，`StringRedisTemplate`默认使用的是`StringRedisSerializer`。

Spring Data JPA为我们提供了下面的Serializer：GenericToStringSerializer、Jackson2JsonRedisSerializer、JacksonJsonRedisSerializer、JdkSerializationRedisSerializer、OxmSerializer、StringRedisSerializer。

<img src=".\img\214872389571.png" alt="Spring Data Redis 调用" style="zoom:120%;" /> 

 主要分成四类： 

- JDK 序列化方式
- String 序列化方式
- JSON 序列化方式
- XML 序列化方式

##### JDK序列化方式

 [`org.springframework.data.redis.serializer.JdkSerializationRedisSerializer`](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/serializer/JdkSerializationRedisSerializer.java) ，**默认情况下，RedisTemplate 使用该数据列化方式**。具体的，可以看看 [`RedisTemplate.afterPropertiesSet()`](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/core/RedisTemplate.java) 方法，在 RedisTemplate 未设置序列化的情况下，使用 JdkSerializationRedisSerializer 作为序列化实现。在 Spring Boot 自动化配置 RedisTemplate Bean 对象时，就未设置。 

使用JDK提供的序列化功能。 优点是反序列化时不需要提供类型信息(class)，但缺点是需要实现Serializable接口，还有序列化后的结果非常庞大，是JSON格式的5倍左右，这样就会消耗redis服务器的大量内存。而且是以二进制形式保存，自然人无法理解。 

```sh
`# 在 `redis-cli` 终端中127.0.0.1:6379> scan 01) "0"2) 1) "\xac\xed\x00\x05t\x00\x05yunai"127.0.0.1:6379> get "\xac\xed\x00\x05t\x00\x05yunai""\xac\xed\x00\x05t\x00\x05shuai"`
```

 具体为什么是这样一串奇怪的 16 进制，胖友可以看看 [`ObjectOutputStream#writeString(String str, boolean unshared)`](https://github.com/JetBrains/jdk8u_jdk/blob/master/src/share/classes/java/io/ObjectOutputStream.java#L1301-L1311) 的代码，实际就是标志位 + 字符串长度 + 字符串内容。 

##### String 序列化方式

 [`org.springframework.data.redis.serializer.StringRedisSerializer`](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/serializer/StringRedisSerializer.java) ，字符串和二进制数组的**直接**转换。代码如下： 

```java
// StringRedisSerializer.java

private final Charset charset;

@Override
public String deserialize(@Nullable byte[] bytes) {
	return (bytes == null ? null : new String(bytes, charset));
}

@Override
public byte[] serialize(@Nullable String string) {
	return (string == null ? null : string.getBytes(charset));
}
```

 **绝大多数情况下，我们 KEY 和 VALUE 都会使用这种序列化方案**。而 VALUE 的序列化和反序列化，自己在逻辑调用 JSON 方法去序列化。为什么呢？继续往下看。 

 [`org.springframework.data.redis.serializer.GenericToStringSerializer`](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/serializer/GenericToStringSerializer.java) ，使用 Spring [ConversionService](https://codeday.me/bug/20180111/117294.html) 实现 `` 对象和 String 的转换，从而 String 和二进制数组的转换。 

 例如说，序列化的过程，首先 <t> 对象通过 ConversionService 转换成 String ，然后 String 再序列化成二进制数组。 

##### JSON 序列化方式

 [`org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer`](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/serializer/GenericJackson2JsonRedisSerializer.java) ，使用 Jackson 实现 JSON 的序列化方式，并且从 Generic 单词可以看出，是支持所有类。怎么体现呢？参见构造方法的代码： 

```java
// GenericJackson2JsonRedisSerializer.java

public GenericJackson2JsonRedisSerializer(@Nullable String classPropertyTypeName) {

	this(new ObjectMapper());

	// simply setting {@code mapper.disable(SerializationFeature.FAIL_ON_EMPTY_BEANS)} does not help here since we need
	// the type hint embedded for deserialization using the default typing feature.
	mapper.registerModule(new SimpleModule().addSerializer(new NullValueSerializer(classPropertyTypeName)));

	// <1>
	if (StringUtils.hasText(classPropertyTypeName)) {
		mapper.enableDefaultTypingAsProperty(DefaultTyping.NON_FINAL, classPropertyTypeName);
	// <2>
	} else {
		mapper.enableDefaultTyping(DefaultTyping.NON_FINAL, As.PROPERTY);
	}
}
```

- `<1>` 处，如果传入了 `classPropertyTypeName` 属性，就是使用使用传入对象的 `classPropertyTypeName` 属性对应的值，作为默认类型（Default Typing）。
- `<2>` 处，如果未传入 `classPropertyTypeName` 属性，则使用传入对象的类全名，作为默认类型（Default Typing）。

什么是**默认类型（Default Typing）**呢？  怎么保证字符串反序列化成对象的类型呢？ 

 Jackson 通过 Default Typing ，**会在字符串多冗余一个类型，这样反序列化就知道具体的类型了**。 

 使用 Jackson Default Typing 机制序列化的结果，如下： 

```java
{
       "@class": "cn.iocoder.springboot.labs.lab10.springdatarediswithjedis.cacheobject.UserCacheObject",
       "id": 1,
       "name": "芋道源码",
       "gender": 1
   }
```

`@class`就是数据类型。

>  注意，`GenericFastJsonRedisSerializer` 不是 Spring Data Redis 内置实现，而是由于 FastJSON 自己实现。 

` Jackson2JsonRedisSerializer  ` 序列化类里已经声明了类型，所以序列化的 JSON 字符串，无需在存储一个 `@class` 属性，用于存储类型。 

```java
// Jackson2JsonRedisSerializer.java
public class Jackson2JsonRedisSerializer<T> implements RedisSerializer<T> {
    // ... 省略不重要的代码

    public static final Charset DEFAULT_CHARSET = StandardCharsets.UTF_8;

    /**
     * 指定类型，和 <T> 要一致。
     */
    private final JavaType javaType;

    private ObjectMapper objectMapper = new ObjectMapper();

}
```

> 使用Jackson库将对象序列化为JSON字符串。优点是速度快，序列化后的字符串短小精悍，不需要实现Serializable接口。

##### XML 序列化方式

 [`org.springframework.data.redis.serializer.OxmSerializer`](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/serializer/OxmSerializer.java) ，使用 Spring [OXM](https://www.jianshu.com/p/1b7c8dc26001) 实现将对象和 String 的转换，从而 String 和二进制数组的转换。 

##### 配置序列化方式

```java
/**
 * 配置Redis序列化
 * @AutoConfigureAfter：在指定的类实例化后开始加载本类
 * @author Jjcc
 * @version 1.0.0
 * @className RedisConfiguration.java
 * @createTime 2019年11月10日 16:13:00
 */
@Configuration
@AutoConfigureAfter(RedisAutoConfiguration.class)
public class RedisConfiguration {

    /**
     * 设置redis存储数据的序列化方式
     * @title redisTemplate
     * @author Jjcc
     * @param redisConnectionFactory redis连接工厂
     * @return org.springframework.data.redis.core.RedisTemplate<java.lang.String,java.lang.Object>
     * @createTime 2019/11/12 20:48
     */
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        // 创建 RedisTemplate 对象
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        //设置 RedisConnection 工厂。它就是实现多种 Java Redis 客户端接入的秘密工厂
        redisTemplate.setConnectionFactory(redisConnectionFactory);

        // 创建 Jackson2JsonRedisSerializer JACKSON2序列化策略对象
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);

        ObjectMapper objectMapper = new ObjectMapper();
        //允许更改底层VisibilityCheckers的配置，以更改自动检测的属性类型的详细信息
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        // 反序列化时出现 Could not read JSON: Unrecognized field "enabled" 问题的解决方法
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
        // Redis序列化 Java8的时间Instant、LocalDateTime、LocalDate所需配置
        objectMapper.registerModules(new ParameterNamesModule(), new Jdk8Module(), new JavaTimeModule());

        //设置JACKSON2序列化策略对象
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);

        //使用 String 序列化方式，序列化 KEY
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        //使用 Jackson2 序列化方式，序列化 VALUE
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
        //使用 String 序列化方式，序列化 HASHKEY
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        //使用 Jackson2 序列化方式，序列化 HASHVALUE
        redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);

        return redisTemplate;
    }
}
```

##### 注意

> **使用 `Jackson2JsonRedisSerializer` 序列化 Value时，出现的几种问题。**

**redis序列化`java8 Instant、LocalDateTime、LocalDate`错误的问题**

所序列化的POJO对象中，存在 java8的时间对象 `Instant、LocalDateTime、LocalDate`时，会存在 **org.springframework.data.redis.serializer.SerializationException: `Could not read JSON: Can not construct instance of java.time.LocalDateTime no suitable constructor found, can not deserialize from Object value (missing default constructor or creator, or perhaps need to add/enable type information?)`:**异常。

解决方法：

1. 指定`LocalDateTime`这样的序列化以及反序列化器：
   `@JsonDeserialize(using = LocalDateTimeDeserializer.class)`
   `@JsonSerialize(using = LocalDateTimeSerializer.class)`

2. 在 配置 `Redis`的序列化配置，添加配置：

   ```java
   ObjectMapper objectMapper = new ObjectMapper();
   // Redis序列化 Java8的时间Instant、LocalDateTime、LocalDate所需配置
   objectMapper.registerModules(new ParameterNamesModule(), new Jdk8Module(), new JavaTimeModule());
   ```

https://blog.csdn.net/m0_37589586/article/details/87782001

https://blog.csdn.net/wwrzyy/article/details/90232835

https://www.jianshu.com/p/d45b2bd7f92a

**redis反序列化 Jackson2JsonRedisSerializer报错`Could not read JSON: Unrecognized field`...**

json序列化时，**不仅是根据get方法来序列化的，而是实体类中所有的有返回值的方法都会将返回的值序列化**，但是反序列化时是根据set方法来实现的，所以**当实体类中有非get，set方法的方法有返回值时，反序列化时就会出错**。

解决方法：

1. `RedisTemplate`的设置里，添加配置：

   ```java
   ObjectMapper om = new ObjectMapper();
   // 反序列化时出现 Could not read JSON: Unrecognized field "enabled" 问题的解决方法
   objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
   ```

2. 在出现问题的 `set` 方法上加上注解：

   `@JSonIgnore`

https://blog.csdn.net/baidu_29092471/article/details/55194363

https://blog.csdn.net/ChuXinstyle/article/details/101271729

### 12.3 使用Redis Repository操作数据

#### SpringBoot启动类中添加注解配置

`@EnableRedisRepositories`：注解开启使用RedisRepositories，CRUD操作将会操作redis中的数据

```java
@EnableCaching
@EnableRedisRepositories
@SpringBootApplication
public class RedisApplication {
 
    public static void main(String[] args) {
        SpringApplication.run(RedisApplication.class, args);
    }
 
}
```

#### 定义实体

`@RedisHash(value = "user")`： *user表示在redis中新建user集合* ； *之后所有的UserEntity的保存操作全部会保存在user这个集合中* ； *保存时Key的格式为——user：id* 

`@Id`：相当于新建集合的key的标识，user：id;

`@Indexed`：在需要条件查询的字段上加上@Indexed

```java
@Data
@ToString
@RedisHash(value = "user")
public class TUser {

    @Id
    private String id;
	 //在需要条件查询的字段上加上@Indexed
    @Indexed
    private String name;

    private String password;
    
    private Cat cat;
}
```

#### 创建DAO层

> 支持方法名和example查询，前提查询字段必须是加了`@Indexed`注解。 

```java
@Repository
public interface UserDao extends CrudRepository<TUser,String>,QueryByExampleExecutor<TUser> {

    TUser findByName(String name);

}

```

#### 创建Service层

```java
@Service
public class UserService {
 
    @Autowired
    private UserDao userDao;
 
//因为使用了RedisRepositories，所以简单的crud将不用使用RedisTemplate
//    @Autowired
//    private RedisTemplate redisTemplate;
 
 
    /**
     * 按user:id的方式存入redis
     * @param user
     */
    public void save(UserEntity user){
        //redisTemplate.opsForValue().set(new Random().nextDouble() + "",user);
 
        userDao.save(user);
 
    }
 
    /**
     * 根据key从redis中查找对应value
     * @param id
     * @return
     */
    public UserEntity findOne(Long id){
 
        //UserEntity user = (UserEntity) redisTemplate.opsForValue().get(key);
 
        UserEntity user = userDao.findById(id).get();
 
        return user;
    }
}
```

#### 创建Controller层

```java
@RestController
@RequestMapping("user")
public class UserController {
 
    @Autowired
    private UserService userService;
 
    /**
     * 保存到redis中
     * @return
     */
    @GetMapping("save")
    public String save(){
 
        UserEntity user = new UserEntity();
 
        user.setName(String.valueOf(new Random().nextInt(100000)));
 
        user.setAge(new Random().nextInt(100000));
 
        userService.save(user);
 
        System.out.println(user.toString());
 
        return "success";
    }
 
    /**
     * 根据key从redis中查找value
     * @param id
     * @return
     */
    @GetMapping("find/{id}")
    public String find(@PathVariable Long id){
       UserEntity user = userService.findOne(id);
 
        System.out.println(user);
        return "success";
    }
}
```

### 12.4 Spring Cache

> `Spring Cache`是`Spring`框架提供的对缓存使用的抽象类，支持多种缓存，比如`Redis`、`EHCache`等，集成很方便。同时提供了多种注解来简化缓存的使用，可对方法进行缓存。 
>
> **每次调用需要缓存功能的方法时，Spring会检查指定参数的指定的目标方法是否已经被调用过；如果有就直接从缓存中获取方法调用后的结果，如果没有就调用方法并缓存结果后返回给用户。下次调用直接从缓存中获取。** 

#### 1. 为什么要做缓存

- 提升性能

   绝大多数情况下，select 是出现性能问题最大的地方。  一方面，select 会有很多像 join、group、order、like 等这样丰富的语义，而这些语义是非常耗性能的；另一方面，大多 数应用都是读多写少，所以加剧了慢查询的问题。 

  分布式系统中远程调用也会耗很多性能，因为有网络开销，会导致整体的响应时间下降。为了挽救这样的性能开销，在业务允许的情况（不需要太实时的数据）下，使用缓存是非常必要的事情。 

- 缓解数据库压力

  当用户请求增多时，数据库的压力将大大增加，通过缓存能够大大降低数据库的压力。

#### 2. 常用缓存操作流程

- 失效

  应用程序先从cache取数据，没有得到，则从数据库中取数据，成功后，放到缓存中并返回。

- 命中

  应用程序从cache中取数据，取到后返回。

- 更新

  先把数据存到数据库中，成功后，再让缓存失效或更新。缓存操作失败，数据库事务回滚

- 删除

  先从数据库里删掉，成功后，再从缓存里删掉。缓存操作失败，数据库事务回滚。

#### 3. spring cache

 第一步：pom.xml 添加 Spring Boot 的 jar 依赖： 

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

 第二步：添加入口启动类 `@EnableCaching` 注解开启 Caching，实例如下。 

```java
@EnableCaching
```

#### 4. 设置CacheManager缓存管理类

 这里使用`RedisCacheManager`，其他的管理类还有：`SimpleCacheManager`、`ConcurrentMapCacheManager`等 

```java
    /**
     * 设置CacheManager缓存管理类
     * @title redisCacheManager
     * @author Jjcc
     * @param redisTemplate
     * @return org.springframework.data.redis.cache.RedisCacheManager
     * @createTime 2019/11/12 20:48
     */
    @Bean
    public RedisCacheManager redisCacheManager(RedisTemplate redisTemplate) {
        RedisCacheWriter redisCacheWriter = RedisCacheWriter.nonLockingRedisCacheWriter(
                Objects.requireNonNull(redisTemplate.getConnectionFactory()));

        //缓存配置对象
         RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig();

        //设置缓存的默认超时时间：30分钟
        redisCacheConfiguration = redisCacheConfiguration.entryTtl(Duration.ofMinutes(30L)).
                //如果是空值，不缓存
                disableCachingNullValues().
                //设置key序列化器
                serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer())).
                //设置value序列化器
                serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(redisTemplate.getValueSerializer()));

        return RedisCacheManager
                .builder(redisCacheWriter)
                .cacheDefaults(redisCacheConfiguration).build();
    }
```

#### 5. 实现一个简单的例子

 缓存对象有几个非常需要注意的点： 

1. **必须实现无参的构造函数**
2. 需要实现Serializable 接口和定义serialVersionUID （因为缓存需要使用JDK的方式序列化和反序列化），这种方法不好。因为我们要修改所有的实体类，实现Serializable接口。我们完全可以修改为使用JSON序列化与反序列化的方式，可读性更强，体积更小，速度更快。

```java
    @Cacheable(value = "student")
    @ApiOperation("获取所有学生信息")
    @GetMapping("/student")
    public AjaxResponse getTableStud() {
        List<TableStudent> list;
        try {
            list = tableStudentService.list();
        } catch (Exception e) {
            e.printStackTrace();
            throw new CustomException(CustomExceptionType.SYSTEM_ERROR, "getTableStud方法错误");
        }

        return AjaxResponse.success(list);
    }
```

<img src=".\img\1573565972711.png" alt="1573565972711" style="zoom:150%;" />

#### 6.  详述缓存声明式注解

使用Spring缓存抽象时我们需要关注以下两点； 

- 确定方法需要被缓存以及他们的缓存策略

- 从缓存中读取之前缓存存储的数据

| 名称           | 解释                                                         |
| -------------- | ------------------------------------------------------------ |
| Cache          | 缓存接口，定义缓存操作。实现有：RedisCache、EhCacheCache、ConcurrentMapCache等 |
| CacheManager   | 缓存管理器，管理各种缓存（cache）组件                        |
| @Cacheable     | 主要针对方法配置，能够根据方法的请求参数对其进行缓存         |
| @CacheEvict    | 清空缓存                                                     |
| @CachePut      | 保证方法被调用，又希望结果被缓存。 与@Cacheable区别在于是否每次都调用方法，常用于更新 |
| @EnableCaching | 开启基于注解的缓存                                           |
| keyGenerator   | 缓存数据时key生成策略                                        |
| serialize      | 缓存数据时value序列化策略                                    |
| @CacheConfig   | 统一配置本类的缓存注解的属性                                 |
| @Caching       | 有时候我们可能组合多个Cache注解使用，此时就需要@Caching组合多个注解标签了。 |

#####  @Cacheable 根据方法的请求参数对其结果进行缓存 

`@Cacheable` 通常应用到读取数据的方法上，如查找方法：**先从缓存中读取，如果没有再调用方法获取数据，然后把数据查询结果添加到缓存中。如果缓存中查找到数据，被注解的方法将不会执行。**

<img src=".\img\3987fa1a743758b511cc5c975dff8645_1173x456.png" alt="img" style="zoom:150%;" />

```java
@Cacheable(value = "emp" ,key = "targetClass + methodName + #p0")
public List<NewJob> queryAll(User uid) {
	return newJobDao.findAllByUid(uid);
}
```

此处的`value`是必需的，它指定了你的缓存存放在哪块命名空间。

此处的`key`是使用的spEL表达式，参考上章。这里有一个小坑，如果你把`methodName`换成`method`运行会报错，观察它们的返回类型，原因在于`methodName`是`String`而`method`是`Method`。

**其它属性** 

打开`@Cacheable`注解的源码，可以看到该注解提供的其他属性，如：

```java
String[] cacheNames() default {}; //和value注解差不多，二选一
String keyGenerator() default ""; //key的生成器。key/keyGenerator二选一使用
String cacheManager() default ""; //指定缓存管理器
String cacheResolver() default ""; //或者指定获取解析器
String condition() default ""; //条件符合则缓存
String unless() default ""; //条件符合则不缓存
boolean sync() default false; //是否使用异步模式
```

##### @CachePut 每次都会触发真实方法的调用 

`@CachePut`通常应用于保存和修改方法配置，能够根据方法的请求参数对其结果进行缓存，和 @Cacheable 不同的是，它每次都会触发被注解方法的调用。  

![img](.\img\f513f295df10fcff859b6981053fca27_1181x457.png)

```java
@CachePut(value = "emp", key = "targetClass + #p0")
public NewJob updata(NewJob job) {
    NewJob newJob = newJobDao.findAllById(job.getId());
    newJob.updata(job);
    return job;
}
```

 简单来说就是用户更新缓存数据。但需要**注意的是该注解的`value` 和 `key` 必须与要更新的缓存相同，也就是与`@Cacheable` 相同。** 

 **其它属性** 

```java
String[] cacheNames() default {}; //与value二选一
String keyGenerator() default "";  //key的生成器。key/keyGenerator二选一使用
String cacheManager() default "";  //指定缓存管理器
String cacheResolver() default ""; //或者指定获取解析器
String condition() default ""; //条件符合则缓存
String unless() default ""; //条件符合则不缓存
```

#####  @CacheEvict 根据条件对缓存进行清空 

 通常应用于删除方法配置，能够根据一定的条件对缓存进行清空。可以清除一条或多条缓存。

 ![img](E:\软件开发资料\Spring全家桶\springboot\img\aaac4267e8bf92dbd10780dca991a1b2_1183x718.png) 

```java
    @Cacheable(value = "emp",key = "#p0.id")
    public NewJob save(NewJob job) {
        newJobDao.save(job);
        return job;
    }

    //清除一条缓存，key为要清空的数据
    @CacheEvict(value="emp",key="#id")
    public void delect(int id) {
        newJobDao.deleteAllById(id);
    }

    //方法调用后清空所有缓存
    @CacheEvict(value="emp",allEntries=true)
    public void delectAll() {
        newJobDao.deleteAll();
    }

    //方法调用前清空所有缓存
    @CacheEvict(value="emp",beforeInvocation=true)
    public void delectAll() {
        newJobDao.deleteAll();
    }
```

**其他属性**

```java
String[] cacheNames() default {}; //与value二选一
String keyGenerator() default "";  //key的生成器。key/keyGenerator二选一使用
String cacheManager() default "";  //指定缓存管理器
String cacheResolver() default ""; //或者指定获取解析器
String condition() default ""; //条件符合则清空
```

##### @Cacheing 多个缓存注解组合使用

 有时候我们可能组合多个Cache注解使用，此时就需要@Caching组合多个注解标签了。 

```java
    @Caching(cacheable = {
            @Cacheable(value = "emp",key = "#p0"),
            ...
    },
    put = {
            @CachePut(value = "emp",key = "#p0"),
            ...
    },evict = {
            @CacheEvict(value = "emp",key = "#p0"),
            ....
    })
    public User save(User user) {
        ....
    }
```

##### @CacheConfig 统一配置本类的缓存注解的属性 

 当我们需要缓存的地方越来越多，你可以使用`@CacheConfig(cacheNames = {"myCache"})`注解来统一指定`value`的值，这时可省略`value`，如果你在你的方法依旧写上了`value`，那么依然以方法的`value`值为准。 

```java
@CacheConfig(cacheNames = {"myCache"})
public class BotRelationServiceImpl implements BotRelationService {
    @Override
    @Cacheable(key = "targetClass + methodName +#p0")//此处没写value
    public List<BotRelation> findAllLimit(int num) {
        return botRelationRepository.findAllLimit(num);
    }
    .....
}
```

**其它属性**

```java
String keyGenerator() default "";  //key的生成器。key/keyGenerator二选一使用
String cacheManager() default "";  //指定缓存管理器
String cacheResolver() default ""; //或者指定获取解析器
```

##### SpEL上下文数据

 Spring Cache提供了一些供我们使用的SpEL上下文数据 

| 名称          | 位置       | 描述                                                         | 示例                   |
| ------------- | ---------- | ------------------------------------------------------------ | ---------------------- |
| methodName    | root对象   | 当前被调用的方法名                                           | `#root.methodname`     |
| method        | root对象   | 当前被调用的方法                                             | `#root.method.name`    |
| target        | root对象   | 当前被调用的目标对象实例                                     | `#root.target`         |
| targetClass   | root对象   | 当前被调用的目标对象的类                                     | `#root.targetClass`    |
| args          | root对象   | 当前被调用的方法的参数列表                                   | `#root.args[0]`        |
| caches        | root对象   | 当前方法调用使用的缓存列表                                   | `#root.caches[0].name` |
| Argument Name | 执行上下文 | 当前被调用的方法的参数，如findArtisan(Artisan artisan),可以通过#artsian.id获得参数 | `#artsian.id`          |
| result        | 执行上下文 | 方法执行后的返回值（仅当方法执行后的判断有效，如 unless cacheEvict的beforeInvocation=false） | `#result`              |

**注意：**

1.当我们要使用root对象的属性作为key时我们也可以将`#root`省略，因为Spring默认使用的就是root对象的属性。 如

```java
@Cacheable(key = "targetClass + methodName + #p0")
```

2.使用方法参数时我们可以直接使用`#参数名`或者`#p参数index`。 如：

```java
@Cacheable(value="users", key="#id")
@Cacheable(value="users", key="#p0")
```

**SpEL提供了多种运算符** 

| **类型**   | **运算符**                                     |
| ---------- | ---------------------------------------------- |
| 关系       | <，>，<=，>=，==，!=，lt，gt，le，ge，eq，ne   |
| 算术       | +，- ，* ，/，%，^                             |
| 逻辑       | &&，\|\|，!，and，or，not，between，instanceof |
| 条件       | ?: (ternary)，?: (elvis)                       |
| 正则表达式 | matches                                        |
| 其他类型   | ?.，?[…]，![…]，^[…]，$[…]                     |

**根据条件操作缓存内容并不影响数据库操作，条件表达式返回一个布尔值，true/false，当条件为true，则进行缓存操作，否则直接调用方法执行的返回结果。** 

- **长度：** `@CachePut(value = "user", key = "#user.id",condition = "#user.username.length() < 10")` 只缓存用户名长度少于10的数据
- **大小：** `@Cacheable(value = "user", key = "#id",condition = "#id < 10")` 只缓存ID小于10的数据
- **组合：** `@Cacheable(value="user",key="#user.username.concat(#user.password)")`
- **提前操作：** `@CacheEvict(value="user",allEntries=true,beforeInvocation=true)` 加上`beforeInvocation=true`后，不管内部是否报错，缓存都将被清除，默认情况为`false`

##### 缓存注解常用策略

查询分两种：一种是查回来一个集合，一种是查一个对象。
那么，我们随便增删改一个对象，都有可能导致集合缓存与数据库数据不一致。
通常情况下，我们该怎么做才能做到一致性？**只要发生删改查，就把集合类缓存销毁** 

 **对于查询方法：** 

 `@Cacheable(value=“obj”)` 或 `@Cacheable(value=“objList”)` 

 **对于修改和新增方法：** 

```java
@Caching(evict = {@CacheEvict(cacheNames = "objList",allEntries = true)},
                  put={@CachePut(cacheNames = "obj",key = "#id")})
```

 **对于删除方法：** 

```java
@Caching(evict = {@CacheEvict(cacheNames = "objList",allEntries = true),
                  @CacheEvict(cacheNames = "obj",key = "#id")})
```

> 在实际的生产环境中，没有一定之规，哪种注解必须用在哪种方法上，`@CachEvict` 注解通常也用于更新方法上。数据的缓存策略，要根据资源的使用方式，做出合理的缓存策略规划。保证缓存与业务数据库的数据一致性。并做好测试，没有一定之规。 
>

### 12.5  项目实践

#### 1. 层次结构

在我们使用数据库时，我们会创建 `dataobject` 包，存放 DO（Data Object）数据库实体对象。

那么同理，我们缓存对象，怎么进行对应呢？对于复杂的缓存对象，我们创建了 `cacheobject` 包，和 `dataobject` 包同层。如：

```
service # 业务逻辑层
dao # 数据库访问层
dataobject # DO
cacheobject # 缓存对象
```

 并且所有的`Cache Object`对象使用`CacheObject`结尾，例如说`UserCacheObject`、`ProductCacheObject` 。 

#### 2. 数据访问层

在我们访问数据库时，我们会创建 `dao` 包，存放每个 DO 对应的 Dao 对应。那么对于每一个 CacheObject 类，我们也会创建一个其对应的 Dao 类。例如说，`UserCacheObject` 对应 `UserCacheObjectDao` 类。示例代码如下： 

```java
@Repository
public class UserCacheDao {

    private static final String KEY_PATTERN = "user:%d"; // user:用户编号 <1>

    @Resource(name = "redisTemplate")
    @SuppressWarnings("SpringJavaInjectionPointsAutowiringInspection")
    private ValueOperations<String, String> operations; // <2>

    private static String buildKey(Integer id) { // <3>
        return String.format(KEY_PATTERN, id);
    }

    public UserCacheObject get(Integer id) {
        String key = buildKey(id);
        String value = operations.get(key);
        return JSONUtil.parseObject(value, UserCacheObject.class);
    }

    public void set(Integer id, UserCacheObject object) {
        String key = buildKey(id);
        String value = JSONUtil.toJSONString(object);
        operations.set(key, value);
    }
}
```

- `<1>` 处，通过静态变量，声明 KEY 的前缀，并且使用冒号作为间隔
- `<3>` 处，声明 `KEY_PATTERN` 对应的 KEY 拼接方法，避免散落在每个方法中。
- `<2>` 处，通过 `@Resource` 注入指定名字的 RedisTemplate 对应的 Operations 对象，这样明确每个 KEY 的类型。
- 剩余的，就是每个方法封装对应的操作。

> 不建议直接在**Service业务层**调用。 如果这样，我们业务代码里，就容易混杂着很多 Redis 访问代码的细节，导致很脏乱。我们试着把 RedisTemplate 想象成 Spring JDBCTemplate ，我们一定会声明对应的 Dao 类，访问数据库。 
>
> 将 `dao` 包下拆成 `mysql`、`redis` 包。这样，MySQL 相关的 Dao 放在 `mysql` 包下，Redis 相关的 Dao 放在 `redis` 。 

### 12.6 Pipeline

#### 1. 背景

> redis通过**tcp协议**来对外提供服务，client通过socket连接发起请求，每个请求在命令发出后会阻塞等待redis服务器进行处理，处理完毕后将结果返回给client。

和一个http的服务器类似，一问一答，请求一次给一次响应。而这个过程在排除掉redis服务本身做复杂操作时的耗时的话，可以看到**最耗时的就是这个网络传输过程。每一个命令都对应了发送、接收两个网络传输**，假如一个流程需要0.1秒，那么一秒最多只能处理10个请求，将严重制约redis的性能。 

在很多场景下，我们要完成一个业务，可能会对redis做连续的多个操作，譬如库存减一、订单加一、余额扣减等等，**这有很多个步骤是需要依次连续执行的**。这样的场景，**网络传输的耗时将是限制redis处理量的主要瓶颈**。循环key，获取value，可能会造成连接池的连接数增多，连接的创建和摧毁，消耗性能 。

pipeline管道就是解决执行大量命令时、会产生大量同学次数而导致延迟的技术。其实原理很简单，**pipeline就是把所有的命令一次发过去，避免频繁的发送、接收带来的网络开销，redis在打包接收到一堆命令后，依次执行，然后把结果再打包返回给客户端。**

根据项目中的缓存数据结构的实际情况，数据结构为`string`类型的，使用RedisTemplate的`multiGet`方法；数据结构为`hash`，使用`Pipeline`(管道)，组合命令，批量操作redis。 

#### 2. 源码解读源码解读

##### executePipelined

**在 RedisTemplate 类中，提供了 2 组四个方法，用于执行 Redis Pipeline 操作 。**

```java
// <1> 基于 Session 执行 Pipeline
@Override
public List<Object> executePipelined(SessionCallback<?> session) {
	return executePipelined(session, valueSerializer);
}
@Override
public List<Object> executePipelined(SessionCallback<?> session, @Nullable RedisSerializer<?> resultSerializer) {
    // ... 省略代码
}

// <2> 直接执行 Pipeline
@Override
public List<Object> executePipelined(RedisCallback<?> action) {
	return executePipelined(action, valueSerializer);
}
@Override
public List<Object> executePipelined(RedisCallback<?> action, @Nullable RedisSerializer<?> resultSerializer) {
    // ... 省略代码
}
```

-  两组方法的差异，在于是否是 `Session` 中执行。 
-  每组方法里，差别在于是否传入 RedisSerializer 参数。**如果不传，则使用 RedisTemplate 的序列化相关的属性。** 

**`executePipelined(RedisCallable<?> action, ....)`方法源码分析**

```java
// RedisTemplate.java
@Override
public List<Object> executePipelined(RedisCallback<?> action, @Nullable RedisSerializer<?> resultSerializer) {
	// <1> 执行 Redis 方法
	return execute((RedisCallback<List<Object>>) connection -> {
		// <2> 打开 pipeline
		connection.openPipeline();
		boolean pipelinedClosed = false; // 标记 pipeline 是否关闭
		try {
			// <3> 执行
			Object result = action.doInRedis(connection);
			// <4> 不要返回结果
			if (result != null) {
				throw new InvalidDataAccessApiUsageException(
						"Callback cannot return a non-null value as it gets overwritten by the pipeline");
			}
			// <5> 提交 pipeline 执行
			List<Object> closePipeline = connection.closePipeline();
			pipelinedClosed = true;
			// <6> 反序列化结果，并返回
			return deserializeMixedResults(closePipeline, resultSerializer, hashKeySerializer, hashValueSerializer);
		} finally {
			if (!pipelinedClosed) {
				connection.closePipeline();
			}
		}
	});
}
```

- `<1>` 处，调用 `#execute(RedisCallback action)` 方法，执行 Redis 方法。注意，此处传入的 `action` 参数，不是我们传入的 RedisCallback 参数。我们的会在该 `action` 中被执行。
- `<2>` 处，调用 `RedisConnection#openPipeline()` 方法，**自动**打开 Pipeline 模式。这样，我们就不需要手动去打开了。
- `<3>` 处，调用我们传入的实现的 `RedisCallback#doInRedis(RedisConnection connection)` 方法，执行在 Pipeline 中，想要执行的 Redis 操作。
- `<4>` 处，不**要返回结果。因为 RedisCallback 是统一定义的接口，所以可以返回一个结果。但是在 Pipeline 中，未提交执行时，显然是没有结果**，返回也没有意思。简单来说，就是我们在实现 `RedisCallback#doInRedis(RedisConnection connection)` 方法时，**返回 `null` 即可**。
- `<5>` 处，调用 `RedisConnection#closePipeline()` 方法，**自动**提交 Pipeline 执行，并返回执行结果。
- `<6>` 处，反序列化结果，并返回 Pipeline 结果。

  **[`org.springframework.data.redis.core.RedisCallback`](https://github.com/spring-projects/spring-data-redis/blob/2eb7067e8c7e859168a281145cc46ccddb42049f/src/main/java/org/springframework/data/redis/core/RedisCallback.java) 接口，Redis 回调接口** 

```java
// RedisCallback.java
public interface RedisCallback<T> {

	/**
	 * Gets called by {@link RedisTemplate} with an active Redis connection. Does not need to care about activating or
	 * closing the connection or handling exceptions.
	 *
	 * @param connection active Redis connection
	 * @return a result object or {@code null} if none
	 * @throws DataAccessException
	 */
	@Nullable
	T doInRedis(RedisConnection connection) throws DataAccessException;
}
```

 传入的 `connection` 参数是 RedisConnection 对象，它提供的 `'low level'` 更底层的 Redis API 操作。 

```java
// RedisStringCommands.java
// RedisConnection 实现 RedisStringCommands 接口

byte[] get(byte[] key);

Boolean set(byte[] key, byte[] value);
```

 **传入和返回的是二进制数组，实际就是 RedisTemplate 已经序列化的入参和会被反序列化的出参。** 

##### 具体示例

```java
/**
 * redis-数据访问层
 * @author Jjcc
 * @version 1.0.0
 * @className TableStudentDAO.java
 * @createTime 2019年11月13日 21:35:00
 */
@Repository
public class TableStudentDAO {

    private  RedisTemplate<String, Object> redisTemplate;

    @Autowired
    public TableStudentDAO(RedisTemplate<String, Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    /**
     * redisTemplate使用pipeline
     * 读取操作底层源码会使用redisTemplate中定义的序列化，而写入操作底层源码不会进行任何序列化相关的操作，
     * 所以在写入操作时，值转成二级制数组阶段就进行序列化；
     * 这里valueSerializable的序列化方式是Jackson2JsonRedisSerializer
     * @title redisPipelineMethod
     * @author Jjcc
     * @return java.util.List<java.lang.Object>
     * @createTime 2019/11/14 15:01
     */
    public List<Object> redisPipelineMethod() {

        return redisTemplate.executePipelined((RedisCallback<List<Object>>) connection -> {
            //set 写入
            for (int i = 0; i < 3; i++) {
                //写入时，需要序列化value；使用何种序列化，需要与取值时使用的序列化对应。
                //redisPipeline方法，对于写入不会使用序列化操作，需要自己实现
                Jackson2JsonRedisSerializer valueSerializer = (Jackson2JsonRedisSerializer) redisTemplate.getValueSerializer();

                byte[] valArray = valueSerializer.serialize("shuai");

                connection.set(String.format("yunai:%d", i).getBytes(), valArray);
            }

            //get
            for (int i = 0; i < 3; i++) {
                connection.get(String.format("yunai:%d", i).getBytes());
            }

            return null;
        });
    }
}
```

> 1.这里的connect是redis原生链接，所以connection的返回结果是基本上是byte数组，如果需要存储的数据，需要对byte[]数组反序列化。
>
> 2.在doInRedis中返回值必须返回为null
>
> 3.connection.openPipeline()可以调用，也可以不调用，但是connection.closePipeline()不能调用，调用了拿不到返回值。因为调用的时候会直接将结果返回，同时也不会对代码进行反序列化。



## 13. 应用监控管理

### 13.1 Actuator应用监控快速入门

### 13.2 SpringBoot Admin



## 14. 动态数据源

### 14.1 基于Spring [AbstractRoutingDataSource](https://github.com/spring-projects/spring-framework/blob/master/spring-jdbc/src/main/java/org/springframework/jdbc/datasource/lookup/AbstractRoutingDataSource.java) 做拓展

简单来说，通过继承 AbstractRoutingDataSource 抽象类，实现一个管理项目中多个 DataSource 的**动态** DynamicRoutingDataSource 实现类。这样，Spring 在获取数据源时，可以通过 DynamicRoutingDataSource 返回**实际**的 DataSource 。

然后，我们可以自定义一个 `@DS` 注解，可以添加在 Service 方法、Dao 方法上，表示其**实际**对应的 DataSource 。

如此，整个过程就变成，执行数据操作时，通过“配置”的 `@DS` 注解，使用 DynamicRoutingDataSource 获得**对应的实际的** DataSource 。之后，在通过该 DataSource 获得 Connection 连接，最后发起数据库操作。

#### 1. 使用 [dynamic-datasource-spring-boot-starter]( https://github.com/jjcc123312/spring-boot-study/tree/springboot-moreDataSource )

##### 优势

网上关于动态数据源的切换的文档有很多，核心只有两种。

1. 构建多套环境，优势是方便控制也容易集成一些简单的分布式事务，缺点是非动态同时代码量较多,配置难度大。
2. 基于spring提供原生的 `AbstractRoutingDataSource` ，参考一些文档自己实现切换。

如果你的数据源较少，场景不复杂，选择以上任意一种都可以。如果你需要更多特性，请尝试本动态数据源。

1. 数据源分组，适用于多种场景 纯粹多库 读写分离 **一主多从 混合模式**。
2. 简单集成Druid数据源监控多数据源，简单集成Mybatis-Plus简化单表，简单集成P6sy格式化sql，简单集成Jndi数据源。
3. 简化Druid和HikariCp配置，提供全局参数配置。
4. 提供自定义数据源来源(默认使用yml或properties配置)。
5. 项目启动后能动态增减数据源。
6. 使用spel动态参数解析数据源，如从session，header和参数中获取数据源。（多租户架构神器）
7. 多层数据源嵌套切换。（一个业务ServiceA调用ServiceB，ServiceB调用ServiceC，每个Service都是不同的数据源）
8. 使用正则匹配或spel表达式来切换数据源（实验性功能）。

##### 劣势

不能使用多数据源事务（同一个数据源下能使用事务），网上其他方案也都不能提供。

如果你需要使用到分布式事务，那么你的架构应该到了微服务化的时候了。

如果呼声强烈，项目达到800 star，作者考虑集成分布式事务。

PS: 如果您只是几个数据库但是有强烈的需求分布式事务，建议还是使用传统方式自己构建多套环境集成atomic这类，网上百度很多。

##### 约定

1. 本框架只做 **切换数据源** 这件核心的事情，并**不限制你的具体操作**，切换了数据源可以做任何CRUD。
2. 配置文件所有以下划线 `_` 分割的数据源 **首部** 即为组的名称，相同组名称的数据源会放在一个组下。
3. **切换数据源即可是组名，也可是具体数据源名称，切换时默认采用负载均衡机制切换。**
4. 默认的数据源名称为 **master** ，你可以通过spring.datasource.dynamic.primary修改。
5. 方法上的注解优先于类上注解。

##### 使用方法

1. 引入`dynamic-datasouce-spring-boot-starter`

```xml
<dependency>
  <groupId>com.baomidou</groupId>
  <artifactId>dynamic-datasource-spring-boot-starter</artifactId>
  <version>2.5.5</version>
</dependency>
```

2.  **排除** 原生Druid的快速配置类。 

`DruidDataSourceAutoConfigure`会注入一个`DataSourceWrapper`，其会在原生的`spring.datasource`下找`url,username,password`等。而我们动态数据源的配置路径是变化的,所以需要排除： 

```yml
spring:
  autoconfigure:
    exclude: com.alibaba.druid.spring.boot.autoconfigure.DruidDataSourceAutoConfigure
```

或者在程序启动类上排除： 

```java
@SpringBootApplication(exclude = DruidDataSourceAutoConfigure.class)
```

3. 配置数据源。 

```yaml
  datasource:
    dynamic:
      primary: master
      datasource:
        master:
          url: jdbc:mysql://47.97.204.235:3306/test?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
          username: root
          password: zsl19970202.
          driver-class-name: com.mysql.jdbc.Driver
        slaveDb_1:
          url: jdbc:mysql://47.97.204.235:3307/test?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
          username: root
          password: 123456
          driver-class-name: com.mysql.jdbc.Driver
        slaveDb_2:
          url: jdbc:mysql://47.97.204.235:3308/test?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
          username: root
          password: 123456
          driver-class-name: com.mysql.jdbc.Driver
      druid:
        # 下面为连接池的补充设置，应用到上面所有数据源中
        # 初始化大小，最小，最大
        initialSize: 5
        minIdle: 5
        maxActive: 20
        # 配置获取连接等待超时的时间
        maxWait: 60000
        # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
        timeBetweenEvictionRunsMillis: 60000
        # 配置一个连接在池中最小生存的时间，单位是毫秒
        minEvictableIdleTimeMillis: 30
        validationQuery: SELECT 1
        validationQueryTimeout: 10000
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        # 打开PSCache，并且指定每个连接上PSCache的大小
        poolPreparedStatements: true
        maxPoolPreparedStatementPerConnectionSize: 20
        filters: stat,wall
        # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
        connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
        # 合并多个DruidDataSource的监控数据
        useGlobalDataSourceStat: true
```

4. 使用 **@DS** 切换数据源。 

 **@DS** 可以注解在方法上和类上，**同时存在方法注解优先于类上注解**。 

 注解在service实现或mapper接口方法上，但强烈不建议同时在service和mapper注解。 (可能会有问题) 

|     注解      |                             结果                             |
| :-----------: | :----------------------------------------------------------: |
|    没有@DS    |                          默认数据源                          |
| @DS("dsName") | **dsName可以为组名(数据源Name前缀，如`slaveDb_1`，dsName=slaveDb，切换时默认采用负载均衡机制切换)也可以为具体某个库的名称** |

```java
@Service
@DS("slave")
public class UserServiceImpl implements UserService {

  @Autowired
  private JdbcTemplate jdbcTemplate;

  public List<Map<String, Object>> selectAll() {
    return  jdbcTemplate.queryForList("select * from user");
  }
  
  @Override
  @DS("slave_1")
  public List<Map<String, Object>> selectByCondition() {
    return  jdbcTemplate.queryForList("select * from user where age >10");
  }
}
```

在mybatis环境下也可注解在mapper接口层。

```java
@DS("slave")
public interface UserMapper {

  @Insert("INSERT INTO user (name,age) values (#{name},#{age})")
  boolean addUser(@Param("name") String name, @Param("age") Integer age);

  @Update("UPDATE user set name=#{name}, age=#{age} where id =#{id}")
  boolean updateUser(@Param("id") Integer id, @Param("name") String name, @Param("age") Integer age);

  @Delete("DELETE from user where id =#{id}")
  boolean deleteUser(@Param("id") Integer id);

  @Select("SELECT * FROM user")
  @DS("slave_1")
  List<User> selectAll();
}
```

 









