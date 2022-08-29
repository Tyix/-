# spring boot 开发实用篇








## 1.热部署



### 什么是热部署？



**Restart**

>重启（restart）：自定义开发代码，包含类、页面配置文件等，加载位置restart类加载器
>重载（reload）：jar包，加载位置base类加载器



### 自动启动热部署

(略)



### 热部署范围配置



自定义：(appliction.yml)

```
spring:
 dovtools:
	resort:
	 exlucde:static/**,config/application.yml 
```



### 关闭热部署



**依据配置优先顺序**

1.application.yml
```
spring:
 dovtools:
   resort:
	  enabled:false
```

2.main函数
```
main(String[] args){
		System.setProperty("spring.devtools.restart.enabled","false");
		Application.run(application.class);
}
```






## 2.配置高级







### @ConfigurationProperties注解


**bean属性绑定（可用于第三方bena）**

* @EnableConfigurationPreperties注解可以将使用@ConfigurationProperties注解对应的类加入Spring容器

* @ConfigurationProperties注解绑定属性支持松散绑定（不区分大小写，忽略 "-"、"_"）
* 绑定前缀命名规范：仅能使用纯小写字母、数字、下划线作为合法字符
* 官方推荐格式：各个字母小写，中间用"-"分割

==注意：@EnableConfigurationPreperties与@Component不能同时使用==



解除使用@ConfigurationProperties注释警告
```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
       </dependency> 
```




### JDK8时间与空间计量单位

时间

>==@DurationUnit==*(ChronoUnit.XXX)*
>
>private Duration TimeOut;


空间

>==@DataSizeUnit==*(DataUnit.XXX)*
>
>private DataSize DataSize;`



### Bean属性校验

1. 添加JSR303规范坐标与Hibern校验框架坐标

> `<dependency>`
>       `<groupId>javax.validation</groupId>`
>       `<artifactId>validation-api</artifactId>`
>   `</dependency>`
>
>   `<dependency>`
>       `<groupId>org.hibernate.validator</groupId>`
>       `<artifactId>hibernate-validator</artifactId>`
>   `</dependency>`

2. 对Bean开启校验功能
>==@Validated==
>`public class Class{
}`

3. 设置校验规则

> ==@Max==*(value=  ,message="  ")*
> `private int port;`







### 进制数据转换规则

> ​							yaml文件中：
>
> ​									`dataSource:` 
>
> ​											`password:0127`
>
> ​							运行后password：87
>
> 出现原因：password未用" "包裹，自动进行进制转换，将0127转为87



**注意yaml文件中对于数字的定义支持进制书写格式，==如需使用字符串请使用引号明确标注==**



> `boolean: TRUE  			#TRUE,true,True,FALSE,false，False均可`
> `float: 3.14    			#6.8523015e+5  #支持科学计数法`
> `int: 123       			#0b1010_0111_0100_1010_1110    #支持二进制、八进制、十六进制`
> `null: ~        			#使用~表示null`
> `string: HelloWorld      		#字符串可以直接书写`
> `string2: "Hello World"  		#可以使用双引号包裹特殊字符`
> `date: 2018-02-17        		#日期必须使用yyyy-MM-dd格式`
> `datetime: 2018-02-17T15:02:31+08:00  #时间和日期之间使用T连接，最后使用+代表时区`



## 3.测试





### 加载测试专用属性

**应用于小范围测试环境**



* 在启动测试环境时可以通过properties参数设置测试环境专用的属性

> ==@SpringBootTest==*(properties = {"test.prop=testValue1"})*
> `public class TestClass{`
>
> `}`

* 在启动测试环境时可以通过args参数设置测试环境专用的传入参数

> ==@SpringBootTest==*(args = {"--test.arg=testValue2"})*
> `public class TestClass{`
>
> `}`

**当两者同时生效时，命令行优先。**



### web环境模拟测试








#### 1.测试类中启动web环境



> ==@SpringBootTest==*(webEnvironment = SpringBootTest.WebEnvironment.XXXX)*
>
> `public class WebTest {`
>
> `}`



#### 2.发送虚拟请求

* 开启虚拟MVC调用
==@AutoConfigureMockMvc==
> `public class WebTest {`
> `}`
* 注入虚拟MVC调用对象
>==@Autowired==
>`MockMvc mvc`

* 创建虚拟请求，当前访问url

> `MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.`*get/post/delete...("/books");*

* 执行请求

> `mvc.perform(builder);`





#### 3.匹配响应状态



* 定义执行状态匹配器   

>  `StatusResultMatchers status = MockMvcResultMatchers.status();`

* 定义预期执行状态   

>  `ResultMatcher ok = status.isOk();`

* 使用本次真实执行结果与预期结果进行比对    

> `mvc.perform(builder).andExpect(ok);`



#### 4.匹配请求体



* 定义执行结果匹配器    

> `ContentResultMatchers content = MockMvcResultMatchers.content();`
> `ContentResultMatchers content = MockMvcResultMatchers.<u>content();`

* 定义预期执行结果

> `ResultMatcher result = content().string("`*XXXXXX*`");`（匹配字符串）
>
> `ResultMatcher result = content.json("`*XXXXXXXXX*"`);`(匹配json)

* 使用本次真实执行结果与预期结果进行比对

> `mvc.perform(builder).andExpect(result);`





#### 5.匹配响应头

>  `HeaderResultMatchers header = MockMvcResultMatchers.header();`
>
> `ResultMatcher resultHeader = header.string("Content-Type", "application/json");`
>
> `mvc.perform(builder).andExpect(resultHeader);}`
> 



### 数据层测试回滚





* 为测试用例添加事务，SpringBoot会对测试用例对应的事务提交操作进行回滚

> `@SpringBootTest`
> ==@Transactional==
> `public class DaoTest {`
> `}`

* 如果想在测试用例中提交事务，可以通过@Rollback注解设置
> `@SpringBootTest`
> `@Transactional`
> ==@Rollback(false)==
> `public class DaoTest {`
> `}`





### 测试用例数据设定



* 测试用例数据通常采用随机值进行测试，使用SpringBoot提供的随机数为其赋值

yaml文件中:

*XXX*:${random.*XXX*}

*XXX*:${random.int(10)}表示10以内随机数

*XXX*:${random.int(5,10)}表示5到10随机数



## 4.数据层解决方案



### SQL





#### 数据源配置

SpringBoot提供了3种内嵌的数据源对象供开发者选择

* *HikariCP*：默认内置数据源对象
* *Tomcat提供DataSource：*HikariCP不可用的情况下，且在web环境中，将使用tomcat服务器配置的数据源对象
* *Commons DBCP*：Hikari不可用，tomcat数据源也不可用，将使用dbcp数据源





#### 持久化解决方案



* SpringBoot内置*JdbcTemplate*持久化解决方案（使用JdbcTemplate需要导入spring-boot-starter-jdbc）





#### 内嵌数据库



SpringBoot提供了3种内嵌数据库供开发者选择，提高开发测试效率

* H2
* HSQL
* Derby

*H2数据库线上运行时请务必关闭*





### NoSQL



#### Redis



##### 1.SpringBoot整合Redis

* 入导redis对应的starter

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

* 配置Redis(采用默认配置)

```
spring:
  redis:
    host: locahost
    port: 6379
```

* 提供操作Redis接口对象RedisTemplate.*opsXXXX*（获取各种数据类型操作接口）



* RedisTemplate以对象作为key和value，内部对数据进行序列化
* StringRedisTemplate以字符串作为key和value，与Redis客户端操作等效（常用）



##### 2. lettcus与jedis区别

spring boot默认使用*lettcus*

* *jedis*连接Redis服务器是直连模式，当多线程模式下使用jedis会==存在线程安全问题==，解决方案可以通过配置连接池使每个连接专用，这样整体性能就大受影响。
* *lettcus*基于Netty框架进行与Redis服务器连接，底层设计中采用StatefulRedisConnection。 StatefulRedisConnection自身是线程安全的，可以保障并发访问安全问题，所以一个连接可以被多线程复用。当然lettcus也支持多连接实例一起工作



#### Mongodb



* MongoDB是一个开源、高性能、无模式的文档型数据库。NoSQL数据库产品中的一种，是最像关系型数据库的非关系型数据库 



* SpringBoot整合Mongodb

  1. 导入Mongodb对应的starter

  2. 配置mongodb访问uri

  3. 提供操作Mongodb接口对象MongoTemplate



