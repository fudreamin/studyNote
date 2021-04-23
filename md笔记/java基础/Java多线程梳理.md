# Java多线程梳理

## 1、基本概念

### 1.1 进程与线程

**进程：**进程（Process）是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位。

**线程：**线程是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。

**二者关系：**

1. 线程是进程的一部分；

 	2.  一个线程只能属于一个进程，而一个进程可以拥有一个到多个线程；

**二者区别：**

1. 根本区别：进程是资源分配的基本单位，而线程是程序执行的基本单位。
2. 内存空间：进程拥有自己独立的资源空间，每启动一个进程，系统会为其分配内存空间，且不同进程直接内存空间不共享；而线程与CPU资源分配无关，多个线程共享同一进程内的资源，使用相同的内存地址。
3. 使用开销：进程和线程是包含关系，多任务既可以由多进程实现，也可以由单进程内的多线程实现，还可以混合多进程＋多线程。区别在于：
   1. 多进程的创建和切换相比多线程而言开销更大，并且多进程之间的通信比多线程更慢；
   2. 但是多进程的稳定性要高于多线程，因为在多线程的情况下，一个进程的崩溃并不会影响到其他进程，而在多线程的情况下，任何一个线程崩溃会导致整个进程崩溃。

### 1.2 并行和串行

- 串行：下载多个文件，按照一定的顺序去进行下载的，也就是说，必须等下载完A之后才能开始下载B，它们在时间上是不可能发生重叠的。
- 并行：下载多个文件，开启多条线程，多个文件同时进行下载，这里是严格意义上的，在同一时刻发生的，并行在时间上是重叠的。

## 2、多线程实现

### 2.1 实现方法

**实现多线程的四种方法：**

1. 继承Thread类；

   ```java
   package com.dreamin.threadtest;
   
   //继承Thread类，重写run()方法，调用start开启线程
   //线程开启不一定立即执行，由CPU调度执行
   public class ThreadTest1 extends Thread {
   
       @Override
       public void run() {
           //run方法线程体
           for (int i = 0; i < 400; i++) {
               System.out.println("我在看代码..."+i);
           }
       }
   
       public static void main(String[] args) {
           //main线程，主线程
   
           //创建一个线程对象
           ThreadTest1 threadTest1=new ThreadTest1();
   
           //调用start方法开启线程
           threadTest1.start();
   
           for (int i = 0; i < 2000; i++) {
               System.out.println("我在学习多线程..."+i);
           }
       }
   
   }
   ```

2. 实现Runnable接口；

   ```java
   package com.dreamin.threadtest;
   
   //实Runnable接口
   public class ThreadTest2 implements Runnable{
   
       @Override
       public void run() {
           //run方法线程体
           for (int i = 0; i < 400; i++) {
               System.out.println("我在看代码..."+i);
           }
       }
   
       public static void main(String[] args) {
   
           ThreadTest2 threadTest2=new ThreadTest2();
           //创建线程对象，通过线程对象来开启我们的线程，代理
           new Thread(threadTest2).start();
           for (int i = 0; i < 2000; i++) {
               System.out.println("我在学习多线程..."+i);
           }
   
       }
   }
   
   ```

3. 实现Callable接口；

   ```java
   package test;
   
   import java.util.concurrent.Callable;
   import java.util.concurrent.ExecutionException;
   import java.util.concurrent.FutureTask;
   import java.util.concurrent.TimeUnit;
   
   public class CallableDemo {
       public static void main(String[] args) throws ExecutionException, InterruptedException {
           for (int i = 0; i < 6; i++) {
               FutureTask<Integer> ft=new FutureTask<Integer>(new MyThread());
               new Thread(ft,String.valueOf(i)).start();
               System.out.println(Thread.currentThread().getName());
               System.out.println(ft.get());
           }
       }
   
       static class MyThread implements Callable<Integer> {
   
           public Integer call() throws Exception {
               TimeUnit.SECONDS.sleep(2);
               System.out.println(Thread.currentThread().getName()+"运行中...");
               return 200;
           }
       }
   }
   ```

4. 线程池；

   - 通过ExecutorService实现。

   ```java
   public static void main(String[] args) {
       ExecutorService threadpool= Executors.newFixedThreadPool(3);
       try {
           for (int i = 1; i <= 100; i++) {
               threadpool.execute(()->{
                   System.out.println(Thread.currentThread().getName()+"\t线程开始运行");
               });
           }
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           threadpool.shutdown();
       }
   }
   ```

   通过Executors来实现线程池常用的有newSingleThreadExecutor、newCachedThreadPool、newFixedThreadPool三种实现方法。

   ```java
   //newFixedThreadPool
   public static ExecutorService newFixedThreadPool(int nThreads) {
       return new ThreadPoolExecutor(nThreads, nThreads,
                                     0L, TimeUnit.MILLISECONDS,
                                     new LinkedBlockingQueue<Runnable>());
   //newCachedThreadPool
   public static ExecutorService newCachedThreadPool() {
       return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                     60L, TimeUnit.SECONDS,
                                     new SynchronousQueue<Runnable>());
   //newSingleThreadExecutor
   public static ExecutorService newSingleThreadExecutor() {
       return new FinalizableDelegatedExecutorService
           (new ThreadPoolExecutor(1, 1,
                                   0L, TimeUnit.MILLISECONDS,
                                   new LinkedBlockingQueue<Runnable>()));    
   ```

   除去基本的五个参数的构造方法外，ThreadPoolExecutor类还拥有一个七个参数的构造方法。

   ```java
   public ThreadPoolExecutor(int corePoolSize,
                             int maximumPoolSize,
                             long keepAliveTime,
                             TimeUnit unit,
                             BlockingQueue<Runnable> workQueue,
                             ThreadFactory threadFactory) {
       this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
            threadFactory, defaultHandler);
   ```

   1. **corePoolSize**：线程池中的常驻核心线程数。
   2. **maximumPoolSize**：线程池中能够容纳同时执行的最大线程数，此值必须大于等于1。
   3. **keepAliveTime**：多余的空闲线程的存活时间当前池中线程数量超过corePoolSize时，当空闲时间
      达到keepAliveTime时，多余线程会被销毁直到只剩下corePoolSize个线程为止。
   4. **unit**： keepAliveTime的单位 。
   5. **workQueue**：任务队列，被提交但尚未被执行的任务。
   6. **threadFactory**：表示生成线程池中工作线程的线程工厂，用于创建线程，一般默认的即可。
   7. **handler**：拒绝策略，表示当队列满了，并且工作线程大于等于线程池的最大线程数（maximumPoolSize）时如何来拒绝请求执行的runnable的策略。
      1. AbortPolicy(默认)：直接抛出RejectedExecutionException异常阻止系统正常运行.
      2. CallerRunsPolicy：“调用者运行”一种调节机制，该策略既不会抛弃任务，也不
         会抛出异常，而是将某些任务回退到调用者，从而降低新任务的流量。
      3. DiscardOldestPolicy：抛弃队列中等待最久的任务，然后把当前任务加人队列中
         尝试再次提交当前任务。
      4. DiscardPolicy：该策略默默地丢弃无法处理的任务，不予任何处理也不抛出异常。
         如果允许任务丢失，这是最好的一种策略。

   **注意**：

   - 在创建了线程池后，线程池中的线程数为零。
   - 当调用execute()方法添加一个请求任务时，线程池会做出如下判断： 
     1. 如果正在运行的线程数量小于corePoolSize，那么马上创建线程运行这个任务； 
     2. 如果正在运行的线程数量大于或等于corePoolSize，那么将这个任务放入队列；
     3.  如果这个时候队列满了且正在运行的线程数量还小于maximumPoolSize，那么还是要创建非核心线程立刻运行这个任务； 
     4. 如果队列满了且正在运行的线程数量大于或等于maximumPoolSize，那么线程池会启动饱和拒绝策略来执行。

   - 当一个线程完成任务时，它会从队列中取下一个任务来执行。

   - 当一个线程无事可做超过一定的时间（keepAliveTime）时，线程会判断：如果当前运行的线程数大于corePoolSize，那么这个线程就被停掉。  所以线程池的所有任务完成后，它最终会收缩到corePoolSize的大小。 

   **但是**，在平时创建线程池中，我们实际上并不会采用上述的三种方式，而是通过构造方法自定义一个线程池。原因在于三者中都存在等待队列近乎无限或者最大线程数近乎无限的问题，这一点可以在源码上清晰的看到。

   ```java
   public static void main(String[] args) {
       ExecutorService threadPool=new ThreadPoolExecutor(2,5,3L, TimeUnit.SECONDS,new ArrayBlockingQueue<>(3), Executors.defaultThreadFactory(),new ThreadPoolExecutor.DiscardPolicy());
       try {
           for (int i = 1; i <= 20; i++) {
               threadPool.execute(()->{
                   System.out.println(Thread.currentThread().getName()+"\t线程开始运行");
               });
           }
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           threadPool.shutdown();
       }
   }
   ```

   

**区别:**

1. 对于继承Thread类和实现接口而言，由于Java中只允许单继承，如果通过继承Thread来实现多线程，就意味着该类无法再继承其他类，而Java支持一个类实现多个接口，避免了单继承的局限性，因此使用实现接口的方法要优于继承Thread类。其次，如果一个类继承Thread，则不适合资源共享。但是如果实现了Runnable接口的话，则很容易的实现资源共享。
2. 对于实现Runnable接口和实现Callable接口而言，实现Callable是由返回值的，而实现Runnable接口并没有，也正因此，通过实现Callable接口可以更方便的知道该线程是否结束。

### 2.2 线程状态

通过观察Thread的源码，很容易看出线程有**NEW、RUNNABLE、 、WAITING、TIMED_WAITING、TERMINATED**六大状态。

```java
public enum State {
    /**
     * Thread state for a thread which has not yet started.
     */
    NEW,

    /**
     * Thread state for a runnable thread.  A thread in the runnable
     * state is executing in the Java virtual machine but it may
     * be waiting for other resources from the operating system
     * such as processor.
     */
    RUNNABLE,

    /**
     * Thread state for a thread blocked waiting for a monitor lock.
     * A thread in the blocked state is waiting for a monitor lock
     * to enter a synchronized block/method or
     * reenter a synchronized block/method after calling
     * {@link Object#wait() Object.wait}.
     */
    BLOCKED,

    /**
     * Thread state for a waiting thread.
     * A thread is in the waiting state due to calling one of the
     * following methods:
     * <ul>
     *   <li>{@link Object#wait() Object.wait} with no timeout</li>
     *   <li>{@link #join() Thread.join} with no timeout</li>
     *   <li>{@link LockSupport#park() LockSupport.park}</li>
     * </ul>
     *
     * <p>A thread in the waiting state is waiting for another thread to
     * perform a particular action.
     *
     * For example, a thread that has called <tt>Object.wait()</tt>
     * on an object is waiting for another thread to call
     * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
     * that object. A thread that has called <tt>Thread.join()</tt>
     * is waiting for a specified thread to terminate.
     */
    WAITING,

    /**
     * Thread state for a waiting thread with a specified waiting time.
     * A thread is in the timed waiting state due to calling one of
     * the following methods with a specified positive waiting time:
     * <ul>
     *   <li>{@link #sleep Thread.sleep}</li>
     *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
     *   <li>{@link #join(long) Thread.join} with timeout</li>
     *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
     *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
     * </ul>
     */
    TIMED_WAITING,

    /**
     * Thread state for a terminated thread.
     * The thread has completed execution.
     */
    TERMINATED;
}
```

**NEW**： 新建状态，线程对象已经创建，但尚未启动;

**RUNNABLE**:就绪状态，可运行状态，调用了线程的start方法，已经在java虚拟机中执行，等待获取操作系统资源如CPU，操作系统调度运行;

**BLOCKED**:堵塞状态。线程等待锁的状态，等待获取锁进入同步块/方法或调用wait后重新进入需要竞争锁;

**WAITING**：等待状态。等待另一个线程以执行特定的操作。调用以下方法进入等待状态。 Object.wait(), Thread.join(),LockSupport.park;

**TIMED_WAITING**: 线程等待一段时间。调用带参数的Thread.sleep, objct.wait,Thread.join，LockSupport.parkNanos,LockSupport.parkUntil;

**TERMINATED**:线程结束状态。

**WAITING** 和**BLOCKED**区别：

- Thread如果是在等待获取锁，此时Thread的状态就是**BLOCKED**；
- 其他调用Object.wait,Thread.join,LockSupport.park等都是**WAITING** 或者**TIMED_WAITING**状态。

## 3、线程同步

### 3.1 多线程安全的三大特性

**原子性**：原子性是指一个操作或者多个操作要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行。

**可见性**：可见性是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。产生这样问题的原因是由于java线程通信是通过共享内存的方式进行通信的，而为了加快执行的速度，线程一般是不会直接操作内存的，而是操作缓存。

**有序性**：有序性是指程序在执行的时候，程序的代码执行顺序和语句的顺序是一致的。产生这样问题的原因是由于指令重排的缘故。在Java内存模型中，为了加快程序的运行速度允许编译器和处理器对指令进行重排序，但是重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。（最简单的就是对于int a;int b;，可能因为指令重排的原因先执行int b;在单线程中并不会产生影响，但如果在多线程的环境下就可能出现问题）

### 3.2 synchronized

synchronized可以保证方法或者代码块在运行时，同一时刻只有一个方法可以进入到临界区，同时它还可以保证共享变量的内存可见性。

**应用方式：**

Java中每一个对象都可以作为锁，这是synchronized实现同步的基础：

1. 普通同步方法（实例方法），锁是当前实例对象 ，进入同步代码前要获得当前实例的锁；
2. 静态同步方法，锁是当前类的class对象 ，进入同步代码前要获得当前类对象的锁；
3. 同步方法块，锁是括号里面的对象，对给定对象加锁，进入同步代码库前要获得给定对象的锁。

**注意**: synchronized虽然实现了多线程的可见性和原子性，但并没有实现多线程的有序性，也就是并不能禁止指令重排。

### 3.3 Lock锁

Lock锁实现提供了比使用同步方法和语句可以获得的更广泛的锁操作。它们允许更灵活的结构，可能具有非常不同的属性，并且可能支持多个关联的条件对象。 

```java
public class SaleTicket {
    public static void main(String[] args) {
        final Ticket ticket = new Ticket();
        new Thread(() -> {
            for (int i = 0; i < 100; i++)
                ticket.sale();
        }, "aa").start();
        new Thread(() -> {
            for (int i = 0; i < 100; i++)
                ticket.sale();
        }, "bb").start();
        new Thread(() -> {
            for (int i = 0; i < 100; i++)
                ticket.sale();
        }, "cc").start();
    }
}

class Ticket {
    private int number = 300;

    private Lock lock = new ReentrantLock();

    public void sale() {
        lock.lock();
        try {
            if (number > 0) {
                System.out.println(Thread.currentThread().getName() + "\t卖出" + number-- + "号票\t还剩" + number + "张");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}

```

### 3.4 volatile关键字

volatile关键字保证了多线程的可见性和有序性。

```JAVA
package test;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;

class MyData {
    volatile int number = 0;
//    int number = 0;
    AtomicInteger atomicInteger = new AtomicInteger();

    public void addTo60() {
        number = 60;
    }

    public void addPlusPlus() {
        number++;
    }

    public void addByAtomic(){
        atomicInteger.getAndIncrement();
    }

}

public class VolatileDemo {

    //可见性验证
    public void see() {
        MyData myData = new MyData();
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "启动");
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            myData.addTo60(); 
            System.out.println(Thread.currentThread().getName() + "更新number为" + myData.number);
        }, "AAA").start();
        System.out.println(Thread.currentThread().getName() + " mission is over");
    }

    //原子性验证
    public void atomic() throws InterruptedException {
        MyData myData = new MyData();
        for (int i = 1; i <= 20; i++) {
            new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    myData.addPlusPlus();
                    myData.addByAtomic();
                }
            }, String.valueOf(i)).start();
        }
        while (Thread.activeCount()>2){
            Thread.yield();
        }
        System.out.println(Thread.currentThread().getName()+"当前number值为"+myData.number);
        System.out.println(Thread.currentThread().getName()+"当前atomicInteger值为"+myData.atomicInteger);
    }

    public static void main(String[] args) throws InterruptedException {
        VolatileDemo volatileDemo=new VolatileDemo();
        volatileDemo.atomic();
    }
}


```

