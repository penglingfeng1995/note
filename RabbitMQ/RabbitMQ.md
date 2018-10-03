# RabbitMQ

## 一，安装RabbitMQ

### erlang环境

​	RabbitMQ是由Pivotal团队(编写spring的团队)使用erlang语言编写，运行RabbitMQ需要erlang的环境，erlang是一种面向并发的编程语言，安装erlang后添加erlang的环境变量

### 安装rabbitmq

​	安装rabbitmq并添加环境变量

### 安装插件

​	安装插件可以有一个网页给我们直接操作

```
rabbitmq-plugins enable rabbitmq_management 
```

### 测试

​	rabbitmq默认开机启动，如果出现服务已启动，去资源管理器关闭rabbit的服务

```
rabbitmq-server start
```

进入   `http://localhost:15672/`会有登录界面，默认用户  guest/guest

## 二，用户设置

### 添加用户

​	点击上方admin栏，下方有add a user,添加一个新的用户，设置权限为admin(超级用户)

### 添加虚拟地址

​	点击右侧Virtual  Hosts  添加一个新的地址  例如`/vhost_1`,

### 分配权限

​	点击刚新添加的用户,在Permissions中set permission给用户分配地址访问权限

## 三，队列模式

### 项目环境

创建maven项目,添加依赖

```xml
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>4.0.2</version>
</dependency>
```

编写得到连接的工具类

```java
package com.znsd.testmq.util;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

public class RabbitMQConnectionUtils {

	public static Connection getConnection() throws IOException, TimeoutException {
		//创建连接工厂
		ConnectionFactory connectionFactory = new ConnectionFactory();
		//ip地址
		connectionFactory.setHost("192.168.2.135");
		//ampq协议的端口号为5672
		connectionFactory.setPort(5672);
		//用户名
		connectionFactory.setUsername("user_plf");
		//密码
		connectionFactory.setPassword("123456");
		//虚拟地址
		connectionFactory.setVirtualHost("/vhost_plf");
		//得到连接
		Connection connection = connectionFactory.newConnection();
		return connection;
	}
}

```



### 简单队列

简单队列是指，生产者发送一条消息，到指定的队列，消费者监听这个队列，得到消息

![](static/1.png)

编写发送者

```java
package com.znsd.testmq.simple;

import java.io.IOException;
import java.util.Date;
import java.util.concurrent.TimeoutException;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.znsd.testmq.util.RabbitMQConnectionUtils;

public class Sender {

	
	public static void main(String[] args) throws IOException, TimeoutException {

		//得到连接
		Connection connection = RabbitMQConnectionUtils.getConnection();
		//得到管道
		Channel channel = connection.createChannel();
		//队列的名称
		String queue="test1_simple_queue";
		//声明队列
		channel.queueDeclare(queue, false, false, false, null);
		//消息
		String msg="test a simple msg by plf"+new Date();
		//发送消息到指定的队列
		channel.basicPublish("", queue, null, msg.getBytes());
		//关闭资源
		channel.close();
		connection.close();
		
		System.out.println("Sender.main()");
	}

}

```

编写接受者

```java
package com.znsd.testmq.simple;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

import com.rabbitmq.client.AMQP.BasicProperties;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConsumerCancelledException;
import com.rabbitmq.client.DefaultConsumer;
import com.rabbitmq.client.Envelope;
import com.rabbitmq.client.ShutdownSignalException;
import com.znsd.testmq.util.RabbitMQConnectionUtils;

public class Receiver {

	public static void main(String[] args) throws Exception {
		//得到同样的管道并声明队列
		Connection connection = RabbitMQConnectionUtils.getConnection();
		Channel channel = connection.createChannel();
		String queue = "test1_simple_queue";
		channel.queueDeclare(queue, false, false, false, null);
		//消费者实例，重写handleDelivery方法，参数body即为消息体
		DefaultConsumer consumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				//得到消息体，转为字符串
				String msg = new String(body);
				System.out.println("[new receive] msg:" + msg);
			}
		};
		//开启监听,指定消费者和队列
		channel.basicConsume(queue, true, consumer);
	}
}

```

### 工作队列

工作队列会把消息分发到不同的消费者中

![](static/2.png)

在生产者中发送多条消息

```java
for(int i=0;i<50;i++) {
    String msg="the 让 "+i+" 次";
    channel.basicPublish("", queue, null, msg.getBytes());
    System.out.println("Sender.main()"+msg);
}
```

定义两个接受者,在重写的方法中，使其sleep1s，另一个sleep3s，模拟业务的执行时间

```java
DefaultConsumer consumer = new DefaultConsumer(channel) {
			@Override
			public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
					throws IOException {
				String msg=new String(body);
				System.out.println("[receive  1  ]  msg:"+msg);
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		};
```

先启动两个接受者，再使用发送者发送消息，不管每个接受者执行业务的快慢，消息总是会对半分

### 订阅模式

订阅模式会把消息发送到一个交换机中，与交换机绑定的队列都可以接受到消息

![](static/3.png)

发送者

```java
Connection connection = RabbitMQConnectionUtils.getConnection();
//得到管道
Channel channel = connection.createChannel();

String exchange="test_exchange1";
//声明交换机,及交换机的类型，fanout为分发
channel.exchangeDeclare(exchange, "fanout");
//消息
String msg="test a simple msg by plf"+new Date();
//发送消息到交换机
channel.basicPublish(exchange, "", null, msg.getBytes());
//关闭资源
channel.close();
connection.close();
```

接受者

```java
Connection connection = RabbitMQConnectionUtils.getConnection();
Channel channel = connection.createChannel();
String queue="test_work_queue22";
String exchange="test_exchange1";
channel.queueDeclare(queue, false, false, false, null);
//把队列绑定到交换机
channel.queueBind(queue, exchange, "");
DefaultConsumer consumer = new DefaultConsumer(channel) {
    @Override
    public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
        throws IOException {
        String msg=new String(body);
        System.out.println("[receive  2  ]  msg:"+msg);
    }
};

channel.basicConsume(queue, true, consumer);
```

第二个接受者绑定到同样的交换机，两个接受者都可以接到消息

### 路由模式

在订阅模式的基础上，发送消息的时候加上路由键，只有拥有指定路由键的队列才可以接受到,可以绑定多个键

![](static/4.png)

发送者

声明交换机的时候，类型改为direct，发送的时候带上routingKey参数

```java
String exchange="test_exchange1";
channel.exchangeDeclare(exchange, "direct");
//消息
String msg="test a simple msg by plf"+new Date();
//发送消息
channel.basicPublish(exchange, "info", null, msg.getBytes());
```

接收者

绑定交换机的时候指定一个或多个路由键,键一致就可以接收消息

```java
channel.queueBind(queue, exchange, "error");
channel.queueBind(queue, exchange, "info");
channel.queueBind(queue, exchange, "warn");
```

### 主题模式

主题模式允许使用通配符形式的路由键

![](static/5.png)

发送者

声明交换机时，类型为topic

```java
String exchange="test_exchange_topic1";
channel.exchangeDeclare(exchange, "topic");
//消息
String msg="test a simple msg by plf"+new Date();
//发送消息
channel.basicPublish(exchange, "korea.weather", null, msg.getBytes());
```

接受者

绑定路由键可以使用`#`号或`*`号作为通配符,比如korea.weather可以匹配到korea.#

```java
channel.queueBind(queue, exchange, "#.news");
channel.queueBind(queue, exchange, "korea.#");
```

## 四，消息确认机制

### 事物机制

发送者发送消息时，可以开启事物，出错回滚

```java
try {
    //开启事物机制
    channel.txSelect();
    //消息体
    String msg="test a simple msg by plf"+new Date();
    //发送消息
    channel.basicPublish("", queue, null, msg.getBytes());
    //提交事物
    channel.txCommit();
    System.out.println("success");
    //关闭资源
} catch (Exception e) {
    //回滚事物
    channel.txRollback();
    System.out.println("faild");
    e.printStackTrace();
}
```

### confirm机制

事物机制会严重影响效率,confirm机制不会

```java
//选择confirm机制
channel.confirmSelect();
//消息
String msg="test a simple msg by plf"+new Date();
//发送消息
channel.basicPublish("", queue, null, msg.getBytes());
//调用waitForConfirms方法，确认消息是否成功
if(channel.waitForConfirms()) {
    System.out.println("success confirm");
}else {
    System.out.println("faild");
}
```

### 消息持久化

声明队列时,把durable参数设置为true，消息则会持久化，当rabbitmq服务停止的时候，未处理的消息仍在

```java
boolean durable=true;
channel.queueDeclare(queue, durable, false, false, null);
```



