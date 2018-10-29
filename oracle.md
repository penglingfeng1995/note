# oracle

## 介绍

oracle是一美国的公司，全称甲骨文股份有限公司，全球第二大的软件公司，仅次于微软，起初是为中央情报局设计数据库，名为oracle，不断的发展，在2009年收购了sun公司，此时sun公司已经收购了开发mysql的AB公司，目前Oracle公司的旗下产品有oracle数据库，mysql数据库，java平台。

我们这次所学习的oracle，就是甲骨文公司旗下的oracle数据库，由于标准的sql的语言，由协会定制，但是DDL，DML，DCL每个数据库都不相同，所以需要学习oracle

## 安装

装不起果断装系统

## 数据类型

### char

char是一种固定长度的字符,不管长度够不够，都会存储占用固定大小，存储内容默认是byte，也可以设置为char，比如char(3),可以存贮最多3个byte长度的字符串，比如‘a’,'abc',如果长度超出会报出一个错误，（ORA-12899: value too large for column）,表示值过大，一个中文默认占用两个字节，char(3)只能存储一个中文，因为两个中文会占用四个字节,在表定义的时候设置类型为char(3 char),此时这个类型会以char型存贮，此时可以存储三个中文，但是依然只能存储三个英文字符

### varchar

已废弃，使用varchar2

### varchar2

varchar2是一种可变长度的字符串,和char不同的是会按内容的实际长度来存储，也就是说varchar2(5)，中存放了'abc',则实际长度为3byte

### number

所有的数字类型都可以使用number类型，使用number(p,s)定义，其中p表示有效长度，s表示精确度,常见的number(5,2),表示有效长度为5，保留两位小数,如果把一个值123.123存入，数据库中值为123.12，如果存入1123.12则会报错（ORA-01438: value larger than specified precision allowed for this column），表示值大于精度范围

### date

可以存储时间

## DLL

### create

创建表

```sql
CREATE TABLE tableName(
	columnName dataType constraint,
	columnName2 dataType2 constraint
);
```

以上是创建表的语法，通过CREATE TABLE关键字来创建，其中tableName表示表的名称，需要自己定义，括号中指定各个字段，columnName表示字段名，也就是列名，dataType表示数据类型，必须使用oracle官方指定的类型如char，number，最后constraint表示约束，比如主键约束，非空约束，

### drop

```sql
drop table tableName
```

直接使用drop 命令加表名直接删除

### alter

**添加列**

```sql
ALTER TABLE tableName ADD colName dataType CONSTRAINT
```

**修改列名**

```sql
ALTER TABLE tableName RENAME COLUMN colName TO colName2
```

**修改数据类型**

```sql
ALTER TABLE tableName MODIFY (columnName dataType)
```

**删除列**

```sql
 AlTER TABLE tableName DROP COLUMN colName
```

**添加约束**

主键

一般情况都要为约束命名

```sql
ALTER TABLE tableName ADD CONSTRAINT constraintName PRIMARY KEY(colName)
```

唯一

```sql
ALTER TABLE tableName ADD CONSTRAINT constraintName UNIQUE(colName)
```

非空

```sql
ALTER TABLE tableName MODIFY colName NOT NULL
```

外键

被引用的外键必须是主键

```sql
ALTER TABLE subTable ADD CONSTRAINT fg_sub_id FOREIGN KEY (sub_id) REFERENCES mainTable(mainId)
```

校验

```sql
ALTER TABLE tableName ADD CONSTRAINT constraintName CHECK(expression)
```

**删除约束**

```sql
ALTER TABLE tableName DROP CONSTRAINT constraintName
```

## SQL

### 复制表

```sql
CREATE TABLE dept2 as select * from dept;
```

## PL/SQL

PL/SQL是oracle数据库对sql语句的扩展，专门用于oracle数据库

### 基本语法

```plsql
DECLARE
	定义变量
BEGIN
	代码块
END;
```

ex:

```plsql
DECLARE
	i varchar2(15) := 'Hello world';
BEGIN
	DBMS_OUTPUT.PUT_LINE(i);
END;
```

### 条件判断

```plsql
IF 条件 THEN
	执行代码块1
ELSIF 条件2
	执行代码块2
ELSE	
	执行代码块3
END IF;
```

### 循环语句

#### 基本

一定要记得加退出循环的条件,不然很容易堆栈溢出

```plsql
LOOP
	循环体
	EXIT WHEN 条件;
END LOOP;
```

#### for

```plsql
FOR i IN 起点..终点 LOOP
	代码块
END LOOP;
```

#### where

```plsql
WHILE 条件 LOOP
	代码块
END LOOP;
```

## cursor

游标相当于一个临时存储查询结果的结果集,执行增删改和单行查询的时候会自动使用隐式游标，显示游标需要用户自己定义

### 显示

显示游标需要在declare中定义，

定义

```plsql
CURSOR 游标名 IS SQL语句;
```

使用

```plsql
OPEN 游标名;
	FETCH 游标名 INTO 变量名;
CLOSE 游标名;
```

使用之前需要打开游标，使用完后需要关闭游标,执行fetch会从游标中抓取数据存放到一个变量中，一次只会抓一条，需要用循环语句执行多条,可以使用%rowtype定义表类型的对象，通过%notfound判断游标是否还在

```plsql
	for r in mc loop
		dbms_output.put_line(r.empno);
	end loop;
```

也可以直接使用for循环来使用游标，自动打开，自动关闭，自动创建对象，自动抓取

### 隐式

隐式游标通过SQL关键字来访问

SQL%rowcount  返回int，代表受影响的行
SQL%found  返回boolean,为true表示操作成功
SQL%notfound  返回boolean,为true表示操作失败
SQL%isopen  执行过程中为true，执行结束返回false

## sequence

由于oracle中没用自动增长，所以需要序列来记录count，来代替自动增长

定义

```sql
create sequence myseq;
```

通过nextval属性取值，每取一次会加一次

```sql
insert into emp2(empno) values(myseq.nextval)
```

**rownum和rowid**

rownum是一个伪列,底层通过序列实现，记录行号,可以实现分页效果

```sql
select rownum,e.* from emp2 e
```

rowid是每一条数据隐藏的唯一标识,比如AAAR7SAAEAAAACrAAA

```sql
select rowid rid,ROWNUM rnum,e.* from emp2 e
```

## procedure

定义

```plsql
CREATE [OR REPLACE] PROCEDURE 存储过程名(
	参数1 IN/OUT 数据类型,
	参数2 IN/OUT 数据类型
)IS
	变量1 数据类型;
BEGIN
	代码块
END;
```

通过create procedure创建存储过程,参数默认为入参，需要定义入参和出参，入参只能度不能写，出参只能写不能读，is后面定义变量，相当于declare，begin和end作为代码块

在其他代码块或sql语句中直接通过过程名来调用存储过程,参数必须匹配

## function

定义

```plsql
CREATE [OR REPLACE] FUNCTION 函数名(
	参数1 IN/OUT 数据类型,
	参数2 IN/OUT 数据类型
)RETUEN 返回值类型
AS
	变量名 数据类型;
BEGIN
	RETUEN 返回值;
END 函数名;
```

通过create function定义函数，函数也有入参和出参，但是有返回值，通过return关键字返回值

直接通过函数名去调用

## trigger

定义

```plsql
CREATE [OR REPLACE] TRIGGER 触发器名
BEFORE/AFTER(触发时机) INSERT/DELETE/UPDATE(触发条件) 
ON 表名 [FOR EACH ROW]
DECLARE
BEGIN
	代码块
END;
```

触发器不能被直接调用，需要声明触发时机，和触发条件,在代码块中可以通过 :old  拿到要被删除或被修改的列，
:new  拿到要被添加或修改进的列

ex

```plsql
create or replace trigger mytri
before delete on emp2 for each row
BEGIN
	insert into emp3(empno,ename,job,mgr)
	values(:old.empno,:old.ename,:old.job,:old.mgr);
end;
```

## transaction

oracle和mysql不一样，mysql默认自动提交，oracle需要手动提交(用可视化工具会自动提交)，通过commit关键字直接提交，通过rollback进行回滚，还可以设置savepoint相当于存档，可以回滚到指定的保存点.

```plsql
begin
insert into emp2(empno) values(1111);
savepoint a1;
insert into emp2(empno) values(2222);
savepoint a2;
insert into emp2(empno) values(3333);
commit;
exception 
	when others then
		rollback to a1;
end;
```

**事物的隔离级别**

1,read-uncommitted  未提交读取

通过select @@tx_isolation;查看事物的隔离级别，mysql默认是repeatable-read

a用户和b用户一起修改事物隔离级别，set tx_isolation='read-uncommitted';
再次查询事物隔离级别，确保修改成功

a用户开启事物start transaction;
添加数据,insert into----但是不提交,
b用户执行查询可以查到刚刚添加的数据

这就造成来**脏读**，
脏读表示当前事物读取到了别的事物未提交的数据

2,read-committed 已提交读取

两个用户同时修改事物隔离级别set tx_isolation='read-committed';并确认

a用户开启事物，添加数据但是不提交，
b用户开启事物，查询，查询不到刚刚a添加的数据
a用户commit，b用户查询才能查到

这就造成来**不可重复读**，
不可重复读表示，两个用户执行同样的sql，结果却不一样

3,repeatable-read 可重复读

两个用户同时修改事物隔离级别set tx_isolation='repeatable-read';并确认

a用户开启事物，b用户开启事物，同时执行查询结果一致
a用户修改一个对象，并提交commit
b用户再查询，发现数据并没用改变，尽管a已经提交，
b也对a刚才修改的对象进行修改，发现修改不了

这就造成了**幻读**
幻读表示当前事物读取不到别的事物已经提交的数据，
而当前事物查询的数据全是假象，不能进行操作

4,serializable 串行化

设置隔离级别 set tx_isolation='serializable';

a和b同时开启事物
a执行修改报错 [Err] 1205 - Lock wait timeout exceeded; try restarting transaction
b commit;
a再次修改，通过

serializable是最高的事物隔离级别，也叫悲观锁
同一时间只能有一个事物能够改变这一行
别的事物不提交，自己永远没办法执行



mysql支持四种隔离级别，oracle支持两种read-commit和seriaclize

## jdbc

### 安装jar

由于oracle没有给maven提供坐标，所以只能自己找jar包，自己注册到maven仓库中去,jar包的位置默认在app/administrator/product/版本号/dbhome_1/jdbc/lib下

打开sql plus查看oracle的版本，执行maven命令安装jar包

```bash
mvn install:install-file -Dfile=F:\ojdbc6.jar  -DgroupId=com.oracle -DartifactId=ojdbc6 -Dversion=11.2.0.1.0 -Dpackaging=jar
```

### 依赖

```xml
<dependency>
    <groupId>com.oracle</groupId>
    <artifactId>ojdbc6</artifactId>
    <version>11.2.0.1.0</version>
</dependency>
```

### 注册驱动

```java
Class.forName("oracle.jdbc.OracleDriver");
String url="jdbc:oracle:thin:@//192.168.2.135:1521/orcl";
String user="scott";
String pwd="tiger";
Connection connection = DriverManager.getConnection(url, user, pwd);
```

### clob

clob是  chararcter large  object 是文档大对象类型，可以存下文档数据

使用setCharacterStream可以把一个流对象设置进来,这里直接使用当前java文件,存入数据库中

```java
String sql="insert into testclob(id,target) VALUES (myseq.nextval,?)";
PreparedStatement ps = connection.prepareStatement(sql);
Reader reader=new FileReader("src/main/java/MyTest.java");
ps.setCharacterStream(1,reader);
ps.executeUpdate();
```

可以通过getClob直接拿到这个对象,通过clob的getSubString设置要截取的长度(通过length())可以拿到字符串，

```java
String sql="select target from testclob where id=6";
PreparedStatement ps = connection.prepareStatement(sql);
ResultSet rs = ps.executeQuery();
while (rs.next()){
    Clob clob = rs.getClob(1);
    Integer length = Integer.parseInt(clob.length()+"");
    String subString = clob.getSubString(1, length);
    System.out.println(subString);
}
```

### blob

blob 是 bianry  large object 是二进制大对象类型,可以存视频，图片

通过setBinaryStream直接把一个文件作为流设置进去,

```java
String sql="insert into testblob(id,target) VALUES (myseq.nextval,?)";
PreparedStatement ps = connection.prepareStatement(sql);
InputStream is=new FileInputStream("src/a.jpg");
ps.setBinaryStream(1,is);
ps.executeUpdate();
```

通过getBinaryStream可以直接得到流

```java
String sql="select target from testblob where id=8";
PreparedStatement ps = connection.prepareStatement(sql);
ResultSet rs = ps.executeQuery();
while (rs.next()){
    InputStream binaryStream = rs.getBinaryStream(1);
}
```

