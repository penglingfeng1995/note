# 概述

分布式协调服务

# 安装

解压后，修改 `conf/zoo.cfg` 配置文件，更改数据文件目录

启动 `./zkServer.sh start` ，暴露 2181 端口

使用`./zkCli.sh` 连接 zk



# 命令

`ls /` 查看根节点下的节点



# 基本使用

```java
public static void main(String[] args) throws Exception {
    ZooKeeper zk = new ZooKeeper("192.168.80.128:2181", 50 * 1000, null);
    zk.create("/hello", "hello world2020".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
    String result = new String(zk.getData("/hello", false, new Stat()));
    System.out.println(result);
    zk.close();
}
```

得到连接，创建节点，得到节点内容