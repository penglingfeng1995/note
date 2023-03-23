# 概述

分布式协调服务

# 安装

解压后，修改 `conf/zoo.cfg` 配置文件，更改数据文件目录

```properties
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/tmp/zookeeper
# the port at which the clients will connect
clientPort=2181
```

tickTime是每次心跳的时间

启动 `./zkServer.sh start` ，暴露 2181 端口

使用`./zkCli.sh` 连接 本机zk

# 命令

`ls /` 查看根节点下的节点

`create /xxx dataxxx`  创建节点，并保存数据

`get /xxx` 获取数据

`set /xxx aaa`  设置数据

`delete /xxx` 删除节点，前提要把子节点删除完

# JAVA基本使用

## 使用官方提供的包

```xml
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.6.3</version>
</dependency>
```

```java
import org.apache.zookeeper.CreateMode;
import org.apache.zookeeper.Watcher;
import org.apache.zookeeper.ZooDefs;
import org.apache.zookeeper.ZooKeeper;

@Test
void test1() throws Exception {
    // 初始化，集群用逗号隔开
    String connectString = "192.168.147.128:2181";
    // 超时时间， 设置短了可能连不上
    int sessionTimeout = 15000;
    // 默认的监听器
    Watcher watcher = event -> System.out.println("触发事件:"+event);
    ZooKeeper zk = new ZooKeeper(connectString,sessionTimeout,watcher);

    // 创建一个持久化的节点 , 第三个参数是权限，设置可见范围，这里是所有人可见。第四个参数是 节点类型，可以设置永久节点，临时节点
    zk.create("/test","数据1".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
    zk.create("/test/tt1","数据2".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);

    // 获取数据
    byte[] data1 = zk.getData("/test", false, null);
    System.out.println("data1:"+new String(data1));
    byte[] data2 = zk.getData("/test/tt1", false, null);
    System.out.println("data2:"+new String(data2));

    // 获取子节点
    List<String> children = zk.getChildren("/test", false);
    System.out.println("/test child: "+ children);

    // 修改节点数据，version给-1，数据版本dataVersion 自动加1
    zk.setData("/test","数据3".getBytes(),-1);
    byte[] data3 = zk.getData("/test", false, null);
    System.out.println("data1:"+new String(data3));

    // 得先删除子节点，才能删除父节点
    zk.delete("/test/tt1",-1);
    zk.delete("/test",-1);
}
```

得到连接，创建节点，得到节点内容

开启监听

```java
// 可以第二个参数设置为true，就是开启监听，默认使用初始化时的watcher
byte[] data1 = zk.getData("/test", true, null);
System.out.println("data1:"+new String(data1));

// 也可以自定义一个 watcher 传进去,该watcher将监听这个节点
// stat参数，是可以new一个该对象，传进去后，会给这个对象赋值，有 mzxid,cversion等信息
Stat stat = new Stat();
byte[] data2 = zk.getData("/test/tt1", new Watcher() {
    @Override
    public void process(WatchedEvent event) {
        System.out.println("getData 触发事件" + event);
    }
}, stat);
System.out.println("data2:"+new String(data2) +",stat:"+stat);
```



## 使用 curator 框架

```xml
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>5.1.0</version>
</dependency>
```

由于原生的api，用起来不太行，于是有了curator ， 是一个 zookeeper 客户端高级api框架

```java
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;


@Test
void test2() throws Exception {

    // 重试策略，这里是每隔一秒重试一次，最多3次
    RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000,3);
    String connectString = "192.168.147.128:2181";

    //        CuratorFramework client = CuratorFrameworkFactory.newClient(connectString, retryPolicy);

    CuratorFramework client = CuratorFrameworkFactory.builder()
        .connectString(connectString)
        .sessionTimeoutMs(15000)
        .connectionTimeoutMs(2000)
        .retryPolicy(retryPolicy)
        .build();

    client.start();

    // 创建，同时创建父节点
    client.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT).forPath("/test/tt1", "测试值1".getBytes());

    // 获取值
    byte[] data1 = client.getData().forPath("/test/tt1");
    System.out.println("data1："+new String(data1));

    // 修改值
    client.setData().forPath("/test","测试值2：".getBytes());

    byte[] data2 = client.getData().forPath("/test/tt1");
    System.out.println("data2："+new String(data2));

    // 获取子节点
    List<String> children = client.getChildren().forPath("/test");
    System.out.println("子节点:"+children);

    // 可以删子节点
    client.delete().deletingChildrenIfNeeded().forPath("/test");


}
```

开启监听

```java
import org.apache.curator.framework.recipes.cache.CuratorCache;
import org.apache.curator.framework.recipes.cache.CuratorCacheListener;

CuratorCache curatorCache = CuratorCache.builder(client, "/plf").build();
CuratorCacheListener listener = CuratorCacheListener.builder()
    .forChanges((oldNode, node) -> {
        log.info("listener2 触发changes监听，旧值:{},新值：{}",  new String(oldNode.getData()), new String(node.getData()));
    })
    .forCreates(childData -> {
        log.info("初始化数据:{}",new String(childData.getData()));
    })
    .build();
curatorCache.listenable().addListener(listener);
curatorCache.start();
```



# 案例：动态上下线

客户端来监听每个服务端的在线状态

客户端代码

开启监听这个节点，有子节点新增，更新，删除，实时更新本地缓存的 set 

```java
private final Set<String> urlSet = new HashSet<>();
String rootPath = "/zk_demo_server";
@Override
public void afterPropertiesSet() throws Exception {
    CuratorCache curatorCache = CuratorCache.builder(zkClient, rootPath).build();
    CuratorCacheListener listener2 = CuratorCacheListener.builder()
        .forCreatesAndChanges((oldNode, node) -> urlSet.add(new String(node.getData())))
        .forDeletes(childData -> urlSet.remove(new String(childData.getData())))
        .build();
    curatorCache.listenable().addListener(listener2);
    curatorCache.start();
}
```

也可以每次都去查，不过性能肯定不如在内存缓存好的。

```java
List<String> serverNodes = zkClient.getChildren().forPath(rootPath);
List<String> urlList = new ArrayList<>();
for (String serverNode : serverNodes) {
    byte[] bytes = zkClient.getData().forPath(rootPath+"/"+serverNode);
    urlList.add(new String(bytes));
}
```



服务端代码

初始化的时候，把自己注册进去，模式选择临时节点，当掉线时，会自动删除节点，zk会通知到客户端

```java
String url = localhost+":"+serverPort;
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath("/zk_demo_server/server_"+serverPort , url.getBytes());
```



# 案例：分布式锁

```java
import org.apache.curator.framework.recipes.locks.InterProcessMutex;

// 锁是可以重复使用的，要注册成bean
InterProcessMutex lock = new InterProcessMutex(client, "/m6/lock/saleProduct");

log.info("准备执行锁");
if (lock.acquire(2, TimeUnit.SECONDS)) {
    try {
        log.info("获得锁");
    } finally {
        lock.release();
        log.info("释放锁");
    }
}
```

