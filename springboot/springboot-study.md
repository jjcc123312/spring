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





















