---
title: "MySQL笔记"
date: 2018-12-03T15:07:19+08:00
draft: true
tags: ["业务组件"]
---

# Mysql学习笔记
## 安装
[资料](https://www.cnblogs.com/starof/p/4680083.html)
删除掉所有有关mariadb的包
```
wget https://cdn.mysql.com/archives/mysql-5.7/mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz
wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.24-1.el7.x86_64.rpm-bundle.tar
rpm -qa | grep -i mariadb
rpm -e --nodeps mariadb-libs-5.5.52-1.el7.x86_64
rpm -ivh mysql-community-common-5.7.23-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.23-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-5.7.23-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-5.7.23-1.el7.x86_64.rpm
```
## 用户
----
### 创建
```
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```
#### 字段含义
* username：创建用户的用户名
* host：指定用户在哪个主机可以登录，如果想让该用户可以从任意远程主机登录，则使用通配符%
* password：该用户的登录密码。如果为空，则该用户不需要密码即可登录。
#### 举例：
```
CREATE USER 'test'@'%' IDENTIFIED BY 'test';
CREATE USER 'test';
```
### 权限
```
GRANT privileges ON databasename.tablename TO 'username'@'host'
```
#### 字段含义
* privileges：用户的权限。如SELECT、INSERT、UPDATE等，如授予所有权限则为ALL。
* databasename：数据库名
* tablename：表名，如授予所有库和表的操作权限则为*，如*.*
#### 例子：
```
GRANT SELECT, INSERT ON testdb.testtb TO 'test'@'%';
GRANT ALL ON *.* TO 'test'@'%';
```
#### 特殊
如想让用户可以给其他用户授权则
```
GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
```
#### 刷新权限表

目的：不重启MySQL服务直接生效权限修改
```
FLUSH PRIVILEGES;
```

### 改密码
```
SET PASSWORD FOR 'username'@'host' = PASSWORD('newpassword');
```
如果是当前用户
```
SET PASSWORD = PASSWORD('newpassword');
```
### 撤销权限
```
REVOKE privilege ON databasename.tablename FROM 'username'@'host';
```
疑问？是否能撤销多个权限
#### 注意
```
GRANT SELECT ON testdb.testtb TO 'test'@'%'
REVOKE SELECT ON *.* FROM 'test'@'%'
```
上诉代码不能撤销testdb.testtb的SELECT权限。

相反
```
GRANT SELECT ON *.* TO 'test'@'%';
REVOKE SELECT ON testdb.testtb FROM 'test'@'%';
```
也不能撤销对testdb.testtb的SELECT权限。
### 查看权限
```
SHOW GRANTS FOR 'test'@'%';
```
#### 例子：
```
REVOKE SELECT ON *.* FROM 'test'@'%';
```
### 删除用户
```
DROP USER 'username'@'host';
```
[MySQL创建用户与授权](https://www.cnblogs.com/sos-blue/p/6852945.html)
## 小操作
显示表结构
```
DESCRIBE tables;
```
### 主从不一致
[读写分离导致数据不一致问题](https://www.cnblogs.com/xiaoyuanren/p/7887451.html)



## binlog
---
my.cnf配置
```
log_bin: on
log_bin_basename: 路径。例如：/var/log/mysql/mysql-bin
log_bin_index: 文件索引，存了binlog的文件名。例如：/var/log/mysql/mysql-bin.index
```
java使用mysql-binlog-connector-java工具解析binlo

#### 参考

[研发应该懂的binlog知识(上)](https://www.cnblogs.com/rjzheng/p/9721765.html)

[研发应该懂的binlog知识(下)](https://www.cnblogs.com/rjzheng/p/9745551.html)

### 主从复制
主库一个log dump线程。

从库一个I/O线程一个SQL线程。

每多一个从库都会多3个线程。

### 工具收集

#### binlog

[mysql-binlog-connector-java](https://github.com/shyiko/mysql-binlog-connector-java)

[binlog增量订阅和消费组件：canal](https://github.com/alibaba/canal)

## 特殊表
---
* information_schema.tables 可查看有多少表
* information_schema.columns 可查看表有什么