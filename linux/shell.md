# shell

shell 是一个命令解释器，可以调用linux命令，执行任务

## start

1,编写一个shell脚本，通常以sh作为后缀

2，以 `#!/bin/bash` 作为第一行，告诉系统以bash程序解析执行

ex：使用echo打印

```bash
#!/bin/bash
echo "hello shell"
```

3,执行

使用sh命令

```sh
sh hello.sh
```

或者开通可执行权限`x`，直接通过相对路径或绝对路径执行

```sh
 ./hello.sh
 /plf/hello.sh
```

## variable

变量分为  系统变量 和 自定义变量

**系统变量**由系统定义好，直接可以使用 ，通过 `$`加变量名

```bash
#!/bin/bash
echo "hello shell"
echo "path=$PATH"
echo "user="$USER
```

得到

```bash
[root@localhost plf]# ./hello.sh
hello shell
path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/java/jdk1.8.0_211/bin:/root/bin
user=root
```

**自定义变量**使用`=` 赋值，同样通过`$` 调用

等号两侧不能有空格，通常大写命名，可以有数字字母下划线，但不能数字开头

```bash
A=100
echo a=$A
```

**销毁变量**使用`unset`

```bash
A=100
echo a=$A
unset A
echo a=$A
```

**静态变量**声明时需加`readonly`,静态变量无法销毁

```bash
readonly B=200
echo b=$B
unset B
echo b=$B
```

对静态变量销毁会报错

```
./hello.sh: 第 13 行:unset: B: 无法反设定: 只读 variable
```

使用命令的结果对变量赋值，可以使用反引号 或 `$(命令)`的形式

```bash
MY_DIR=`ls -l /home`
MY_DATE=$(date)

echo mydir=$MY_DIR
echo mydate=$MY_DATE

```

得到

```
mydir=总用量 4 drwx------. 15 plf plf 4096 6月 29 17:03 plf drwx------. 5 tom netcaregrp 161 11月 3 15:04 tom drwx------. 5 xiaomi xiao 147 6月 29 16:41 xiaomi drwx------. 5 xiaosan duo 128 6月 29 20:01 xiaosan
mydate=2019年 11月 09日 星期六 19:19:35 CST
```

使用提升为**环境变量**，可以在命令行及其他shell脚本中使用

```bash
PLF_HOME=/plf
export PLF_HOME
```

立即执行脚本使环境变量生效

```sh
source ./hello.sh
```

ex:在`/etc/profile`中配置环境变量，并生效

```sh
vim /etc/profile
```

进入最后一行加上,对path赋值时要注意把之前的值带上，使用`:`分割

```bash
export CATALINA_HOME=/student/apache-tomcat-8.5.47
export PATH=$PATH:$CATALINA_HOME/bin
```

生效

```sh
source /etc/profile
```

执行命令验证，启动或关闭tomcat

```sh
catalina.sh start
catalina.sh stop
```

**位置参数**

通过执行命令时，把参数带入程序中

`$n`  0代表命令本身 ，1-9代表1-9位的参数，大于10要使用${10}

`$@` 代表所有参数，区别对待

`$*` 代表 所有参数，当成整体

`$#` 代表参数的个数

ex:

```bash
#!/bin/bash
echo "hello shell"
echo "下标="$0 $1 $2
echo "阿特="$@
echo "星号="$*
echo "井号=" $#
```

执行命令时，空格后添加参数，多个参数用空格分割

```sh
[root@localhost plf]# ./hello.sh  10 20
hello shell
下标=./hello.sh 10 20
阿特=10 20
星号=10 20
井号= 2
```

**预定义变量**

预定义变量是程序本身就可以使用的变量

`$$`当前进程的pid

`$!` 最后一个进程的pid

`$?`  最后一个进程的状态，0为正常

```shell
echo "美元="$$
echo "叹号="$!
echo "问号="$?
```

得到

```bash
[root@localhost plf]# ./hello.sh
美元=19303
叹号=
问号=0
```

## operator

运算符,对变量进行运算

1,使用`$((表达式))`

2,使用`$[表达式]`,推荐

3，使用`expr 表达式`,变量与符号间要加空格，整个表达式需要用反引号包起来，一次只能执行一种运算

```sh
#!/bin/bash
echo "helloshell"
RESULT1=$(((2+3)*4))
echo $RESULT1

RESULT2=$[(2+3)*3]
echo $RESULT2

TEMP=`expr 2 + 3`
RESULT3=`expr $TEMP \* 2`
echo $RESULT3
```

结果

```bash
[root@localhost plf]# ./hello.sh
helloshell
20
15
10
```

## condition

用来判断语句

语法 `[ 条件 ]`, 中括号里是条件，条件前后加空格

常用判断

`=` 比较两个字符串

`-lt`  小于，less then 。 `-gt`  大于，great then

`-le` 小于等于，less equals 。`-ge` 大于等于，great equals

`-eq` 等于 ，equals ，`-ne` 不等于，not equals

判断文件权限

`-r` 读，`-w`写，`-x`执行

判断文件

`-f` 文件存在，且是普通文件

`-e` 文件存在

`-d` 文件存在，且是目录

```sh
#!/bin/bash
echo "helloshell"
if [ "a"="a" ]
then
        echo "a=a"
fi

if [ 12 -gt 5 ]
then
        echo "great then"
fi

if [ -e /plf/a.txt ]
then
        echo "exists"
fi
```

### if

语法

```sh
if [ condition ]
then 
	xxxx
fi
```

或

```sh
if [ condition ]
then
	xx
elif [ condition ]
then
	xxx
fi
```

ex: 判断输入的值

```sh
#!/bin/bash
echo "helloshell"
if [ $1 -ge 60 ]
then
        echo "及格"
elif [ $1 -lt 60 ]
then
        echo "不及格"
fi
```

执行

```sh
[root@localhost plf]# ./hello.sh  20
helloshell
不及格
[root@localhost plf]# ./hello.sh  70
helloshell
及格
```

### case

对值逐一匹配，语法,一次匹配最后使用`;;`结尾，`*`可以匹配所有值，最后反写 case

```sh
case 变量 in
"值1")
exe
;;
"值2")
exe
;;
*)
exe
;;
esac
```

ex:

```sh
#!/bin/bash
echo "helloshell"
case $1 in
"1")
echo "周一"
;;
"2")
echo "周二"
;;
*)
echo "other"
;;
esac

```

执行

```sh
[root@localhost plf]# ./hello.sh  1
helloshell
周一
[root@localhost plf]# ./hello.sh  2
helloshell
周二
[root@localhost plf]# ./hello.sh  3
helloshell
other
```

### for

语法一

```sh
for i in v1 v2 v3
do
	execute
done
```

ex: `$*` 会把输入的值当成一个整体，`$@`会区分每一个元素

```sh
#!/bin/bash
echo "helloshell"
for i in 1 2 3
do
        echo i=$i
done

for j in "$*"
do
        echo j=$j
done

for m in "$@"
do
        echo m=$m
done
```

执行

```sh
[root@localhost plf]# ./hello.sh  4 5 6
helloshell
i=1
i=2
i=3
j=4 5 6
m=4
m=5
m=6
```

语法二

```sh
for((定义变量;条件;循环控制))
do
	exe
done
```

ex:累加运算

```sh
#!/bin/bash
echo "helloshell"
SUM=0
for((i=0;i<100;i++))
do
        SUM=$[$SUM+$i]
done
echo SUM=$SUM
```

执行

```sh
[root@localhost plf]# ./hello.sh
helloshell
SUM=4950
```

### while

语法

```sh
while [ condition ] 
do
	exe
done
```

ex:改写累加程序

```sh
#!/bin/bash
echo "helloshell"

SUM=0
i=0
while [ $i -le 100 ]
do
        SUM=$[$SUM+$i]
        i=$[$i+1]
done
echo SUM=$SUM

```

## read

脚本执行的时候，可以读取用户输入

```sh
read [opts] 变量名
```

选项

p 提示文字

t 等待输入时间，默认单位秒，超过时间不再等待，往下执行

ex

```sh
#!/bin/bash
echo "helloshel"

read -p "请输入密码" NUM1
echo pwd=$NUM1

read -p "zzz" -t 10 NUM2
echo zzz=$NUM2
```

执行

```sh
[root@localhost plf]# ./hello.sh
helloshel
请输入密码qwe
pwd=qwe
zzzzzz=
```

## function

### 系统函数

系统自带的函数，可以直接使用

`basename 文件 [后缀]` 得到文件的文件名，指定后缀，显示时会去除后缀

```sh
[root@localhost plf]# basename /plf/a.txt
a.txt
[root@localhost plf]# basename /plf/a.txt .txt
a
```

`dirname 文件` :得到文件的路径

```sh
[root@localhost plf]# dirname /plf/a.txt
/plf
```

### 自定义函数

语法

```sh
[function] fname(){
    exe
}
```

ex

```sh
#!/bin/bash
echo "helloshel"

function mySum(){
        SUM=$[$n1+$n2]
        echo $SUM
}

n1=3
n2=4
mySum n1 n2
```

