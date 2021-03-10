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
    MyRunnable instance=new MyRunnable();
    Thread thread = new Thread(instance);
    thread.start();
}
```

