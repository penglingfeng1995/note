# 概述

httpclient 是一款http请求工具

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.2</version>
</dependency>
```

# GET

创建client，创建请求，执行请求，处理响应，关闭资源。

```java
public static void main(String[] args) throws IOException {
    CloseableHttpClient client = HttpClients.createDefault();

    HttpGet get = new HttpGet("https://www.hao123.com");

    CloseableHttpResponse response = client.execute(get);

    if (response.getStatusLine().getStatusCode() == HttpStatus.SC_OK) {
        HttpEntity entity = response.getEntity();
        String content = EntityUtils.toString(entity, StandardCharsets.UTF_8);
        System.out.println(content);
    }
    
    response.close();
    client.close();
}
```

## 参数

get 请求携带参数，使用 URIBuilder

```java
URIBuilder uriBuilder = new URIBuilder("https://www.toutiao.com/search");
uriBuilder.addParameter("keyword","美女");

HttpGet get = new HttpGet(uriBuilder.build());
```

# POST

发送 post 请求，只需替换请求对象即可。

```java
HttpPost post = new HttpPost("https://www.toutiao.com");
```

## 参数

设置 UrlEncodedFormEntity 对象。

```java
HttpPost post = new HttpPost("https://www.toutiao.com");

List<NameValuePair> params = new ArrayList<>();
params.add(new BasicNameValuePair("keyword", "张三"));
post.setEntity(new UrlEncodedFormEntity(params));
```

# 连接池

使用连接池创建 client 对象。

```java
PoolingHttpClientConnectionManager manager = new PoolingHttpClientConnectionManager();
manager.setMaxTotal(100);
CloseableHttpClient client = HttpClients.custom().setConnectionManager(manager).build();
```

# 请求配置

创建并设置 RequestConfig 对象。

```java
HttpPost post = new HttpPost("https://www.toutiao.com");
RequestConfig requestConfig = RequestConfig.custom()
    .setConnectTimeout(1000)
    .setSocketTimeout(1000 * 10)
    .build();
post.setConfig(requestConfig);
```

# Jsoup

```xml
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.11.3</version>
</dependency>
```

jsoup 是一款html分析工具，也可以直接发起请求

```java
Document document = Jsoup.parse(new URL("https://www.toutiao.com"), 1000);
Elements title = document.getElementsByTag("title");
String text = title.first().text();
System.out.println(text);
```

虽然可以发起请求，但是不支持多线程，连接池等方式，通常只用于解析。

```java
Document document = Jsoup.parse(content);
String title = document.getElementsByTag("title").first().text();
System.out.println(title);
```

