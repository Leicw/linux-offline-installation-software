

# centOS 7  离线安装 MySQL 5.6

> centOS 7 离线安装 MySQL 5.6

> **准备环境**
>
> 1、离线 centOS 7（此处为 centOS 7 最小安装）
>
> 2、nginx 安装文件 （mysql-5.6.51-linux-glibc2.12-x86_64.tar.gz 已放到下面的链接中，请自行下载或自己去官网下载最新版）
>
> 3、nginx 依赖包 （目前已放到 lib 目录中，可自行下载）

目前所有安装所需要的包和依赖都可去以下地址自行根据所对应软件进行下载

[https://gitee.com/livekeys/linux-offline-installation-software.git](https://gitee.com/livekeys/linux-offline-installation-software.git)



## 一、卸载 mariadb

> 查询之前是否有 mariadb ，有就将其删除，并删除以前的配置文件 /etc/my.cnf

```bash
[root@localhost ~]# rpm -qa | grep mariadb*
mariadb-libs-5.5.56-2.el7.x86_64
[root@localhost ~]# rpm -e mariadb-libs --nodeps
[root@localhost ~]# rpm -qa | grep mariadb*
[root@localhost ~]# rm -f /etc/my.cnf
[root@localhost ~]#
```



## 二、安装依赖

> 将 lib 中的所有依赖上传到 linux 中，并用命令进行安装

```bash
[root@localhost lib]# yum -y localinstall *.rpm
```



## 三、安装 MySQL 5.6

1、解压并把文件复制到你想要安装的目录（此处我将 /opt/mysql 作为安装目录）

```bash
[root@localhost mysql_install]# tar -xzvf mysql-5.6.51-linux-glibc2.12-x86_64.tar.gz
[root@localhost mysql_install]# ls
lib  mysql-5.6.51-linux-glibc2.12-x86_64  mysql-5.6.51-linux-glibc2.12-x86_64.tar.gz
[root@localhost mysql_install]# cp -R mysql-5.6.51-linux-glibc2.12-x86_64 /opt/mysql
[root@localhost mysql_install]# cd /opt/mysql
[root@localhost mysql]# ls
bin  data  docs  include  lib  LICENSE  man  mysql-test  README  scripts  share  sql-bench  support-files
```

2、新建 mysql 组和 mysql 用户，并将 mysql 目录更换成 mysql 用户组

```bash
[root@localhost mysql]# groupadd mysql
[root@localhost mysql]# useradd -g mysql mysql
[root@localhost mysql]# cd ..
[root@localhost opt]# ls
mysql
[root@localhost opt]# chown -R mysql:mysql mysql
[root@localhost opt]# ll
total 0
drwxr-xr-x. 13 mysql mysql 191 Mar 23 01:42 mysql
[root@localhost opt]# cd mysql
[root@localhost mysql]# ll
total 224
drwxr-xr-x.  2 mysql mysql   4096 Mar 23 01:42 bin
drwxr-xr-x.  3 mysql mysql     18 Mar 23 01:42 data
drwxr-xr-x.  2 mysql mysql     55 Mar 23 01:42 docs
drwxr-xr-x.  3 mysql mysql   4096 Mar 23 01:42 include
drwxr-xr-x.  3 mysql mysql   4096 Mar 23 01:42 lib
-rw-r--r--.  1 mysql mysql 200256 Mar 23 01:42 LICENSE
drwxr-xr-x.  4 mysql mysql     30 Mar 23 01:42 man
drwxr-xr-x. 10 mysql mysql   4096 Mar 23 01:42 mysql-test
-rw-r--r--.  1 mysql mysql    566 Mar 23 01:42 README
drwxr-xr-x.  2 mysql mysql     30 Mar 23 01:42 scripts
drwxr-xr-x. 28 mysql mysql   4096 Mar 23 01:42 share
drwxr-xr-x.  4 mysql mysql   4096 Mar 23 01:42 sql-bench
drwxr-xr-x.  2 mysql mysql    136 Mar 23 01:42 support-files
```

3、新建 /var/lib/mysql 目录用于存放 mysq.sock（也可以新建其他的目录），并将该目录所属组更改成 myql

```bash
[root@localhost mysql]# mkdir /var/lib/mysql
[root@localhost mysql]# chown -R mysql:mysql /var/lib/mysql
[root@localhost mysql]# ls -al /var/lib/mysql
total 4
drwxr-xr-x.  2 mysql mysql    6 Mar 23 01:51 .
drwxr-xr-x. 26 root  root  4096 Mar 23 01:51 ..
```

4、进入 mysql 安装目录，进行初始化 mysql

```bash
[root@localhost mysql]# ./scripts/mysql_install_db --user=mysql --basedir=/opt/mysql --datadir=/opt/mysql/data
Installing MySQL system tables...2022-03-23 01:53:51 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2022-03-23 01:53:51 0 [Note] Ignoring --secure-file-priv value as server is running with --bootstrap.
2022-03-23 01:53:51 0 [Note] /opt/mysql/bin/mysqld (mysqld 5.6.51) starting as process 13346 ...
2022-03-23 01:53:51 13346 [Note] InnoDB: Using atomics to ref count buffer pool pages
2022-03-23 01:53:51 13346 [Note] InnoDB: The InnoDB memory heap is disabled
2022-03-23 01:53:51 13346 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
2022-03-23 01:53:51 13346 [Note] InnoDB: Memory barrier is not used
2022-03-23 01:53:51 13346 [Note] InnoDB: Compressed tables use zlib 1.2.11
2022-03-23 01:53:51 13346 [Note] InnoDB: Using Linux native AIO
2022-03-23 01:53:51 13346 [Note] InnoDB: Using CPU crc32 instructions
2022-03-23 01:53:51 13346 [Note] InnoDB: Initializing buffer pool, size = 128.0M
2022-03-23 01:53:51 13346 [Note] InnoDB: Completed initialization of buffer pool
2022-03-23 01:53:51 13346 [Note] InnoDB: The first specified data file ./ibdata1 did not exist: a new database to be created!
2022-03-23 01:53:51 13346 [Note] InnoDB: Setting file ./ibdata1 size to 12 MB
2022-03-23 01:53:51 13346 [Note] InnoDB: Database physically writes the file full: wait...
2022-03-23 01:53:51 13346 [Note] InnoDB: Setting log file ./ib_logfile101 size to 48 MB
2022-03-23 01:53:51 13346 [Note] InnoDB: Setting log file ./ib_logfile1 size to 48 MB
2022-03-23 01:53:51 13346 [Note] InnoDB: Renaming log file ./ib_logfile101 to ./ib_logfile0
2022-03-23 01:53:51 13346 [Warning] InnoDB: New log files created, LSN=45781
2022-03-23 01:53:51 13346 [Note] InnoDB: Doublewrite buffer not found: creating new
2022-03-23 01:53:51 13346 [Note] InnoDB: Doublewrite buffer created
2022-03-23 01:53:51 13346 [Note] InnoDB: 128 rollback segment(s) are active.
2022-03-23 01:53:51 13346 [Warning] InnoDB: Creating foreign key constraint system tables.
2022-03-23 01:53:51 13346 [Note] InnoDB: Foreign key constraint system tables created
2022-03-23 01:53:51 13346 [Note] InnoDB: Creating tablespace and datafile system tables.
2022-03-23 01:53:51 13346 [Note] InnoDB: Tablespace and datafile system tables created.
2022-03-23 01:53:51 13346 [Note] InnoDB: Waiting for purge to start
2022-03-23 01:53:51 13346 [Note] InnoDB: 5.6.51 started; log sequence number 0
2022-03-23 01:53:51 13346 [Note] RSA private key file not found: /opt/mysql/data//private_key.pem. Some authentication plugins will not work.
2022-03-23 01:53:51 13346 [Note] RSA public key file not found: /opt/mysql/data//public_key.pem. Some authentication plugins will not work.
2022-03-23 01:53:51 13346 [Note] Binlog end
2022-03-23 01:53:51 13346 [Note] InnoDB: FTS optimize thread exiting.
2022-03-23 01:53:51 13346 [Note] InnoDB: Starting shutdown...
2022-03-23 01:53:53 13346 [Note] InnoDB: Shutdown completed; log sequence number 1625977
OK

Filling help tables...2022-03-23 01:53:53 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2022-03-23 01:53:53 0 [Note] Ignoring --secure-file-priv value as server is running with --bootstrap.
2022-03-23 01:53:53 0 [Note] /opt/mysql/bin/mysqld (mysqld 5.6.51) starting as process 13368 ...
2022-03-23 01:53:53 13368 [Note] InnoDB: Using atomics to ref count buffer pool pages
2022-03-23 01:53:53 13368 [Note] InnoDB: The InnoDB memory heap is disabled
2022-03-23 01:53:53 13368 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
2022-03-23 01:53:53 13368 [Note] InnoDB: Memory barrier is not used
2022-03-23 01:53:53 13368 [Note] InnoDB: Compressed tables use zlib 1.2.11
2022-03-23 01:53:53 13368 [Note] InnoDB: Using Linux native AIO
2022-03-23 01:53:53 13368 [Note] InnoDB: Using CPU crc32 instructions
2022-03-23 01:53:53 13368 [Note] InnoDB: Initializing buffer pool, size = 128.0M
2022-03-23 01:53:53 13368 [Note] InnoDB: Completed initialization of buffer pool
2022-03-23 01:53:53 13368 [Note] InnoDB: Highest supported file format is Barracuda.
2022-03-23 01:53:53 13368 [Note] InnoDB: 128 rollback segment(s) are active.
2022-03-23 01:53:53 13368 [Note] InnoDB: Waiting for purge to start
2022-03-23 01:53:53 13368 [Note] InnoDB: 5.6.51 started; log sequence number 1625977
2022-03-23 01:53:53 13368 [Note] RSA private key file not found: /opt/mysql/data//private_key.pem. Some authentication plugins will not work.
2022-03-23 01:53:53 13368 [Note] RSA public key file not found: /opt/mysql/data//public_key.pem. Some authentication plugins will not work.
2022-03-23 01:53:53 13368 [Note] Binlog end
2022-03-23 01:53:53 13368 [Note] InnoDB: FTS optimize thread exiting.
2022-03-23 01:53:53 13368 [Note] InnoDB: Starting shutdown...
2022-03-23 01:53:55 13368 [Note] InnoDB: Shutdown completed; log sequence number 1625987
OK

To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your system

PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
To do so, start the server, then issue the following commands:

  /opt/mysql/bin/mysqladmin -u root password 'new-password'
  /opt/mysql/bin/mysqladmin -u root -h localhost.localdomain password 'new-password'

Alternatively you can run:

  /opt/mysql/bin/mysql_secure_installation

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the manual for more instructions.

You can start the MySQL daemon with:

  cd . ; /opt/mysql/bin/mysqld_safe &

You can test the MySQL daemon with mysql-test-run.pl

  cd mysql-test ; perl mysql-test-run.pl

Please report any problems at http://bugs.mysql.com/

The latest information about MySQL is available on the web at

  http://www.mysql.com

Support MySQL by buying support/licenses at http://shop.mysql.com

New default config file was created as /opt/mysql/my.cnf and
will be used by default by the server when you start it.
You may edit this file to change server settings
```

4、复制和修改配置文件

```bash
[root@localhost mysql]# cp ./support-files/my-default.cnf /etc/my.cnf
[root@localhost mysql]# chown mysql:mysql /etc/my.cnf
[root@localhost mysql]# chmod 644 /etc/my.cnf
```

**my.cnf 配置文件内容如下：**（这里根据自己的需求修改其他配置）

**注意以下几点：**

- `socket` 写上面我们第三步新建的那个目录
- `basedir` 为 mysql 安装目录
- `datadir` 为数据目录（这里是 mysql 安装目录下的 data 目录）

```bash
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
socket=/var/lib/mysql/mysql.sock

[mysqld]
skip-name-resolve
#设置 13306 端口
port = 13306
socket=/var/lib/mysql/mysql.sock
# 设置mysql的安装目录
basedir=/opt/mysql
# 设置mysql数据库的数据的存放目录
datadir=/opt/mysql/data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
lower_case_table_name=1
max_allowed_packet=16M
```

5、复制启动脚本到资源目录

```bash
[root@localhost mysql]# cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld
[root@localhost mysql]# chmod +x /etc/rc.d/init.d/mysqld
[root@localhost mysql]# chown mysql:mysql /etc/rc.d/init.d/mysqld
```

6、修改启动脚本

```bash
[root@localhost mysql]# vim /etc/rc.d/init.d/mysqld
```

注：因为，我没有将 mysql 安装在 /usr/local/mysql 目录下，所以在启动文件里需要修改以下两个值

```bash
basedir=/opt/mysql
datadir=/opt/mysql/data
```

7、将 mysqld 服务加入到系统服务里面

```bash
[root@localhost mysql]# chkconfig --add mysqld
[root@localhost mysql]# chkconfig --list mysqld

Note: This output shows SysV services only and does not include native
      systemd services. SysV configuration data might be overridden by native
      systemd configuration.

      If you want to list systemd services use 'systemctl list-unit-files'.
      To see services enabled on particular target use
      'systemctl list-dependencies [target]'.

mysqld          0:off   1:off   2:on    3:on    4:on    5:on    6:off
```

8、使用命令 systemctl 启动 mysql 服务（Active 状态为 active (running) 即为启动成功）

```bash
[root@localhost mysql]# systemctl start mysql
[root@localhost mysql]# systemctl status mysql
● mysqld.service - LSB: start and stop MySQL
   Loaded: loaded (/etc/rc.d/init.d/mysqld; bad; vendor preset: disabled)
   Active: active (running) since Wed 2022-03-23 02:11:17 EDT; 5s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 13452 ExecStart=/etc/rc.d/init.d/mysqld start (code=exited, status=0/SUCCESS)
   CGroup: /system.slice/mysqld.service
           ├─13463 /bin/sh /opt/mysql/bin/mysqld_safe --datadir=/opt/mysql/data --pid-file=/opt/mysql/data/lo...
           └─13688 /opt/mysql/bin/mysqld --basedir=/opt/mysql --datadir=/opt/mysql/data --plugin-dir=/opt/mys...

Mar 23 02:11:16 localhost.localdomain systemd[1]: Starting LSB: start and stop MySQL...
Mar 23 02:11:16 localhost.localdomain mysqld[13452]: Starting MySQL.Logging to '/opt/mysql/data/localhost....r'.
Mar 23 02:11:17 localhost.localdomain mysqld[13452]: SUCCESS!
Mar 23 02:11:17 localhost.localdomain systemd[1]: Started LSB: start and stop MySQL.
Hint: Some lines were ellipsized, use -l to show in full.
```

9、将 mysql 客户端配置到环境变量中，并使配置生效

```bash
[root@localhost mysql]# vim /etc/profile
# 在末尾增加以下两行
export MYSQL_HOME=/opt/mysql
export PATH=$MYSQL_HOME/bin:$PATH
[root@localhost mysql]# source /etc/profile
```

10、测试是否能连接 mysql

**注：**第一次登录不需要密码，直接回车就行

```bash
[root@localhost ~]# mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.51 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>

```

11、配置 `root` 账号的密码为 `P@ssw0rd`

```bash
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> update user set password=password('P@ssw0rd') where user='root' and host='localhost';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

12、设置远程主机登录

```bash
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> grant all privileges on *.* to 'root'@'%' identified by 'P@ssw0rd' with grant option;
Query OK, 0 rows affected (0.00 sec)
```

13、重启 mysqld 服务并测试登录

```bash
[root@localhost ~]# systemctl restart mysql
[root@localhost ~]# mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.51 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)

mysql>
```

**至此，已完成 mysql 5.6 的安装**



> 如有错误的地方，欢迎留言指正，谢谢！