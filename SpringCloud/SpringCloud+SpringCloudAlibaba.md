# SpringCloud + SpringCloudAlibaba

## 1.版本选型

### Springboot版本选择

[git源码地址](https://github.com/spring-projects/spring-boot/releases/)

[Springboot3 Release-Notes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Release-Notes)

### Springcloud版本选择

[git源码地址](https://github.com/spring-cloud)

Cloud命名规则：Spring Cloud 采用了英国伦敦地铁站的名称来命名，并由地铁站名称字母A-Z依次类推的形式来发布迭代版本（已被废弃）。

[SpringCloud官网](https://spring.io/projects/spring-cloud#overview)

### Springcloud Alibaba版本选择

[github版本说明](https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)

[Springcloud Alibaba参考手册](https://spring-cloud-alibaba-group.github.io/github-pages/2022/zh-cn/2022.0.0.0-RC2.html)

## 2.Cloud各种组件的停更/升级/替换

SpringCloud的作用：让程序员专注于业务逻辑，其他的交给第三方。

<img src="./assets/image-20240429110710796.png" alt="image-20240429110710796" style="zoom:67%;" />

## 3.微服务架构编码Base工程模块构建

使用框架进行java开发，一定是**约定 > 配置 > 编码**。

### 1. idea新建Project和Maven父工程

#### 创建maven父工程步骤

1. New Project

<img src="./assets/image-20240509194536212.png" alt="image-20240509194536212" style="zoom:67%;" />

2.聚合总父工程名字

<img src="./assets/image-20240509194618812.png" alt="image-20240509194618812" style="zoom:67%;" />

3.字符编码

<img src="./assets/image-20240509194628718.png" alt="image-20240509194628718" style="zoom:67%;" />

4.注解生效激活

<img src="./assets/image-20240509194643733.png" alt="image-20240509194643733" style="zoom:67%;" />

5.java编译版本选择

<img src="./assets/image-20240509194655048.png" alt="image-20240509194655048" style="zoom:67%;" />

6.FileType过滤

<img src="./assets/image-20240509194715894.png" alt="image-20240509194715894" style="zoom:67%;" />

#### 父工程pom文件内容

```xml
 搜索

便笺
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.atguigu.cloud</groupId>
    <artifactId>mscloudV5</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <hutool.version>5.8.22</hutool.version>
        <lombok.version>1.18.26</lombok.version>
        <druid.version>1.1.20</druid.version>
        <mybatis.springboot.version>3.0.2</mybatis.springboot.version>
        <mysql.version>8.0.11</mysql.version>
        <swagger3.version>2.2.0</swagger3.version>
        <mapper.version>4.2.3</mapper.version>
        <fastjson2.version>2.0.40</fastjson2.version>
        <persistence-api.version>1.0.2</persistence-api.version>
        <spring.boot.test.version>3.1.5</spring.boot.test.version>
        <spring.boot.version>3.2.0</spring.boot.version>
        <spring.cloud.version>2023.0.0</spring.cloud.version>
        <spring.cloud.alibaba.version>2022.0.0.0-RC2</spring.cloud.alibaba.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <!--springboot 3.2.0-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-parent</artifactId>
                <version>${spring.boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--springcloud 2023.0.0-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring.cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--springcloud alibaba 2022.0.0.0-RC2-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring.cloud.alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--SpringBoot集成mybatis-->
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.springboot.version}</version>
            </dependency>
            <!--Mysql数据库驱动8 -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <!--SpringBoot集成druid连接池-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <!--通用Mapper4之tk.mybatis-->
            <dependency>
                <groupId>tk.mybatis</groupId>
                <artifactId>mapper</artifactId>
                <version>${mapper.version}</version>
            </dependency>
            <!--persistence-->
            <dependency>
                <groupId>javax.persistence</groupId>
                <artifactId>persistence-api</artifactId>
                <version>${persistence-api.version}</version>
            </dependency>
            <!-- fastjson2 -->
            <dependency>
                <groupId>com.alibaba.fastjson2</groupId>
                <artifactId>fastjson2</artifactId>
                <version>${fastjson2.version}</version>
            </dependency>
            <!-- swagger3 调用方式 http://你的主机IP地址:5555/swagger-ui/index.html -->
            <dependency>
                <groupId>org.springdoc</groupId>
                <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
                <version>${swagger3.version}</version>
            </dependency>
            <!--hutool-->
            <dependency>
                <groupId>cn.hutool</groupId>
                <artifactId>hutool-all</artifactId>
                <version>${hutool.version}</version>
            </dependency>
            <!--lombok-->
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
                <optional>true</optional>
            </dependency>
            <!-- spring-boot-starter-test -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <version>${spring.boot.test.version}</version>
                <scope>test</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

### 2. Mapper4一键生成

mybatis-generator

>http://mybatis.org/generator

Mapper4

> https://github.com/abel533/Mapper

Mapper5

> https://github.com/mybatis-mapper/mapper

一键生成步骤

- 建好SQL数据库及表

```mysql
DROP TABLE IF EXISTS `t_pay`;

 

CREATE TABLE `t_pay` (

  `id` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,

  `pay_no` VARCHAR(50) NOT NULL COMMENT '支付流水号',

  `order_no` VARCHAR(50) NOT NULL COMMENT '订单流水号',

  `user_id` INT(10) DEFAULT '1' COMMENT '用户账号ID',

  `amount` DECIMAL(8,2) NOT NULL DEFAULT '9.9' COMMENT '交易金额',

  `deleted` TINYINT(4) UNSIGNED NOT NULL DEFAULT '0' COMMENT '删除标志，默认0不删除，1删除',

  `create_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',

  `update_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',

  PRIMARY KEY (`id`)

) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COMMENT='支付交易表';

 

INSERT INTO t_pay(pay_no,order_no) VALUES('pay17203699','6544bafb424a');

 

SELECT * FROM t_pay;
```

- 创建Module

<img src="./assets/image-20240510192159677.png" alt="image-20240510192159677" style="zoom:80%;" />

- 写POM文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.atguigu.cloud</groupId>
        <artifactId>mscloudV5</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <!--我自己独一份，只是一个普通Maven工程，与boot和cloud无关-->
    <artifactId>mybatis_generator2024</artifactId>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!--Mybatis 通用mapper tk单独使用，自己独有+自带版本号-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.13</version>
        </dependency>
        <!-- Mybatis Generator 自己独有+自带版本号-->
        <dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.4.2</version>
        </dependency>
        <!--通用Mapper-->
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper</artifactId>
        </dependency>
        <!--mysql8.0-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--persistence-->
        <dependency>
            <groupId>javax.persistence</groupId>
            <artifactId>persistence-api</artifactId>
        </dependency>
        <!--hutool-->
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <resources>
            <resource>
                <directory>${basedir}/src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>${basedir}/src/main/resources</directory>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.2</version>
                <configuration>
                    <configurationFile>${basedir}/src/main/resources/generatorConfig.xml</configurationFile>
                    <overwrite>true</overwrite>
                    <verbose>true</verbose>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>8.0.33</version>
                    </dependency>
                    <dependency>
                        <groupId>tk.mybatis</groupId>
                        <artifactId>mapper</artifactId>
                        <version>4.2.3</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
</project>
```

- 写配置文件

  - src\main\resources路径下新建

    - config.properties

    - ```properties
      #User表包名
      package.name=com.atguigu.cloud
      # mysql5.7
      jdbc.driverClass = com.mysql.jdbc.Driver
      jdbc.url= jdbc:mysql://localhost:3306/db2024?useUnicode=true&characterEncoding=UTF-8&useSSL=false
      jdbc.user = root
      jdbc.password =123456
      
      
      
      #t_pay表包名
      package.name=com.atguigu.cloud
      # mysql8.0
      jdbc.driverClass = com.mysql.cj.jdbc.Driver
      jdbc.url= jdbc:mysql://localhost:3306/db2024?characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2B8&rewriteBatchedStatements=true&allowPublicKeyRetrieval=true
      jdbc.user = root
      jdbc.password =123456
      ```

    - generatorConfig.xml

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE generatorConfiguration
              PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
              "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
      
      <generatorConfiguration>
          <properties resource="config.properties"/>
      
          <context id="Mysql" targetRuntime="MyBatis3Simple" defaultModelType="flat">
              <property name="beginningDelimiter" value="`"/>
              <property name="endingDelimiter" value="`"/>
      
              <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
                  <property name="mappers" value="tk.mybatis.mapper.common.Mapper"/>
                  <property name="caseSensitive" value="true"/>
              </plugin>
      
              <jdbcConnection driverClass="${jdbc.driverClass}"
                              connectionURL="${jdbc.url}"
                              userId="${jdbc.user}"
                              password="${jdbc.password}">
              </jdbcConnection>
      
              <javaModelGenerator targetPackage="${package.name}.entities" targetProject="src/main/java"/>
      
              <sqlMapGenerator targetPackage="${package.name}.mapper" targetProject="src/main/java"/>
      
              <javaClientGenerator targetPackage="${package.name}.mapper" targetProject="src/main/java" type="XMLMAPPER"/>
      
              <table tableName="t_pay" domainObjectName="Pay">
                  <generatedKey column="id" sqlStatement="JDBC"/>
              </table>
          </context>
      </generatorConfiguration>
      ```

- 双击插件mybatis-generator:generate

<img src="./assets/image-20240510192806365.png" alt="image-20240510192806365" style="zoom:67%;" />

### 3. Rest通用Base工程构建

创建微服务的小口诀（步骤）

>1.建module
>
>2.改POM
>
>3.写YML
>
>4.主启动类
>
>5.业务类

<img src="./assets/image-20240511161450250.png" alt="image-20240511161450250" style="zoom:67%;" />

### 4. 创建微服务项目

#### RestTemplate

> RestTemplate提供了多种便捷访问远程Http服务的方法， 是一种简单便捷的访问restful服务模板类，是Spring提供的用于访问Rest服务的客户端模板工具集

官网地址

> https://docs.spring.io/spring-framework/docs/6.0.11/javadoc-api/org/springframework/web/client/RestTemplate.html

RestTemplateConfig

```java
@Configuration
public class RestTemplateConfig
{
    @Bean
    public RestTemplate restTemplate()
    {
        return new RestTemplate();
    }
}
```

最终项目结构

<img src="./assets/image-20240511173834902.png" alt="image-20240511173834902" style="zoom:67%;" />

## 4.Consul服务注册与发现

### 1.为什么要引入服务注册中心

微服务所在的IP地址和端口号硬编码到订单微服务中，会存在非常多的问题

（1）如果订单微服务和支付微服务的IP地址或者端口号发生了变化，则支付微服务将变得不可用，需要同步修改订单微服务中调用支付微服务的IP地址和端口号。

（2）如果系统中提供了多个订单微服务和支付微服务，则无法实现微服务的负载均衡功能。

（3）如果系统需要支持更高的并发，需要部署更多的订单微服务和支付微服务，硬编码订单微服务则后续的维护会变得异常复杂。

所以，在微服务开发的过程中，需要引入服务治理功能，实现微服务之间的动态注册与发现。

### 2.Consul简介

consul官网地址：https://www.consul.io/

consul简介：https://developer.hashicorp.com/consul/docs/intro

spring consul：https://docs.spring.io/spring-cloud-consul/docs/current/reference/html

#### consul作用

- 服务发现：提供HTTP和DNS两种发现方式
- 健康监测：支持多种方式，HTTP、TCP、Docker、Shell脚本定制化监控
- KV存储：Key、Value的存储方式
- 多数据中心：Consul支持多数据中心
- 可视化Web界面

Consul下载：https://developer.hashicorp.com/consul/downloads

### 3.安装并运行consul

下载完成后解压，在包含consul.exe文件的文件夹下使用命令行窗口

<img src="./assets/image-20240511182714826.png" alt="image-20240511182714826" style="zoom:67%;" />

```shell
consul --version
```

<img src="./assets/image-20240511182734848.png" alt="image-20240511182734848" style="zoom:67%;" />

```shell
consul agent -dev
```

<img src="./assets/image-20240511182805018.png" alt="image-20240511182805018" style="zoom:67%;" />

访问Consul首页：http://localhost:8500

### 4.服务注册与发现

#### 服务提供者8001

- 建module：cloud-provider-payment8001

- 改POM，配置来源：https://docs.spring.io/spring-cloud-consul/reference/quickstart.html

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.atguigu.cloud</groupId>
        <artifactId>mscloudV5</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>cloud-provider-payment8001</artifactId>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>


    <dependencies>
        <!--SpringCloud consul discovery -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包 -->
        <dependency>
            <groupId>com.atguigu.cloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--SpringBoot通用依赖模块-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--SpringBoot集成druid连接池-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
        </dependency>
        <!-- Swagger3 调用方式 http://你的主机IP地址:5555/swagger-ui/index.html -->
        <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
        </dependency>
        <!--mybatis和springboot整合-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <!--Mysql数据库驱动8 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--persistence-->
        <dependency>
            <groupId>javax.persistence</groupId>
            <artifactId>persistence-api</artifactId>
        </dependency>
        <!--通用Mapper4-->
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper</artifactId>
        </dependency>
        <!--hutool-->
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
        </dependency>
        <!-- fastjson2 -->
        <dependency>
            <groupId>com.alibaba.fastjson2</groupId>
            <artifactId>fastjson2</artifactId>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.28</version>
            <scope>provided</scope>
        </dependency>
        <!--test-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

- 写yml

```yaml
server:
  port: 8001

# ==========applicationName + druid-mysql8 driver===================
spring:
  application:
    name: cloud-payment-service

  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2024?characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2B8&rewriteBatchedStatements=true&allowPublicKeyRetrieval=true
    username: root
    password: 123456
  ####Spring Cloud Consul for Service Discovery
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}

# ========================mybatis===================
mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.cloud.entities
  configuration:
    map-underscore-to-camel-case: true
```

- 主启动类，`@EnableDiscoveryClient`开启服务注册发现

```java
@SpringBootApplication
@MapperScan("com.atguigu.cloud.mapper") //import tk.mybatis.spring.annotation.MapperScan;
@EnableDiscoveryClient
public class Main8001
{
    public static void main(String[] args)
    {
        SpringApplication.run(Main8001.class,args);
    }
}
```

#### 服务消费者80

- 将controller中的硬编码换掉

```java
//public static final String PaymentSrv_URL = "http://localhost:8001";//先写死，硬编码

public static final String PaymentSrv_URL = "http://cloud-payment-service";//服务注册中心上的微服务名称
```

- 修改RestTemplateConfig类，否则会报错：java.net.UnknownHostException:cloud-payment-service

```java
@Configuration
public class RestTemplateConfig
{
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate()
    {
        return new RestTemplate();
    }
}
```

#### 三个注册中心的异同点

<img src="./assets/image-20240512110330063.png" alt="image-20240512110330063" style="zoom:67%;" />

>CAP:
>
>C：Consistency（强一致性）
>
>A：Availability（可用性）
>
>P：Partition tolerance（分区容错性）

**经典CAP图**

最多只能同时较好的满足两个。

 CAP理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，

因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三 大类：

CA - 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。

CP - 满足一致性，分区容忍必的系统，通常性能不是特别高。

AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。

<img src="./assets/image-20240512110540175.png" alt="image-20240512110540175" style="zoom:67%;" />

AP架构（Eureka）

<img src="./assets/image-20240512110639043.png" alt="image-20240512110639043" style="zoom:67%;" />

CP架构（Zookeeper/Consul）

<img src="./assets/image-20240512110705455.png" alt="image-20240512110705455" style="zoom:67%;" />

### 5.服务配置与刷新

#### 服务配置案例步骤

**修改cloud-provider-payment8001**

- 改POM

```xml
<!--SpringCloud consul config-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-config</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

- 改YML

applicaiton.yml是用户级的资源配置项

bootstrap.yml是系统级的，优先级更加高

Spring Cloud会创建一个“Bootstrap Context”，作为Spring应用的`Application Context`的父上下文。初始化的时候，`Bootstrap Context`负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的`Environment`。

`Bootstrap`属性有高优先级，默认情况下，它们不会被本地配置覆盖。 `Bootstrap context`和`Application Context`有着不同的约定，所以新增了一个`bootstrap.yml`文件，保证`Bootstrap Context`和`Application Context`配置的分离。

 application.yml文件改为bootstrap.yml,这是很关键的或者两者共存

因为bootstrap.yml是比application.yml先加载的。bootstrap.yml优先级高于application.yml

bootstrap.yml

```yaml
spring:
  application:
    name: cloud-payment-service
    ####Spring Cloud Consul for Service Discovery
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
      config:
        profile-separator: '-' # default value is ","，we update '-'
        format: YAML
```

application.yml

```yaml
server:
  port: 8001

# ==========applicationName + druid-mysql8 driver===================
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2024?characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2B8&rewriteBatchedStatements=true&allowPublicKeyRetrieval=true
    username: root
    password: 123456
  profiles:
    active: dev # 多环境配置加载内容dev/prod,不写就是默认default配置

# ========================mybatis===================
mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.cloud.entities
  configuration:
    map-underscore-to-camel-case: true
```

- consul服务器key/value配置填写

  - 1.参考规则

  - >config/cloud-payment-service/data
    >    /cloud-payment-service-dev/data
    >
    >   /cloud-payment-service-prod/data

  - 2.创建config文件夹,以 / 结尾

  - 3.创建3个文件夹以 / 结尾

  - >cloud-payment-service/
    >
    >cloud-payment-service-dev/
    >
    >cloud-payment-service-prod/

  - 4.在创建data文件,填写需要存储的数据,以yaml格式存储.

<img src="./assets/image-20240512142059630.png" alt="image-20240512142059630" style="zoom:67%;" />

取出数据：使用`@Value`注解取出数据

```java
@GetMapping(value = "/pay/get/info")
private String getInfoByConsul(@Value("${atguigu.info}") String atguiguInfo)
{
    return "atguiguInfo: "+atguiguInfo+"\t"+"port: "+port;
}
```

#### 动态刷新案例步骤

在主启动类上添加`@RefreshScope`注解

```java
@SpringBootApplication
@MapperScan("com.atguigu.cloud.mapper") //import tk.mybatis.spring.annotation.MapperScan;
@EnableDiscoveryClient //服务注册和发现
@RefreshScope // 动态刷新
public class Main8001
{
    public static void main(String[] args)
    {
        SpringApplication.run(Main8001.class,args);
    }
}
```

修改刷新时间(实际生产中不要修改)

```yaml
spring:
  cloud:
    consul:
      config:
        watch:
          wait-time: 1
```

## 5. LoadBalancer负载均衡服务调用

官网：https://docs.spring.io/spring-cloud-commons/reference/spring-cloud-commons/loadbalancer.html

**loadbalancer本地负载均衡客户端 VS Nginx服务端负载均衡区别**

> Nginx是服务器负载均衡，客户端所有请求都会交给nginx，然后由nginx实现转发请求，即负载均衡是由服务端实现的。
>
> loadbalancer本地负载均衡，在调用微服务接口时候，会在注册中心上获取注册信息服务列表之后缓存到JVM本地，从而在本地实现RPC远程服务调用技术。

<img src="./assets/image-20240512144447024.png" alt="image-20240512144447024" style="zoom:67%;" />

#### 负载均衡案例

- 仿照8001微服务创建新的8002微服务

- 启动Consul，将8001/8002启动后注册进微服务

  - Consul数据持久化配置并且注册为Windows服务

    1. D:\devSoft\consul_1.17.0_windows_386目录下新建，空文件夹mydata，新建文件consul_start.bat
    2. consul_start.bat文件信息

    ```bat
    @echo.服务启动......  
    @echo off  
    @sc create Consul binpath= "D:\devSoft\consul_1.17.0_windows_386\consul.exe agent -server -ui -bind=127.0.0.1 -client=0.0.0.0 -bootstrap-expect  1  -data-dir D:\devSoft\consul_1.17.0_windows_386\mydata   "
    @net start Consul
    @sc config Consul start= AUTO  
    @echo.Consul start is OK......success
    @pause
    ```

    3. 右键管理员权限打开consul_start.bat脚本文件

    <img src="./assets/image-20240512145850882.png" alt="image-20240512145850882" style="zoom:67%;" />

    <img src="./assets/image-20240512145838049.png" alt="image-20240512145838049" style="zoom:67%;" />

    4. 后续consul的配置数据会保存进mydata文件夹，重启电脑后也会存在

- 订单80模块修改POM并注册进consul，新增LoadBalancer组件

```xml
<!--loadbalancer-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

- 订单80模块修改Controller并启动80

```java
@GetMapping(value = "/consumer/pay/get/info")
private String getInfoByConsul()
{
    return restTemplate.getForObject(PaymentSrv_URL + "/pay/get/info", String.class);
}
```

- 测试，客户端会交替访问8001/8002，应为LB的模式默认为轮询

#### 负载均衡案例小总结

在80微服务层添加以下代码，可以获取所有/特定服务列表

```java
@Resource
private DiscoveryClient discoveryClient;
@GetMapping("/consumer/discovery")
public String discovery()
{
    List<String> services = discoveryClient.getServices();
    for (String element : services) {
        System.out.println(element);
    }

    System.out.println("===================================");

    List<ServiceInstance> instances = discoveryClient.getInstances("cloud-payment-service");
    for (ServiceInstance element : instances) {
        System.out.println(element.getServiceId()+"\t"+element.getHost()+"\t"+element.getPort()+"\t"+element.getUri());
    }

    return instances.get(0).getServiceId()+":"+instances.get(0).getPort();
}
```

轮询原理

<img src="./assets/image-20240512150307635.png" alt="image-20240512150307635" style="zoom:67%;" />

#### 负载均衡算法原理

官方提供有两种，轮询和随机，默认使用轮询。

从默认的轮询切换为随机算法，修改RestTemplateConfig

```java
@Configuration
@LoadBalancerClient(
        //下面的value值大小写一定要和consul里面的名字一样，必须一样
        value = "cloud-payment-service",configuration = RestTemplateConfig.class)
public class RestTemplateConfig
{
    @Bean
    @LoadBalanced //使用@LoadBalanced注解赋予RestTemplate负载均衡的能力
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

    @Bean
    ReactorLoadBalancer<ServiceInstance> randomLoadBalancer(Environment environment,
                                                            LoadBalancerClientFactory loadBalancerClientFactory) {
        String name = environment.getProperty(LoadBalancerClientFactory.PROPERTY_NAME);

        return new RandomLoadBalancer(loadBalancerClientFactory.getLazyProvider(name, ServiceInstanceListSupplier.class), name);
    }
}
```

## 6. OpenFeign服务接口调用

OpenFeign内部已经实现了LoadBalance，因此在实际应用中，我们只使用OpenFeign即可。

### 简介

Feign是一个**声明性web服务客户端**。它使编写web服务客户端变得更容易。使用Feign创建一个接口并对其进行注释。它具有可插入的注释支持，包括Feign注释和JAX-RS注释。Feign还支持可插拔编码器和解码器。Spring Cloud添加了对Spring MVC注释的支持，以及对使用Spring Web中默认使用的HttpMessageConverter的支持。Spring Cloud集成了Eureka、Spring Cloud CircuitBreaker以及Spring Cloud LoadBalancer，以便在使用Feign时提供负载平衡的http客户端。

一句话：只需创建一个Rest接口并在该接口上添加注解@FeignClient即可

**官网**

https://docs.spring.io/spring-cloud-openfeign/docs/current/reference/html/#spring-cloud-feign

**GitHub**

https://github.com/spring-cloud/spring-cloud-openfeign

### OpenFeign能干什么

前面在使用**SpringCloud LoadBalancer**+RestTemplate时，利用RestTemplate对http请求的封装处理形成了一套模版化的调用方法。***但是在实际开发中，\***

由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，OpenFeign在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。在OpenFeign的实现下，我们只需创建一个接口并使用注解的方式来配置它(在一个微服务接口上面标注一个***@FeignClient\***注解即可)，即可完成对服务提供方的接口绑定，统一对外暴露可以被调用的接口方法，大大简化和降低了调用客户端的开发量，也即由服务提供者给出调用接口清单，消费者直接通过OpenFeign调用即可，O(∩_∩)O。

OpenFeign同时还集成SpringCloud LoadBalancer

可以在使用OpenFeign时提供Http客户端的负载均衡，也可以集成阿里巴巴Sentinel来提供熔断、降级等功能。而与SpringCloud LoadBalancer不同的是，通过OpenFeign只需要定义服务绑定接口且以声明式的方法，优雅而简单的实现了服务调用。

### OpenFeign通用使用步骤

微服务Api接口+@FeignClient注解标签

<img src="./assets/image-20240513102402783.png" alt="image-20240513102402783" style="zoom:67%;" />

**流程步骤**

- 建Mould：cloud-consumer-feign-order80

- 改POM

- ```xml
  <!--多引入一个openfeign模块-->
  <!--openfeign-->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-openfeign</artifactId>
  </dependency>
  ```

- 写YML

- ```yaml
  server:
    port: 80
  
  spring:
    application:
      name: cloud-consumer-openfeign-order
    ####Spring Cloud Consul for Service Discovery
    cloud:
      consul:
        host: localhost
        port: 8500
        discovery:
          prefer-ip-address: true #优先使用服务ip进行注册
          service-name: ${spring.application.name}
  ```

- 主启动类

- ```java
  @SpringBootApplication
  @EnableDiscoveryClient //该注解用于向使用consul为注册中心时注册服务
  @EnableFeignClients//启用feign客户端,定义服务+绑定接口，以声明式的方法优雅而简单的实现服务调用
  public class MainOpenFeign80
  {
      public static void main(String[] args)
      {
          SpringApplication.run(MainOpenFeign80.class,args);
      }
  }
  ```

- 业务类

  - 业务类架构图

  - <img src="./assets/image-20240513103216184.png" alt="image-20240513103216184" style="zoom:67%;" />

  - 修改cloud-api-commons通用模块

    - 引入openfeign依赖

    - ```xml
      <!--openfeign-->
      <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-starter-openfeign</artifactId>
      </dependency>
      ```

    - 新建服务接口PayFeignApi，并标注`@FeignClient`注解

    - ```java
      @FeignClient(value = "cloud-payment-service")
      public interface PayFeignApi
      {
          /**
           * 新增一条支付相关流水记录
           * @param payDTO
           * @return
           */
          @PostMapping("/pay/add")
          public ResultData addPay(@RequestBody PayDTO payDTO);
      
          /**
           * 按照主键记录查询支付流水信息
           * @param id
           * @return
           */
          @GetMapping("/pay/get/{id}")
          public ResultData getPayInfo(@PathVariable("id") Integer id);
      
          /**
           * openfeign天然支持负载均衡演示
           * @return
           */
          @GetMapping(value = "/pay/get/info")
          public String mylb();
      }
      ```

    - Controller层调用，即cloud-consumer-feign-order模块调用payment模块的openfeign接口

    - ```java
      @RestController
      @Slf4j
      public class OrderController
      {
          @Resource
          private PayFeignApi payFeignApi;
      
          @PostMapping("/feign/pay/add")
          public ResultData addOrder(@RequestBody PayDTO payDTO)
          {
              System.out.println("第一步：模拟本地addOrder新增订单成功(省略sql操作)，第二步：再开启addPay支付微服务远程调用");
              ResultData resultData = payFeignApi.addPay(payDTO);
              return resultData;
          }
      
          @GetMapping("/feign/pay/get/{id}")
          public ResultData getPayInfo(@PathVariable("id") Integer id)
          {
              System.out.println("-------支付微服务远程调用，按照id查询订单支付流水信息");
              ResultData resultData = payFeignApi.getPayInfo(id);
              return resultData;
          }
      
          /**
           * openfeign天然支持负载均衡演示
           *
           * @return
           */
          @GetMapping(value = "/feign/pay/mylb")
          public String mylb()
          {
              return payFeignApi.mylb();
          }
      }
      ```

### OpenFeign高级特性

#### 1. 超时控制

OpenFeign默认等待60s，超时后报错。

修改客户端的YML文件来调整超时控制，官网：[Spring Cloud OpenFeign](https://docs.spring.io/spring-cloud-openfeign/docs/current/reference/html/#spring-cloud-feign-overriding-defaults)

全局配置

```yaml
spring:
  cloud:
    openfeign:
      client:
        config:
          default:
            #连接超时时间
                      connectTimeout: 3000
            #读取超时时间
                     readTimeout: 3000
```

指定配置

```yaml
spring:
  cloud:
    openfeign:
      client:
        config:
          your-service-name:
            #连接超时时间
                      connectTimeout: 5000
            #读取超时时间
                      readTimeout: 5000
```

OpenFeign允许全局配置和指定配置同时存在，当同时存在时，指定配置的优先级高于全局配置

#### 2. 重试机制

重试机制默认是关闭的。

**开启Retryer功能**

- 新增配置类FeignConfig并修改Retryer配置

- ```java
  @Configuration
  public class FeignConfig
  {
      @Bean
      public Retryer myRetryer()
      {
          //return Retryer.NEVER_RETRY; //Feign默认配置是不走重试策略的
  
          //最大请求次数为3(1+2)，初始间隔时间为100ms，重试间最大间隔时间为1s
          return new Retryer.Default(100,1,3);
      }
  }
  ```

#### 3. 默认HttpClient修改

OpenFeign中http client

如果不做特殊配置，OpenFeign默认使用JDK自带的HttpURLConnection发送HTTP请求， 

由于默认HttpURLConnection没有连接池、性能和效率比较低，如果采用默认，性能上不是最牛B的，所以加到最大。

**改用Apache HttpClient5发送HTTP请求：**

在POM文件中导入依赖

```xml
<!-- httpclient5-->
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient5</artifactId>
    <version>5.3</version>
</dependency>
<!-- feign-hc5-->
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-hc5</artifactId>
    <version>13.1</version>
</dependency>
```

在YML配置开启

```yml
#  Apache HttpClient5 配置开启
spring:
  cloud:
    openfeign:
      httpclient:
        hc5:
          enabled: true
```

#### 4. 请求 / 响应压缩

**对请求和响应进行GZIP压缩**

Spring Cloud OpenFeign支持对请求和响应进行GZIP压缩，以减少通信过程中的性能损耗。

通过下面的两个参数设置，就能开启请求与相应的压缩功能：

spring.cloud.openfeign.compression.request.enabled=true

spring.cloud.openfeign.compression.response.enabled=true

**细粒度化设置**

对请求压缩做一些更细致的设置，比如下面的配置内容指定压缩的请求数据类型并设置了请求压缩的大小下限，

只有超过这个大小的请求才会进行压缩：

spring.cloud.openfeign.compression.request.enabled=true

spring.cloud.openfeign.compression.request.mime-types=text/xml,application/xml,application/json #触发压缩数据类型

spring.cloud.openfeign.compression.request.min-request-size=2048 #最小触发压缩的大小

**YML配置**

```yml
spring:
  cloud:
    openfeign:
      compression:
        request:
          enabled: true
          min-request-size: 2048 #最小触发压缩的大小
          mime-types: text/xml,application/xml,application/json #触发压缩数据类型
        response:
          enabled: true
```

#### 5. 日志打印功能

Feign 提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解 Feign 中 Http 请求的细节，说白了就是对Feign接口的调用情况进行监控和输出.

日志级别

> NONE：默认的，不显示任何日志；
>
> BASIC：仅记录请求方法、URL、响应状态码及执行时间；
>
> HEADERS：除了 BASIC 中定义的信息之外，还有请求和响应的头信息；
>
> FULL：除了 HEADERS 中定义的信息之外，还有请求和响应的正文及元数据。

在FeignConfig配置类中配置bean，修改日志级别

```java
@Configuration
public class FeignConfig
{
    @Bean
    public Retryer myRetryer()
    {
        return Retryer.NEVER_RETRY; //默认
    }

    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```

在YML文件中开启日志功能

<img src="./assets/image-20240513110930793.png" alt="image-20240513110930793" style="zoom:67%;" />

公式(三段)：**logging.level** + 含有@FeignClient注解的完整带包名的接口名+debug

```yml
# feign日志以什么级别监控哪个接口
logging:
  level:
    com:
      atguigu:
        cloud:
          apis:
            PayFeignApi: debug 
```

