
### 基本概念

#### 进程和线程

**进程**

* 进程是操作系统结构的基础，是程序在一个数据集合上运行的过程，是**系统进行资源分配和调度的基本单位**。
* 程序由指令和数据组成，但这些指令要运行，数据要读写，就必须将指令加载至CPU，数据加载至内存。在指令运行过程中还需要用到磁盘、网络等设备。进程就是用来加载指令、管理内存、管理IO等。
* 当一个程序被运行，从磁盘加载这个程序的代码至内存，这时就开启了一个进程。
* 进程就可以视为程序的一个实例。有的程序可以同时运行多个实例进程（浏览器），有的只能运行一个实例进程（QQ音乐）

**线程**

* 线程是操作系统调度的最小单位，也叫做轻量级进程。
* 一个进程内可以分多个线程。
* 一个线程就是一个指令流，将指令流里中的一条条指令以一定顺序交给CPU执行。
* Java中，线程作为最小调度单位，进程作为资源分配的最小单位。在windows中进程是不活动的，只是作为线程的容器。

**区别**

* 进程基本上相互独立，而线程存在于进程内，是进程的一个子集。
* 进程拥有共享资源，如内部空间等，供其内部的线程共享。
* 进程通讯较为复杂：
  * 同一台计算机的进程通信称为IPC(Inter-process communication）
  * 不同计算机之间的进程通信，需要通过网络，并遵守共同的协议，例如http。
* 线程通信相对简单，因为它们共享进程内的内存，例如多个线程可以访问同一个共享变量。
* 线程更轻量，线程上下文切换成本一般上要比进程上下文切换低。

#### 并行和并发

**并行(parallel)**

在同一时刻，有多条指令在多个处理器上同时执行。在宏观上和微观上，都是一起执行的。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f038f7bba8704ae9850665715e4a3802~tplv-k3u1fbpfcp-watermark.awebp)

当系统有一个以上 CPU 时，则线程的操作有可能并行。当一个 CPU 执行一个线程时，另一个 CPU 可以执行另一个线程，两个线程互不抢占 CPU 资源，可以同时进行。

**并发(concurrency）**

在同一时刻，只有一条指令执行，但多个指令被快速的轮换执行。在宏观上是一起执行的效果，在微观上不是一起执行。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7dc1e5f67301491298315deeb019a5f7~tplv-k3u1fbpfcp-watermark.awebp)

当系统只有一个 CPU时，则它不可能同时进行一个以上的线程，只能把 CPU 运行时间划分成若干个时间段，再将时间段分配给各个线程执行，在一个时间段的线程代码运行时，其它线程处于挂起状态。

#### 线程上下文切换

因为一些原因导致 CPU 不再执行当前的线程，转而执行另一个线程的代码。

- 线程的 CPU 时间片用完。
- 垃圾回收。
- 有更高优先级的线程需要运行。
- 线程自己调用了 `sleep`、`yield`、`wait`、`join`、`park`、`synchronized`、`lock` 等方法。

当上下文切换发生时，需要由操作系统**保存当前线程的状态，并恢复另一个线程的状态**，Java 中程序计数器就是用来记住下一条 JVM 指令的执行地址， 是线程私有的。

- 状态包括程序计数器、虚拟机栈中每个栈帧的信息，如局部变量、操作数栈、返回地址等。
- 上下文切换频繁发生会影响性能。

#### 守护线程(Daemon)

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

#### 协程

协程（Coroutine）最直观的解释是**线程中的线程**、**轻量级线程**或者**用户态线程**，一个线程可以拥有多个协程，且**不被操作系统内核管理，完全由程序所控制**（即协程的调度、切换都发生在用户态）。 Java 语言并没有对协程的原生支持，但是某些开源框架（Kilim）模拟出了协程的功能。

**线程的缺点**

- 线程中的同步锁是重量级操作。
- 线程在阻塞状态和可运行状态之间切换，需要耗费一定的 CPU 资源。
- 线程的上线文切换，需要耗费一定的 CPU 资源。

**协程的优点**

- 不会引起线程上下文切换。
- 协程的开销远远小于线程的开销。



### 线程池的使用

线程池

> 一个管理线程的池子

#### 线程池的作用

* **降低资源消耗**：通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
* **提高响应速度**：当任务到达时，可以不需要等待线程创建就能立即执行。
* **提高线程可管理性**：统一管理线程，避免系统创建大量同类型线程导致内存消耗。

#### 线程池执行原理

当ThreadPoolExecutor 完成预热之后（当前运行的线程数大于等于 corePoolSize），提交的大部分任务都会被放到 BlockingQueue。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/42c62a5dbc044a46bc395a97d268b6e1~tplv-k3u1fbpfcp-watermark.awebp)

#### 线程池参数解析

ThreadPoolExecutor 的通用构造函数如下：

```java
public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> blockingQueue, ThreadFactory threadFactory, RejectedExecutionHandler handler);
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1644b822dfd4d159f939531e6ca7f2e~tplv-k3u1fbpfcp-watermark.awebp)

* corePoolSize：核心线程大小。当有新任务时，如果线程池中的线程数没有达到核心线程大小，则会创建新的线程执行任务，否则将任务放入阻塞队列。
* maximumPoolSize：最大线程数。当阻塞队列填满时，如果线程池中线程数没有超过最大线程数，则会创建新的线程运行任务(非核心线程)。否则根据拒绝策略处理新任务。非核心线程类似于临时借来的资源，这些线程在空闲时间超过 keepAliveTime 之后，就应该退出，避免资源浪费。
* keepAliveTime：线程空闲时间。**非核心线程**空闲后，保持存活的时间，此参数只对非核心线程有效。设置为0，表示多余的空闲线程会被立即终止。
* unit：线程空闲时间单位
* blockingQueue：阻塞队列。
* threadFactory：线程池创建一个新的线程时，都是通过线程工厂方法来完成的。在 ThreadFactory 中只定义了一个方法 newThread，每当线程池需要创建新线程就会调用它。

```java
public class MyThreadFactory implements ThreadFactory {
    private final String poolName;
    
    public MyThreadFactory(String poolName) {
        this.poolName = poolName;
    }
    
    public Thread newThread(Runnable runnable) {
        //将线程池名字传递给构造函数，用于区分不同线程池的线程
        return new MyAppThread(runnable, poolName);
    }
}
```

* handler：线程拒绝策略。当队列和线程池都满了时，根据拒绝策略处理新任务。
  * AbortPolicy:默认的策略，直接抛出RejectedExecutionException
  * DiscardPolicy:不处理，直接丢弃
  * DiscardOldestPolicy:将等待队列队首的任务丢弃，并执行当前任务
  * CallerRunsPolicy:由调用线程处理该任务
  * 自定义拒绝策略：实现RejectedExecutionHandler接口

#### 合理设置线程池大小

如果线程池线程数量太小，当有大量请求需要处理，系统响应比较慢影响体验，甚至会出现任务队列大量堆积任务导致OOM。

如果线程池线程数量过大，大量线程可能会同时在争取 CPU 资源，这样会导致大量的上下文切换（cpu给线程分配时间片，当线程的cpu时间片用完后保存状态，以便下次继续运行），从 而增加线程的执行时间，影响了整体执行效率。

**CPU 密集型任务(N+1)**： 这种任务消耗的主要是 CPU 资源，可以将线程数设置为 N（CPU 核心数）+1，比 CPU 核心数多出来的一个线程是为了防止某些原因导致的任务暂停（线程阻塞，如io操作，等待锁，线程sleep）而带来的影响。一旦某个线程被阻塞，释放了cpu资源，而在这种情况下多出来的一个线程就可以充分利用 CPU 的空闲时间。

**I/O 密集型任务(2N)**： 系统会用大部分的时间来处理 I/O 操作，而线程等待 I/O 操作会被阻塞，释放 cpu资源，这时就可以将 CPU 交出给其它线程使用。因此在 I/O 密集型任务的应用中，我们可以多配置一些线程，具体的计算方法：最佳线程数 = CPU核心数 * (1/CPU利用率) = CPU核心数 * (1 + (I/O耗时/CPU耗时))，一般可设置为2N

#### 常用线程池

* **FixedThreadPool**

固定线程数的线程池。任何时间点，最多只有 nThreads 个线程处于活动状态执行任务。

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
	return new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>());
}
```

使用无界队列 LinkedBlockingQueue（队列容量为 Integer.MAX_VALUE），运行中的线程池不会拒绝任务，即不会调用RejectedExecutionHandler.rejectedExecution()方法。

maxThreadPoolSize 是无效参数，故将它的值设置为与 coreThreadPoolSize 一致。

keepAliveTime 也是无效参数，设置为0L，因为此线程池里所有线程都是核心线程，核心线程不会被回收（除非设置了executor.allowCoreThreadTimeOut(true)）

适用场景：适用于处理CPU密集型的任务，确保CPU在长期被工作线程使用的情况下，尽可能少的分配线程，即适用执行长期的任务。需要注意的是，**FixedThreadPool 不会拒绝任务，在任务比较多的时候会导致 OOM。**

* **SingleThreadExecutor**

只有一个线程的线程池，相当于大小为1的FixedThreadPool。

```java
public static ExecutionService newSingleThreadExecutor() {
	return new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>());
}
```

使用无界队列 LinkedBlockingQueue。线程池只有一个运行的线程，新来的任务放入工作队列，线程处理完任务就循环从队列里获取任务执行。保证顺序的执行各个任务。

适用场景：适用于串行执行任务的场景，一个任务一个任务地执行。**在任务比较多的时候也是会导致 OOM。**

* **CachedThreadPool**

一个任务创建一个线程

```java
public static ExecutorService newCachedThreadPool() {
	return new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>());
}
```

如果主线程提交任务的速度高于线程处理任务的速度时，`CachedThreadPool` 会不断创建新的线程。极端情况下，这样会导致耗尽 cpu 和内存资源。

使用没有容量的SynchronousQueue作为线程池工作队列，当线程池有空闲线程时，`SynchronousQueue.offer(Runnable task)`提交的任务会被空闲线程处理，否则会创建新的线程处理任务。

适用场景：用于并发执行大量短期的小任务。`CachedThreadPool`允许创建的线程数量为 Integer.MAX_VALUE ，**可能会创建大量线程，从而导致 OOM。**

* **ScheduledThreadPoolExecutor**

在一定时间延迟后运行任务，或者定期执行任务。在实际项目中基本不会被用到，因为有其他方案选择比如`quartz`。

适用场景：周期性执行任务的场景，需要限制线程数量的场景。



### 线程的使用

#### 线程创建

* 实现Runnable接口
* 实现Callable接口
* 继承Thread类

实现Runnable和Callable接口的类只能当做一个可以在线程中运行的任务，不是真正意义上的线程。   
因此最后还需要通过Thread类调用，可以理解为任务是通过线程驱动从而执行的。

##### 实现Runnable接口
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

##### 实现Callable接口
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
##### 继承Thread类
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

#### 线程常用方法

##### sleep()
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
##### yield()
Thread.yield()的调用声明了当前线程已经完成了生命周期中最重要的部分，可以切换给其它线程来执行。该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行。
```java
public void run() {
    Thread.yield();
}
```
#####  interrupt() 

一个线程执行完毕后会自动结束，如果在运行过程中发生异常一会提前结束。

通过调用一个线程的 interrupt() 来中断该线程，如果该线程处于阻塞、限期等待或者无限期等待状态，那么就会抛出 InterruptedException，从而提前结束该线程。但是不能中断 I/O 阻塞和 synchronized 锁阻塞。

在 main() 中启动一个线程之后再中断它，由于线程中调用了 Thread.sleep() 方法，因此会抛出一个 InterruptedException，从而提前结束线程，不执行之后的语句。

```java
private static class MyThread1 extends Thread {
        @Override
        public void run() {
            try {
                Thread.sleep(2000);
                System.out.println("Thread run");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
}
```

```java
public static void main(String[] args) throws InterruptedException {
    Thread thread1 = new MyThread1();
    thread1.start();
    thread1.interrupt();
    System.out.println("Main run");
}


java.lang.InterruptedException: sleep interrupted
    at java.lang.Thread.sleep(Native Method)
    at InterruptExample.lambda$main$0(InterruptExample.java:5)
    at InterruptExample$$Lambda$1/713338599.run(Unknown Source)
    at java.lang.Thread.run(Thread.java:745)
```

##### interrupted()

如果一个线程的 run() 方法执行一个无限循环，并且没有执行 sleep() 等会抛出 InterruptedException 的操作，那么调用线程的 interrupt() 方法就无法使线程提前结束。

但是调用 interrupt() 方法会设置线程的中断标记，此时调用 interrupted() 方法会返回 true。因此可以在循环体中使用 interrupted() 方法来判断线程是否处于中断状态，从而提前结束线程。
```java
public class InterruptExample {

    private static class MyThread2 extends Thread {
        @Override
        public void run() {
            while (!interrupted()) {
                // ..
            }
            System.out.println("Thread end");
        }
    }
}

public static void main(String[] args) throws InterruptedException {
    Thread thread2 = new MyThread2();
    thread2.start();
    thread2.interrupt();
}
```

##### 线程池的中断操作

调用 Executor 的 shutdown() 方法会等待线程都执行完毕之后再关闭，但是如果调用的是 shutdownNow() 方法，则相当于调用每个线程的 interrupt() 方法。

```java
public static void main(String[] args) {
    ExecutorService executorService = Executors.newCachedThreadPool();
    executorService.execute(() -> {
        try {
            Thread.sleep(2000);
            System.out.println("Thread run");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });
    executorService.shutdownNow();
    System.out.println("Main run");
}

Main run
java.lang.InterruptedException: sleep interrupted
    at java.lang.Thread.sleep(Native Method)
    at ExecutorInterruptExample.lambda$main$0(ExecutorInterruptExample.java:9)
    at ExecutorInterruptExample$$Lambda$1/1160460865.run(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
    at java.lang.Thread.run(Thread.java:745)
```

如果只想中断 Executor 中的一个线程，可以通过使用 submit() 方法来提交一个线程，它会返回一个 Future<?> 对象，通过调用该对象的 cancel(true) 方法就可以中断线程。

```java
Future<?> future = executorService.submit(() -> {
    // ..
});
future.cancel(true);
```



##### join()

当多个线程可以一起工作去解决某个问题时，如果某些部分必须在其它部分之前完成，那么就需要对线程进行协调。

在线程中调用另一个线程的 join() 方法，会将当前线程挂起，而不是忙等待，直到目标线程结束。

对于以下代码，虽然 b 线程先启动，但是因为在 b 线程中调用了 a 线程的 join() 方法，b 线程会等待 a 线程结束才继续执行，因此最后能够保证 a 线程的输出先于 b 线程的输出。

```java
public class JoinExample {

    private class A extends Thread {
        @Override
        public void run() {
            System.out.println("A");
        }
    }

    private class B extends Thread {

        private A a;

        B(A a) {
            this.a = a;
        }

        @Override
        public void run() {
            try {
                a.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("B");
        }
    }

    public void test() {
        A a = new A();
        B b = new B(a);
        b.start();
        a.start();
    }
}

public static void main(String[] args) {
    JoinExample example = new JoinExample();
    example.test();
}

A
B
```

##### wait()、notify()、notifyAll()

调用 wait() 使得线程等待某个条件满足，线程在等待时会被挂起，当其他线程的运行使得这个条件满足时，其它线程会调用 notify() 或者 notifyAll() 来唤醒挂起的线程。
它们都属于 Object 的一部分，而不属于 Thread。
只能用在同步方法或者同步控制块中使用，否则会在运行时抛出 IllegalMonitorStateException。
使用 wait() 挂起期间，线程会释放锁。这是因为，如果没有释放锁，那么其它线程就无法进入对象的同步方法或者同步控制块中，那么就无法执行 notify() 或者 notifyAll() 来唤醒挂起的线程，造成死锁。

```java
public class WaitNotifyExample {

    public synchronized void before() {
        System.out.println("before");
        notifyAll();
    }

    public synchronized void after() {
        try {
            wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("after");
    }
}

public static void main(String[] args) {
    ExecutorService executorService = Executors.newCachedThreadPool();
    WaitNotifyExample example = new WaitNotifyExample();
    executorService.execute(() -> example.after());
    executorService.execute(() -> example.before());
}

before
after
```

wait() 和 sleep() 的区别

* wait() 是 Object 的方法，而 sleep() 是 Thread 的静态方法
* wait() 会释放锁，sleep() 不会

##### await()、signal()、signalAll()

java.util.concurrent 类库中提供了 Condition 类来实现线程之间的协调，可以在 Condition 上调用 await() 方法使线程等待，其它线程调用 signal() 或 signalAll() 方法唤醒等待的线程。

相比于 wait() 这种等待方式，await() 可以指定等待的条件，因此更加灵活。

使用 Lock 来获取一个 Condition 对象。

```java
public class AwaitSignalExample {

    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public void before() {
        lock.lock();
        try {
            System.out.println("before");
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }

    public void after() {
        lock.lock();
        try {
            condition.await();
            System.out.println("after");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}

public static void main(String[] args) {
    ExecutorService executorService = Executors.newCachedThreadPool();
    AwaitSignalExample example = new AwaitSignalExample();
    executorService.execute(() -> example.after());
    executorService.execute(() -> example.before());
}

before
after
```

#### 线程状态

一个线程只能处于一种状态，并且这里的线程状态特指 Java 虚拟机的线程状态，不能反映线程在特定操作系统下的状态。

##### 新建（NEW）

创建后尚未启动

##### 可运行（RUNABLE）

正在 Java 虚拟机中运行。但是在操作系统层面，它可能处于运行状态，也可能等待资源调度（例如处理器资源），资源调度完成就进入运行状态。所以该状态的可运行是指可以被运行，具体有没有运行要看底层操作系统的资源调度。

##### 阻塞（BLOCKED）

请求获取 monitor lock 从而进入 synchronized 函数或者代码块，但是其它线程已经占用了该 monitor lock，所以出于阻塞状态。要结束该状态进入 RUNABLE 需要其他线程释放 monitor lock。

##### 无限期等待（WAITING）

等待其它线程显式地唤醒。

阻塞和等待的区别在于，阻塞是被动的，它是在等待获取 monitor lock。而等待是主动的，通过调用 Object.wait() 等方法进入。

|                                   进入方法 | 退出方法                             |
| -----------------------------------------: | ------------------------------------ |
| 没有设置 Timeout 参数的 Object.wait() 方法 | Object.notify() / Object.notifyAll() |
| 没有设置 Timeout 参数的 Thread.join() 方法 | 被调用的线程执行完毕                 |
|                    LockSupport.park() 方法 | LockSupport.unpark(Thread)           |

##### 限期等待（TIMED_WAITING）

无需等待其它线程显式地唤醒，在一定时间之后会被系统自动唤醒。

| 进入方法                                 | 退出方法                                        |
| ---------------------------------------- | ----------------------------------------------- |
| Thread.sleep() 方法                      | 时间结束                                        |
| 设置了 Timeout 参数的 Object.wait() 方法 | 时间结束 / Object.notify() / Object.notifyAll() |
| 设置了 Timeout 参数的 Thread.join() 方法 | 时间结束 / 被调用的线程执行完毕                 |
| LockSupport.parkNanos() 方法             | LockSupport.unpark(Thread)                      |
| LockSupport.parkUntil() 方法             | LockSupport.unpark(Thread)                      |

调用 Thread.sleep() 方法使线程进入限期等待状态时，常常用“使一个线程睡眠”进行描述。调用 Object.wait() 方法使线程进入限期等待或者无限期等待时，常常用“挂起一个线程”进行描述。睡眠和挂起是用来描述行为，而阻塞和等待用来描述状态。

##### 死亡（TERMINATED）

可以是线程结束任务之后自己结束，或者产生了异常而结束。



![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7b6d9a4d192c4db98309d2eec857fde5~tplv-k3u1fbpfcp-watermark.awebp?)



#### 线程死锁

多个线程同时被阻塞，它们中的一个或者全部都在等待某个资源被释放。由于线程被无限期地阻塞，因此程序不可能正常终止。

如下图所示，线程 A 持有资源 2，线程 B 持有资源 1，他们同时都想申请对方的资源，所以这两个线程就会互相等待而进入死锁状态。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9567e0d027f14bc6a6efd7bfcdb534ce~tplv-k3u1fbpfcp-watermark.awebp)



```java
public class DeadLockDemo {
    private static Object resource1 = new Object();//资源 1
    private static Object resource2 = new Object();//资源 2

    public static void main(String[] args) {
        new Thread(() -> {
            synchronized (resource1) {
                System.out.println(Thread.currentThread() + "get resource1");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource2");
                synchronized (resource2) {
                    System.out.println(Thread.currentThread() + "get resource2");
                }
            }
        }, "线程 1").start();

        new Thread(() -> {
            synchronized (resource2) {
                System.out.println(Thread.currentThread() + "get resource2");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource1");
                synchronized (resource1) {
                    System.out.println(Thread.currentThread() + "get resource1");
                }
            }
        }, "线程 2").start();
    }
}

//代码输出
Thread[线程 1,5,main]get resource1
Thread[线程 2,5,main]get resource2
Thread[线程 1,5,main]waiting get resource2
Thread[线程 2,5,main]waiting get resource1
```

1. 线程 A 通过 synchronized (resource1) 获得 resource1 的监视器锁，然后通过 Thread.sleep(1000);
2. 线程 A 休眠 1s 为的是让线程 B 得到执行然后获取到 resource2 的监视器锁。
3. 线程 A 和线程 B 休眠结束了都开始企图请求获取对方的资源，然后这两个线程就会陷入互相等待的状态，这也就产生了死锁。

### 互斥同步

Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问，第一个是 JVM 实现的 synchronized，而另一个是 JDK 实现的 ReentrantLock。

#### synchronized

> 属于独占锁、悲观锁、可重入锁、非公平锁。

作用于同一个对象，如果调用两个对象上的同步代码块，就不会进行同步。

* 同步一个代码块

```java
public void func() {
    synchronized (this) {
        // ...
    }
}
```

* 同步一个方法

```java
public synchronized void func () {
// ...
}
```

作用于整个类，也就是说两个线程调用同一个类的不同对象上的这种同步语句，也会进行同步。

* 同步一个类
```java
public void func() {
    synchronized (SynchronizedExample.class) {
        // ...
    }
}
```

*  同步一个静态方法

```java
public synchronized static void fun() {
    // ...
}
```

#### ReentrantLock

> 继承了Lock类，可重入锁、悲观锁、独占锁、互斥锁、同步锁

ReentrantLock 是 java.util.concurrent（J.U.C）包中的锁。

```java
public class LockExample {

    private Lock lock = new ReentrantLock();

    public void func() {
        lock.lock();
        try {
            for (int i = 0; i < 10; i++) {
                System.out.print(i + " ");
            }
        } finally {
            lock.unlock(); // 确保释放锁，从而避免发生死锁。
        }
    }
}

public static void main(String[] args) {
    LockExample lockExample = new LockExample();
    ExecutorService executorService = Executors.newCachedThreadPool();
    executorService.execute(() -> lockExample.func());
    executorService.execute(() -> lockExample.func());
}

0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9
```

除非需要使用 ReentrantLock 的高级功能，否则优先使用 synchronized。这是因为 synchronized 是 JVM 实现的一种锁机制，JVM 原生地支持它，而 ReentrantLock 不是所有的 JDK 版本都支持。并且使用 synchronized 不用担心没有释放锁而导致死锁问题，因为 JVM 会确保锁的释放。





### 并发工具 J.U.C 

java.util.concurrent（J.U.C）大大提高了并发性能

#### CountDownLatch

用来控制一个或者多个线程等待多个线程。维护了一个计数器 cnt，每次调用 countDown() 方法会让计数器的值减 1，减到 0 的时候，那些因为调用 await() 方法而在等待的线程就会被唤醒。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3e01012985d6409a872b0735c0478df8~tplv-k3u1fbpfcp-zoom-1.image)

```java
public class CountdownLatchExample {

    public static void main(String[] args) throws InterruptedException {
        final int totalThread = 10;
        CountDownLatch countDownLatch = new CountDownLatch(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.print("run..");
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        System.out.println("end");
        executorService.shutdown();
    }
}

run..run..run..run..run..run..run..run..run..run..end

```

#### CyclicBarrier

用来控制多个线程互相等待，只有当多个线程都到达时，这些线程才会继续执行。

和 CountdownLatch 相似，都是通过维护计数器来实现的。线程执行 await() 方法之后计数器会减 1，并进行等待，直到计数器为 0，所有调用 await() 方法而在等待的线程才能继续执行。

CyclicBarrier 和 CountdownLatch 的一个区别是，CyclicBarrier 的计数器通过调用 reset() 方法可以循环使用，所以它才叫做循环屏障。

CyclicBarrier 有两个构造函数，其中 parties 指示计数器的初始值，barrierAction 在所有线程都到达屏障的时候会执行一次。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b09b37bf30d34b238ce48ba459a654ff~tplv-k3u1fbpfcp-zoom-1.image)

```java
public class CyclicBarrierExample {

    public static void main(String[] args) {
        final int totalThread = 10;
        CyclicBarrier cyclicBarrier = new CyclicBarrier(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.print("before..");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException | BrokenBarrierException e) {
                    e.printStackTrace();
                }
                System.out.print("after..");
            });
        }
        executorService.shutdown();
    }
}

before..before..before..before..before..before..before..before..before..before..after..after..after..after..after..after..after..after..after..after..
```

#### Semaphore

Semaphore 类似于操作系统中的信号量，可以控制对互斥资源的访问线程数。

以下代码模拟了对某个服务的并发请求，每次只能有 3 个客户端同时访问，请求总数为 10。

```java
public class SemaphoreExample {

    public static void main(String[] args) {
        final int clientCount = 3;
        final int totalRequestCount = 10;
        Semaphore semaphore = new Semaphore(clientCount);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalRequestCount; i++) {
            executorService.execute(()->{
                try {
                    semaphore.acquire();
                    System.out.print(semaphore.availablePermits() + " ");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            });
        }
        executorService.shutdown();
    }
}

2 1 2 2 2 2 2 1 2 2
```

#### BlockingQueue

java.util.concurrent.BlockingQueue 接口有以下阻塞队列的实现：

* **FIFO 队列**(先进先出) ：LinkedBlockingQueue、ArrayBlockingQueue（固定长度）
* **优先级队列** ：PriorityBlockingQueue

提供了阻塞的 take() 和 put() 方法：如果队列为空 take() 将阻塞，直到队列中有内容；如果队列为满 put() 将阻塞，直到队列有空闲位置。

**使用 BlockingQueue 实现生产者消费者问题**

```java
public class ProducerConsumer {

    private static BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);

    private static class Producer extends Thread {
        @Override
        public void run() {
            try {
                queue.put("product");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print("produce..");
        }
    }

    private static class Consumer extends Thread {

        @Override
        public void run() {
            try {
                String product = queue.take();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print("consume..");
        }
    }
}

public static void main(String[] args) {
    for (int i = 0; i < 2; i++) {
        Producer producer = new Producer();
        producer.start();
    }
    for (int i = 0; i < 5; i++) {
        Consumer consumer = new Consumer();
        consumer.start();
    }
    for (int i = 0; i < 3; i++) {
        Producer producer = new Producer();
        producer.start();
    }
}

produce..produce..consume..consume..produce..consume..produce..consume..produce..consume..
```

#### FutureTask

在介绍 Callable 时我们知道它可以有返回值，返回值通过 Future<V> 进行封装。FutureTask 实现了 RunnableFuture 接口，该接口继承自 Runnable 和 Future<V> 接口，这使得 FutureTask 既可以当做一个任务执行，也可以有返回值。

FutureTask 可用于异步获取执行结果或取消执行任务的场景。当一个计算任务需要执行很长时间，那么就可以用 FutureTask 来封装这个任务，主线程在完成自己的任务之后再去获取结果。

```java
public class FutureTaskExample {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<Integer> futureTask = new FutureTask<Integer>(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                int result = 0;
                for (int i = 0; i < 100; i++) {
                    Thread.sleep(10);
                    result += i;
                }
                return result;
            }
        });

        Thread computeThread = new Thread(futureTask);
        computeThread.start();

        Thread otherThread = new Thread(() -> {
            System.out.println("other task is running...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        otherThread.start();
        System.out.println(futureTask.get());
    }
}

other task is running...
4950
```

#### ForkJoin

主要用于并行计算中，和 MapReduce 原理类似，都是把大的计算任务拆分成多个小任务并行计算。

```java
public class ForkJoinExample extends RecursiveTask<Integer> {

    private final int threshold = 5;
    private int first;
    private int last;

    public ForkJoinExample(int first, int last) {
        this.first = first;
        this.last = last;
    }

    @Override
    protected Integer compute() {
        int result = 0;
        if (last - first <= threshold) {
            // 任务足够小则直接计算
            for (int i = first; i <= last; i++) {
                result += i;
            }
        } else {
            // 拆分成小任务
            int middle = first + (last - first) / 2;
            ForkJoinExample leftTask = new ForkJoinExample(first, middle);
            ForkJoinExample rightTask = new ForkJoinExample(middle + 1, last);
            leftTask.fork();
            rightTask.fork();
            result = leftTask.join() + rightTask.join();
        }
        return result;
    }
}

public static void main(String[] args) throws ExecutionException, InterruptedException {
    ForkJoinExample example = new ForkJoinExample(1, 10000);
    ForkJoinPool forkJoinPool = new ForkJoinPool();
    Future result = forkJoinPool.submit(example);
    System.out.println(result.get());
}

```

ForkJoin 使用 ForkJoinPool 来启动，它是一个特殊的线程池，线程数量取决于 CPU 核数。



###  



