---
title: MySQL主从(Master-Slave)实操
categories:
  - MySQL
tags:
  - 主从复制
toc: true
author: 虢國技酱
comments: true
date: 2019-05-22 16:07:58
description:
original:
permalink:
---

![](/images/mysql/10.jpg)

MySQL数据库自身提供的主从复制功能可以方便的实现数据的多处自动备份，实现数据库的拓展。多个数据备份不仅可以加强数据的安全性，通过实现读写分离还能进一步提升数据库的负载性能。

<!-- more -->

## 介绍
  * MySQL自身提供的主从复制是基于MySQL server层的逻辑日志binlog实现的；binlog开启后，会记录一个数据库操作逻辑二级制文件 
  * 主从数据库一般有：一主多从 和 多主多从。默认情况下 主数据库(master) 负责数据的写入，从数据库(slave) 负责数据查询。多个从数据库能够有效解决应用查询数据库的瓶颈，提高应用性能    
  * 读写分离，主从数据库不仅可以提高应用在数据库方向的性能；灾备，同时在数据安全方面也是一项非常使用的方案   
  * 主数据库一旦启动binlog二进制日志后，在该数据库上的操作都会以“事件”的方式记录到binlog中，从数据库(slave)通过一个I/O线程与主数据库(master)保持通信，并监控master的binlog变化，如果发现master二进制日志文件发生变化，则会把变化复制到自己的中继日志中，然后slave的一个SQL线程会把相关的“事件”执行到自己的数据库中，以此实现从数据库和主数据库的一致性，也就实现了主从复制。  


## 配置
  * 主服务器
    * 开启二进制日志(binlog)     
      ```
      [mysqld]
      log-bin=mysql-bin
      ```
    * 配置唯一的 `server-id`      
      ```
      [mysqld]
      log-bin=mysql-bin
      server-id=1
      ```
    * 重启mysql服务mysqld
      ```
      server mysqld restart  # 容器的话直接重启容器：docker restart container-name|container-ID
      ```
    * 创建一个用于slave和master通信的用户账号
      * 创建账号    
        ```
        mysql> CREATE USER 'repl'@'172.17.0.%' IDENTIFIED BY 'slavepass';
        ```
      * 分配权限     
        ```
        mysql> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'172.17.0.%';
        ```
      * 刷新权限  
        ```
        mysql> flush privileges;
        ```
    * 获得master二进制日志文件名及位置
      ```
      mysql > SHOW MASTER STATUS;
      +------------------+----------+--------------+------------------+
      | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
      +------------------+----------+--------------+------------------+
      | mysql-bin.000003 | 73       | test         | manual,mysql     |
      +------------------+----------+--------------+------------------+ 
      ```
  * 从服务器
    * 配置唯一的server-id
      ```
      [mysqld]
      server-id=2
      ```
    * 重启mysql服务mysqld
      ```
      server mysqld restart  # 容器的话直接重启容器：docker restart container-name|container-ID
      ```
    * 使用master创建的账号配置从服务器要读取的master信息
      ```
      mysql>  CHANGE MASTER TO
              MASTER_HOST='172.17.0.2',           # Master的地址
              MASTER_USER='repl',                 # 用于数据同步的用户
              MASTER_PASSWORD='slavepass',        # 用于同步的用户的密码
              MASTER_LOG_FILE='mysql-bin.000002', # 指定 Slave 从哪个日志文件开始复制数据，即上文中提到的 File 字段的值
              MASTER_LOG_POS=73,                  # 从哪个 Position 开始读，即上文中提到的 Position 字段的值
              MASTER_PORT=3306;                   # Master的端口号
      ```
    * 启动slave
      ```
      mysql> start slave; 
      ```
    * 修改slave从服务器上的主服务器master配置
      * 首先，关闭slave
        ```
        mysql> stop slave;
        ```
      * 然后，修改`MASTER_*`的配置
        ```
        mysql>  CHANGE MASTER TO
              MASTER_HOST='172.17.0.3',
              MASTER_USER='repl',
              MASTER_PASSWORD='slavepass',
              MASTER_LOG_FILE='mysql-bin.000002',
              MASTER_LOG_POS=73,
              MASTER_PORT=3306;
        ```
      * 启动slave
        ```
        mysql> start slave;
        ```
    * 设置slave只读`read_only`(可选)
      * 在我们常见的应用中，从数据库一般只服务查询，所以配置从服务器只读可以避免一下人为失误导致的数据不一致
      * 一般我们使用`read_only`设置slave只读，这种模式下会限制普通用户的写操作；但是不会影响slave同步复制的功能，同时也不能限制拥有super权限的用户进行写数据；
      * 关于MySQL `read_only` 更多信息：[]();
## 验证
  * 查看主从同步状态，主要关注这三个信息：`Slave_IO_Running`,`Slave_SQL_Running`,`Last_IO_Error`,`Last_SQL_Error`
    ![](/images/mysql/11.png)
  * 测试主从复制：在Master创建一个数据库，然后检查Slave是否存在此数据库

## 可能出现问题
  * 如果 `Slave_IO_Running` 和 `Slave_SQL_Running` 都是No, 请确认是否开启主从复制过程(`start slave`);
    ![](/images/mysql/12.png)
  * 如果 `Slave_IO_Running` 一直是 `Connecting`时，查看`Last_IO_Error`提示的错误信息
    ![](/images/mysql/11.png)
    这种情况一般下面几种原因造成的:
    * 网络不通 
      检查ip,端口
    * 密码不对
      检查是否创建用于同步的用户和用户密码是否正确
    * pos不对
      检查Master的 Position

## docker注意事项
  * 查询容器的ip
    ```
    docker inspect --format='{{.NetworkSettings.IPAddress}}' container-name|container-id
    ``` 
    > docker inspect
    > Description: Return low-level information on Docker objects
    > Usage: docker inspect [OPTIONS] NAME|ID [NAME|ID...]
    > 参考 [docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/)
  * 容器端口
    Master对外映射的端口是3339，Slave对外映射的端口是3340。因为docker容器是相互独立的，每个容器有其独立的ip，所以不同容器使用相同的端口并不会冲突。这里我们应该尽量使用mysql默认的3306端口，否则可能会出现无法通过ip连接docker容器内mysql的问题。 

> 参考：
> * [MySQL主从复制(Master-Slave)实践](https://www.cnblogs.com/gl-developer/p/6170423.html)
> * [基于Docker的Mysql主从复制搭建](https://www.cnblogs.com/songwenjie/p/9371422.html)
