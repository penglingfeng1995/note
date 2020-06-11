# mysql5

在线安装

1,指定yum源

```bash
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

2,安装依赖

```bash
yum -y install mysql57-community-release-el7-10.noarch.rpm
```

3,安装mysql服务

```bash
yum -y install mysql-community-server
```

三步执行完后,会替换掉原来的mariadb

![](imgs/35.png)

4,启动mysql服务

```bash
systemctl start  mysqld.service
```

5,查看mysql状态

```bash
systemctl status mysqld.service
```

显示为active（running）为成功

![](imgs/36.png)

6,得到mysql的root用户初始密码

```bash
grep "password" /var/log/mysqld.log
```

![](imgs/37.png)

7,进入数据库,复制刚才的密码

```bash
mysql -uroot -p
```

8,修改root密码

先修改密码安全等级，之后就可以设置为简单密码

```bash
set global validate_password_policy=0;
set global validate_password_length=1;
```

然后修改密码

```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
```

![](imgs/38.png)

9,开启远程访问

进入mysql

```bash
grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
```

刷新后退出

```bash
flush privileges; 
```

退出 exit

10,移除之前的yum源，避免自动更新

```bash
yum -y remove mysql57-community-release-el7-10.noarch
```

11,防火墙开放端口

```bash
firewall-cmd --zone=public --add-port=3306/tcp --permanent
```

刷新

```bash
firewall-cmd --reload
```

12,然后就可以使用工具连接

![](imgs/39.png)



# mysql8

centos7安装mysql8

1,检测是否安装过mysql

```bash
rpm -qa | grep -i mysql
```

如果安装过，则是要yum删除

```bash
yum -y remove MySQL-*
```

查找有关mysql的目录

```bash
find / -name mysql
```

删除找到的目录，后再次查找检测

```bash
rm -rf {目录}
```

删除默认配置和密码

```bash
rm -rf /etc/my.cnf
rm -rf /root/.mysql_sercret
```

2,安装

指定rpm源

```bash
sudo rpm -Uvh https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
```

安装，按y执行，最后完毕

```bash
sudo yum --enablerepo=mysql80-community install mysql-community-server
```

3,启动

```bash
sudo service mysqld start
```

查看状态，得到 `active (running)`

```bash
service mysqld status
```

4,配置

获取root临时密码

```bash
grep "A temporary password" /var/log/mysqld.log
```

使用root登录

```bash
 mysql -uroot -p
```

尝试修改root用户在本机登录的密码

```bash
alter user 'root'@'localhost' identified by '123456';
```

此时会报错，提示密码不安全

修改密码复杂度和长度

```bash
set global validate_password.length=6;
set global validate_password.policy=0;
set global validate_password.check_user_name=off;
```

再次修改密码，成功

创建允许外部远程连接的用户,并授权

```bash
create user 'root'@'%' identified by '123456';
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;
```

暴露 3306 端口

# jdk

1,查看旧版本

```bash
java -version
```

2,查看安装包

```bash
rpm -qa | grep java
```

3,卸载旧版本

```bash
rpm -e --nodeps java-1.7.0-openjdk
```

4,解压安装包

```bash
tar -zxvf OpenJDK11U-jdk_x64_linux_hotspot_11.0.6_10.tar.gz
```

5,编辑 `/etc/profile` ，设置环境变量

```bash
export JAVA_HOME=/java/jdk11
export PATH=$PATH:$JAVA_HOME/bin
```

激活 `source /etc/profile`



