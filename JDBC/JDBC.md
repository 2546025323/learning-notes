# JDBC

## 一、JDBC核心API

### 1.1 引入mysql-jdbc驱动jar

#### 1. 驱动jar版本选择

|  mysql版本  | 推荐驱动版本 |                       备注                        |
| :---------: | :----------: | :-----------------------------------------------: |
| mysql 5.5.x |    5.0.x     |               com.mysql.jdbc.Driver               |
| mysql 5.7.x |    5.1.x     |               com.mysql.jdbc.Driver               |
|  msyql 8.x  |    8.0.x     | 建议: 8.0.25+省略时区设置com.mysql.cj.jdbc.Driver |

#### 2. java工程导入依赖

a.项目创建lib文件夹

b.导入驱动依赖jar包

c.jar包右键-添加为项目依赖

<img src="F:\笔记\JDBC\assets\image.png" style="zoom:70%">

### 1.2 jdbc基本使用步骤（6步）

1. 注册驱动
2. 获取连接
3. 创建发送sql语句对象
4. 发送sql语句，并获取返回结果
5. 结果集解析
6. 释放资源

### 1.3 基于statement查询

#### 准备数据库

```mysql
CREATE DATABASE atguigu;

USE atguigu;

CREATE TABLE t_user(
   id INT PRIMARY KEY AUTO_INCREMENT COMMENT '用户主键',
   account VARCHAR(20) NOT NULL UNIQUE COMMENT '账号',
   PASSWORD VARCHAR(64) NOT NULL COMMENT '密码',
   nickname VARCHAR(20) NOT NULL COMMENT '昵称');
   
INSERT INTO t_user(account,PASSWORD,nickname) VALUES
  ('root','123456','经理'),('admin','666666','管理员');
```

![img](F:\笔记\JDBC\assets\image-1696763388796.png)

#### 基于statement实现查询

```java
public class StatementBasePart {
    public static void main(String[] args) throws SQLException {
        //1.注册驱动
        DriverManager.registerDriver(new Driver());
        //2.获取链接
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:13306/atguigu", "root", "0108");
        //3.创建发送sql语句对象
        Statement statement = connection.createStatement();
        //4.发送sql语句并获取返回结果
        String sql = "select * from t_user";
        ResultSet resultSet = statement.executeQuery(sql);
        //5.结果集解析
        while (resultSet.next()){
            int id = resultSet.getInt("id");
            String account = resultSet.getString("account");
            String password = resultSet.getString("password");
            String nickname = resultSet.getString("nickname");
            System.out.println(id+"::"+account+"::"+password+"::"+nickname);
        }
        //6.关闭资源
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

### 1.4 基于statement方式存在的问题

#### 准备数据库

```sql
CREATE DATABASE atguigu;

USE atguigu;

CREATE TABLE t_user(
   id INT PRIMARY KEY AUTO_INCREMENT COMMENT '用户主键',
   account VARCHAR(20) NOT NULL UNIQUE COMMENT '账号',
   PASSWORD VARCHAR(64) NOT NULL COMMENT '密码',
   nickname VARCHAR(20) NOT NULL COMMENT '昵称');
   
INSERT INTO t_user(account,PASSWORD,nickname) VALUES
  ('root','123456','经理'),('admin','666666','管理员');
```

![img](F:\笔记\JDBC\assets\image-1696763388796.png)

#### 查询目标

模拟登录,控制台输入账号和密码,判断是否登陆成功成功!

![img](F:\笔记\JDBC\assets\image-1696765945053.png)

#### jdbc的查询使用

         * 类加载： java文件 -> 编译 -> 【 class字节码文件 -->  类加载 --> jvm虚拟中  --> Class对象】
                  * 类加载具体步骤：  加载 【class文件转成对象加载到虚拟机中】->连接 【验证（检查类文件） -> 准备 (静态变量赋默认值) -> 解析 (调用静态代码块) 】 ->初始化 -> (赋真实值)
                  *                以下7种方式会触发类加载：
                  1. new关键字
                  2. 调用静态属性
                  3. 调用静态方法
                  4. 接口 包含1.8 新特性 default关键字
                  5. 反射 【Class.forName() 类名.class】
                  6. 子类调用会触发父类的静态代码块
                  7. 触发类的入口方法main

#### getConnection()方法

三个参数：

- String URL: 连接数据库地址
- String user: 连接数据库用户名
- String password: 连接数据库用户对应的密码

数据库URL语法：

​	jdbc:数据库厂商://ip:端口号/数据库名

​	例：jdbc:mysql://localhost:3306/day01

​			jdbc:mysql://192.168.33.45/3306/day01

两个参数：

- String URL : 写法还是jdbc的路径写法！
- Properties : 就是一个参数封装容器！至少要包含 user / password key!存储连接账号信息！

一个参数：

- String URL: URl可以携带目标地址，可以通过?分割，在后面key=value&key=value形式传递参
  - jdbc:mysql:///day01?user=root&password=123456

扩展路径参数(了解):

- serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf8&useSSL=true

#### 查询方法

ResultSet resultSet = executeQuery(DQL语句)

- 若查询到则返回一个结果集resultSet 。

int row  = executeUpdate(非DQL语句)

- 返回数据库影响行数。如删除一行数据，row=1；修改0行，row=0；

#### next()方法

Boolean  = next()

- false：没有更多行了
- true：还有更多行，并移动到下一行

#### 基于statement实现模拟登录

```java
public class JdbcStatementLoginPart {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入账户：");
        String account = scanner.nextLine();
        System.out.println("请输入密码：");
        String password = scanner.nextLine();
        scanner.close();
        Class.forName("com.mysql.jdbc.Driver");
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:13306/atguigu", "root", "0108");
        Statement statement = connection.createStatement();
        String sql = "select * from t_user where account= '" + account + "' and password='"+ password + "'";
        ResultSet resultSet = statement.executeQuery(sql);
        if(resultSet.next()){
            System.out.println("登录成功");
        }else{
            System.out.println("登录失败");
        }
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

#### 存在问题

1. SQL语句需要字符串拼接,比较麻烦
2. SQL语句需要字符串拼接,比较麻烦
3. 可能发生注入攻击,动态值充当了SQL语句结构,影响了原有的查询结果!

### 1.5 基于prepareStatement方式优化

利用preparedStatement解决上述案例注入攻击和SQL语句拼接问题! (重点掌握)

```java
public class PSLoginTest {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        Scanner scanner = new Scanner(System.in);
        String account = scanner.nextLine();
        String passowrd = scanner.nextLine();
        scanner.close();
        //注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //获取链接
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:13306/atguigu", "root", "0108");
        //编写sql语句
        String sql = "select * from t_user where account = ? and password = ?;";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        /**
         * ? 占位符
         * 使用preparedStatement.setObject()方法给占位符赋值
         * 从左往右赋值，从1开始。
         */
        preparedStatement.setObject(1, account);
        preparedStatement.setObject(2, passowrd);
        ResultSet resultSet = preparedStatement.executeQuery();
        if(resultSet.next()){
            System.out.println("登录成功！");
        }else{
            System.out.println("登录失败！");
        }
        resultSet.close();
        preparedStatement.close();
        connection.close();

    }
}
```

### 1.6 基于preparedStatement实现curd

#### 数据库数据插入

```java
public void testInset() throws ClassNotFoundException, SQLException {
        //1.注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2.获取连接
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:13306/atguigu", "root", "0108");
        //3.构造sql语句
        String sql = "insert into t_user (account, password, nickname) values (?, ?, ?);";
        //4.填充占位符
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1, "test");
        preparedStatement.setObject(2, "test");
        preparedStatement.setObject(3, "test");
        //5.执行sql语句
        int i = preparedStatement.executeUpdate();
        //6.输出结果
        if(i > 0){
            System.out.println("插入成功");
        }else{
            System.out.println("插入失败");
        }
        //7.释放资源
        preparedStatement.close();
        connection.close();

    }
```

#### 数据库数据修改

```java
public void testUpdate() throws ClassNotFoundException, SQLException {
        //1.注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2.获取链接
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:13306/atguigu?user=root&password=0108");
        //3.构造sql语句
        String sql = "update t_user set nickname = ? where account = ?;";
        //4.填充占位符
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1, "mytest");
        preparedStatement.setObject(2, "test");

        //5.执行sql语句
        int i = preparedStatement.executeUpdate();
        //6.输出结果
        if(i > 0){
            System.out.println("更新成功");
        }else{
            System.out.println("更新失败");
        }
        //7.释放资源
        preparedStatement.close();
        connection.close();
    }
```

#### 数据库数据删除

```java
public void testDelete() throws ClassNotFoundException, SQLException {
        //1.注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2.获取链接
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:13306/atguigu?user=root&password=0108");
        //3.构造sql语句
        String sql = "Delete from t_user where account = ?;";
        //4.填充占位符
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1, "test");

        //5.执行sql语句
        int i = preparedStatement.executeUpdate();
        //6.输出结果
        if(i > 0){
            System.out.println("删除成功");
        }else{
            System.out.println("删除失败");
        }
        //7.释放资源
        preparedStatement.close();
        connection.close();
    }
```

#### 数据库数据查询

```java
public void testSelect() throws ClassNotFoundException, SQLException {
        //1.注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2.获取链接
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:13306/atguigu?user=root&password=0108");
        //3.构造sql语句
        String sql = "select * from t_user";
        //4.填充占位符
        //5.执行sql语句
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        ResultSet resultSet = preparedStatement.executeQuery();
        //6.输出结果
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();
        List<Map> list = new ArrayList<>();
        while(resultSet.next()){
            Map map = new HashMap();
            for (int i = 1; i <= columnCount; i++) {
                map.put(metaData.getColumnLabel(i), resultSet.getObject(i));
            }
            list.add(map);
        }
        System.out.println("list =" + list);
        //7.释放资源
        resultSet.close();
        preparedStatement.close();
        connection.close();
    }
```

## 二、JDBC扩展提升

### 2.1 自增长主键回显

> **java程序**获取**插入**数据时mysql维护**自增长**维护的主键**id值**,这就是主键回显
>
> 作用: 在多表关联插入数据时,一般主表的主键都是自动生成的,所以在插入数据之前无法知道这条数据的主键,但是从表需要在插入数据之前就绑定主表的主键,这是可以使用主键回显技术:

```java
/**
     * 回显主键
     * TODO:
     *      prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)：设置preparedStatement传回主键
     *      preparedStatement.getGeneratedKeys()：获取主键，返回值是一个ResultSet对象。
     */
    @Test
    public void testReturnPrimaryKey() throws Exception {
        Class.forName("com.mysql.jdbc.Driver");
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:13306/atguigu?user=root&password=0108");
        String sql = "insert into t_user(account, password, nickname) values(?, ?, ?);";
        PreparedStatement preparedStatement = connection.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
        preparedStatement.setObject(1, "dd");
        preparedStatement.setObject(2, "dd");
        preparedStatement.setObject(3, "dd");
        int i = preparedStatement.executeUpdate();
        if(i > 0){
            System.out.println("插入成功");
            ResultSet generatedKeys = preparedStatement.getGeneratedKeys();
            generatedKeys.next();
            int anInt = generatedKeys.getInt(1);
            System.out.println("id = " + anInt);

        }else{
            System.out.println("插入失败");
        }
        preparedStatement.close();
        connection.close();
    }
```

### 2.2 批量插入数据

>批量数据插入优化
>
>提升大量数据插入效率

```java
    /**
     * 批量插入数据
     * 执行时间 = 26ms
     * TODO:
     *      preparedStatement.addBatch()：追加数据到sql语句后面。
     *      preparedStatement.executeBatch()：在数据追加完成后，执行批量插入数据。
     *      需要在获取连接时，使用 url?rewriteBatchedStatements=true
     *      insert 语句必须使用 values
     *      语句后面不能添加分号;
     */
    @Test
    public void testInsertBatch() throws Exception {
        Class.forName("com.mysql.jdbc.Driver");
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:13306/atguigu?rewriteBatchedStatements=true", "root", "0108");
        String sql = "insert into t_user(account, password, nickname) values(?, ?, ?)";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        long start = System.currentTimeMillis();
        for (int i = 0; i < 10000; i++) {
            preparedStatement.setObject(1, "aaa" + i);
            preparedStatement.setObject(2, "aaa" + i);
            preparedStatement.setObject(3, "aaa" + i);
            preparedStatement.addBatch();
        }
        long end = System.currentTimeMillis();
        System.out.println("执行时间 = " + (end - start));
        preparedStatement.executeBatch();
        preparedStatement.close();
        connection.close();
    }
```

## 三、Druid数据库连接池技术

### 1. Druid配置

| 配置                          | 缺省  | 说明                                                         |
| ----------------------------- | ----- | ------------------------------------------------------------ |
| name                          |       | 配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。 如果没有配置，将会生成一个名字，格式是：”DataSource-” + System.identityHashCode(this) |
| jdbcUrl                       |       | 连接数据库的url，不同数据库不一样。例如：mysql : jdbc:mysql://10.20.153.104:3306/druid2 oracle : jdbc:oracle:thin:@10.20.149.85:1521:ocnauto |
| username                      |       | 连接数据库的用户名                                           |
| password                      |       | 连接数据库的密码。如果你不希望密码直接写在配置文件中，可以使用ConfigFilter。详细看这里：[https://github.com/alibaba/druid/wiki/%E4%BD%BF%E7%94%A8ConfigFilter](https://github.com/alibaba/druid/wiki/使用ConfigFilter) |
| driverClassName               |       | 根据url自动识别 这一项可配可不配，如果不配置druid会根据url自动识别dbType，然后选择相应的driverClassName(建议配置下) |
| initialSize                   | 0     | 初始化时建立物理连接的个数。初始化发生在显示调用init方法，或者第一次getConnection时 |
| maxActive                     | 8     | 最大连接池数量                                               |
| maxIdle                       | 8     | 已经不再使用，配置了也没效果                                 |
| minIdle                       |       | 最小连接池数量                                               |
| maxWait                       |       | 获取连接时最大等待时间，单位毫秒。配置了maxWait之后，缺省启用公平锁，并发效率会有所下降，如果需要可以通过配置useUnfairLock属性为true使用非公平锁。 |
| poolPreparedStatements        | false | 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。 |
| maxOpenPreparedStatements     | -1    | 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100 |
| validationQuery               |       | 用来检测连接是否有效的sql，要求是一个查询语句。如果validationQuery为null，testOnBorrow、testOnReturn、testWhileIdle都不会其作用。 |
| testOnBorrow                  | true  | 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。 |
| testOnReturn                  | false | 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能 |
| testWhileIdle                 | false | 建议配置为true，不影响性能，并且保证安全性。申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。 |
| timeBetweenEvictionRunsMillis |       | 有两个含义： 1)Destroy线程会检测连接的间隔时间2)testWhileIdle的判断依据，详细看testWhileIdle属性的说明 |
| numTestsPerEvictionRun        |       | 不再使用，一个DruidDataSource只支持一个EvictionRun           |
| minEvictableIdleTimeMillis    |       |                                                              |
| connectionInitSqls            |       | 物理连接初始化的时候执行的sql                                |
| exceptionSorter               |       | 根据dbType自动识别 当数据库抛出一些不可恢复的异常时，抛弃连接 |
| filters                       |       | 属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有： 监控统计用的filter:stat日志用的filter:log4j防御sql注入的filter:wall |
| proxyFilters                  |       | 类型是List，如果同时配置了filters和proxyFilters，是组合关系，并非替换关系 |

### 2. 软编码方式

> 存放在src/druid.properties

```properties
# druid连接池需要的配置参数,key固定命名
driverClassName=com.mysql.jdbc.Driver
username=root
password=0108
url=jdbc:mysql://localhost:13306/atguigu
```

```java
@Test
    public void test1() throws Exception {
        Properties properties = new Properties();
        InputStream ips = testDruidPart.class.getClassLoader().getResourceAsStream("druid.properties");
        properties.load(ips);
        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        Connection connection = dataSource.getConnection();
        String sql  ="select * from t_user where account = ?;";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1, "root");
        ResultSet resultSet = preparedStatement.executeQuery();
        if(resultSet.next()){
            System.out.println(1);

        }else{
            System.out.println(2);
        }
        preparedStatement.close();
        connection.close();
    }
```

## 四、JDBC使用优化以及工具类封装

### 1. JDBC工具类封装V1.0

```java
/**
 * 不涉及事务，提供数据库链接的工具类
 */
public class JDBCUtilsV1 {
    private static DataSource dataSource;
    static {
        Properties properties = new Properties();
        InputStream ips = JDBCUtilsV1.class.getClassLoader().getResourceAsStream("druid.properties");
        try {
            properties.load(ips);
            dataSource = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    /**
     * 提供数据库链接
     */
    public static Connection connection() throws SQLException {
        Connection connection = dataSource.getConnection();
        return connection;
    }
    /**
     * 回收数据库链接
     */
    public static void freeConnection(Connection connection) throws SQLException {
        connection.close();
    }
}
```

### 2. JDBC工具类封装V2.0

```Java
ThreadLocal的介绍：

JDK 1.2的版本中就提供java.lang.ThreadLocal，为解决多线程程序的并发问题提供了一种新的思路。
使用这个工具类可以很简洁地编写出优美的多线程程序。通常用来在在多线程中管理共享数据库连接、
Session等

ThreadLocal用于保存某个线程共享变量，原因是在Java中，每一个线程对象中都有一个
ThreadLocalMap<ThreadLocal, Object>，其key就是一个ThreadLocal，而Object即为该线程的
共享变量。而这个map是通过ThreadLocal的set和get方法操作的。对于同一个static ThreadLocal，
不同线程只能从中get，set，remove自己的变量，而不会影响其他线程的变量。

1、ThreadLocal对象.get: 获取ThreadLocal中当前线程共享变量的值。

2、ThreadLocal对象.set: 设置ThreadLocal中当前线程共享变量的值。

3、ThreadLocal对象.remove: 移除ThreadLocal中当前线程共享变量的值。
```

```java
/**
 * 涉及事务处理的JDBC工具类
 * 一个线程共享一个链接
 */
public class JDBCUtilsV2 {
    private static DataSource dataSource;
    private static ThreadLocal<Connection> tl = new ThreadLocal<>();
    static {
        Properties properties = new Properties();
        InputStream ips = JDBCUtilsV2.class.getClassLoader().getResourceAsStream("druid.properties");
        try {
            properties.load(ips);
            dataSource = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public static Connection getConnection() throws SQLException {
        Connection connection = tl.get();
        if(connection == null){
            connection = dataSource.getConnection();
            tl.set(connection);
        }

        return connection;
    }
    public static void freeConnection() throws SQLException {
        Connection connection = tl.get();
        if(connection != null){
            tl.remove();
            connection.setAutoCommit(true);
            connection.close();
        }
    }
}
```

### 3. 封装BaseDAO

```java
public class BaseDAO {
    /**
     * 通用增、删、改操作
     * @param sql 具体的sql语句
     * @param args 占位符的具体值
     * @return 影响行数
     * @throws SQLException
     */
    public int Update(String sql, Object... args) throws SQLException {
        Connection connection = JDBCUtilsV2.getConnection();
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        if(args != null && args.length > 0){
            for (int i = 1; i <= args.length; i++) {
                preparedStatement.setObject(i, args[i - 1]);
            }
        }
        int i = preparedStatement.executeUpdate();
        preparedStatement.close();
        if(connection.getAutoCommit()){
            JDBCUtilsV2.freeConnection();
        }
        return i;
    }
    public <T> ArrayList<T> query(Class<T> clazz, String sql, Object... args) throws SQLException, IllegalAccessException, InstantiationException, NoSuchFieldException {
        Connection connection = JDBCUtilsV2.getConnection();
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        if(args != null && args.length > 0){
            for (int i = 1; i <= args.length; i++) {
                preparedStatement.setObject(i, args[i - 1]);
            }
        }

        ArrayList<T> list = new ArrayList<>();
        ResultSet resultSet = preparedStatement.executeQuery();
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();
        while (resultSet.next()){
            T t = clazz.newInstance();
            for (int i = 1; i <= columnCount; i++) {
                Object value = resultSet.getObject(i);
                String columnLabel = metaData.getColumnLabel(i);
                Field declaredField = clazz.getDeclaredField(columnLabel);
                declaredField.setAccessible(true);
                declaredField.set(t, value);
            }
            list.add(t);
        }
        resultSet.close();
        preparedStatement.close();
        if(connection.getAutoCommit()){
            JDBCUtilsV2.freeConnection();
        }
        return list;
    }
}
```

