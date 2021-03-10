### 一.使用线程

* 实现Runnable接口
* 实现Callable接口
* 继承Thread类

实现Runnable和Callable接口的类只能当做一个可以在线程中运行的任务，不是真正意义上的线程。   
因此最后还需要通过Thread类调用，可以理解为任务是通过线程驱动从而执行的。

#### 实现Runnable接口

需要实现接口中的run()方法

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        // ...
    }
}
```

使用Runnable实例再创建一个Thread实例，然后调用Thread实例的start()方法启动线程。

```java
public static void main(String[] args) {
    MyRunnable myRun=new MyRunnable();
    Thread thread = new Thread(myRun);
    thread.start();
}
```

#### 实现Callable接口
与Runnable相比，Callable可以有返回值，返回值通过FutureTask进行封装。
```java
public class MyCallable implements Callable<Integer> {
    public Integer call() {
        return 123;
    }
}
```
```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
    MyCallable mc = new MyCallable();
    FutureTask<Integer> ft = new FutureTask<>(mc);
    Thread thread = new Thread(ft);
    thread.start();
    System.out.println(ft.get());
}
```
#### 继承Thread类
同样也需要实现run()方法，因为Thread类也实现了Runnable接口。  
当调用start()方法启动一个线程时，虚拟机会将该线程放入就绪队列中等待被调度，当一个线程被调度时会执行该线程的run()方法。
```java
public class MyThread extends Thread {
    public void run() {
        // ...
    }
}
```
```java
public static void main(String[] args) {
    MyThread mt = new MyThread();
    mt.start();
}
```

实现接口和继承Thread类哪种方式更好呢？
>1.Java 不支持多重继承，因此继承了 Thread 类就无法继承其它类，但是可以实现多个接口   
>2.类可能只要求可执行就行，继承整个 Thread 类开销过大

### 二.基础线程机制
#### Executor
Executor管理多个异步任务的生命周期和执行，这里的异步指多个任务的执行互不干扰，不需要进行同步操作。   
主要有三种Executor:
* CachedThreadPool：一个任务创建一个线程
* FixedThreadPool： 所有任务只能使用固定大小的线程池
* SingleThreadPool：相当于大小为1的FixedThreadPool
```java
public static void main(String[] args) {
    ExecutorService executorService = Executors.newCachedThreadPool();
    for (int i = 0; i < 5; i++) {
        executorService.execute(new MyRunnable());
    }
    executorService.shutdown();
}
```