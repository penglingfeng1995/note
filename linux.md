## 常用命令

ls     查看当前目录

ll   	查看详细信息  -h

cd   进入目录    `..`返回上一级     `/`返回根目录

pwd   查看当前路径

mkdir   创建文件夹  -p

rmdir	删除空文件夹

touch 创建文件

cat		打开小文件

more	打开大文件有分页

less        打开文件

tail		打开文件最后几行

cp    复制

mv   剪切

rm   删除 -r  -f  

find  查找

vim    iao进入插入模式   esc 退回一般模式    :底行模式  q!强制退出   wq保存退出

grep   -i忽略大小写

tar -zcvf  xx.tar.gz xx   压缩

tar -zxvf  xx.tar.gz tdir  解压

ps -ef   查看进程

kill -9 pid  结束进程

ifconfig  网络配置

```shell
$ sudo ifconfig eth0 172.20.3.20 netmask 255.255.255.0
```

以上命令可以设置ip和网关

centos7使用:

```shell
ip addr
```



ping ip  测试延迟

reboot  重启

halt   关机

setup  网络设置

chmod  设置权限   chmod  u=rwx  xx.txt

​		rwx  r读w写x执行   u所属用户g所属组o其他 a所有

clear  清屏

rpm   -ivh 安装程序  -qa  查看所以程序  -e --nodeps 卸载

yum  联网下载

```
yum install glibc.i686
```

```
yum install gcc-c++
```

```
yum install libaio
```

service iptables stop   关闭防火墙

centos7:防火墙

```shell
启动： systemctl start firewalld
关闭： systemctl stop firewalld
查看状态： systemctl status firewalld 
开机禁用  ： systemctl disable firewalld
开机启用  ： systemctl enable firewalld
```





**通过xshell上传文件**

```shell
# yum install lrzsz -y
```

安装lrzsz，测试是否安装成功

```shell
# rpm -qa |grep lrzsz
lrzsz-0.12.20-36.el7.x86_64
```

使用rz上传文件,会打开本地电脑的文件夹,上传到当前目录

```shell
# rz
```



**vim编辑器**

安装

```shell
# yum -y install vim-enhanced
```

查看文件

```shell
# vim /xx/xx 
```

按下a/i/o可以对文本进行编辑,Esc退出编辑,`:q!`直接退出，`:wq`保存并退出