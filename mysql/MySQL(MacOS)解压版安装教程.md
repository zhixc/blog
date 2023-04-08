# Mac mysql解压版安装

在 Mac 系统上, 安装 MySQL Server 一般是用 DMG 包在图形化界面下按提示安装, 此外 mysql还提供了 Compressed TAR Archive 二进制包安装方式, 即免安装解压运行版, 相比 DMG 包, 免安装版过程更为简洁, 纯命令行操作, 更符合码客们的折腾精神。

```ruby
系统环境: OS X Yosemite 10.10.3
登录用户: wid (有 sudo 权限)
MySQL版本: 5.6.24 (mysql-5.6.24-osx10.9-x86_64.tar.gz)

MySQL下载: http://dev.mysql.com/downloads/mysql/
```

找到下载的 MySQL tar.gz 文件位置, 浏览器下载的一般在当前用户的 Downloads 目录, 即 `/Users/<YourName>/Downloads`, 进入终端, 解压 tar.gz 文件:

```xml
cd /Users/<YourName>/Downloads

tar zxvf mysql-5.6.24-osx10.9-x86_64.tar.gz
```

解压完成后得到 `mysql-5.6.24-osx10.9-x86_64` 目录, 移动解压目录到 MySQL 默认安装路径 `/usr/local/mysql` 下, `/usr/local` 路径不存在时, 先 `sudo mkdir /usr/local` 创建。

```bash
# 移动解压后的二进制包到安装目录
sudo mv mysql-5.6.24-osx10.9-x86_64 /usr/local/mysql

备注：这里一定要用mysql这个名字，如果用原来解压名mysql-5.6.24-0sx10.9-x86_64,启动的时候会报找不到/usr/local/mysql目录，我开始就在这里犯了一个小错。。
# 更改 mysql 安装目录所属用户与用户组
cd /usr/local
sudo chown -R root:wheel mysql


# 执行 scripts 目录下的 mysql_install_db 脚本完成一些默认的初始化(创建默认配置文件、授权表等)
cd /usr/local/mysql
sudo scripts/mysql_install_db --user=mysql
```

安装完成, [测试](http://lib.csdn.net/base/softwaretest)启动、重启与停止:

```bash
cd /usr/local/mysql

# 启动
sudo support-files/mysql.server start

# 重启
sudo support-files/mysql.server restart

# 停止
sudo support-files/mysql.server stop

# 检查 MySQL 运行状态
sudo support-files/mysql.server status
```

------

##### 初始化 MySQL root 密码

```bash
# 需要 MySQL 在运行状态执行
cd /usr/local/mysql/bin

./mysqladmin -u root password <your-password>
```

##### 通过自带的 MySQL Client 连接数据库

```bash
cd /usr/local/mysql/bin

./mysql -u root -p

<your-password>
```



##### 设置UTF-8编码

新建一个文本文件，重命名为my.cnf

里面填入以下内容

```
[client]
default-character-set=utf8
 
[mysql]
default-character-set=utf8
 
[mysqld]
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8
```

然后保存

将 my.cnf 文件移动到系统硬盘根目录下的 etc（或者 /private/etc，这两个 etc 文件夹本质上是同一个） 文件夹下，需要鉴定密码，这个密码是你的mac电脑登录（锁屏）密码

如果看不到硬盘根目录下的 etc（或 private）文件夹，在硬盘根目录下按 command + shift + .

即可查看隐藏文件和文件夹。



##### 查看数据库编码

上一步操作成功以后，重启电脑，再启动mysql即可，登录mysql，然后输入

```
show variables like '%char%';
```

然后回车，即可看到如下内容：

```
fiodeAir:mysql fio$ cd /usr/local/mysql/bin
fiodeAir:bin fio$ 
fiodeAir:bin fio$ ./mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.5.44 MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show variables like '%char%';
+--------------------------+----------------------------------+
| Variable_name            | Value                            |
+--------------------------+----------------------------------+
| character_set_client     | utf8                             |
| character_set_connection | utf8                             |
| character_set_database   | utf8                             |
| character_set_filesystem | binary                           |
| character_set_results    | utf8                             |
| character_set_server     | utf8                             |
| character_set_system     | utf8                             |
| character_sets_dir       | /usr/local/mysql/share/charsets/ |
+--------------------------+----------------------------------+
8 rows in set (0.00 sec)

mysql> 
```



