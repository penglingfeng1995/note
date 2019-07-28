# NIO

旧的io需要输入输出流，进行单向传输

新api需要打开一个管道，数据传入缓冲区，进行双向传输

## 缓冲区

缓冲区用于读写数据，nio提供了除boolean外的七个基本数据类型的缓冲区

通过`allocate`并指定容量获取缓冲区实例

```java
ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
ShortBuffer shortBuffer = ShortBuffer.allocate(1024);
IntBuffer intBuffer = IntBuffer.allocate(1024);
LongBuffer longBuffer = LongBuffer.allocate(1024);
DoubleBuffer doubleBuffer = DoubleBuffer.allocate(1024);
FloatBuffer floatBuffer = FloatBuffer.allocate(1024);
CharBuffer charBuffer = CharBuffer.allocate(1024);
```

**核心属性**

`capacity`  :总容量，一旦声明不可改变，声明时定义

`position` : 当前正在操作数据的位置

`limit` :  可以操作数据的范围

`mark` : 标记，可以通过`reset()`方法使`position`会到mark的位置,初始值为-1

```java
int position = byteBuffer.position();
int limit = byteBuffer.limit();
int capacity = byteBuffer.capacity();
```

**方法**

```java
//得到对象   mark=-1,capacity=1024,position=0,limit=1024
ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

//存入数据,position+6
byteBuffer.put("abcdef".getBytes());

//切换成读取模式，position=0,limit=6
byteBuffer.flip();

//取数据,单个，position+1
byte b = byteBuffer.get();
//从指定位置获取单个，但不会改变position
byte b1 = byteBuffer.get(2);

//读取数据存入指定数组，position+5,数组的大小不能超过remain,也就是剩余数据大小
byte[] bytes=new byte[5];
byteBuffer.get(bytes);

//将position恢复到0
byteBuffer.rewind();

//读取2个元素到目标数组的0下标,position+2
byte[] bytes2=new byte[3];
byteBuffer.get(bytes2,0,2);

//使mark=position
byteBuffer.mark();
//position+1
byte b2 = byteBuffer.get();
//将position=mark
byteBuffer.reset();
byte b3 = byteBuffer.get();

//查看是否有剩余数据
boolean hasRemaining = byteBuffer.hasRemaining();
//剩余多少 ,remaining=limit-position
int remaining = byteBuffer.remaining();

//将position=0,mark=-1,capacity=limit=1024,重置属性，但不会清空数据
byteBuffer.clear();
byte b4 = byteBuffer.get();
```

**直接缓冲区**

非直接缓冲区的数据保存在jvm的堆内存中，直接缓冲区的数据保存在物理内存中

```java
ByteBuffer byteBuffer = ByteBuffer.allocateDirect(1024);
```

## 通道

通道负责传输缓冲区的数据，本身不负责存储

获取通道,得到缓冲区，复制文件

```java
FileChannel inChannel = FileChannel.open(Paths.get("a.txt"), StandardOpenOption.READ);
FileChannel outChannel = FileChannel.open(Paths.get("b.txt"), StandardOpenOption.WRITE,StandardOpenOption.CREATE,StandardOpenOption.READ);

MappedByteBuffer inBuffer = inChannel.map(FileChannel.MapMode.READ_ONLY, 0, inChannel.size());
MappedByteBuffer outBuffer = outChannel.map(FileChannel.MapMode.READ_WRITE, 0, inChannel.size());

byte[] bytes=new byte[inBuffer.limit()];
inBuffer.get(bytes);
outBuffer.put(bytes);
```

## Charset

查看所有支持的字符集

```java
SortedMap<String, Charset> map = Charset.availableCharsets();
```

得到指定的字符集，和编码器

```java
Charset charset = Charset.forName("UTF-8");
CharsetDecoder charsetDecoder = charset.newDecoder();
CharsetEncoder charsetEncoder = charset.newEncoder();
```

