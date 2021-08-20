# Study Kingshard

学习 kingshard（一个mysql分库分表中间件）

# 连接 kingshard
```shell
$ mysql -h127.0.0.1 -P9696 -uroot -proot
```

# MySQL测试集群启动
```shell
$ cd docker/mysql

$ docker-compose up -d
```

# MySQL连接
```shell
# 连接主库
$ mysql -h127.0.0.1 -P33060 -uroot -p111111

# 连接从库1
$ mysql -h127.0.0.1 -P33061 -uroot -p111111

# 连接从库2
$ mysql -h127.0.0.1 -P33062 -uroot -p111111

# 查看 Server Id
mysql> show variables like 'server_id';
```
# MySQL测试数据

创建测试用表结构：
```sql
mysql> create database ks;

mysql> use ks;

mysql> create table `user` (
  `id` int(11) not null,
  `age` int(11) default null,
  `name` char(50) default null,
   primary key (`id`)
) engine=InnoDB default charset=utf8;
```

插入测试数据：
```sql
mysql> insert into user(id,age,name) values(1,33,'sparklee');
```

# MySQL主从配置

在主库上创建用于数据复制的用户 repl：
```sql
# 创建用于主从复制的专用用户
mysql> create user 'repl'@'%' identified by 'repl';

# 给用户授予复制权限
mysql> grant replication slave on *.* to 'repl'@'%';

# 刷新用户权限
mysql> flush privileges;

# 查看主库状态
mysql> show master status;
```

在所有从库中执行 SQL 连接主库：
```sql
# 连接主库
mysql> change master to master_host='mysqlmaster',master_port=3306,master_user='repl',master_password='111111',master_auto_position=1;

# 启动复制
mysql> start slave;

# 查看主从复制状态
mysql> show slave status\G
Slave_IO_Running:  Yes
Slave_SQL_Running: Yes

# 暂时复制
mysql> stop slave;
```

# 工具使用

1、YAML Lint
> 可以使用在线工具 [YAML Lint](http://www.yamllint.com/) 校验 yaml 文件内容的格式是否正确。
