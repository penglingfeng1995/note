# 概述

jasypt 是一款加密工具

```xml
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>2.1.0</version>
</dependency>
```

# 加密

可以先基于springboot 项目写一个测试用例进行加密

```java
@SpringBootTest
class DemoApplicationTests {

    // starter 会自动创建加密器实例
    @Autowired
    private StringEncryptor jasyptStringEncryptor;
    
    @Test
    void testJasypt(){
        String encrypt = jasyptStringEncryptor.encrypt("abc123");
        System.out.println(encrypt);
    }
}
```

外面运行发现，提示少了个属性

```java
java.lang.IllegalStateException: Required Encryption configuration property missing: jasypt.encryptor.password
```

这个 password 就是 盐，要求必须要加盐

我们可以先配置一个盐，在 `application.properties` 中，盐的复杂程度可以自定义

```properties
jasypt.encryptor.password=123
```

再次执行可以得到加密后的字符串，每次执行生产的密文会不一样，例如

```
8pdSoi0Embw40rwmgp7LPgDKBXchzMrl
```

# 解密

我们可以对密文进行解密，就可以得到原文

```java
@Test
void testJasypt2(){
    String decrypt = jasyptStringEncryptor.decrypt("AQq2goLxQNVMk5eflOxzrg==");
    System.out.println(decrypt);// abc123
}
```

此时如果更改 盐值，解密会失败，抛出 `org.jasypt.exceptions.EncryptionOperationNotPossibleException` ，要想解密成功盐值需要与加密时一致。

生产环境需要更复杂的盐，比如uuid

# 属性注入

我们可以用加密后的密码，设置在配置文件中，使用 `ENC()` 包裹

```properties
my.pwd=ENC(rv/gRE53qiVjBq3s9A6w+w==)
```

jasypt 会识别这种属性，进行拦截解密

```java
@Value("${my.pwd}")
private String myPwd;
```

在我们使用属性时，直接就是明文。