# Java常用注解

## 元注解

### 简介

Java元注解（Meta-Annotations）是一种特殊的注解，它们用于标注其他注解，提供对其他注解的附加信息和限制。在Java中，元注解用来修饰其他的注解类型，为注解添加元数据，定义注解的行为、生命周期等特性。

### 常用注解及示例

#### @Target

用于定义注解可以应用在哪些类型的元素上。当创建自定义注解时，使用 `@Target` 注解来指定这个注解能被应用于类、方法、字段、参数等程序元素的哪个部分。

`ElementType` 枚举类型有以下几种取值：

- `ElementType.ANNOTATION_TYPE`：表示注解可以应用于另一个注解声明上。
- `ElementType.CONSTRUCTOR`：表示注解可以应用于构造器声明上。
- `ElementType.FIELD`：表示注解可以应用于字段声明上（包括枚举常量）。
- `ElementType.LOCAL_VARIABLE`：表示注解可以应用于局部变量声明上。
- `ElementType.METHOD`：表示注解可以应用于方法声明上。
- `ElementType.PACKAGE`：表示注解可以应用于包声明上。
- `ElementType.PARAMETER`：表示注解可以应用于方法或构造器参数声明上。
- `ElementType.TYPE`：表示注解可以应用于类、接口（包括注解类型）或枚举声明上。

示例

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE) // 可以应用于类或接口
public @interface CustomAnnotationForType {
    // ...
}
```

#### @Retention

指定了注解的存在阶段，是仅在源代码中存在（SOURCE）、编译时存在并记录到class文件中（CLASS），还是运行时仍然可被JVM读取（RUNTIME）。

示例

```java
// 定义一个仅在源码级别有效的注解
@Retention(RetentionPolicy.SOURCE)
public @interface SourceOnly {}

// 定义一个编译时可见但运行时不可见的注解
@Retention(RetentionPolicy.CLASS)
public @interface CompileTimeOnly {}

// 定义一个编译时和运行时都可见的注解
@Retention(RetentionPolicy.RUNTIME)
public @interface RuntimeVisible {}
```



## Spring

### 简介

Spring 是一个开源的企业级 Java 应用程序框架，由 Rod Johnson 创建并在 2003 年首次发布。Spring 主要解决了企业应用开发中的复杂性问题，提倡轻量级、松耦合的架构理念，并提供了全面的解决方案。

### maven坐标

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!-- Spring Boot版本通常由父POM管理 -->
</dependency>
```

### 常用注解及示例

#### @Autowired

`@Autowired` 是 Spring 框架中的一个注解，用于处理自动装配 Bean（即依赖注入，Dependency Injection）。当我们在类字段、构造器或者方法上使用 `@Autowired` 注解时，Spring 容器会尝试自动匹配并注入该类型（或具有限定条件的接口实现类）的 Bean。

```java
@Service
public class UserService {
	//字段注入
	//UserRepository 类型的 Bean 将会被 Spring 自动注入到 UserService 类的 userRepository 字段中。
    @Autowired
    private UserRepository userRepository;
}
```

```java
@Service
public class UserService {

    private final UserRepository userRepository;
    //构造器注入
	//Spring 容器会在创建 UserService 实例时通过构造函数自动注入 UserRepository。
    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

```java
@Service
public class UserService {

    private UserRepository userRepository;
    //方法注入
	//Spring 会调用带有 @Autowired 的 setter 方法注入 UserRepository。
    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

#### @Service

`@Service` 是 Spring Framework 中的一个注解，它属于 Spring 的组件扫描（Component Scanning）和依赖注入（Dependency Injection，DI）体系。在类级别上使用 `@Service` 注解，可以指示 Spring 容器将该类视为一个服务层（Service Layer）的 Bean，从而对其进行实例化、管理生命周期，并可以通过 DI 方式注入到其他需要它的 Bean 中。

在 Spring 的事务管理中，通常会对标有 `@Service` 注解的类的方法进行@Transactional事务增强处理。

示例

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public User findById(Long id) {
        // ...
    }

    @Transactional
    public void updateUser(User user) {
        // ...
    }
}
```

#### @Repository

`@Repository` 是 Spring Framework 中的一个注解，它用于标记持久层（Repository 或 DAO，Data Access Object）的类。这个注解不仅告诉 Spring 容器这是一个需要管理的组件（类似 `@Component`）

示例

```java
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // 自定义查询方法
    User findByUsername(String username);
}
```

#### @Component

`@Component` 是 Spring Framework 中的一个注解，它是 Spring 的组件扫描（Component Scan）机制的基础。在类上使用 `@Component` 注解，表明此类是一个由 Spring 容器管理的组件，Spring 容器会自动检测并将其纳入管理，可以在运行时通过依赖注入（Dependency Injection，DI）的方式使用。

`@Component` 是 Spring 中最基础的注解，其他几个常见的注解如 `@Service`、`@Repository` 和 `@Controller` 都是它的扩展，它们分别对应服务层、数据访问层和控制层的组件。尽管如此，你仍然可以直接使用 `@Component` 注解来标记任意需要由 Spring 管理的类。

示例

```java
import org.springframework.stereotype.Component;

@Component
public class CustomService {

    // ...
    public String performAction() {
        // ...
    }
}
```

#### @Transactional

`@Transactional` 是 Spring Framework 提供的一个注解，用于管理数据库事务。当在类或方法上使用此注解时，Spring 会通过 AOP（面向切面编程）技术，在方法执行前后自动开启、提交或回滚事务。

示例

```java
@Service
@Transactional
public class UserService {

    @Autowired
    private UserRepository userRepository;

    @Transactional(readOnly = true)
    public User findById(Long id) {
        // 读取用户信息，这是一个只读事务
        return userRepository.findById(id).orElse(null);
    }

    @Transactional(rollbackFor = SomeBusinessException.class)
    public void updateUser(User user) {
        // 更新用户信息，如果抛出SomeBusinessException异常，将自动回滚事务
        userRepository.save(user);
    }
}
```

## Spring AOP

### 简介



![](assets/img025.png)



### maven坐标

### 常用注解及示例

#### @Aspect

是来自 Spring AOP（面向切面编程）框架中的注解，它用于定义一个切面（Aspect），即包含跨越多个对象的横切关注点（cross-cutting concern）的模块化部分。在Spring中，AOP通过代理模式实现，允许开发者将诸如日志记录、事务管理、权限检查等通用功能从业务逻辑中分离出来，并集中处理。

当一个类被标记为 `@Aspect`，它就变成了一个切面类，其中可以定义多个通知（advice），比如前置通知（Before advice）、后置通知（After advice）、返回通知（After returning advice）、异常通知（After throwing advice）和环绕通知（Around advice）。这些通知可以在连接点（join point）上执行，连接点通常是指方法调用。

示例

```java
@Aspect
public class LoggingAspect {

    @Before("execution(* com.example.service.MyService.*(..))")
    public void beforeMethod(JoinPoint joinPoint) {
        // 在执行MyService类下的任何方法前进行日志记录
        System.out.println("Executing: " + joinPoint.getSignature().getName());
    }

    // 其他通知定义...
}
```

#### @Before

**前置通知（Before advice）**： 使用 `@Before` 注解定义。在目标方法执行前被调用，但不能阻止方法的执行。如果前置通知抛出异常，则会阻止目标方法的执行。

示例

```java
@Before("execution(* com.example.service.MyService.*(..))")
public void beforeAdvice(JoinPoint joinPoint) {
    System.out.println("前置通知: 准备执行 " + joinPoint.getSignature().getName());
}
```

#### @AfterReturning

#### @AfterThrowing

**后置通知（After advice）**： 在 Spring AOP 中并没有单独的 `@After` 注解，但是可以使用 `@AfterReturning` 和 `@AfterThrowing` 来分别处理方法正常返回和抛出异常后的操作。

示例

```java
//返回通知（After returning advice）：使用 @AfterReturning 注解定义。在目标方法成功执行并返回后被调用。
@AfterReturning(pointcut = "execution(* com.example.service.MyService.*(..))", returning = "result")
public void afterReturningAdvice(JoinPoint joinPoint, Object result) {
    System.out.println("返回通知: 方法 " + joinPoint.getSignature().getName() + " 执行完毕，结果是 " + result);
}

//异常通知（After throwing advice）：使用 @AfterThrowing 注解定义。在目标方法抛出异常后被调用。
@AfterThrowing(pointcut = "execution(* com.example.service.MyService.*(..))", throwing = "ex")
public void afterThrowingAdvice(JoinPoint joinPoint, Exception ex) {
    System.out.println("异常通知: 方法 " + joinPoint.getSignature().getName() + " 抛出了异常 " + ex.getClass().getName());
}
```

#### @Around

**环绕通知（Around advice）**： 使用 `@Around` 注解定义。环绕通知是最强大的通知类型，它可以在方法执行前后添加自定义行为，并可以选择是否继续执行目标方法。环绕通知需要返回一个 `proceedingJoinPoint.proceed()` 的值，这将触发实际的目标方法执行。

示例

```java
@Around("execution(* com.example.service.MyService.*(..))")
public Object aroundAdvice(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
    System.out.println("环绕通知: 开始执行 " + proceedingJoinPoint.getSignature().getName());

    // 执行目标方法之前的操作

    Object result = null;
    try {
        // 调用 proceed() 方法执行目标方法
        result = proceedingJoinPoint.proceed();
    } catch (Throwable throwable) {
        // 处理目标方法抛出的异常
    }

    // 执行目标方法之后的操作

    System.out.println("环绕通知: 结束执行 " + proceedingJoinPoint.getSignature().getName());
    return result;
}
```

#### @Pointcut

**@Pointcut** 注解用于定义一个切入点（Pointcut），它是一个或多个通知（Advice）可以应用到的一组连接点（Join Point）。通过定义切入点表达式，我们可以清晰地声明哪些方法或者类上的哪些行为是我们关注的横切关注点（Cross-cutting Concerns）。

示例

```java
@Aspect
public class LoggingAspect {

    // 定义一个切入点表达式
    @Pointcut("execution(* com.example.service.*Service.*(..))")
    public void businessService() {
        // 这个方法体可以为空，主要作用是定义了一个名称为businessService的切入点
    }

    // 使用上面定义的切入点
    @Before("businessService()")
    public void beforeMethod(JoinPoint joinPoint) {
        System.out.println("前置通知: 准备执行 " + joinPoint.getSignature().getName());
    }

    // 其他通知...
}
```



## SpringMVC

### 简介

Spring MVC（Spring Model-View-Controller）是Spring Framework中专门用于构建Web应用程序的一个模块，它遵循MVC设计模式，提供了一种强大而灵活的机制来开发Java EE环境下的Web应用。Spring MVC主要目标是实现Web层的职责分离，包括模型(Model)、视图(View)和控制器(Controller)三个核心组成部分

### maven坐标

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!-- 注意：这里通常不需要指定版本号，因为在使用Spring Boot时会通过父POM继承 -->
    <!-- 如果不是Spring Boot项目，请确认使用的Spring Framework和相关依赖的版本 -->
</dependency>
```

### 常用注解及示例

#### @Controller

`@Controller` 是 Spring MVC 框架中的一个注解，用于标记一个类作为 Spring MVC 控制器（Controller）。在 Spring Web 应用程序中，控制器负责处理用户的 HTTP 请求，并决定如何响应这些请求。

当 Spring 容器启动并进行组件扫描时，会自动识别并注册所有带有 `@Controller` 注解的类。这些类中的方法可以通过 `@RequestMapping` 等注解映射到不同的 HTTP 请求路径，进而处理这些请求。

控制器方法通常会返回一个 `ModelAndView` 对象、字符串（代表视图名称）或 `ResponseEntity` 对象，也可以直接返回一个对象，配合 `@ResponseBody` 或 `@RestController` 注解将对象转换为 JSON 或其他格式的数据直接响应给客户端。

示例

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class DemoController {

    @GetMapping("/hello")
    public String hello() {
        // 此方法返回字符串，Spring MVC 会寻找一个与此字符串名称相对应的视图进行渲染
        return "hello";  // 假设有一个名为 "hello.jsp" 或 "hello.html" 的视图文件
    }

    @GetMapping("/greeting")
    @ResponseBody
    public Greeting greeting() {
        // 此方法返回一个对象，并配合 @ResponseBody 注解，Spring MVC 会将对象转换为 JSON 并写入 HTTP 响应体
        return new Greeting("Hello, World!");
    }
}

class Greeting {
    private String message;

    public Greeting(String message) {
        this.message = message;
    }

    // getters and setters...
}
```

#### @RestController

@RestController时@Controller、@ResponseBody两个注解的组合注解。myRestControllerAlias并不是真正意义上的别名，它不能改变URL映射，只是在Spring MVC上下文中提供了该Controller bean的另一个名称。如果不添加("myRestControllerAlias")，则MyRestController类在Spring MVC的上下文中的名称默认为首字母小写，其他不变，即myRestController。

示例

```Java
@RestController("myRestControllerAlias")
public class MyRestController {

    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        // 返回一个User对象，会被自动转化为JSON格式的HTTP响应体
        return userService.getUserById(id);
    }
}
```

#### @RequestMapping

@RequestMapping 是Spring MVC框架中的一个重要注解，用于映射HTTP请求到具体的处理器方法上。它可以根据请求的不同特征（如URI路径、HTTP方法、请求参数等）将请求路由至对应的方法。如果加在类上，则所有的方法的HTTP请求映射路径都加上前缀，比如`hello()`的HTTP映射路径变为`/head/hello`。默认method为GET。

示例

```java
@RestController
@RequestMapping("/head")
public class MyController {

    @RequestMapping("/hello") //将映射所有GET请求到根路径 /hello 上。
    public String hello() {
        return "Hello World!";
    }

    //将映射所有GET请求到 /greeting 路径上。
    @RequestMapping(value = "/greeting", method = RequestMethod.GET)
    public String greeting() {
        return "Greetings!";
    }
```

派生注解

**@GetMapping**： 用于处理 GET 类型的 HTTP 请求。

**@PostMapping**： 用于处理 POST 类型的 HTTP 请求，通常用于创建资源。

**@PutMapping**： 用于处理 PUT 类型的 HTTP 请求，通常用于更新整个资源。

**@DeleteMapping**： 用于处理 DELETE 类型的 HTTP 请求，用于删除资源。

**@PatchMapping**： 用于处理 PATCH 类型的 HTTP 请求，通常用于更新资源的部分内容。

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) { ... }

@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody User user) { ... }

@PutMapping("/users/{id}")
public ResponseEntity<Void> updateUser(@PathVariable Long id, @RequestBody User updatedUser) { ... }

@DeleteMapping("/users/{id}")
public ResponseEntity<Void> deleteUser(@PathVariable Long id) { ... }

@PatchMapping("/users/{id}")
public ResponseEntity<Void> patchUser(@PathVariable Long id, @RequestBody User partialUpdate) { ... }
```

#### @RequestBody

`@RequestBody` 是一个注解，广泛应用于基于 Spring MVC 架构的 RESTful API 开发中，特别是与 Spring Boot 结合时。这个注解用于处理 HTTP 请求体中的数据，将请求体内容绑定到控制器方法的入参对象上。

当客户端发送一个 POST、PUT 或 PATCH 请求，并在请求体中包含 JSON 或 XML 格式的数据时，`@RequestBody` 注解可以帮助框架将这些数据反序列化成 Java 对象，这样开发者就可以直接操作这些对象进行业务逻辑处理。

```java
@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody User user) {
    // 这里的 User 类将会被请求体中的 JSON 或 XML 数据填充
    userService.save(user);
    return ResponseEntity.ok(user);
}
```

#### @RequestParam

`@RequestParam` 是 Spring MVC 中用于处理 HTTP 请求参数的注解。当客户端通过 URL 查询字符串或 HTTP 请求体（仅限POST方法且content-type为application/x-www-form-urlencoded或multipart/form-data时）发送参数时，`@RequestParam` 注解可以帮助将这些参数值绑定到控制器方法的参数上。

示例

```java
@GetMapping("/users")
public User findUser(@RequestParam("id") Long userId) {
    // 获取URL中形如 /users?id=123 的参数值
    return userService.getUserById(userId);
}

@PostMapping("/users/search")
public User searchUser(@RequestParam("username") String username) {
    // 获取POST请求体中名为username的参数值
    return userService.findUserByUsername(username);
}
```

`@RequestParam` 注解还有一些可选的属性，例如：

- `required`：默认为 true，表示该参数必须存在于请求中。如果设为 false，则请求中可以不包含此参数。
- `defaultValue`：设置默认值，当请求中未提供该参数时使用。
- `value`：用于指定请求参数的名称，如果不指定，默认使用方法参数名作为请求参数名。

#### @PathVariable

`@PathVariable` 是 Spring MVC 中的一个注解，用于处理 URI 路径中的动态部分（占位符），将这些动态部分的值绑定到控制器方法的参数上。

在定义 RESTful API 路由时，我们经常需要使用动态路径参数来标识资源，例如 `/users/{userId}` 中的 `{userId}`。`@PathVariable` 注解可以帮助我们将这个 `{userId}` 的值映射到方法参数中。

示例

```java
@GetMapping("/users/{userId}")
public User getUser(@PathVariable Long userId) {
    // 这里的 userId 将会从请求的 URI 路径中获取，例如：/users/123
    return userService.getUserById(userId);
}
```

## SpringBoot

### 简介

### maven坐标

### 常用注解及示例

#### @SpringBootApplication

`@SpringBootApplication` 是 Spring Boot 框架中一个重要的注解，它是一个复合注解，包含了以下三个注解：

1. `@SpringBootConfiguration`：表示这是一个 Spring 配置类，与传统的 `@Configuration` 注解作用相同，用于创建 Bean 定义。
2. `@EnableAutoConfiguration`：启用自动配置功能，这是 Spring Boot 的核心特性之一。它会根据项目类路径中的依赖、项目的Java版本以及其他一些因素，自动配置Spring容器以满足应用的基本功能需求，如数据库连接、Web服务器配置等。
3. `@ComponentScan`（默认扫描 basePackages = {"com.example.myapp"}）：启用组件扫描，查找并注册带有 `@Component`、`@Service`、`@Repository` 或 `@Controller` 等注解的类到 Spring 容器中。

在 Spring Boot 应用程序中，通常只需要在主启动类上添加 `@SpringBootApplication` 注解即可快速搭建起一个基于 Spring Boot 的应用程序。

示例

```java
@SpringBootApplication
public class MyAppApplication {

    public static void main(String[] args) {
        SpringApplication.run(MyAppApplication.class, args);
    }
}
```



## MyBatis / MyBatisPlus

### 简介

### maven坐标

### 常用注解及示例

#### @Mapper

`@Mapper` 注解通常在 MyBatis 或 MyBatis-Plus 等持久层框架中使用，它用于标记一个接口为数据访问对象（DAO, Data Access Object）接口，也就是映射接口，这类接口中定义了与数据库交互的各种 SQL 查询方法。

当一个接口被标记为 `@Mapper` 后，MyBatis 框架会自动为其生成实现类，并处理 SQL 映射关系。在 Spring 环境下，还需要在 Spring 容器中注册这些 Mapper 接口，通常使用 MyBatis-Spring 或 MyBatis-Plus-SpringBoot-Starter 的自动配置来实现。

示例

```java
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface UserMapper {
    User selectById(Long id);
    void insert(User user);
    void update(User user);
    void deleteById(Long id);
}
```

#### @Select

用于在 Mapper 接口的方法上定义 SQL 查询语句。

示例

```java
@Select("SELECT * FROM users WHERE id = #{id}")
User selectUserById(Long id);
```

#### @Insert

用于定义插入数据的 SQL 语句。

示例

```java
@Insert("INSERT INTO users(username, password) VALUES(#{username}, #{password})")
@Options(useGeneratedKeys = true, keyProperty = "id") // 使用生成的主键值，并将其注入到对象的 id 属性
int insertUser(User user);
```

#### @Update

用于定义更新数据的 SQL 语句。

示例

```java
@Update("UPDATE users SET username = #{username}, password = #{password} WHERE id = #{id}")
int updateUser(User user);
```

#### @Delete

用于定义删除数据的 SQL 语句。

示例

```java
@Select("SELECT * FROM users WHERE id = #{id}")
@ResultMap("userResultMap")
User selectUserById(Long id);
```

#### @ResultMap

引用已定义的结果映射（ResultMap），用于自定义结果集与 Java 对象的映射关系。

示例

```java
@Select("SELECT * FROM users WHERE id = #{id}")
@ResultMap("userResultMap")
User selectUserById(Long id);
```

#### @Results、@Result

在方法级别直接定义结果映射，用于处理复杂查询结果。

示例

```java
@Select("SELECT u.*, r.role_name FROM users u JOIN roles r ON u.role_id = r.id WHERE u.id = #{id}")
@Results({
    @Result(property = "id", column = "u.id"),
    @Result(property = "username", column = "u.username"),
    @Result(property = "roleName", column = "r.role_name")
})
User getUserWithRoleInfo(Long id);
```

#### @Options

`@Options` 注解通常与 `@Insert`, `@Update`, `@Select`, 和 `@Delete` 等 SQL 映射注解配合使用，用于配置 SQL 执行时的更多选项。这个注解可以帮助开发者定制如是否自动获取生成主键、缓存策略、批处理大小等行为。

示例

```java
@Insert("INSERT INTO users(username, password) VALUES(#{username}, #{password})")
@Options(useGeneratedKeys = true, keyProperty = "id") // 使用生成的主键值，并将其注入到对象的 id 属性
int insertUser(User user);
```

#### @Param

`@Param` 注解用于方法参数上，以明确指定参数的名称，这样在 SQL 语句中就可以通过该名称来引用参数。这在处理多参数或复杂参数时非常有用，尤其是当参数类型不是简单类型，而是复合对象或者 Map 的时候。

示例

```java
public interface UserMapper {
    @Select("SELECT * FROM users WHERE username = #{username}")
    User findByUsername(@Param("username") String username);

    @Select("SELECT * FROM orders WHERE user_id = #{user.id} AND status = #{status}")
    List<Order> findOrdersByUserAndStatus(@Param("user") User user, @Param("status") String status);
    
    @Update("UPDATE users SET password = #{newPassword} WHERE id = #{user.id}")
    int updatePassword(@Param("user") User user, @Param("newPassword") String newPassword);
}
```



## Lombok

### 简介

Lombok 是一个Java库，它通过注解的方式帮助程序员消除Java类中大量的样板代码，尤其是对于getter/setter、构造函数、toString、equals/hashCode等方法的实现。

### maven坐标

```xml
<dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <!-- 替换下面的版本号为最新稳定版 -->
        <version>1.18.24</version>
        <!-- 对于IDEA等一些IDE，可以设置为provided，因为IDE本身已经集成了Lombok插件 -->
        <scope>provided</scope>
</dependency>
```

### 常用注解

##### @Getter / @Setter

- 用于字段上方，自动生成对应的getter和setter方法。

##### @ToString

- 放在类上，会自动生成`toString()`方法，包含类的所有字段。

##### @EqualsAndHashCode

- 放在类上，自动生成`equals()`和`hashCode()`方法，用于对象相等性判断和哈希表操作。可以通过`exclude`或`of`属性排除某些字段参与生成。

##### @NoArgsConstructor / @AllArgsConstructor / @RequiredArgsConstructor

- `@NoArgsConstructor`：生成无参构造函数。
- `@AllArgsConstructor`：生成包含所有字段的构造函数。
- `@RequiredArgsConstructor`：生成包含所有final和非null修饰的字段的构造函数。

##### @Data

- 组合注解，相当于同时应用了`@ToString`, `@EqualsAndHashCode`, `@Getter`, `@Setter`以及`@RequiredArgsConstructor`（如果存在任何final或非null字段的话）。通常用于简化数据传输对象（DTO）或POJO类的创建。

##### @Value

- 类似于`@Data`，但它产生的类是不可变的（所有字段都是final并且私有的），并且默认不会有setter方法，同时也生成equals和hashCode方法。

##### @Builder

- 为类生成一个内部 Builder 类，允许通过链式调用的方式来构建对象实例，尤其适用于具有大量可选参数的类。

##### @Slf4j / @Log4j / @Log4j2 / @XSlf4j / @CommonsLog

- 提供日志记录功能，根据不同的注解生成对应的日志框架（如SLF4J、Apache Log4j、Java Util Logging等）的日志实例。

##### @SneakyThrows

- 用于方法上，允许方法内部的受检异常无声地转换为未受检异常，即不会强制要求在方法签名上声明throws子句。

### 示例

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class OrderOverViewVO implements Serializable {
    //待接单数量
    private Integer waitingOrders;

    //待派送数量
    private Integer deliveredOrders;

    //已完成数量
    private Integer completedOrders;

    //已取消数量
    private Integer cancelledOrders;

    //全部订单
    private Integer allOrders;
}
```

## Swagger

### 简介

Swagger是一套流行的API开发工具，通过使用Swagger的注解，可以在Java代码中直接描述API规范，以便生成清晰的API文档和交互式的API界面。交互界面网址http://localhost:8080/doc.html

### maven坐标

```xml
<!-- Swagger 2 with Springfox -->
<!-- 使用Swagger 2时，通常配合Springfox库 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <!-- 替换为Swagger 2的最新稳定版本 -->
    <version>2.9.2</version>
</dependency>

<!-- 以及 Swagger UI for visualization -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

```xml
<!-- Swagger 3 (OpenAPI 3) with springdoc-openapi -->
<!--这个依赖包含了对OpenAPI 3的支持以及Swagger UI，所以只需要这一项依赖就可以同时生成OpenAPI规范文档和可视化界面。-->
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <!-- 替换为OpenAPI 3的最新稳定版本 -->
    <version>1.6.x</version> <!-- 查看最新版本并替换 -->
</dependency>
```

### 配置类

在 Spring Boot 应用中创建 Swagger 配置类并启用 Swagger

```java
@Configuration
@EnableSwagger2WebMvc // 对于 Springfox v2.x 版本
// 或者对于 Springfox v3.x 版本
@EnableSwaggerUiWebFlux // 对于 WebFlux 应用
@EnableSwagger2WebMvc // 对于 Spring MVC 应用
public class SwaggerConfig {
    // ...
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
            .select()
            .apis(RequestHandlerSelectors.any())
            .paths(PathSelectors.any())
            .build();
    }
    //示例
    @Bean
    public Docket docket1() {
        ApiInfo apiInfo = new ApiInfoBuilder()
                .title("苍穹外卖项目接口文档")
                .version("2.0")
                .description("苍穹外卖项目接口文档")
                .build();
        Docket docket = new Docket(DocumentationType.SWAGGER_2)
                .groupName("管理端接口")
                .apiInfo(apiInfo)
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.sky.controller.admin"))
                .paths(PathSelectors.any())
                .build();
        return docket;
    }
}
```

### 常用注解

#### Swagger2

##### @Api

- 用于标记一个类，表示一个RESTful API资源，可以提供资源的简短描述、API组、版本等信息。

```java
@Api(value = "用户管理API", description = "用户相关的增删改查操作", tags = {"用户管理"})
public class UserController {
    // ...
}
```

##### @ApiOperation

- 用于标记在Controller类的方法上，说明一个具体的HTTP请求的操作。

```java
@ApiOperation(value = "获取用户信息", notes = "通过用户ID获取用户详细信息", httpMethod = "GET")
@GetMapping("/{id}")
public User getUser(@PathVariable Long id) {
    // ...
}
```

##### @ApiParam

- 用于方法参数上，描述参数的信息。

```java
@GetMapping("/{id}")
@ApiOperation("获取用户信息")
public User getUser(
    @ApiParam(name = "id", value = "用户ID", required = true, example = "1") 
    @PathVariable Long id) {
    // ...
}
```

##### @ApiResponses

- 用于描述方法可能抛出的HTTP响应，可以包含多个@ApiResponse。

```java
@ApiResponses({
    @ApiResponse(code = 200, message = "成功", response = User.class),
    @ApiResponse(code = 404, message = "用户未找到")
})
```

##### @ApiModelProperty

- 用于类的字段上，描述实体类属性的信息。

```java
public class User {
    @ApiModelProperty(value = "用户ID", example = "1", required = true)
    private Long id;
    // ...
}
```

#### Swagger3

##### @OpenAPIDefinition

- 定义整个API的基本信息，如标题、描述、版本等。

```java
@OpenAPIDefinition(info = @Info(title = "用户管理API", version = "1.0", description = "用户管理相关的API接口"))
```

##### @Tag

替换 Swagger 2 中的 `@Api`，用于标记并分类API资源。

```java
@Tag(name = "用户管理", description = "用户相关的增删改查操作")
public class UserController {
    // ...
}
```

##### @Operation

- 替换 `@ApiOperation`，用于描述单个HTTP操作（GET、POST等）。

```java
@GetMapping("/{id}")
@Operation(summary = "获取用户信息", description = "通过用户ID获取用户详细信息")
public User getUser(@Parameter(description = "用户ID", required = true) @PathVariable Long id) {
    // ...
}
```

##### @Parameter 和 @RequestBody

- 用于描述方法参数，包括路径参数、查询参数、请求体等。

```java
@Parameter(name = "id", in = ParameterIn.PATH, description = "用户ID", required = true, schema = @Schema(type = "integer"))

@RequestBody(content = @Content(schema = @Schema(implementation = CreateUserRequest.class)))
```

##### @ApiResponse

- 继续保留，用于描述HTTP响应状态码及其对应的消息体。

```java
@ApiResponse(responseCode = "200", description = "成功", content = @Content(schema = @Schema(implementation = User.class)))
@ApiResponse(responseCode = "404", description = "用户未找到")
```

##### @Schema

- 用于类级别的字段注解，替换 `@ApiModelProperty`，不仅用于方法参数，也用于响应体和请求体的模式定义。

```java
public class User {
    @Schema(description = "用户ID", example = "1", required = true)
    private Long id;
    // ...
}
```

##### @Hidden

- 如果不希望某个字段出现在API文档中，可以使用此注解隐藏它。