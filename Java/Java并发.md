# Java并发

## 基本线程机制

### 线程

- 线程：是进程中的一个单一顺序控制流，一个进程可以包含多个并发执行的线程。
- 多线程：并发编程可以将程序划分为多个分离的、独立运行的任务。通过使用多线程机制，可以将这些子任务交由**执行线程**来驱动。

### 实现线程的三种方式

- 实现`Runnable`接口：无返回值

  ```java
  //1.定义任务：实现Runnable接口并重写run方法
  public class MyRunnable implements Runnable {
  	 @Override
  	 public void run() {
  	 	//通常run方法内总会有循环
  	 }
  }
  //2.驱动任务：将任务实例提交给Thread构造器，并调用start方法
  public class DriveTask{
  	public static void main(String[] args) {
  		Thread thread = new Thread(new MyRunnable());
  		thread.start();
           //new Thread(new MyRunnable()).start();
  	}
  }
  ```

  - `start()`方法：为该线程的执行进行必须的初始化操作，然后调用`run()`方法。

    注：`start()`方法执行完毕后会迅速返回，继续执行`main`主线程中的其他语句。

  - `run()`方法：在新线程中启动该任务，并与其他线程并发执行。

- 实现`Callable`接口：可以有返回值，通过FutureTask封装

  ```java
  //1.定义任务：实现Callable接口并重写call方法
  public class MyCallable implements Callable<String>{
  	 @Override
  	 public String call() throws Exception {
  	 	//...
  	 }
  }
  //2.驱动任务：将任务实例提交给Thread构造器，并调用start方法
  public class DriveTask{
  	public static void main(String[] args) {
          /* FutureTask包装器:
  		 * public FutureTask(Callable<V> callable)
  		 * public FutureTask(Runnable runnable, V result)
  		 */
          FutureTask<String> t = new FutureTask<String>(new MyCallable());
          Thread thread = new Thread(t);
          thread.start();
          String result = t.get();
  
          //当有多个任务执行时(线程池) 
          ExecutorService exec = Executors.newCachedThreadPool();
          ArrayList<Future<String>> res = new ArrayList<Future<String>>();
          for(...)
              res.add(exec.submit(new MyCallable())); //submit方法会产生Future对象
          for(Future<String> f : res)
              f.get();
  	}
  }
  ```

- 继承`Thread`类

  ```java
  public class MyThread extends Thread {
  	@Override
  	public void run() {
  		// ...
  	}
  }
  public static void main(String[] args) {
  	new MyThread().start();
  }
  
  ```
  
- 实现接口要比直接继承`Thread`类要好些

  - Java 只⽀持单继承，但是可以实现多个接⼝；
  - 继承整个 `Thread` 类开销过⼤。

### 线程池

执行器（`Executor`，位于`java.util.concurrent`JUC包中）：允许管理多个异步任务的执行，而无需显式管理线程的生命周期。

```java
// ExecutorService是具有服务生命周期的Executor
ExecutorService
        exec1 = Executors.newCachedThreadPool(),     //可为所有任务创建一个线程
        exec2 = Executors.newFixedThreadPool(5),     //包含有限个可以执行任务的线程
        exec3 = Executors.newSingleThreadExecutor(); //相当于数量为1的FixedThreadPool。当有多个任务被提交，那么这些任务将排队直到轮到自己执行，所有的任务使用相同的线程
for (int i = 0; i < 5; i++) {
    exec1.execute(new MyThread());  //调用run()方法
    exec1.submit(new MyCallable()); //调用call()方法，会产生Future对象
}
exec1.shutdown(); //关闭服务
```

### 线程优先级

`Priority` (线程优先级)

- 将线程的重要性传递给调度器，即使CPU处理线程集的顺序是不确定的，但调度器更**倾向于让优先级高的线程先执行**。

- 优先级不会导致死锁，因为优先级较低的线程只是**执行的频率较低**，并不意味着它不会执行。

- Java有10个优先级即1~10，不过与多数操作系统不能映射的很好，为了使程序可移植，规定只使用`MAX_PRIORITY(10)`、`NORM_PRIORITY(5)`、`MIN_PRIORITY`(1)。

  ```java
   @Override
    public void run() {
         Thread.currentThread().setPriority(Thread.MAX_PRIORITY);
         /*
         Thread.currentThread()：表示当前线程
         Thread.currentThread().setPriority()：修改线程优先级
         Thread.currentThread().getPriority()：得到当前线程优先级
         */
    }
  ```

### 休眠 sleep()

`sleep(long millis)` (休眠)：使任务终止执行给定的时间。该方法会抛出`InterruptedException`，需要在run方法中捕获，因为异常不能跨线程传播回主线程`main()`。

```java
public void run() {
    try{
        Thread.sleep(3000); //3000ms
    }catch(InterruptedException e){
        ...
    }
}
```

### 让步 yield()

`yield()` (让步)：静态方法`Thread.yield()`，调用此方法会给线程调度器暗示：“当前线程已经执行完生命周期中最重要的部分，此时正是切换给其他任务执行的大好时机”。当然，这只是一个暗示或者说建议，没有任何机制保证它将会被采纳，并且也是建议具有相同优先级的其它线程可以运⾏。

### 守护线程

`Daemon` (守护线程)：也称后台线程，指程序在运行时在后台提供一种通用服务的线程

- 不属于程序中不可或缺的一部分

- 所有非后台线程都终止，则此时会杀死进程中的所有后台程序；即只要有任何非后台线程还在运行，程序就不会终止。

  ```java
  Thread daemon = new Thread(new MyRunnable());
  daemon.setDaemon(true); //必须在线程启动前，调用setDaemon方法
  daemon.start();
  System.out.println(daemon.isDaemon());
  ```

## 共享受限资源

### 解决共享资源竞争



### 原子性与易变形



### 原子类



### 临界区



### 同步



## 任务终结

### 阻塞

### 中断

## 线程间的协作

### join() P670



## 死锁

## JUC

### JUC简介

### AQS

## 性能调优

## 高效并发

[JVM-高效并发](./JVM-高效并发)