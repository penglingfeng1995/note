# Python从入门到精通

author:plf          QQ：1528389316

闲来无事，学习pyhon.

### 安装python

作者学习python的时候是2018-3-16号，听说官方已经不打算支持python2了，本文使用的是python3.

从官网上下载python并安装

安装PyCharm,一款jetBrains开发的python的IDE工具

### 第一个程序  HelloWorld

使用print()方法可以在控制台输入

```python
print("Hello World!")
```

python3默认采用UTF-8编码,所以支持中文,

```python
print("张三")
```

##基本语法

### 注释

python的注释以**#**开头，注释会在python解释运行是忽略

```python
print("Hello World!")
#这是第一个注释
print("张三")#这是第二个注释
```

多行注释，使用三个单引号或者三个双引号可以实现多行注释的效果

```python
print("张三")
"""
    多行注释
    第一行
    第二行
"""
'''
    单引号
    第一行
    第二行
'''
```

### 缩进

python强制使用缩进作为代码块,判断代码的范围，用缩进来判断,不能有空格

```python
if 5>0:
    print("成立")
    print("代码块一中")
else:
    print("不成立")
    print("代码块二中")
print("离开了判断条件")
```

### 多行语句

python一般一行直接写完,如果语句很长，可以在行的结尾使用反斜杠  `\ ` ,连接下一行

```python
a=1+1+1+1+1+\
    2+\
    3+\
    4
print(a)
```

在(),[],{}中多行不需要反斜杠

```python
a=["a","b",
   "c","d","e",
   "f"]
print(a)
```

### 一行多条

python不强制使用 `;` 作为结束符号，加了也没关系，如果想在一行中执行多条语句使用`;`隔开

```python
print("zs");print("ls")
```

### 数字类型

python中有四种数字类型,int(整数) ,bool(布尔),float(浮点),complex(复数)

```python
a=1    #int型
b=True #布尔型True or False
c=1.23 #浮点型
d=1+2j #复数
```

### 字符串

str在python中代表字符串,字符串通常用双引号或者单引号，使用反斜杠连接多行字符串,还可以使用三引号多行表示,换行也会被算在内.

```python
a="张三"
b='李' \
  '四'
c='''王五
麻子
赵六'''
print(a,b,c)
```

在单引号中可以显示双引号，双引号中也可以直接显示单引号,想要在双引号中显示双引号或者一些其他特殊字符可以使用反斜杠 \  进行转义 ,如\n代表换行，在字符串前加r，可以使反斜杠不发生转义而是显示

```python
print("i can't do it")
print('i name is "zs"')
print("单引号’\n 双引号\" ")
print(r"单引号’\n 双引号\" ")
'''
i can't do it
i name is "zs"
单引号’
 双引号" 
单引号’\n 双引号\" 
'''
```

可以把两个字符串相加

```python
a="zs"
b="ls"
c=a+b
print(c)  #结果为zsls
```

还可以相乘

```python
a="zs"*3
print(a)   #结果zszszs
```

python一大特色就是**分片**，应用于字符串和数组，使用方法  **变量名[开始下标：结束坐标]**，包头不包尾

和大多数编程语言一样，下标从0开始，比如下面的例子，a是0下标，b是1下标，以此类推

在python中还可以从右往左算下标，倒数第一个为-1，倒数第二个为-2，比如g是-1，f是-2

```python
a="abcdefg" 
#  0123456
print(a)       #打印整个字符串                           abcdefg
print(a[0])    #打印下标为0的字符                        a
print(a[2:4])  #从2下标到4下标但是不包括4下标             cd
print(a[2:])   #从2下标输出至结尾                        cdefg
print(a[:4])   #从开头输出到4下标                        abcd
print(a[:-2])  #从开头输出到倒数第2个下标                 abcde
```

### 输入

使用input()方法可以打印提示内容并获取用户输入并返回一个字符串变量

```python
a=input("请输入")
print("你刚输入的是 "+a+" .")
```

## 集合类型

### list

在python中list是一个有序集合，可以随时添加或者删除元素,使用方括号`[]`来定义

```python
studentList=["张三","李四","王五","麻子"]
```

通过`len()`方法来查看长度

```python
print(len(studentList))  #-->4
```

通过下标取出元素

```python
print(studentList[1])  #-->李四
```

同样也支持分片

```python
print(studentList[1:3])  #-->['李四', '王五']
```

**添加：**通过`append()`方法可以把元素添加到list的尾部

```python
studentList=["张三","李四","王五","麻子"]
studentList.append("赵六")
print(studentList)   #-->['张三', '李四', '王五', '麻子', '赵六']
```

**插入：**通过`insert()`方法可以插入到指定下标位置

```python
studentList=["张三","李四","王五","麻子"]
studentList.insert(2,"赵六")
print(studentList)   #-->['张三', '李四', '赵六', '王五', '麻子']
```

**删除：**通过`pop()`方法可以删除元素，参数为int型，代表下标，不加参数会删除最后一个

```python
studentList=["张三","李四","王五","麻子"]
studentList.pop(2)
print(studentList)   #-->['张三', '李四', '麻子']
```

**替换：**直接给对应的下标赋值

```python
studentList=["张三","李四","王五","麻子"]
studentList[2]="赵六"
print(studentList)  #-->['张三', '李四', '赵六', '麻子']
```

**元素类型：**list的元素类型没有限制，还可以是数组,数组中的数组叫二维数组，可以使用多个方括号取值

```python
studentList=["张三",True,123,["zs","ls"]]
print(studentList[3][1][1])  #-->s
```

### tuple

tuple在python中称作元组,和list非常类似，但是不能被改变,使用圆括号定义

```python
studentList=("zs",124,True,["a","b"])
```

在定义只有一个元素的元组需要加逗号，避免歧义，否则会被直接当成一个元素

```python
a=(1,)
```

### dict

dict在python中代表字典，存储键值对,使用方括号定义,前面放键key，中间加冒号`:`,后面放值value,键值对使用逗号隔开.

```python
studentList={"zs":12,"ls":13,"ww":14}
```

通过键名取值

```python
studentList={"zs":12,"ls":13,"ww":14}
print(studentList["zs"])  #-->12
```

直接通过给键设置值的方式进行添加和修改

```python
studentList={"zs":12,"ls":13,"ww":14}
studentList["zl"]=66
print(studentList) #-->{'zs': 12, 'ls': 13, 'ww': 14, 'zl': 66}
```

**in**关键字,通过in关键字可以判断一个元素或者键是否存在于一个集合中

```python
studentList={"zs":12,"ls":13,"ww":14}
print("zs" in studentList)  #-->True
```

通过`get(key,default)`方法取值，如果键不存在返回默认值,没有设置默认值则返回None

```python
studentList={"zs":12,"ls":13,"ww":14}
print(studentList.get("ww")) 	 #-->14
print(studentList.get("zz"))	 #-->None	
print(studentList.get("yy",22))  #-->22
```

使用`pop()`方法通过键，来删除整个键值对

```python
studentList={"zs":12,"ls":13,"ww":14}
studentList.pop("zs")
print(studentList)  #-->{'ls': 13, 'ww': 14}
```

### set

set,是一个不能重复的无序集合，定义一个set需要一个list,放入set方法，或者直接使用中括号`{}`,只放值

```python
a_set=set(["a","a","b","b","c"])
print(a_set)   #-->{'a', 'b', 'c'}
b_set={"zs","ss","ss"}
print(b_set)   #-->{'zs', 'ss'}
```

通过remove方法和add方法做添加和删除

```python
b_set={"zs","ls","ww"}
b_set.add("zl")
b_set.remove("ls")
print(b_set)
```

可以把两个set集合当做数学集合，做交集和并集

```python
b_set={"zs","ls","ww"}
a_set={"zs","zz","ww"}
print(a_set & b_set)   #-->{'ww', 'zs'}      交集
print(a_set | b_set)   #-->{'ls', 'zz', 'zs', 'ww'}  并集
```

## 判断和循环

### if     else

在if 后接表达式加冒号`:`，表达式的结果为True则执行代码块

```python
if 1>0 :
    print("执行代码块一")
print("结束判断")
```

多个条件的时候使用`elif`加第二个条件,如果执行了其中一个条件将不执行其他条件

```python
if 1>0 :
    print("执行代码块一")
elif 2>0:
    print("代码块二")
else:
    print("代码块三")
print("结束判断")
```

一个代码块需要复杂条件时,可以使用`&`代表与，`| `代表或，加多个条件

```python
if 1>0 & 2<0 | 1==1:
    print("执行代码块一")
```

### for

在python中使用`for`  变量名   in   目标集合,遍历数组，还可以遍历字符串

 ```python
a_list=["zs","ls","ww"]
for name in a_list:
    print(name)
 ```

### while

while 只要满足条件就会无限循环

```python
a=5  #定义一个变量
while a>0:  #当a为正数时就进循环
    a-=1    #每次循环a都会减少1，，a-=1相当于a=a-1
    print(a)
'''
4
3
2
1
0
'''
```

break关键字可以让循环提前结束

```python
a=5
while a>0:
    a-=1
    if a==2:
        break
    print(a)
```

continue关键字可以跳出本次循环，直接执行下次循环

```python
a = 0
while a < 10:
    a += 1
    if a % 2==0:#当a为偶数时，跳出本次
        continue
    print(a)  #打印奇数

```



## 函数

函数又叫方法，python内置了很多函数，供我们调用，一个函数通常有函数名，参数和返回值

比如`abs()`方法，这是一个求绝对值的方法,传入一个int型会给我们一个绝对值,

```python
a=-12
print(abs(a))  #-->12
```

方法也是对象,也可以被引用

```python
a=abs
print(a(-2))
```

### 自定义函数

使用`def `定义一个函数，语法为  def  函数名(参数):  使用return结束方法，并返回结果,结果类型可以有多种

```python
def my_abs(x):
    if x>0:
        return "zs"
    else:
        return  22

print(my_abs(1))  #方法被定义后可以直接调用  -->zs
```

如果写在不同的文件里，需要使用`from  文件名  import  方法名`,即可在这个文件中使用方法

```python
from MyTestMethod import  my_abs

print(my_abs(2))
```

### 空函数

定义一个方法，但是还没想好怎么写，这时候加个pass可以让这个代码不报错

```python
def my_pass():
    pass
```

### 检查参数类型

我们的方法是需要一个int型做判断，如果传入字符串则会出错，提前使用`isinstance()`方法判断参数的类型，如果类型不对直接使用`raise`关键字抛出一个异常

```python
def my_abs(x):
    if not isinstance(x,(int)):
        raise TypeError("参数只能是int")
    if x>0:
        return "zs"
    else:
        return  22
```

### 返回多个值

python中的方法可以返回多个值,可以用两个变量来接收参数，如果用一个变量接，这个变量将会是个元组tuple

```python
def re_two():
    return 2,"zs"

x,y=re_two()
print(x)  #-->2
print(y)  #-->zs

a=re_two()
print(a)  #-->(2, 'zs')
```

### 默认参数

当参数设置多个时，如果参数不填满，就会报错,这时候就有可以给参数设置默认值,没有默认值的参数是必须填写的，有默认值的参数如果给了，会覆盖默认值，也可以不给，方法会直接使用默认值，通过参数名称可以设置指定的参数值

```python
def many_param(a,b=5,c=6):
    print(a)
    print(b)
    print(c)

many_param(1)   #-->156
print("---------")
many_param(1,2)  #-->126
print("---------") 
many_param(1,c=2) #-->152
```

### 可变参数

以往我们如果方法需要一个list,直接传一个list

```python
def my_for(items):
    for i in items:
        print(i)

my_for([1,2,3])
```

有了可变参数，我们可以这样写,在参数前加星号`*`,代表可以传多个该变量

```python
def my_for(*items):
    for i in items:
        print(i)

my_for(1,2,3)
```

调用参数时,也可以把一个集合前面加*,可以拆成多个参数调用

```python
a=[1,2,3]
my_for(*a)
```

### 关键字参数

可变参数会把参数封装成一个tuple，而关键字参数可以把键值对封装成一个dict

```python
def my_keywords(**kw):
    print(kw)

my_keywords(a="zs",b="ls")  #-->{'a': 'zs', 'b': 'ls'}
```





