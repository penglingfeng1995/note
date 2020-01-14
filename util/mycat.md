# 概述

数据库中间件，实现数据库的分库分表，读写分离

# 安装

一，解压，修改 server.xml ,修改登录的用户名和密码，和mycat的逻辑库

```xml
<user name="croot" defaultAccount="true">
                <property name="password">123456</property>
                <property name="schemas">TESTDB</property>
```

