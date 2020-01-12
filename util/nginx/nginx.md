# 概述

nginx 是一个高性能的http 和反向代理服务器

## 反向代理

正向代理，代理的是用户，用户提交请求，由代理转发到服务器，服务器只知道是那个代理的请求，并不知道是具体的哪个用户的请求，比如 vpn。而反向代理，代理的是服务器，用户请求代理，用户只知道代理服务器的地址，而不知道具体的服务器地址

# 安装

一，安装 gcc c++编译器

```bash
yum install -y gcc gcc-c++
```

二，安装 pcre ，解压后进入目录

```bash
./configure
make && make install
pcre-config --version
```

三，安装 openssl

```bash
yum install -y zlib zlib-devel libtool openssl openssl-devel
```

四，安装nginx,解压后进入目录

```bash
./configure
make && make install
```

安装后 `/usr/local/nginx` 目录下会出现 sbin 目录，启动测试 ,查看进程，访问 目标80端口。

```bash
./nginx
ps -ef |grep nginx
```



# 命令

`./nginx -v` 查看版本

`./nginx -s stop` 关闭

`./nginx` 启动

` ./nginx -s reload` 重载

# 配置

在 `/usr/local/nginx/conf` 中，有个 `nginx.conf` 作为配置文件

配置文件 由 global，events，http 组成

global 从文件开始至 events 结束，控制全局的配置

events 控制用户的网络连接

http 块，包含 http全局和server块, server块又包含全局server 和 location块

# 反向代理

此时 nginx 会对外暴露一个 80 端口，当请求 `/ ` 时会被转发到 `proxy_pass` 设置的地址

```
    server {
        listen       80;
        server_name  192.168.80.128;

        location / {
            root   html;
            proxy_pass http://192.168.80.128:8080;
            index  index.html index.htm;
        }
```

以上配置还可以添加多个 location 将不同的前缀转发至不同的服务

```
        location /student {
            proxy_pass http://192.168.80.128:8080/student;
        }

        location /ssm {
            proxy_pass http://192.168.80.128:8082/ssm;
        }
```

当 请求`http://plf.com/student/index.jsp` 会转发至第一个服务，请求`http://plf.com/ssm/select.do` 会转发至第二个服务（同一个linux启动两个tomcat，tomcat需要更改连接端口，访问端口和 AJP端口，且不能设置 CATALINA_HOME）

并且在第一个服务上可以通过相对路径请求第二服务上的资源，

如`fetch("../ssm/select.do").then(res=> res.text()).then(res=>{console.log(res)})`.

# 负载均衡

在 http 块中，定义一个 `upstream` 并命名 如 ssmServer，定义两个 server 属性，只需指定 ip 和 port

后续的location 定义时，使用这个别名 ssmServer 代替 ip 和 port ，当请求时默认会**轮询**两个服务器。

```
    upstream ssmServer {
        server 192.168.80.128:8082;
        server 192.168.80.128:8085;
    }
    server {
        listen       80;
        server_name  192.168.80.128;

        location /ssm {
            proxy_pass http://ssmServer/ssm;
        }
```

## 分配方式

**权重**

通过在负载均衡配置中 添加 `weight` 属性，属性越大，被分配到的次数越多

```
    upstream ssmServer {
        server 192.168.80.128:8082 weight=1;
        server 192.168.80.128:8085 weight=10;
    }
```

**ip hash**

记住用户的 ip ，以后此用户将一直访问同一个服务，可以解决 session 问题。

```
    upstream ssmServer {
        ip_hash;
        server 192.168.80.128:8082;
        server 192.168.80.128:8085;
    }
```

**fair**

根据服务器响应时间分配，响应快优先分配



# 动静分离

实现静态资源与服务器分离

在 系统 目录下 创建 `/ssm2/static/a.html` ，请求 `http://plf.com/static/a.html`

```
        location /static {
           root /ssm2;
        }
```



# 集群

在多台机器上使用 keepalived  一起监听同一个虚拟 ip  ，通过虚拟 ip 访问 nginx。同时检测 nginx 是否宕机，如果宕机则切换到另一个 keepalived