---
title: 阿里云环境配置
date: 2020-09-16 14:20:57
categories: 
- Linux
---

# 阿里云环境配置

>  之前的学生免费试用的阿里云服务器过期了，只能再买一年重新配下环境，记录一下安装步骤，方便以后少走弯路。

## 1、MySQL

1. 删除原来的数据库：

```
[root@localhost ~]# rpm -qa|grep mariadb  // 查询出来已安装的mariadb
[root@localhost ~]# rpm -e --nodeps 文件名  // 卸载mariadb，文件名为上述命令查询出来的文件
```

2. 安装MySQL官方的yum repository

```
[root@localhost ~]# wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

3. 下载rpm包

```
[root@localhost ~]# yum -y install mysql57-community-release-el7-10.noarch.rpm
```

4. 安装MySQL服务

```
[root@localhost ~]# yum -y install mysql-community-server
```

- 如果安装不成功，则先执行：

  ```
  yum module disable mysql
  ```

5. mysql基本命令

```
启动：systemctl start  mysqld.service

重启：systemctl restart mysqld.service

停止：systemctl stop mysqld.service

查看状态：systemctl status mysqld.service
```

- 配置开机自启：

```
[root@woitumi-128 ~]# systemctl enable mysqld

[root@woitumi-128 ~]# systemctl daemon-reload   刚刚配置的服务需要让systemctl能识别，就必须刷新配置
```

6. 修改密码

```
获取mysql初始密码: grep 'temporary password' /var/log/mysqld.log
进入mysql: mysql -u root -p
MySQL版本5.7.6版本以前用户可以使用如下命令：
mysql> SET PASSWORD = PASSWORD('123456'); 
MySQL版本5.7.6版本开始的用户可以使用如下命令：
mysql> ALTER USER USER() IDENTIFIED BY '123456';
```

- mysql可能禁止密码过于简单

  - 设置 validate_password_policy 的全局参数为 0

  - ```
    set global validate_password_policy=0;
    ```

  - 设置密码最低长度

  - ```
    set global validate_password_length=6; 
    ```

​	7.退出数据库

```
mysql> exit;
```

8. 连接本地navicat问题

   1. 必须在阿里云服务器安全组中开放3306端口

   2. 开放数据库的远程访问权限

      ```
      use mysql;
      select host from user where user='root';
      update user set host = '%' where user ='root';
      flush privileges;
      ```

## 2、Java环境

1、使用xftp上传文件

2、解压缩

```
rpm -ivh jdk-8u221-linux-x64.rpm
```

3、环境变量

```
vim /etc/profile
```

```
//i进入编辑 esc :wq退出编辑
JAVA_HOME=/usr/java/jdk1.8.0_221-amd64
CLASSPATH=%JAVA_HOME%/lib:%JAVA_HOME%/jre/lib
PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin
export PATH CLASSPATH JAVA_HOME
```

4、让配置生效

```
source /etc/profile
```

