# MySQL

## MySQL环境搭建

### MySQL的启动与关闭

```mysql
# windows 启动mysql
net start mysql服务名
# windows 关闭mysql
net stop mysql服务名
```

### 选择数据库

```mysql
# 使用数据库
mysql -u用户名 -p密码 -h服务器端 -P端口号
mysql -uroot -pabc123 -hlocalhost -P3306
```

### 查看当前版本

```mysql
# windows下
c:\> mysql -V
c:\> mysql --version
# mysql下
mysql> select version();
```

### 退出登录

```mysql
exit
或
quit
```

### 数据库操作

```mysql
# 查看所有数据库
show databases;
```

