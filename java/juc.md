# JUC

多线程的使用，如果使用不当，不仅不会提高效率，甚至会导致系统崩溃

`juc`指1.5新加入的包`java.util.concurrent`包,用于线程的工具包

## volatile关键字

被volaile修饰的属性，会直接加载入主存中。多个线程可以实时获取最新数据

```java
public class SubJob2 implements Runnable {

     private int count=0;

    @Override
    public void run() {
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        count++;
        System.out.println(count);
    }

    public int getCount() {
        return count;
    }
}
```

执行,值虽然改变了，但是main线程中并没有刷新值，if一致进不去，注释放开才会刷新

```java
public class ThreadTest {

    public static void main(String[] args) throws Exception {
        SubJob2 subJob2 = new SubJob2();
        new Thread(subJob2).start();

        while (true){
            if (subJob2.getCount()==1){
                System.out.println("main:"+subJob2.getCount());
                break;
            }
//            System.out.println("main:"+subJob2.getCount());
        }
    }
}
```

加上volatile关键字

```java
private volatile int count=0;
```

## ConcurrentHashMap

采用分段锁机制，既能保证线程安全，也能保证效率

## CountDownLatch

可以等待所有线程完成，再执行后续操作，通常用于结果汇总

创建一个线程，打印0-5000,传入一个latch,任务执行完成调用countDown方法，表示已完成

```java
public class SubJob2 implements Runnable {

    private CountDownLatch latch;

    public SubJob2(CountDownLatch latch) {
        this.latch = latch;
    }

    @Override
    public void run() {
        for (int i=0;i<5000;i++){
            System.out.println(i);
        }
        //标记已完成
        latch.countDown();
    }

}
```

创建CountDownLatch对象，指定线程数，创建多个线程执行任务,调用await方法等待n个线程完成后，执行后续代码

```java
public static void main(String[] args) throws Exception {
    int size=5;
    final CountDownLatch latch = new CountDownLatch(size);
    SubJob2 subJob2 = new SubJob2(latch);
    for (int i = 0; i < size; i++) {
        new Thread(subJob2).start();
    }
	
    //等待指定个数的线程完成，执行后续代码
    latch.await();
    System.out.println("end-------");

}
```

## Callable接口

创建线程的第三种方式,重写call方法，会带有一个泛型返回值,并且可以抛出异常

```java
public class SubJob3 implements Callable<String> {

    @Override
    public String call() throws Exception {
        return "aaa";
    }
}
```

需要封装到`FutureTask`来执行，并接收结果

```java
SubJob3 subJob3 = new SubJob3();
FutureTask<String> futureTask = new FutureTask<>(subJob3);

new Thread(futureTask).start();

//get操作会等待线程执行完成,才能拿到结果
String result = futureTask.get();
System.out.println(result);
```

## 同步锁Lock

当涉及到多线程安全问题时，可以使用同步方法和同步代码块，还有同步锁

调用lock方法加锁，unlock解锁(必须在finally中)，相当于同步代码块

```java
public class SubJob2 implements Runnable {

    private int c = 100;

    private Lock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
            //加锁
            lock.lock();
            try {
                if (c > 0) {
                    System.out.println(Thread.currentThread().getName() + ":" + c--);
                } else {
                    break;
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                //释放锁
                lock.unlock();
            }
       }
    }
}
```

这种方式需要使用`Condition`对象来实现线程通信,`await/signal`代替原来的`wait/notify`

```java
public class SubJob2 implements Runnable {

    private int c = 100;

    private Lock lock = new ReentrantLock();
    private Condition condition=lock.newCondition();
    @Override
    public void run() {
        while (true) {
            //加锁
            lock.lock();
            try {
                //唤醒其他线程
                condition.signalAll();
                if (c > 0) {
                    System.out.println(Thread.currentThread().getName() + ":" + c--);
                } else {
                    break;
                }
                //等待被唤醒
                condition.await();
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
            //释放锁
        }
    }

}
```

## 线程池

`java.util.concurrent.Executor`是线程使用与调度的根接口

`java.util.concurrent.ThreadPoolExecutor` 是线程池的主要实现类

`java.util.concurrent.ScheduledThreadPoolExecutor` 也是线程池的实现类，可以完成调度功能

**线程池的使用**

少数情况下需要通过new的方式自己创建，大多数情况使用`Executors`来方便的创建线程池

```java
//单线程，线程池
ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
//固定大小的线程池
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(10);
//可以调度的线程
ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(10);
//根据需求自动更改数量
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
```

执行任务

使用submit执行Runnable任务

```java
public static void main(String[] args) throws Exception {

    ExecutorService pool = Executors.newFixedThreadPool(5);

    SubJob2 subJob2 = new SubJob2();
    for (int i = 0; i < 10; i++) {
        pool.submit(subJob2);
    }

    //等待任务完成，关闭线程池
    pool.shutdown();
    //立即关闭
    //pool.shutdownNow();
}
```

执行callable任务,定义结果集收集返回值

```java
public static void main(String[] args) throws Exception {

    ExecutorService pool = Executors.newFixedThreadPool(5);

    SubJob3 subJob3 = new SubJob3();
    //定义结果集
    List<Future<String>> futureList=new ArrayList<>();
    for (int i = 0; i < 10; i++) {
        //Future可以获取未来线程完成的结果
        Future<String> future = pool.submit(subJob3);
        futureList.add(future);
    }

    for (Future<String> future : futureList) {
        //get方法会等待线程执行完成拿到结果
        String result = future.get();
        System.out.println(result);
    }
    //等待任务完成，关闭线程池
    pool.shutdown();
}
```

**调度线程池**

第二个参数时延迟多久，第三个参数为单位,以下表示，5s后执行一次

```java
ScheduledExecutorService pool = Executors.newScheduledThreadPool(10);

SubJob2 subJob2 = new SubJob2();
pool.schedule(subJob2,5,TimeUnit.SECONDS);
```

## Fork/Join

ForkJoinPool可以将任务不断拆分(fork),在把结果汇聚(join),直到不能拆为止

任务需要实现RecursiveTask（有返回值）,或RecursiveAction（无返回值）,重写`compute`方法

通过创建本类对象，把任务的范围进行拆分，调用fork方法执行，结果用join获取,汇总返回

需要设计一个不能再拆分的阈值

```java
public class SubJob4 extends RecursiveTask<Integer > {


    private Integer start;
    private Integer end;

    private Integer threshold=1000;
    public SubJob4(Integer start, Integer end) {
        this.start = start;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        int range=end-start;
        if (range<threshold){
            int sum=0;
            for (int i=0;i<end;i++){
                sum+=i;
            }
            return sum;
        }else{
            int mid=(start+end)/2;

            SubJob4 left = new SubJob4(start, mid);
            left.fork();

            SubJob4 right = new SubJob4(mid + 1, end);
            right.fork();

            return left.join()+right.join();
        }
    }
}
```

`ForkJoinPool`执行`invoke`方法执行任务

```java
public static void main(String[] args) throws Exception {

    ForkJoinPool pool = (ForkJoinPool) Executors.newWorkStealingPool();
    SubJob4 subJob4 = new SubJob4(0, 1000000);

    Integer result = pool.invoke(subJob4);
    System.out.println(result);
}
```

