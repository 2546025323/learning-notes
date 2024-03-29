# 环境准备

## 1. Docker安装

```shell
sudo yum install -y yum-utils

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable docker --now

#测试工作
docker ps
#  批量安装所有软件
docker compose  
```

创建 `/prod` 文件夹，准备以下文件

## 2. prometheus.yml

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'redis'
    static_configs:
      - targets: ['redis:6379']

  - job_name: 'kafka'
    static_configs:
      - targets: ['kafka:9092']
```

## 3. docker-compose.yml

```yaml
version: '3.9'

services:
  redis:
    image: redis:latest
    container_name: redis
    restart: always
    ports:
      - "6379:6379"
    networks:
      - backend

  zookeeper:
    image: bitnami/zookeeper:latest
    container_name: zookeeper
    restart: always
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    networks:
      - backend

  kafka:
    image: bitnami/kafka:3.4.0
    container_name: kafka
    restart: always
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      ALLOW_PLAINTEXT_LISTENER: yes
      KAFKA_CFG_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    networks:
      - backend
  
  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    container_name:  kafka-ui
    restart: always
    depends_on:
      - kafka
    ports:
      - "8080:8080"
    environment:
      KAFKA_CLUSTERS_0_NAME: dev
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
    networks:
      - backend

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: always
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    networks:
      - backend

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: always
    depends_on:
      - prometheus
    ports:
      - "3000:3000"
    networks:
      - backend

networks:
  backend:
    name: backend
```

## 4. 启动环境

```shell
docker compose -f docker-compose.yml up -d
```

# NoSQL

## 1. 场景整合

maven依赖导入

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

properties配置

```properties
spring.data.redis.host=192.168.200.100
spring.data.redis.password=123456
```

测试

```java
@Autowired
StringRedisTemplate redisTemplate;

@Test
void redisTest(){
    redisTemplate.opsForValue().set("a","1234");
    Assertions.assertEquals("1234",redisTemplate.opsForValue().get("a"));
}
```

## 2. 自动配置原理

1. META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports中导入了`RedisAutoConfiguration`、RedisReactiveAutoConfiguration和RedisRepositoriesAutoConfiguration。所有属性绑定在`RedisProperties`中

2. RedisReactiveAutoConfiguration属于响应式编程，不用管。RedisRepositoriesAutoConfiguration属于 JPA 操作，也不用管

3. RedisAutoConfiguration 配置了以下组件

   1. LettuceConnectionConfiguration： 给容器中注入了连接工厂LettuceConnectionFactory，和操作 redis 的客户端DefaultClientResources。

   2. `RedisTemplate<Object, Object>`： 可给 redis 中存储任意对象，会使用 jdk 默认序列化方式。

   3. `StringRedisTemplate`： 给 redis 中存储字符串，如果要存对象，需要开发人员自己进行序列化。key-value都是字符串进行操作··

## 3. 定制化

### 1. 序列化机制

```java
@Configuration
public class AppRedisConfiguration {


    /**
     * 允许Object类型的key-value，都可以被转为json进行存储。
     * @param redisConnectionFactory 自动配置好了连接工厂
     * @return
     */
    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        //把对象转为json字符串的序列化工具
        template.setDefaultSerializer(new GenericJackson2JsonRedisSerializer());
        return template;
    }
}
```

### 2. redis客户端

RedisTemplate、StringRedisTemplate： 操作redis的工具类

- 要从redis的连接工厂获取链接才能操作redis
- **Redis客户端**

- Lettuce： 默认
- Jedis：可以使用以下切换

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>io.lettuce</groupId>
                    <artifactId>lettuce-core</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

<!--        切换 jedis 作为操作redis的底层客户端-->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
        </dependency>
```

### 3. 配置参考

```properties
spring.data.redis.host=8.130.74.183
spring.data.redis.port=6379
#spring.data.redis.client-type=lettuce

#设置lettuce的底层参数
#spring.data.redis.lettuce.pool.enabled=true
#spring.data.redis.lettuce.pool.max-active=8

spring.data.redis.client-type=jedis
spring.data.redis.jedis.pool.enabled=true
spring.data.redis.jedis.pool.max-active=8
```

# 接口文档

> Swagger 可以快速生成**实时接口**文档，方便前后开发人员进行协调沟通。遵循 **OpenAPI** 规范。
>
> 文档：https://springdoc.org/v2/

## 1. OpenAPI  3 架构

![image.png](./assets/1681977849796-4b79ef53-8969-488d-aaa8-73fc333e8be7.webp)

## 2. 整合

maven坐标

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.1.0</version>
</dependency>
```

properties配置

```properties
# /api-docs endpoint custom path 默认 /v3/api-docs
springdoc.api-docs.path=/api-docs

# swagger 相关配置在  springdoc.swagger-ui
# swagger-ui custom path
springdoc.swagger-ui.path=/swagger-ui.html

springdoc.show-actuator=true
```

## 3. 使用

### 1. 常用注解

| 注解         | 标注位置            | 作用                   |
| ------------ | ------------------- | ---------------------- |
| @Tag         | controller 类       | 标识 controller 作用   |
| @Parameter   | 参数                | 标识参数作用           |
| @Parameters  | 参数                | 参数多重说明           |
| @Schema      | model 层的 JavaBean | 描述模型作用及每个属性 |
| @Operation   | 方法                | 描述方法作用           |
| @ApiResponse | 方法                | 描述响应状态码等       |

### 2. Docket配置

> 多个Docket分组配置

```java
  @Bean
  public GroupedOpenApi publicApi() {
      return GroupedOpenApi.builder()
              .group("springshop-public")
              .pathsToMatch("/public/**")
              .build();
  }
  @Bean
  public GroupedOpenApi adminApi() {
      return GroupedOpenApi.builder()
              .group("springshop-admin")
              .pathsToMatch("/admin/**")
              .addMethodFilter(method -> method.isAnnotationPresent(Admin.class))
              .build();
  }
```

> 只有一个Docket

```properties
springdoc.packagesToScan=package1, package2
springdoc.pathsToMatch=/v1, /api/balance/**
```

### 3. OpenAPI配置

API的一些描述信息

```java
  @Bean
  public OpenAPI springShopOpenAPI() {
      return new OpenAPI()
              .info(new Info().title("SpringShop API")
              .description("Spring shop sample application")
              .version("v0.0.1")
              .license(new License().name("Apache 2.0").url("http://springdoc.org")))
              .externalDocs(new ExternalDocumentation()
              .description("SpringShop Wiki Documentation")
              .url("https://springshop.wiki.github.org/docs"));
  }
```

# 远程调用

**RPC（Remote Procedure Call）：远程过程调用**

![image.png](./assets/1683348699620-39b5acb1-9f2a-4a7f-af3a-7829769ca567.webp)

本地过程调用：不同方法都在同一个**JVM**运行

远程过程调用：

- 服务提供者
- 服务消费者
- 通过连接对方服务器进行请求、响应交互，实现调用效果



API / SDK 的区别是什么？

- API（Application Programming Interface）：接口
  - 远程功能调用
- SDk（SoftwareDevelopment kit）：工具包
  - 导入jar包，直接调用功能即可



> ***开发过程中\****，我们经常需要调用别人写的功能*
>
> - *如果是****内部\****微服务，可以通过****依赖cloud\****、****注册中心、openfeign\****等进行调用*
> - *如果是****外部\****暴露的，可以****发送 http 请求、或遵循外部协议\****进行调用*
>
> *SpringBoot 整合提供了很多方式进行远程调用*
>
> - ***轻量级客户端方式\***
>
> - ***RestTemplate\****： 普通开发*
> - ***WebClient\****： 响应式编程开发*
> - ***Http Interface\****： 声明式编程*
>
> - ***Spring Cloud分布式\****解决方案方式*
>
> - *Spring Cloud OpenFeign*
>
> - ***第三方框架\***
>
> - *Dubbo*
> - *gRPC*
> - *...*

## 1. WebClient

> 非阻塞、响应式HTTP客户端

### 1. 创建与配置

发请求：

- 请求方式：GET \ POST \ DELETE \ xxxx
- 请求路径：/xxx
- 请求参数：aa=bb&cc==dd&xxx
- 请求头：aa=bb, cc=dd
- 请求体：

创建 WebClient 非常简单:

●WebClient.create()

●WebClient.create(String baseUrl)

还可以使用 WebClient.builder() 配置更多参数项:

●uriBuilderFactory: 自定义UriBuilderFactory ，定义 baseurl.

●defaultUriVariables: 默认 uri 变量.

●defaultHeader: 每个请求默认头.

●defaultCookie: 每个请求默认 cookie.

●defaultRequest: Consumer 自定义每个请求.

●filter: 过滤 client 发送的每个请求

●exchangeStrategies: HTTP 消息 reader/writer 自定义.

●clientConnector: HTTP client 库设置.

```java
//获取响应完整信息
WebClient client = WebClient.create("https://example.org");
```

### 2. 获取响应

> retrieve()方法用来声明如何提取响应数据。比如

```java
//获取响应完整信息
WebClient client = WebClient.create("https://example.org");

Mono<ResponseEntity<Person>> result = client.get()
        .uri("/persons/{id}", id).accept(MediaType.APPLICATION_JSON)
        .retrieve()
        .toEntity(Person.class);

//只获取body
WebClient client = WebClient.create("https://example.org");

Mono<Person> result = client.get()
        .uri("/persons/{id}", id).accept(MediaType.APPLICATION_JSON)
        .retrieve()
        .bodyToMono(Person.class);

//stream数据
Flux<Quote> result = client.get()
        .uri("/quotes").accept(MediaType.TEXT_EVENT_STREAM)
        .retrieve()
        .bodyToFlux(Quote.class);

//定义错误处理
Mono<Person> result = client.get()
        .uri("/persons/{id}", id).accept(MediaType.APPLICATION_JSON)
        .retrieve()
        .onStatus(HttpStatus::is4xxClientError, response -> ...)
        .onStatus(HttpStatus::is5xxServerError, response -> ...)
        .bodyToMono(Person.class);
```

### 3. 定义请求体

```java
//1、响应式-单个数据
Mono<Person> personMono = ... ;

Mono<Void> result = client.post()
        .uri("/persons/{id}", id)
        .contentType(MediaType.APPLICATION_JSON)
        .body(personMono, Person.class)
        .retrieve()
        .bodyToMono(Void.class);

//2、响应式-多个数据
Flux<Person> personFlux = ... ;

Mono<Void> result = client.post()
        .uri("/persons/{id}", id)
        .contentType(MediaType.APPLICATION_STREAM_JSON)
        .body(personFlux, Person.class)
        .retrieve()
        .bodyToMono(Void.class);

//3、普通对象
Person person = ... ;

Mono<Void> result = client.post()
        .uri("/persons/{id}", id)
        .contentType(MediaType.APPLICATION_JSON)
        .bodyValue(person)
        .retrieve()
        .bodyToMono(Void.class);
```

# 消息服务

https://kafka.apache.org/documentation/ 

## 消息队列-场景

### 1. 异步

![image.png](./assets/1683508559265-41439f88-8a77-421b-8072-64ac18836e14.webp)

### 2. 解耦

![image.png](./assets/1683509053856-89249929-3fd0-46c9-bef0-05fdf1d4a57a.webp)

### 3. 削峰

![image.png](./assets/1683509270156-4f382e30-4c48-48f4-be07-502178966813.webp)

### 4. 缓冲

![image.png](./assets/1683509501803-432efaf3-227a-488c-9751-67bdb8cbeb5e.webp)

## 消息队列-KafKa

### 1. 消息模式

![image.png](./assets/1682662791504-6cf21127-d9da-4602-a076-ae38c298f0ac.webp)

### 2. Kafka工作原理

![image.png](./assets/1683170677428-6ffa28b6-d522-435f-9e50-20fe3ddfd024.webp)

### 3. SpringBoot整合

参照：https://docs.spring.io/spring-kafka/docs/current/reference/html/#preface

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

配置

```properties
spring.kafka.bootstrap-servers=172.20.128.1:9092
```



修改`C:\Windows\System32\drivers\etc\hosts`文件，配置`8.130.32.70 kafka`

### 4. 消息发送

```java
@SpringBootTest
class Boot07KafkaApplicationTests {

    @Autowired
    KafkaTemplate kafkaTemplate;
    @Test
    void contextLoads() throws ExecutionException, InterruptedException {
        StopWatch watch = new StopWatch();
        watch.start();
        CompletableFuture[] futures = new CompletableFuture[10000];
        for (int i = 0; i < 10000; i++) {
            CompletableFuture send = kafkaTemplate.send("order", "order.create."+i, "订单创建了："+i);
            futures[i]=send;
        }
        CompletableFuture.allOf(futures).join();
        watch.stop();
        System.out.println("总耗时："+watch.getTotalTimeMillis());
    }

}
```

```java
@Component
public class MyBean {

    private final KafkaTemplate<String, String> kafkaTemplate;

    public MyBean(KafkaTemplate<String, String> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void someMethod() {
        this.kafkaTemplate.send("someTopic", "Hello");
    }

}
```

### 5. 消息监听

```java
@Component
public class OrderMsgListener {

    @KafkaListener(topics = "order",groupId = "order-service")
    public void listen(ConsumerRecord record){
        System.out.println("收到消息："+record); //可以监听到发给kafka的新消息，以前的拿不到
    }

    @KafkaListener(groupId = "order-service-2",topicPartitions = {
            @TopicPartition(topic = "order",partitionOffsets = {
                    @PartitionOffset(partition = "0",initialOffset = "0")
            })
    })
    public void listenAll(ConsumerRecord record){
        System.out.println("收到partion-0消息："+record);
    }
}
```

### 6. 参数配置

消费者

```properties
spring.kafka.consumer.value-deserializer=org.springframework.kafka.support.serializer.JsonDeserializer
spring.kafka.consumer.properties[spring.json.value.default.type]=com.example.Invoice
spring.kafka.consumer.properties[spring.json.trusted.packages]=com.example.main,com.example.another
```

生产者

```properties
spring.kafka.producer.value-serializer=org.springframework.kafka.support.serializer.JsonSerializer
spring.kafka.producer.properties[spring.json.add.type.headers]=false
```

### 7. 自动配置原理

kafka 自动配置在KafkaAutoConfiguration

1. 容器中放了 KafkaTemplate 可以进行消息收发
2. 容器中放了KafkaAdmin 可以进行 Kafka 的管理，比如创建 topic 等
3. kafka 的配置在KafkaProperties中
4. @EnableKafka可以开启基于注解的模式

# Web安全

- Apache Shiro
- Spring Security
- 自研：Filter

## Spring Security

### 1. 安全架构

#### 1. 认证：Authentication

> who are you?
>
> 登录系统，用户系统

#### 2. 授权：Authorization

> what are you allowed to do？
>
> 权限管理，用户授权

#### 3. 攻击防护

> - XSS（Cross-site scripting）
> - CSRF（Cross-site request forgery）
> - CORS（Cross-Origin Resource Sharing）
> - SQL注入
> - ...

#### 4. 权限模型

##### 1. RBAC（Role Based Access Controll）

> - 用户（t_user）
>
> - id,username,password，xxx
> - 1,zhangsan
> - 2,lisi 
>
> - 用户_角色（t_user_role）【N对N关系需要中间表】
>
> - zhangsan, admin
> - zhangsan,common_user
> - **lisi,** **hr**
> - **lisi, common_user**
>
> - 角色（t_role）
>
> - id,role_name
> - admin
> - hr
> - common_user
>
> - 角色_权限(t_role_perm)
>
> - admin, 文件r
> - admin, 文件w
> - admin, 文件执行
> - admin, 订单query，create,xxx
> - **hr, 文件r**
>
> - 权限（t_permission）
>
> - id,perm_id
> - 文件 r,w,x
> - 订单 query,create,xxx

##### 2. ACL（Access Controll List）

> 直接用户和权限挂钩
>
> - 用户（t_user）
>
> - zhangsan
> - lisi
>
> - 用户_权限(t_user_perm)
>
> - zhangsan,文件 r
> - zhangsan,文件 x
> - zhangsan,订单 query
>
> - 权限（t_permission）
>
> - id,perm_id
> - 文件 r,w,x
> - 订单 query,create,xxx

### 2. Spring Security 原理

#### 1. 过滤器链架构

> Spring Security 利用FilterChainProxy封装了一系列拦截器链，实现各种安全拦截功能
>
> Servlet三大组件：Servlet、Filter、Listener

<img src="./assets/1682513527616-e1e9054a-9049-4005-8c92-86392f3012a2.webp" alt="image.png" style="zoom:67%;" />

#### 2. FilterChainProxy

<img src="./assets/1682513900851-013516c0-b3d4-4a09-823a-e924a5fa8f2c.webp" alt="image.png" style="zoom:67%;" />

#### 3. SecurityFilterChain

<img src="./assets/1683548784456-8c66fd8e-783e-4f89-b81f-d3f2771a3ef9.webp" alt="image.png" style="zoom:67%;" />

### 3. 使用

#### 1. HttpSecurity

```java
@Configuration
@Order(SecurityProperties.BASIC_AUTH_ORDER - 10)
public class ApplicationConfigurerAdapter extends WebSecurityConfigurerAdapter {
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.antMatcher("/match1/**")
      .authorizeRequests()
        .antMatchers("/match1/user").hasRole("USER")
        .antMatchers("/match1/spam").hasRole("SPAM")
        .anyRequest().isAuthenticated();
  }
}
```

#### 2. MethodSecurity

```java
@SpringBootApplication
@EnableGlobalMethodSecurity(securedEnabled = true)
public class SampleSecureApplication {
}

@Service
public class MyService {

  @Secured("ROLE_USER")
  public String secure() {
    return "Hello Security";
  }

}
```

### 4. 实战

```java
@Configuration
public class AppSecurityConfiguration {

    @Bean
    SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        //请求授权
        http.authorizeHttpRequests(registry -> {
            registry.requestMatchers("/").permitAll() //1、首页所有人都允许
                    .anyRequest().authenticated(); //2、剩下的任意请求都需要 认证（登录）
        });

        //表单登录
        //3、表单登录功能：开启默认表单登录功能；Spring Security提供默认登录页
        http.formLogin(formLogin -> {
            formLogin.loginPage("/login").permitAll(); //自定义登录页位置，并且所有人都能访问
        });

        return http.build();
    }
    @Bean //查询用户详情；
    UserDetailsService userDetailsService(PasswordEncoder passwordEncoder){
        UserDetails zhangsan = User.withUsername("zhangsan")
                .password(passwordEncoder.encode("123456")) //使用密码加密器加密密码进行存储
                .roles("admin", "hr")
                .authorities("file_read", "file_write")
                .build();

        UserDetails lisi = User.withUsername("lisi")
                .password(passwordEncoder.encode("123456"))
                .roles("hr")
                .authorities("file_read")
                .build();

        UserDetails wangwu = User.withUsername("wangwu")
                .password(passwordEncoder.encode("123456"))
                .roles("admin")
                .authorities("file_write","world_exec")
                .build();

        //默认内存中保存所有用户信息
        InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager(zhangsan,lisi,wangwu);
        return manager;
    }


    @Bean //密码加密器
    PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

# 可观测性

> 可观测性Observability

对线上应用进行观测、监控、预警...

- 健康状况【组件状态、存活状态】Health
- 运行指标【cpu、内存、垃圾回收、吞吐量、响应成功率...】Metrics
- 链路追踪
- ...

## 1. SpringBoot Actuator

### 1. 实战

#### 1. 场景引入

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

#### 2. 暴露目标

```yaml
management:
  endpoints:
    enabled-by-default: true #暴露所有端点信息
    web:
      exposure:
        include: '*'  #以web方式暴露
```

#### 3. 访问数据

- 访问 [http://localhost:8080/actuator](http://localhost:8080/actuator/)；展示出所有可以用的监控端点
- http://localhost:8080/actuator/beans
- http://localhost:8080/actuator/configprops
- http://localhost:8080/actuator/metrics
- http://localhost:8080/actuator/metrics/jvm.gc.pause
- [http://localhost:8080/actuator/](http://localhost:8080/actuator/metrics)endpointName/detailPath

### 2. Endpoint

#### 1. 常用端点

| ID                 | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `auditevents`      | 暴露当前应用程序的审核事件信息。需要一个`AuditEventRepository组件`。 |
| `beans`            | 显示应用程序中所有Spring Bean的完整列表。                    |
| `caches`           | 暴露可用的缓存。                                             |
| `conditions`       | 显示自动配置的所有条件信息，包括匹配或不匹配的原因。         |
| `configprops`      | 显示所有`@ConfigurationProperties`。                         |
| `env`              | 暴露Spring的属性`ConfigurableEnvironment`                    |
| `flyway`           | 显示已应用的所有Flyway数据库迁移。 需要一个或多个`Flyway`组件。 |
| `health`           | 显示应用程序运行状况信息。                                   |
| `httptrace`        | 显示HTTP跟踪信息（默认情况下，最近100个HTTP请求-响应）。需要一个`HttpTraceRepository`组件。 |
| `info`             | 显示应用程序信息。                                           |
| `integrationgraph` | 显示Spring `integrationgraph` 。需要依赖`spring-integration-core`。 |
| `loggers`          | 显示和修改应用程序中日志的配置。                             |
| `liquibase`        | 显示已应用的所有Liquibase数据库迁移。需要一个或多个`Liquibase`组件。 |
| `metrics`          | 显示当前应用程序的“指标”信息。                               |
| `mappings`         | 显示所有`@RequestMapping`路径列表。                          |
| `scheduledtasks`   | 显示应用程序中的计划任务。                                   |
| `sessions`         | 允许从Spring Session支持的会话存储中检索和删除用户会话。需要使用Spring Session的基于Servlet的Web应用程序。 |
| `shutdown`         | 使应用程序正常关闭。默认禁用。                               |
| `startup`          | 显示由`ApplicationStartup`收集的启动步骤数据。需要使用`SpringApplication`进行配置`BufferingApplicationStartup`。 |
| `threaddump`       | 执行线程转储。                                               |
| `heapdump`         | 返回`hprof`堆转储文件。                                      |
| `jolokia`          | 通过HTTP暴露JMX bean（需要引入Jolokia，不适用于WebFlux）。需要引入依赖`jolokia-core`。 |
| `logfile`          | 返回日志文件的内容（如果已设置`logging.file.name`或`logging.file.path`属性）。支持使用HTTP`Range`标头来检索部分日志文件的内容。 |
| `prometheus`       | 以Prometheus服务器可以抓取的格式公开指标。需要依赖`micrometer-registry-prometheus`。 |

#### 2.定制端点

- 健康监控：返回存活、死亡
- 指标监控：次数、率

##### HealthEndpoint

```java
import org.springframework.boot.actuate.health.Health;
import org.springframework.boot.actuate.health.HealthIndicator;
import org.springframework.stereotype.Component;

@Component
public class MyHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        int errorCode = check(); // perform some specific health check
        if (errorCode != 0) {
            return Health.down().withDetail("Error Code", errorCode).build();
        }
        return Health.up().build();
    }

}

构建Health
Health build = Health.down()
                .withDetail("msg", "error service")
                .withDetail("code", "500")
                .withException(new RuntimeException())
                .build();
```



```yaml
management:
    health:
      enabled: true
      show-details: always #总是显示详细信息。可显示每个模块的状态信息
```



```java
@Component
public class MyComHealthIndicator extends AbstractHealthIndicator {

    /**
     * 真实的检查方法
     * @param builder
     * @throws Exception
     */
    @Override
    protected void doHealthCheck(Health.Builder builder) throws Exception {
        //mongodb。  获取连接进行测试
        Map<String,Object> map = new HashMap<>();
        // 检查完成
        if(1 == 2){
//            builder.up(); //健康
            builder.status(Status.UP);
            map.put("count",1);
            map.put("ms",100);
        }else {
//            builder.down();
            builder.status(Status.OUT_OF_SERVICE);
            map.put("err","连接超时");
            map.put("ms",3000);
        }


        builder.withDetail("code",100)
                .withDetails(map);

    }
}
```

##### MetricsEndpoint

```java
class MyService{
    Counter counter;
    public MyService(MeterRegistry meterRegistry){
         counter = meterRegistry.counter("myservice.method.running.counter");
    }

    public void hello() {
        counter.increment();
    }
}

```



## 2. 监控案例落地

> 基于 Prometheus + Grafana

<img src="./assets/1683607956768-13b9f5cb-30fc-42a2-9119-ec7570b921f0.webp" alt="image.png" style="zoom:67%;" />

### 1. 安装Prometheus + Grafana

```shell
#安装prometheus:时序数据库
docker run -p 9090:9090 -d \
-v pc:/etc/prometheus \
prom/prometheus

#安装grafana；默认账号密码 admin:admin
docker run -d --name=grafana -p 3000:3000 grafana/grafana
```

### 2. 导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
    <version>1.10.6</version>
</dependency>
```

```yaml
management:
  endpoints:
    web:
      exposure: #暴露所有监控的端点
        include: '*'
```

访问： http://localhost:8001/actuator/prometheus  验证，返回 prometheus 格式的所有指标

> 部署java应用

```shell
#安装上传工具
yum install lrzsz

# 下载openjdk
wget https://download.oracle.com/java/17/archive/jdk-17.0.5_linux-x64_bin.tar.gz

mkdir -p /opt/java
tar -xzf jdk-17.0.5_linux-x64_bin.tar.gz -C /opt/java/
sudo vi /etc/profile
#加入以下内容
export JAVA_HOME=/opt/java/jdk-17.0.5
export PATH=$PATH:$JAVA_HOME/bin

#环境变量生效
source /etc/profile


# 后台启动java应用
nohup java -jar springboot3-actuator-0.0.1-SNAPSHOT.jar > output.log 2>&1 &
```

确认可以访问到： http://8.130.32.70:9999/actuator/prometheus

### 3. 配置Prometheus 拉取数据

```yaml
## 修改 prometheus.yml 配置文件
scrape_configs:
  - job_name: 'spring-boot-actuator-exporter'
    metrics_path: '/actuator/prometheus' #指定抓取的路径
    static_configs:
      - targets: ['172.24.111.157:9999']
        labels:
          nodename: 'app-demo'
```

### 4. 配置Grafana 监控面板

- 添加数据源（Prometheus）
- 添加面板。可去 dashboard 市场找一个自己喜欢的面板，也可以自己开发面板;[Dashboards | Grafana Labs](https://grafana.com/grafana/dashboards/?plcmt=footer)

### 5. 效果

![image-20240328170233028](./assets/image-20240328170233028.png)

# AOT

## 1. AOT 与 JIT

**AOT**：Ahead-of-time（提前编译）：程序执行前，全部被编译成机器码

**JIT**：Just in Time（即时编译）：程序边编译，边运行

翻译：

- **源代码（.c、.cpp、.go、.java。。。） \==编译==  机器码**

**语言：**

- **编译**型语言：编译器
- **解释**型语言：解释器

### 1. Complier 与 Interpreter

Java：半编译半解释

https://anycodes.cn/editor

<img src="./assets/1683766057952-6f218ecf-4d0a-44ee-a930-1fc7f19085db.webp" alt="image.png" style="zoom:67%;" />

| 对比项                 | **编译器**                                         | **解释器**                                               |
| ---------------------- | -------------------------------------------------- | -------------------------------------------------------- |
| **机器执行速度**       | **快**，因为源代码只需被转换一次                   | **慢**，因为每行代码都需要被解释执行                     |
| **开发效率**           | **慢**，因为需要耗费大量时间编译                   | **快**，无需花费时间生成目标代码，更快的开发和测试       |
| **调试**               | **难以调试**编译器生成的目标代码                   | **容易调试**源代码，因为解释器一行一行地执行             |
| **可移植性（跨平台）** | 不同平台需要重新编译目标平台代码                   | 同一份源码可以跨平台执行，因为每个平台会开发对应的解释器 |
| **学习难度**           | 相对较高，需要了解源代码、编译器以及目标机器的知识 | 相对较低，无需了解机器的细节                             |
| **错误检查**           | 编译器可以在编译代码时检查错误                     | 解释器只能在执行代码时检查错误                           |
| **运行时增强**         | 无                                                 | 可以**动态增强**                                         |

### 2. AOT 与 JIT 对比

|      | JIT                                                          | AOT                                                          |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 优点 | 1.具备**实时调整**能力 2.生成**最优机器指令** 3.根据代码运行情况**优化内存占用** | 1.速度快，优化了运行时编译时间和内存消耗 2.程序初期就能达最高性能 3.加快程序启动速度 |
| 缺点 | 1.运行期边编译**速度慢** 2.初始编译不能达到**最高性能**      | 1.程序第一次编译占用时间长 2.牺牲**高级语言**一些特性        |

在 OpenJDK 的官方 Wiki 上，介绍了HotSpot 虚拟机一个相对比较全面的、**即时编译器（JIT）**中采用的[优化技术列表](https://xie.infoq.cn/link?target=https%3A%2F%2Fwiki.openjdk.java.net%2Fdisplay%2FHotSpot%2FPerformanceTacticIndex)。

<img src="./assets/1683773230399-b3fe0f68-f85a-4efb-bf38-d1783ea63d49.webp" alt="image.png" style="zoom:80%;" />

可使用：-XX:+PrintCompilation 打印JIT编译信息

### 3. JVM框架

.java \=== .class === 机器码

![未命名绘图.png](./assets/1684124528903-feb1ce8f-302a-4872-a63d-ae5da99501eb.webp)

**JVM**: 既有**解释器**，又有**编辑器（JIT：即时编译）**；

### 4. Java的执行过程

> 建议阅读：
>
> - 美团技术：https://tech.meituan.com/2020/10/22/java-jit-practice-in-meituan.html
> - openjdk官网：https://wiki.openjdk.org/display/HotSpot/Compiler

#### 1. 流程概要

<img src="./assets/1683774822566-f6860477-868e-4115-8ee9-7fe9d787e7a9.webp" alt="img" style="zoom:80%;" />

#### 2. 详细流程

<img src="./assets/1684143084003-df41f505-f8d0-4ab9-a684-5c39037e8e30.webp" alt="未命名绘图2.png" style="zoom:80%;" />

### 5. JVM编译器

JVM中集成了两种编译器，Client Compiler 和 Server Compiler；

- Client Compiler注重启动速度和局部的优化
- Server Compiler更加关注全局优化，性能更好，但由于会进行更多的全局分析，所以启动速度会慢。



Client Compiler：

- HotSpot VM带有一个Client Compiler **C1编译器**
- 这种编译器**启动速度快**，但是性能比较Server Compiler来说会差一些。
- 编译后的机器码执行效率没有C2的高



Server Compiler：

- Hotspot虚拟机中使用的Server Compiler有两种：**C2** 和 **Graal**。
- 在Hotspot VM中，默认的Server Compiler是**C2编译器。**

### 6. 分层编译

Java 7开始引入了分层编译(**Tiered Compiler**)的概念，它结合了**C1**和**C2**的优势，追求启动速度和峰值性能的一个平衡。分层编译将JVM的执行状态分为了五个层次。**五个层级**分别是：

- 解释执行。
- 执行不带profiling的C1代码。
- 执行仅带方法调用次数以及循环回边执行次数profiling的C1代码。
- 执行带所有profiling的C1代码。
- 执行C2代码。

**profiling就是收集能够反映程序执行状态的数据**。其中最基本的统计数据就是方法的调用次数，以及循环回边的执行次数。

![img](./assets/1683775747739-e428b122-ace8-4b33-b860-6a6c7ea11abd.webp)

- 图中第①条路径，代表编译的一般情况，**热点方法**从解释执行到被3层的C1编译，最后被4层的C2编译。
- 如果**方法比较小**（比如Java服务中常见的**getter/setter**方法），3层的profiling没有收集到有价值的数据，JVM就会断定该方法对于C1代码和C2代码的执行效率相同，就会执行图中第②条路径。在这种情况下，JVM会在3层编译之后，放弃进入C2编译，**直接选择用1层的C1编译运行**。
- 在**C1忙碌**的情况下，执行图中第③条路径，在解释执行过程中对程序进行**profiling** ，根据信息直接由第4层的**C2编译**。
- 前文提到C1中的执行效率是**1层>2层>3层**，**第3层**一般要比**第2层**慢35%以上，所以在**C2忙碌**的情况下，执行图中第④条路径。这时方法会被2层的C1编译，然后再被3层的C1编译，以减少方法在**3层**的执行时间。
- 如果**编译器**做了一些比较**激进的优化**，比如分支预测，在实际运行时**发现预测出错**，这时就会进行**反优化**，重新进入**解释执行**，图中第⑤条执行路径代表的就是**反优化**。

总的来说，C1的编译速度更快，C2的编译质量更高，分层编译的不同编译路径，也就是JVM根据当前服务的运行情况来寻找当前服务的最佳平衡点的一个过程。从JDK 8开始，JVM默认开启分层编译。

**云原生**：Cloud Native； Java小改版；



最好的效果：

存在的问题：

- java应用如果用jar，解释执行，热点代码才编译成机器码；初始启动速度慢，初始处理请求数量少。
- 大型云平台，要求每一种应用都必须秒级启动。每个应用都要求效率高。

希望的效果：

- java应用也能提前被编译成**机器码**，随时**急速启动**，一启动就急速运行，最高性能
- 编译成机器码的好处：

- 另外的服务器还需要安装Java环境
- 编译成**机器码**的，可以在这个平台 Windows X64 **直接运行**。



**原生**镜像：**native**-image（机器码、本地镜像）

- 把应用打包成能适配本机平台 的可执行文件（机器码、本地镜像）

## 2. GraalVM

https://www.graalvm.org/

> **GraalVM**是一个高性能的**JDK**，旨在**加速**用Java和其他JVM语言编写的**应用程序**的**执行**，同时还提供JavaScript、Python和许多其他流行语言的运行时。 
>
> **GraalVM**提供了**两种**运行**Java应用程序**的方式：
>
> - 1. 在HotSpot JVM上使用**Graal即时（JIT）编译器**
> - 2. 作为**预先编译（AOT）**的本机**可执行文件**运行（**本地镜像**）。
>
>  GraalVM的多语言能力使得在单个应用程序中混合多种编程语言成为可能，同时消除了外部语言调用的成本。

### 1. 架构

<img src="./assets/1683779334574-5040fa9c-d4cc-497a-aa2c-c937ccd2078d.webp" alt="image.png" style="zoom:67%;" />

### 2. 安装

> 跨平台提供原生镜像原理

<img src="./assets/1684149328177-6e1474c9-bec3-4b9a-afbe-b17b851f3ab1.webp" alt="image.png" style="zoom:67%;" />

#### 1. VisualStudio

https://visualstudio.microsoft.com/zh-hans/free-developer-offers/

<img src="./assets/1684143751616-6c4e0a2d-8507-452b-a6fc-8fe16d4c772d.webp" alt="image.png" style="zoom:67%;" />

<img src="./assets/1684143930294-6e36dd3d-c994-44b2-aa7c-a120b23ab044.webp" alt="image.png" style="zoom:67%;" />

<img src="./assets/1684152594737-ef7b01c4-304e-42d8-bcf7-00727014ec34.webp" alt="image.png" style="zoom:67%;" />

<img src="./assets/image-20240329192218801.png" alt="image-20240329192218801" style="zoom:67%;" />

记住你安装的地址；

#### 2. GraalVM

##### 1. 下载安装

下载 GraalVM + native-image：[Releases · graalvm/graalvm-ce-builds (github.com)](https://github.com/graalvm/graalvm-ce-builds/releases)

<img src="./assets/1684144159433-2fbcf2fa-c2f3-482f-8fee-3473f5bb0fcf.webp" alt="image.png" style="zoom:67%;" />

<img src="./assets/1684144366433-026a2e7c-a5fb-48ec-8b5a-ae390063b67e.webp" alt="image.png" style="zoom:67%;" />

##### 2. 配置

修改 JAVA_HOME 与 Path，指向新bin路径

![img](./assets/1684144739494-b078d166-5e09-421d-b237-7ee1a2c153f6.png)

<img src="./assets/1684144848621-d8577753-5a5b-402a-863b-617f43e35db1.webp" alt="image.png" style="zoom:67%;" />

验证JDK环境为GraalVM提供的即可：

<img src="./assets/1684144703862-26be3be1-dd2d-491e-8eca-2317495d77cb.webp" alt="image.png" style="zoom:67%;" />

##### 3. 依赖

安装 native-image 依赖：

1. 网络环境好：参考：https://www.graalvm.org/latest/reference-manual/native-image/#install-native-image

```shell
gu install native-image
```

2. 网络不好，使用我们下载的离线jar;`native-image-xxx.jar`文件

```shell
gu install --file native-image-installable-svm-java17-windows-amd64-22.3.2.jar
```

##### 4. 验证

```shell
native-image
```

### 3. 测试

#### 1. 创建项目

- 1. 创建普通java项目。编写HelloWorld类；

- 使用`mvn clean package`进行打包
- 确认jar包是否可以执行`java -jar xxx.jar`
- 可能需要给 `MANIFEST.MF`添加 `Main-Class: 你的主类`

#### 2. 编译镜像

- 编译为原生镜像（native-image）：使用`native-tools`终端

![image.png](./assets/1684147385110-bd82ed80-a65a-439f-b82d-fec40e40edec.webp)

```shell
#从入口开始，编译整个jar
native-image -cp boot3-15-aot-common-1.0-SNAPSHOT.jar com.atguigu.MainApplication -o Haha

#编译某个类【必须有main入口方法，否则无法编译】
native-image -cp .\classes org.example.App
```



#### 3. Linux平台测试

安装gcc等环境

```shell
yum install lrzsz
sudo yum install gcc glibc-devel zlib-devel
```

- 下载安装配置Linux下的GraalVM、native-image

- 下载：https://www.graalvm.org/downloads/
- 安装：GraalVM、native-image
- 配置：JAVA环境变量为GraalVM

```shell
tar -zxvf graalvm-ce-java17-linux-amd64-22.3.2.tar.gz -C /opt/java/

sudo vim /etc/profile
#修改以下内容
export JAVA_HOME=/opt/java/graalvm-ce-java17-22.3.2
export PATH=$PATH:$JAVA_HOME/bin

source /etc/profile
```

安装native-image

```shell
gu install --file native-image-installable-svm-java17-linux-amd64-22.3.2.jar
```

使用native-image编译jar为原生程序

```shell
native-image -cp xxx.jar org.example.App
```



## 3. SpringBoot整合

### 1. 依赖导入

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.graalvm.buildtools</groupId>
            <artifactId>native-maven-plugin</artifactId>
        </plugin>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

### 2. 生成native-image

1、运行aot提前处理命令：`mvn springboot:process-aot`

2、运行native打包：`mvn -Pnative native:build`

```shell
# 推荐加上 -Pnative
mvn -Pnative native:build -f pom.xml
```

<img src="./assets/1684152780642-b82e9976-170c-4118-bcd3-a319bc325774.webp" alt="image.png" style="zoom:67%;" />

### 3. 常见问题

可能提示如下各种错误，无法构建原生镜像，需要配置环境变量；

- 出现`cl.exe`找不到错误
- 出现乱码
- 提示`no include path set`
- 提示fatal error LNK1104: cannot open file 'LIBCMT.lib'
- 提示 LINK : fatal error LNK1104: cannot open file 'kernel32.lib'
- 提示各种其他找不到



**需要修改三个环境变量**：`Path`、`INCLUDE`、`lib`

- 1、 Path：添加如下值

- `C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.33.31629\bin\Hostx64\x64`

- 2、新建`INCLUDE`环境变量：值为

```latex
C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.33.31629\include;C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\shared;C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\ucrt;C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\um;C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\winrt
```

<img src="./assets/1684154634081-c986db1e-7ab8-4fb6-ada7-f6999570310a-1711712722271-13.png" alt="img" style="zoom:80%;" />

- 3、新建`lib`环境变量：值为

```latex
C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.33.31629\lib\x64;C:\Program Files (x86)\Windows Kits\10\Lib\10.0.19041.0\um\x64;C:\Program Files (x86)\Windows Kits\10\Lib\10.0.19041.0\ucrt\x64
```

<img src="./assets/1684156564048-2dbf1d6f-b0f0-4493-aef9-a2c1b7d22d58.webp" alt="image.png" style="zoom:80%;" />
