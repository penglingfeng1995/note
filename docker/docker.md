# docker

docker 是一个容器虚拟化技术

docker官网 :<https://www.docker.com/> 

## image 

镜像相当于模板，已经安装好部分软件的linux安装包

## container

容器就是根据镜像创建的实例，一个个正在允许的linux虚拟机

## repository

存放镜像的仓库,远程仓库和本地仓库

官方仓库 <https://hub.docker.com/> 

## 安装docker

### centos6.8

1，安装依赖 `sudo yum install -y epel-release`

2，安装docker `sudo yum install -y docker-io`

### centos7

1,安装依赖

```bash
sudo yum install -y yum-utils \
device-mapper-persistent-data \
lvm2
```

2,安装docker

```bash
sudo yum -y install docker-ce
```

3,启动docker

```bash
sudo systemctl start docker
```

4,验证安装

```bash
docker version
```

## 配置阿里云镜像仓库

## 命令

查看帮助 `docker --help`

### 镜像

docker images : 列出本地的仓库

docker search [imageName] : 查询远程仓库的镜像

docker pull [imageName] : 拉取镜像最新版到本地仓库

docker rmi [imNa]  ：删除本地仓库的镜像，-f 强制删除

### 容器

docker run [id name] :创建并运行一个容器

 -it 可以打开容器的交互式伪终端

docker ps :查看正在运行的容器

退出容器: exit 关闭容器并退出

docker start [id] :启动容器

docker restart [id] : 重启容器

docker stop [id] :停止容器

docker kill [id] :强制停止容器