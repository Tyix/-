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

>`spring:
	    dovtools:
	        resort:
	          exlucde:static/**,config/application.yml ....`



### 关闭热部署



**依据配置优先顺序**

1.application.yml

>`spring:
	    dovtools:
	      resort:
	        enabled:false`

2.main函数
>`main(String[] args){
		System.setProperty("spring.devtools.restart.enabled","false");
		Application.run(application.class);
}`







## 2.配置高级







### @ConfigurationProperties注解


**bean属性绑定（可用于第三方bena）**

* @EnableConfigurationPreperties注解可以将使用@ConfigurationProperties注解对应的类加入Spring容器

* @ConfigurationProperties注解绑定属性支持松散绑定（不区分大小写，忽略 "-"、"_"）
* 绑定前缀命名规范：仅能使用纯小写字母、数字、下划线作为合法字符
* 官方推荐格式：各个字母小写，中间用"-"分割

==注意：@EnableConfigurationPreperties与@Component不能同时使用==



解除使用@ConfigurationProperties注释警告
>`        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
       </dependency> `





### JDK8时间与空间计量单位

时间

>==@DurationUnit==*(ChronoUnit.XXX)*
>
>private Duration TimeOut;`

空间

>==@DataSizeUnit==*(**DataUnit.`*XXX*`)*
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



### 测试类中启动web环境



> ==@SpringBootTest==*(webEnvironment = SpringBootTest.WebEnvironment.XXXX)*
>
> `public class WebTest {`
>
> `}`
