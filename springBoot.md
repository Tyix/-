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


#### ElasticSearch

*待学习...*



## 整合第三方技术


### 缓存


#### spring cache


* 导入缓存技术对应的starter

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

* 启用缓存

  > `@SpringBootApplication`
  > ==@EnableCaching==
  > `public class Application {
  > }`

* 设置当前操作的结果数据进入缓存

*@Cacheable/@CachePut....*



#### 缓存供应商变更：Ehcache

* 加入Ehcache坐标（缓存供应商实现）

```
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
</dependency>
```

* 缓存设定为使用Ehcache

 ```
  spring:
    cache:
      type: ehcache
      ehcache:
        config: ehcache.xml
 ```

* 提供ehcache配置文件ehcache.xml

#### 缓存供应商变更：Redis



* 加入Redis坐标（缓存供应商实现）

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

* 配置Redis服务器，缓存设定为使用Redis，设置Redis相关配置。

```
spring:
  redis:
    host: localhost
    port: 6379
  cache:
    type: redis
    redis:
      use-key-prefix: true
      key-prefix: tbl_
      time-to-live: 10s
      cache-null-values: false
```





#### 缓存供应商变更:memcached

memcached客户端选择:

* Memcached Client for Java：最早期客户端，稳定可靠，用户群广
* SpyMemcached：效率更高
* Xmemcached：并发处理更

**SpringBoot未提供对memcached的整合，需要使用硬编码方式实现客户端初始化管理**



* 加入Xmemcache坐标（缓存供应商实现）

```
<dependency>
    <groupId>com.googlecode.xmemcached</groupId>
    <artifactId>xmemcached</artifactId>
    <version>2.4.7</version>
</dependency>
```

* 配置memcached服务器必要属性

```
memcached:						
  servers: localhost:11211   #memcached服务器地址
  poolSize: 10               #连接池的数量
  opTimeout: 3000            #设置默认操作超时
```

* 创建读取属性配置信息类，加载配置

  ```
  @Component
  @ConfigurationProperties(prefix = "memcached")
  @Data
  public class XMemcachedProperties {
      private String servers;
      private Integer poolSize;
      private Long opTimeout;
  }
  ```

* 创建客户端配置类

```
@Configuration
public class XMemcachedConfig {
	@Autowired
	private XMemcachedProperties xMemcachedProperties;
@Bean
	public MemcachedClient getMemcachedClinet() throws IOException {
        MemcachedClientBuilder builder = new XMemcachedClientBuilder(xMemcachedProperties.getServers());
        MemcachedClient memcachedClient = builder.build();
        return memcachedClient;
    }
}
```

#### 缓存供应商变更:jetcache



* jetCache对SpringCache进行了封装，在原有功能基础上实现了多级缓存、缓存统计、自动刷新、异步调用、数据报表等功能

* jetCache设定了本地缓存与远程缓存的多级缓存解决方案：
   * 本地缓存（local）
      * LinkedHashMap
      * Caffeine
   * 远程缓存（remote）
      * Redis
      * Tair







* 加入jetcache坐标

```
<dependency>
    <groupId>com.alicp.jetcache</groupId>
    <artifactId>jetcache-starter-redis</artifactId>
    <version>2.6.2</version>
</dependency>
```



* 配置必要属性

```
jetcache:
  local:
    default:
      type: linkhashmap
      keyConvertor: fastjson
  remote:
    default:
      type: redis
      host: localhost
      port: 6379
      poolConfig:
        maxTotal: 50
```



* 开启jetcache注解支持

> `@SpringBootApplication`
> ==@EnableCreateCacheAnnotation==
> `public class Application {`
>     `public static void main(String[] args) {`
>         `SpringApplication.run(Application.class,args);`
>     `}`
> `}`



* 声明缓存对象

```
@CreateCache(name="XXXX",expire = 3600)
private Cache<String, String> jetSMSCache;
```









* 启用方法注解

> `@SpringBootApplication`
> `@EnableCreateCacheAnnotation`
> ==@EnableMethodCache==*(basePackages = "com.tyx")*
> `public class Application {`
>     `public static void main(String[] args) {`
>         `SpringApplication.run(Application.class,args);`
>     `}`
> `}`



* 缓存对象必须保障可序列化

  > `@Data`
  > `public class Book` ==implements Serializable== `{`
  >
  > }
  
  

* 用方法注解操作缓存 

   *@Cached/ @CacheUpdate/ @CacheInvalidate...*





#### 缓存供应商变更:j2cache



* j2cache是一个缓存整合框架，可以提供缓存的整合方案，使各种缓存搭配使用，自身不提供缓存功能



* 加入j2cache坐标，加入整合缓存的坐标

```
<dependency>
    <groupId>net.oschina.j2cache</groupId>
    <artifactId>j2cache-spring-boot2-starter</artifactId>
    <version>2.8.0-release</version>
</dependency>

<dependency>
    <groupId>net.oschina.j2cache</groupId>
    <artifactId>j2cache-core</artifactId>
    <version>2.8.4-release</version>
</dependency>

<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
</dependency>
```



* 配置使用j2cache（application.yml）

  ```
  j2cache:
    config-location: j2cache.properties
  ```



* 配置一级缓存与二级缓存以及一级缓存数据到二级缓存的发送方式（j2cache.properties）

```
# 配置1级缓存
j2cache.L1.provider_class = ehcache
ehcache.configXml = ehcache.xml

# 配置1级缓存数据到2级缓存的广播方式：可以使用redis提供的消息订阅模式，也可以使用jgroups多播实现
j2cache.broadcast = net.oschina.j2cache.cache.support.redis.SpringRedisPubSubPolicy

# 配置2级缓存
j2cache.L2.provider_class = net.oschina.j2cache.cache.support.redis.SpringRedisProvider
j2cache.L2.config_section = redis
redis.hosts = localhost:6379
```



* 设置使用缓存
  > ==@Autowired==
  > `private CacheChannel cacheChannel;`



### 任务



#### SpringBoot整合Quartz



* 工作（Job）：用于定义具体执行的工作
* 工作明细（JobDetail）：用于描述定时工作相关的信息
* 触发器（Trigger）：用于描述触发工作的规则，通常使用cron表达式定义调度规则
* 调度器（Scheduler）：描述了工作明细与触发器的对应关系





* 导入SpringBoot整合quartz的坐标

  ```
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-quartz</artifactId>
  </dependency>
  ```



* 定义具体要执行的任务，继承QuartzJobBean

```
public class QuartzTaskBean extends QuartzJobBean {
    @Override
    protected void executeInternal(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        
    }
}
```



* 定义工作明细与触发器，并绑定对应关系

```
@Configuration
public class QuartzConfig {
    @Bean
    public JobDetail printJobDetail(){
        return JobBuilder.newJob(QuartzTaskBean.class).storeDurably().build();
    }
    @Bean
    public Trigger printJobTrigger() {
        CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule("0/3 * * * * ?");
        return TriggerBuilder.newTrigger().forJob(printJobDetail()).withSchedule(cronScheduleBuilder).build();
    }
}
```



#### Spring Task



* 开启定时任务功能

```
@SpringBootApplication
@EnableScheduling
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```



* 设置定时执行的任务，并设定执行周期

  ```
  @Component
  public class Class {
  	@Scheduled(cron = "0/5 * * * * ?")
      public void method(){
      }
  }
  ```



* 定时任务相关配置

```
spring:
  task:
    scheduling:
    # 任务调度线程池大小 默认 1
      pool:
       size: 1
    # 调度线程名称前缀 默认 scheduling-
      thread-name-prefix: ssm_
      shutdown:
    # 线程池关闭时等待所有任务完成
       await-termination: false
    # 调度线程关闭前最大等待时间，确保最后一定关闭
       await-termination-period: 10s
```



### 邮件



#### SpringBoot整合JavaMail



* SMTP（Simple Mail Transfer Protocol）：简单邮件传输协议，用于发送电子邮件的传输协议
* POP3（Post Office Protocol - Version 3）：用于接收电子邮件的标准协议
* IMAP（Internet Mail Access Protocol）：互联网消息协议，是POP3的替代协议





* 导入SpringBoot整合JavaMail的坐标

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```



* 配置JavaMail

```
spring:
  mail:
  host: smtp.qq.com
  username: XXXXXXXXXX@qq.com
  password: XXXXXXXXX					（具体邮件账户设置中获得）
```



* 发送简单邮件

```
public class SendMailServiceImpl implements SendMailService{

    @Autowired
    private JavaMailSender javaMailSender;
    
    @Override
    public void sendMail() {
        SimpleMailMessage mailMessage = new SimpleMailMessage();
        mailMessage.setFrom("XXXXXXXXXX");
        mailMessage.setTo("XXXXXXXX");
        mailMessage.setSubject("XXXXXXXXXXXX");
        mailMessage.setText("XXXXXXXXXXXXX");
        javaMailSender.send(mailMessage);
    }
}
```



* 附件与HTML文本支持

  ```
  @Override
  public void sendMail() {
      try {
          MimeMessage mimeMessage = javaMailSender.createMimeMessage();
          MimeMessageHelper mimeMessageHelper = new MimeMessageHelper(mimeMessage, true);
          mimeMessageHelper.setFrom(from);
          mimeMessageHelper.setTo(to);
          mimeMessageHelper.setSubject(subject);
          mimeMessageHelper.setText("<a href='https://www.XXXX.com/'>", true);
          File file = new File("logo.png");
          mimeMessageHelper.addAttachment("美图.png", file);
          javaMailSender.send(mimeMessage);
      } catch (Exception e) {
          e.printStackTrace();
      }
  }
  ```







### 消息



#### 基本概念



* 消息发送方~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~生产者
* 消息接收方~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~消费者
* 同步消息
* 异步消息



* 企业级应用中广泛使用的三种异步消息传递技术
  * JMS
  * AMQP
  * MQTT





##### JMS

* JMS（Java Message Service）：一个规范，等同于JDBC规范，提供了与消息服务相关的API接口

* JMS消息模型
  * peer-2-peer：点对点模型，消息发送到一个队列中，队列保存消息。队列的消息只能被一个消费者消费，或超时
  * **pub**lish-**sub**scribe：发布订阅模型，消息可以被多个消费者消费，生产者和消费者完全独立，不需要感知对方的存在

* JMS消息种类

  * TextMessage

  * MapMessage

  * **BytesMessage**

  * StreamMessage

  * ObjectMessage

  * Message （只有消息头和属性）

* JMS实现：ActiveMQ、Redis、HornetMQ、RabbitMQ、RocketMQ（没有完全遵守JMS规范）








##### AMQ

* AMQP（advanced message queuing protocol）：一种协议（高级消息队列协议，也是消息代理规范），规范了网络交换的数据格式，兼容JMS

* 优点：具有跨平台性，服务器供应商，生产者，消费者可以使用不同的语言来实现

* AMQP消息模型

  * direct exchange
  * fanout exchange
  * topic exchange
  * headers exchange
  * system exchange

* AMQP消息种类：byte[]
* AMQP实现：RabbitMQ、StormMQ、RocketMQ



##### MQTT

MQTT（Message Queueing Telemetry Transport）消息队列遥测传输，专为小设备设计，是物联网（IOT）生态系统中主要成分之一







##### Kafka

Kafka，一种高吞吐量的分布式发布订阅消息系统，提供实时消息功能





#### SpringBoot整合ActiveMQ



* 导入SpringBoot整合ActiveMQ坐标

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-activemq</artifactId>
</dependency>
```

* 配置ActiveMQ（采用默认配置）

```
spring:
  activemq:
    broker-url: tcp://localhost:61616
  jms:
    pub-sub-domain: true
    template:
      default-destination: tyx
```

* 生产与消费消息

```
@Autowired
private JmsMessagingTemplate jmsMessagingTemplate;

public void send(){
    jmsMessagingTemplate.convertAndSend("destination","message");
}

public void receive(){
    jmsMessagingTemplate.receiveAndConvert("destination",XXX.class)
}
```



* 使用消息监听器对消息队列监听

```
@Component
public class MessageListener {
    @JmsListener(destination = "XXXX")
    public void receive(){
        
    }
}
```

* 流程性业务消息消费完转入下一个消息队列

```
@Component
public class MessageListener {
    @JmsListener(destination = "XXXX")
    @SendTo("otherDestination")
    public String receive(){
        return string;
    }
}
```

























## 监控





### Spring Boot Admin



* Spring Boot Admin，开源社区项目，用于管理和监控SpringBoot应用程序。 客户端注册到服务端后，通过HTTP请求方式，服务端定期从客户端获取对应的信息，并通过UI界面展示对应信息。



* Admin服务端/客户端

```
<properties>
    <spring-boot-admin/client（客户端）.version>2.7.4</spring-boot-admin/client（客户端）.version>
</properties>
<dependencies>
    <dependency>
        <groupId>de.codecentric</groupId>
        <artifactId>spring-boot-admin-starter-server/client（客户端）     </artifactId>          
    </dependency>

</dependencies>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-dependencies</artifactId>
            <version>${spring-boot-admin.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```



* 设置启用Spring-Admin（服务端）

> `@SpringBootApplication`
> ==@EnableAdminServer==
> `public class Application {`
>     `public static void main(String[] args) {`
>         `SpringApplication.run(Application.class, args);`
>     `}`
> `}`



* Admin服务端

```
server:
  port: 8080
```

* Admin客户端

```
spring:
  boot:
    admin:
      client:
        url: http://localhost:8080
management:
  endpoint:
    health:
      show-details: always
  endpoints:
      web:
        exposure:
          include: "*"
```



### 端点





* Actuator提供了SpringBoot生产就绪功能，通过端点的配置与访问，获取端点信息
* 端点描述了一组监控信息，SpringBoot提供了多个内置端点，也可以根据需要自定义端点信息
* 访问当前应用所有端点信息：/actuator
* 访问端点详细信息：/actuator/端点名称



* 启用指定端点

```
management:
  endpoint:
    health:（health必须配置）					#端点名称
      show-details: always
    info:					#端点名称
      enabled: true
```



#### 为info端点添加自定义指标

```
info:
  appName: @project.artifactId@
  version: @project.version@
  author: tyx
```



```
@Component
public class AppInfoContributor implements InfoContributor {
    @Override
    public void contribute(Info.Builder builder) {
        Map<String,Object> infoMap = new HashMap<>();
        infoMap.put("buildTime","2006");
        builder.withDetails(infoMap);
        
        builder.withDetail("runTime",System.currentTimeMillis()).withDetail("author","tyx");
    }
}
```





#### 为Health端点添加自定义指标



```
@Component
public class AppHealthContributor extends AbstractHealthIndicator {

    @Override
    protected void doHealthCheck(Health.Builder builder) throws Exception {
        boolean condition=true;
        if(condition){
            Map<String,Object> infoMap = new HashMap<>();
            infoMap.put("buildTime","2006");
            builder.withDetail("runTime",System.currentTimeMillis()).withDetail("company","传智教育");
            builder.withDetails(infoMap);
            builder.status(Status.UP);
        }else{
            builder.status(Status.DOWN);
        }
    }
}
```





#### 为Metrics端点添加自定义指标

```
@Service
public class ServiceImpl extends ServiceImpl<XXXXDao, XXXX> implements XXXXService {
    private Counter counter;
    
    public BookServiceImpl(MeterRegistry meterRegistry){
        counter = meterRegistry.counter("用户付费操作次数：");
    }
    
    @Override
    public boolean delete(Integer id) {
        counter.increment();
    }
}
```









#### 自定义监控指标



```
@Component
@Endpoint(id="pay")
public class PayEndPoint {
 @ReadOperation
 public Object getPay(){
   //调用业务操作，获取支付相关信息结果，最终return出去
    Map payMap = new HashMap();
    payMap.put("level 1",103);
    payMap.put("level 2",315);
    payMap.put("level 3",666);
    return payMap;
  }
}
```
