### 导入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.mongodb/mongo-java-driver -->
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongo-java-driver</artifactId>
    <version>3.6.3</version>
</dependency>
```

### 得到客户端

这里需要先启动mongodb的服务，使用ip和端口号

```java
MongoClient mongoClient=new MongoClient("192.168.80.128", 27017);
```

### 得到数据库和表

```java
MongoDatabase database = mongoClient.getDatabase("test");
MongoCollection<Document> collection = database.getCollection("plf");
```

原始的mongo的api使用  MongoCollection 相当于表，集合，去操作数据

### 添加文档对象

```java
Document document=new Document();
document.append("username", "王五");
document.append("password", "123456");
collection.insertOne(document);
```

使用Document对象相当于每条数据，这里新建一个Document对象，通过append添加键值对，也就是属性，通过insertOne去添加这个文档对象。

### 简单查询

直接通过find方法，得到一个文档集合，直接迭代

```java
FindIterable<Document> finds = collection.find();
for (Document document : finds) {
    System.out.println(document);
}
```

