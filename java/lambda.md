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

###基本语法

个人理解，lambda表达式会返回前面声明的接口的实现类的实例，使用`->`分割lambda表达式前面是参数（如果参数只有一个可以不写小括号，但还是建议写上，如果写了参数类型，则所以参数都要写类型，不建议加类型）,后面是代码块(如果只有一行代码，不用加中括号，如果抽象方法有返回值会直接返回这一行代码的返回值)，这些会被用于声明的接口中的唯一抽象方法，代码块就相当于实现类里的方法的代码块;

```java
MathFun mf=(a,b)->{
    System.out.println("zzz");
    return a+b;
};
```

###四大函数式接口

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







