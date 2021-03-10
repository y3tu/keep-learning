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
>Executor管理多个异步任务的生命周期和执行，这里的异步指多个任务的执行互不干扰，不需要进行同步操作。   

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
#### Daemon
>守护线程指在程序运行的时候在后台提供一种通用服务的线程，比如垃圾回收线程就是一个很称职的守护者，并且这种线程并不属于程序中不可或缺的部分。因此，当所有的非守护线程结束时，程序也就终止了，同时会杀死进程中的所有守护线程。反过来说，只要任何非守护线程还在运行，程序就不会终止。

守护线程和非守护线程的没啥本质的区别：唯一的不同之处就在于虚拟机的离开：如果用户线程已经全部退出运行了，只剩下守护线程存在了，虚拟机也就退出了。 因为没有了被守护者，守护线程也就没有工作可做了，也就没有继续运行程序的必要了。   

将线程转换为守护线程可以通过调用Thread对象的setDaemon(true)方法来实现。在使用守护线程时需要注意以下三点：
* thread.setDaemon(true)必须在thread.start()之前设置，否则会跑出一个IllegalThreadStateException异常。你不能把正在运行的常规线程设置为守护线程。
* 在Daemon线程中产生的新线程也是Daemon的。
* 守护线程应该永远不去访问固有资源，如文件、数据库，因为它会在任何时候甚至在一个操作的中间发生中断。
```java
public static void main(String[] args) {
    Thread thread = new Thread(new MyRunnable());
    thread.setDaemon(true);
    thread.start();
}
```
#### sleep()
Thread.sleep(millisec) 方法会休眠当前正在执行的线程，millisec 单位为毫秒。   
sleep() 可能会抛出 InterruptedException，因为异常不能跨线程传播回 main() 中，因此必须在本地进行处理。线程中抛出的其它异常也同样需要在本地进行处理。
```java
public void run() {
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```
#### yield()
Thread.yield()的调用声明了当前线程已经完成了生命周期中最重要的部分，可以切换给其它线程来执行。该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行。
```java
public void run() {
    Thread.yield();
}
```
### 三.中断