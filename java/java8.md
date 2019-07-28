# lambda表达式

使用lambda表达式的前提是需要有一个接口，且这个接口只有一个抽象方法,java8中支持接口中有default方法;

可以通过@FunctionalInterface注解修饰这个接口，可以限制这个接口只有一个抽象方法;

比如

```java
@FunctionalInterface
public interface MathFun {
    int suanshu(int a,int b);
}
```

比如我现在要做一个加法的实现类，需要有一个实现类,或者使用一个匿名内部类

```java
MathFun mf1=new MathFun() {
    @Override
    public int suanshu(int a, int b) {
        return a+b;
    }
};
System.out.println(mf1.suanshu(1,2));
```

而使用lambda表达式代码可以省略很多

```java
MathFun mf=(a,b)->a+b;
System.out.println(mf.suanshu(1,2));
```

### 基本语法

个人理解，lambda表达式会返回前面声明的接口的实现类的实例，使用`->`分割lambda表达式前面是参数（如果参数只有一个可以不写小括号，但还是建议写上，如果写了参数类型，则所以参数都要写类型，不建议加类型）,后面是代码块(如果只有一行代码，不用加中括号，如果抽象方法有返回值会直接返回这一行代码的返回值)，这些会被用于声明的接口中的唯一抽象方法，代码块就相当于实现类里的方法的代码块;

```java
MathFun mf=(a,b)->{
    System.out.println("zzz");
    return a+b;
};
```

### 四大函数式接口

**Consumer**，消费者接口，一个泛型的参数，无返回值

```java
public interface Consumer<T> {
    void accept(T t);
}
```

**Supplier**，生产者接口，无参数，返回泛型类型

```java
public interface Supplier<T> {
    T get();
}
```

**Function**，函数接口，第一个泛型为参数类型，第二个泛型为返回值类型

```java
public interface Function<T, R> {
    R apply(T t);
}
```

**Predicate**，判断式接口，参数为泛型，返回boolean

```java
public interface Predicate<T> {
    boolean test(T t);
}
```

### 方法引用

如果已经有方法实现来我想要的功能，且该方法的参数和返回值类型和本抽象方法一致，则可以使用方法引用

使用`::`来引用，`::`前面放类或实例，后面放方法名称，不用加括号；

比如：

```java
Consumer<String> consumer=System.out::println;
```

这里直接使用了系统标准输出流，out作为对象，直接调用它的println方法作为我们的实现，相当于把它的代码拷贝来过来作为我们接口的实现，前提是返回值和参数一至；

```java
public void println(String x) {
    synchronized (this) {
        print(x);
        newLine();
    }
}
```

```java
Consumer<String  s> 
    void accept(String s);
```

这里println方法和我们的带了泛型的accept方法的参数和返回值一致，直接引用了println方法代码块；

比如之前Comparator使用Integer的比较方法

```java
Comparator<Integer> comparator=(o1,o2)->Integer.compare(o1,o2);
```

可以简写为

```java
Comparator<Integer> comparator1=Integer::compare;
```

相当于直接使用了Integer的比较方法作为比较器的实现

### 构造方法引用

lambda表达式也可以直接调用一个类的构造方法,通过`类名::new`调用

```java
public interface TestGet<T> {
    T get(String name,int age,double salary);
}
```

```java
TestGet<Employee> testGet=Employee::new;
```

```java
public Employee() {
    //空构造方法
}

public Employee(String name, int age, double salary) {
    this.name = name;
    this.age = age;
    this.salary = salary;
}
```

会自动匹配参数一致的构造方法

还可以调用数组的构造方法

```java
Function<Integer,String[]> function=String[]::new;
```



# Stream API

stream 是java8新增的对象，与io流没用任何关系，用来操作数据，通过一连贯的流水线式的方法执行操作；

### 创建Stream

Collection的子类都可以通过stream来得到Stream

```java
List<Employee> list=new ArrayList<>();
list.add(new Employee("张三",11,6000.00));
list.add(new Employee("李四",12,5000.00));
list.add(new Employee("王五",13,4000.00));
list.add(new Employee("赵六",14,3000.00));
list.add(new Employee("田七",15,2000.00));
Stream<Employee> stream = list.stream();
```

数组对象,可以通过工具类Arrays的stream方法得到Stream

```java
Stream<Employee> stream1 = Arrays.stream(new Employee[10]);
```

其他方法

```java
//直接用多个对象
Stream<String> stream2 = Stream.of("zs", "ls", "ww");
//要被迭代的目标，和迭代的操作
Stream<Integer> iterate = Stream.iterate(0, x -> x + 1);
//生产对象的方法
Stream<Double> generate = Stream.generate(() -> Math.random());
```

一个Stream创建之后可以有中间操作和终止操作，进行任何操作后当前stream都会关闭，中间操作（排序，匹配，筛选等）会返回一个新的Stream对象，但是不会真的执行这里面的方法，直到有终止操作的命令，才会把所有的中间操作都执行一遍，终止操作（forEach）无返回值，会直接关闭stream。

### 遍历

#### forEach

Stream的forEach方法中需要一个Consumer的实现类，来指定foreach里的操作，一个消费接口，这里直接引用println方法

```java
stream.forEach(System.out::println);
```

### 筛选

#### filter

过滤，filter方法需要一个断言接口的实例，返回一个stream

```java
Stream<T> filter(Predicate<? super T> predicate);
```

```java
Stream<Employee> stream = list.stream();
Stream<Employee> empsByAge = stream.filter((e) -> {
    System.out.println("正在判断"+e.getName()+"的age");
    return e.getAge() > 12;
}).filter((e)->{
    System.out.println("判断"+e.getName()+"salary");
    return e.getSalary()<4000;
});
System.out.println("开始遍历");
empsByAge.forEach(System.out::println);
```

结果，没调用forEach之前，是不会执行判断的方法，直到执行forEach，才会依次执行各种中间操作

```
"C:\Program Files (x86)\Java\jdk1.8.0_91\bin\java" -ea -Didea.test.cyclic.buffer.size=1048576 "-
开始遍历
正在判断张三的age
正在判断李四的age
正在判断王五的age
判断王五salary
正在判断赵六的age
判断赵六salary
Employee{name='赵六', age=14, salary=3000.0}
正在判断田七的age
判断田七salary
Employee{name='田七', age=15, salary=2000.0}

Process finished with exit code 0
```

#### distinct

唯一，会根据泛型对象的equals方法和hashcode方法自动判断，一定要记得**重写equals和hashcode**

```java
Stream<T> distinct();
```

###切片

####limit

截断，限制流中对象的数量，在管道操作中的位置会影响到结果

```java
Stream<Employee> empsByAge = stream.limit(2).filter((e) -> {
    System.out.println("正在判断"+e.getName()+"的age");
    return e.getAge() > 10;
}).filter((e)->{
    System.out.println("判断"+e.getName()+"salary");
    return e.getSalary()<3000;
});
```

把limit操作放在前面，会先执行limit的操作，把前两个元素截取出来，其他元素全部抛弃，再对截取出来的元素进行之后的操作。

```java
开始遍历
正在判断张三的age
判断张三salary
正在判断李四的age
判断李四salary

Process finished with exit code 0
```

把limit放在最后

```java
Stream<Employee> empsByAge = stream.filter((e) -> {
    System.out.println("正在判断"+e.getName()+"的age");
    return e.getAge() > 10;
}).filter((e)->{
    System.out.println("判断"+e.getName()+"salary");
    return e.getSalary()<6000;
}).limit(2);
```

结果，会从第一个元素开始依次执行操作，直到到达limit的上限，直接结束，不执行其他元素

```java
开始遍历
正在判断张三的age
判断张三salary
正在判断李四的age
判断李四salary
Employee{name='李四', age=12, salary=5000.0}
正在判断王五的age
判断王五salary
Employee{name='王五', age=13, salary=4000.0}

Process finished with exit code 0
```

#### skip

跳过，skip方法会跳过指定个数的元素，与limit一样，顺序有很大的影响，

### 映射

#### map

map方法会接受一个Function接口，作用在每个元素上，方法的返回值会替换原有的元素，

```java
Stream<Employee> empsByAge = stream
                            .map((e)->{
                                e.setSalary(e.getSalary()-200);
                                return e;
                            });
```

结果

```java
开始遍历
Employee{name='张三', age=11, salary=5800.0}
Employee{name='李四', age=12, salary=4800.0}
Employee{name='王五', age=13, salary=3800.0}
Employee{name='赵六', age=14, salary=2800.0}
Employee{name='田七', age=15, salary=1800.0}
```

#### flatmap

flatmap会接受一个函数接口，这个接口的方法会返回一个Stream流

```java
<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);
```

### 排序

#### sorted

sorted方法会对实现了Comparable接口的对象进行排序，也就是自然排序，String，Integer等对象都是实现来这个接口的，所以可以直接用,普通对象需要**实现Comparable接口**

```java
public class Employee implements Comparable<Employee>{
        @Override
    	public int compareTo(Employee o) {
        return this.getSalary()>o.getSalary()? 0:-1;
   	 }
```

对员工实现Comparable接口，重写compareTo方法，按工资排序

```java
Stream<Employee> empsByAge = stream
                			.sorted();
```

#### sotred(Comparator c)

带参数的sorted需要一个`Comparator<T>`接口，按照比较器进行比较

```java
Stream<Employee> empsByAge = stream
                			.sorted((e1,e2)->e1.getSalary()>e2.getSalary()?0:-1);
```

### 匹配

这一部分属于终止操作，因为这里会返回boolean型

#### allMatch

allMatch需要一个断言型接口，所有元素都满足匹配条件时，返回true

```java
boolean allMatch(Predicate<? super T> predicate);
```

#### anyMatch

存在匹配的元素返回true

```java
boolean anyMatch(Predicate<? super T> predicate);
```

#### noneMatch

不存在匹配的元素返回true

```java
boolean noneMatch(Predicate<? super T> predicate);
```

### 查找

#### findFirst

返回第一个元素

```java
Optional<T> findFirst();
```

#### findAny

查找任意元素

#### count

返回元素的个数

```java
long count();
```

#### max

返回最大值，需要一个比较器

```java
Optional<T> max(Comparator<? super T> comparator);
```

#### min

同上

#  时间

# Optional



