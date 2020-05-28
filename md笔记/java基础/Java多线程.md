# java多线程

（Java Thread）

## 1、进程和线程

**进程**：是并发执行的程序在执行过程中分配和管理资源的基本单位，是一个动态概念，竞争计算机系统资源的基本单位。

**线程**：是进程的一个执行单元，是进程内科调度实体。比进程更小的独立运行的基本单位。线程也被称为轻量级进程。

区别:

- 地址空间：同一进程的线程共享本进程的地址空间，而进程之间则是独立的地址空间。
- 资源拥有：同一进程内的线程共享本进程的资源如内存、I/O、cpu等，但是进程之间的资源是独立的。
- 线程的执行过程是线性的，尽管中间会发生中断或者暂停，但是进程所拥有的资源只为改线状执行过程服务，一旦发生线程切换，这些资源需要被保护起来。
- 进程分为单线程进程和多线程进程，单线程进程宏观来看也是线性执行过程，微观上只有单一的执行过程。多线程进程宏观是线性的，微观上多个执行操作。

- 一个进程崩溃后，在保护模式下不会对其他进程产生影响，但是一个线程崩溃整个进程都死掉。所以多进程要比多线程健壮。

- 进程切换时，消耗的资源大，效率高。所以涉及到频繁的切换时，使用线程要好于进程。同样如果要求同时进行并且又要共享某些变量的并发操作，只能用线程不能用进程。

- 执行过程：每个独立的进程程有一个程序运行的入口、顺序执行序列和程序入口。但是线程不能独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
- 线程是处理器调度的基本单位，但是进程不是。

## 2、多线程概念

### 2.1 并行和串行

以文件下载为例：

- 串行：下载多个文件，按照一定的顺序去进行下载的，也就是说，必须等下载完A之后才能开始下载B，它们在时间上是不可能发生重叠的。

- 并行：下载多个文件，开启多条线程，多个文件同时进行下载，这里是严格意义上的，在同一时刻发生的，并行在时间上是重叠的。

### 2.2 多线程

- 多线程，是指从软件或者硬件上实现多个线程并发执行的技术。具有多线程能力的计算机因有硬件支持而能够在同一时间执行多于一个线程，进而提升整体处理性能。

- 多线程在单核CPU上交替执行，因为切换的很快，所以有同时进行的错觉。在多核CPU上可以真正的同时执行。
- 多线程是为了同步完成多项任务，不是为了提高运行效率，而是为了提高资源使用效率来提高系统的效率。线程是在同一时间需要完成多项任务的时候实现的。

## 3、多线程实现

### 3.1 继承Thread类

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

### 3.2 实现Runnable接口

- 推荐使用：避免单继承局限性，灵活方便，方便同一个对象被多个线程使用

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

并发问题：

```java
package com.dreamin.threadtest;

public class ThreadTest3 implements Runnable{

    private static int tickets=50;

    @Override
    public void run() {
        while(true){
            if(tickets<=0){
                break;
            }
            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName()+tickets--+"张票");
        }
    }

    public static void main(String[] args) {
        ThreadTest3 threadTest3=new ThreadTest3();
        new Thread(threadTest3,"小明").start();
        new Thread(threadTest3,"老师").start();
        new Thread(threadTest3,"黄牛").start();
    }
}

```

龟兔赛跑：

```java
package com.dreamin.threadtest;

//模拟龟兔赛跑
public class ThreadTest4 implements Runnable{

    private static String winner;

    @Override
    public void run() {
        for (int i = 1; i <= 100; i++) {
            if(i%10==0&&Thread.currentThread().getName()=="兔子"){
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            Boolean flag=gameOver(i);
            if(flag){
                break;
            }
            System.out.println(Thread.currentThread().getName()+"跑了"+i+"步");
        }
    }

    //判断是否完成比赛
    private boolean gameOver(int steps){
        if(winner!=null){
            return true;
        }
        if(steps>=100){
            System.out.println("胜利者为："+Thread.currentThread().getName());
            winner=Thread.currentThread().getName();
            return true;
        }
        return false;
    }

    public static void main(String[] args) {
        ThreadTest4 threadTest4=new ThreadTest4();
        new Thread(threadTest4,"兔子").start();
        new Thread(threadTest4,"乌龟").start();
    }
}
```

### 3.3 实现Callable接口

- 可以定义返回值
- 可以抛出异常

```java
package com.dreamin.threadtest;

import java.util.concurrent.*;

public class ThreadTest5 implements Callable<Boolean> {
    @Override
    public Boolean call() throws Exception {
        for (int i = 0; i < 20; i++) {
            System.out.println("我在看代码..."+i);
        }
        return true;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ThreadTest5 threadTest5=new ThreadTest5();
        //创建执行服务
        ExecutorService executorService= Executors.newFixedThreadPool(1);
        //提交执行
        Future<Boolean> future=executorService.submit(threadTest5);
        //获取结果
        Boolean rs=future.get();
        //关闭服务
        executorService.shutdown();

        for (int i = 0; i < 30; i++) {
            System.out.println("我在看书..."+i);
        }
    }
}
```

## 4、静态代理

- 真实对象和代理对象要实现同一接口
- 代理对象要代理真实角色

好处：

- 代理对象可以做很多真实对象做不了的额事情
- 真实对象可以专注做自己的事情

```java
package com.dreamin.threadtest;

public class StaticProxy {
    public static void main(String[] args) {
        WeddingCompany weddingCompany=new WeddingCompany(new Person());
        weddingCompany.happyMarry();
    }
}

interface Marry {
    void happyMarry();
}

//结婚人
class Person implements Marry {

    @Override
    public void happyMarry() {
        System.out.println("张三要结婚了！");
    }
}

//代理婚庆公司
class WeddingCompany implements Marry {

    private Marry target;

    public WeddingCompany(Marry target) {
        this.target = target;
    }

    @Override
    public void happyMarry() {
        before();
        this.target.happyMarry();
        after();
    }

    public void before() {
        System.out.println("结婚之前，布置场景");
    }

    public void after() {
        System.out.println("结婚之后，收尾款");
    }
}
```

## 5、λ（Lamda）表达式 

1、函数式接口（Functional Interface）

- 任何接口，如果只包含一个抽象方法，那么它就是一个函数式接口。
- 对于函数式接口，我们可以通过lamda表达式创建该接口的对象。

2、lamda表达式推导

```java
package com.dreamin.threadtest;

public class ThreadTest6 {
    //3.静态内部类
    static class Like2 implements ILike{
        @Override
        public void lamda() {
            System.out.println("I Like Lamda2.");
        }
    }

    public static void main(String[] args) {
        ILike iLike=new Like();
        iLike.lamda();

        iLike=new Like2();
        iLike.lamda();

        //4.局部内部类
        class Like3 implements ILike{
            @Override
            public void lamda() {
                System.out.println("I Like Lamda3.");
            }
        }
        iLike=new Like3();
        iLike.lamda();


        //5.匿名内部类,没有类名，必须借助接口或者父类
        iLike=new ILike() {
            @Override
            public void lamda() {
                System.out.println("I Like Lamda4.");
            }
        };
        iLike.lamda();

        //6.用lamda简化
        iLike=()->{
            System.out.println("I Like Lamda5.");
        };
        iLike.lamda();
    }
}

//1.定义一个函数式接口
interface ILike {
    public void lamda();
}

//2.实现类
class Like implements ILike{
    @Override
    public void lamda() {
        System.out.println("I Like Lamda.");
    }
}
```

3、Lamda表达式简化

- lamda表达式只有在代码为一行的情况才能简化为一行，否则需要代码块包裹。
- 前提是接口为函数式接口。
- 多个参数可以去掉参数类型，要去掉就都去掉：(a,b)

```java
package com.dreamin.threadtest;

public class LamdaTest2{
    public static void main(String[] args) {
        ILove iLove=a-> System.out.println("I Love You "+a);
        iLove.love(1);

        ILove2 iLove2=(a,b)->{
            System.out.println("I Love You "+a);
            System.out.println("I Love You "+b);
        };
        iLove2.love(1,2);
    }

}

interface ILove{
    void love(int a);
}

interface ILove2{
    void love(int a,int b);
}

//class Love implements ILove{
//
//    @Override
//    public void love(int a) {
//        System.out.println("I Love You "+a);
//    }
//}

```

## 6、线程状态

线程五大状态：创建状态（New）、就绪状态（Runnable）、运行状态（Running）、阻塞状态（Blocked）、死亡状态（Dead）。

![](https://external-30160.picsz.qpic.cn/f704c3421d9a6d27382e5c263d8cfafd)

具体过程：

![](https://external-30160.picsz.qpic.cn/d0cb519a5b6ece923a71d90087379162)

- 创建状态：即单纯地创建一个线程，创建线程有三种方式。


- 就绪状态：在创建了线程之后，调用Thread类的start()方法来启动一个线程，即表示线程进入就绪状态。


- 运行状态：当线程获得CPU时间，线程才从就绪状态进入到运行状态。


- 阻塞状态：线程进入运行状态后，可能由于多种原因让线程进入阻塞状态，如：调用sleep()方法让线程睡眠，调用wait()方法让线程等待，调用join()方法、suspend()方法（它现已被弃用！）以及阻塞式IO方法。


- 死亡状态：run()方法的正常退出就让线程进入到死亡状态，还有当一个异常未被捕获而终止了run()方法的执行也将进入到死亡状态。

## 7、多线程常用方法

| 方法                          | 说明                                       |
| ----------------------------- | ------------------------------------------ |
| setPriority(int newPriority)  | 更改线程的优先级                           |
| void sleep(long milliseconds) | 让当前线程在指定的毫秒内休眠               |
| void join()                   | 等待该线程停止                             |
| static void yield()           | 暂停当前正在执行的线程对象，并执行其他线程 |
| void interrupt()              | 中断线程，不推荐用这个方式                 |
| Boolean isAlive()             | 测试线程是否处于活动状态                   |

### 7.1 停止线程

- 不推荐使用JDK提供的stop()、destroy()方法。【已废弃】

- 建议线程利用次数正常停止
- 建议使用一个标志位进行中止变量，当flag==false,则中止线程

```java
package com.dreamin.threadtest;

//测试线程停止
public class TestStop implements Runnable{
    private Boolean flag=true;
    @Override
    public void run() {
        int i=0;
        while (flag){
            System.out.println("run...Thread..."+i++);
        }
    }

    public void stop(){
        this.flag=false;
    }

    public static void main(String[] args) {
        TestStop testStop=new TestStop();
        new Thread(testStop).start();
        for (int i = 0; i < 1000; i++) {
            System.out.println("main"+i);
            if(i==900){
                //调用stop方法切换标志位，让线程停止
                testStop.stop();
                System.out.println("线程该停止了");
            }
        }
    }
}
```

### 7.2 线程休眠

- sleep(时间)指定当前线程阻塞的毫秒数；
- sleep时间达到后线程进入就绪状态；
- sleep可以模拟网络延时，倒计时等；
- 每一个对象都有一个锁，sleep不会释放锁。
- 线程休眠的是当前的线程

```java
package com.dreamin.threadtest;

import java.text.SimpleDateFormat;
import java.util.Date;

//模拟网络延时
public class SleepTest {

    public static void main(String[] args) {
        Date date=new Date(System.currentTimeMillis());
        while(true){
            try {
                date=new Date(System.currentTimeMillis());
                System.out.println(new SimpleDateFormat("YYYY-MM-DD HH:mm:ss").format(date));
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

}
```

### 7.3 线程礼让

- 礼让过程，让当前正在执行的线程暂停
- 让线程从运行状态转为就绪状态
- 让CPU重新调度，礼让不一定成功，看CPU调度。

```java
package com.dreamin.threadtest;

//测试线程礼让
public class TestYield{

    public static void main(String[] args) {
        MyYield myYield=new MyYield();
        new Thread(myYield,"线程1").start();
        new Thread(myYield,"线程2").start();
        new Thread(myYield,"线程3").start();
    }

}

class MyYield implements Runnable{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"线程开始执行");
        Thread.yield();
        System.out.println(Thread.currentThread().getName()+"线程停止执行");
    }

}
```

### 7.4 线程强制执行

```java
package com.dreamin.threadtest;

public class TestJoin implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 1000; i++) {
            System.out.println("线程vip来了"+i);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        TestJoin testJoin=new TestJoin();
        Thread thread=new Thread(testJoin);
        thread.start();

        //主线程
        for (int i = 0; i < 500; i++) {
            System.out.println("main"+i);
            if(i==200){
                thread.join();
            }
        }
    }

}
```

### 7.5 线程状态

Thread.State()方法底层代码：

```java
 public static enum State {
        NEW,
        RUNNABLE,
        BLOCKED,
        WAITING,
        TIMED_WAITING,
        TERMINATED;

        private State() {
        }
    }
```

```java
package com.dreamin.threadtest;

//观察测试线程的状态
public class StateTest {
    public static void main(String[] args) throws InterruptedException {
        Thread thread=new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("///////");
        });

        //观察状态
        Thread.State state=thread.getState();
        System.out.println(state);//NEW

        //观察启动后
        thread.start();
        state=thread.getState();
        System.out.println(state);//RUNNABLE

        //只要线程不中止，就一直输出状态
        while (state!=Thread.State.TERMINATED){
            Thread.sleep(100);
            state=thread.getState();//更新线程状态
            System.out.println(state);
        }
    }

}
```

### 7.6 线程优先级

- Java提供一个线程调度器来监控程序中启动后进入就绪状态的所有线程，线程调度按照优先级决定应该调度哪个线程来执行。

- 线程的优先级用数字表示，范围1~10，默认为5。

- 使用以下方式改变或获取优先级：

  getPriority()    setPriority(int priority)

- 优先级低只是意味着获得调度的概率低，并不是优先级低就不会先被调用，还是得看CPU调度。

```java
package com.dreamin.threadtest;

//测试线程优先级
public class PriorityTest {
    public static void main(String[] args) {
        //主线程默认优先级,不可更改
        System.out.println(Thread.currentThread().getPriority());
        MyPriority myPriority = new MyPriority();
        Thread t1 = new Thread(myPriority, "线程1");
        Thread t2 = new Thread(myPriority, "线程2");
        Thread t3 = new Thread(myPriority, "线程3");
        Thread t4 = new Thread(myPriority, "线程4");
        Thread t5 = new Thread(myPriority, "线程5");
        Thread t6 = new Thread(myPriority, "线程6");
        t1.setPriority(Thread.MIN_PRIORITY);
        t1.start();
        t2.setPriority(3);
        t2.start();
        t3.setPriority(5);
        t3.start();
        t4.setPriority(7);
        t4.start();
        t5.setPriority(8);
        t5.start();
        t6.setPriority(Thread.MAX_PRIORITY);
        t6.start();
    }
}

class MyPriority implements Runnable {

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + ":" + Thread.currentThread().getPriority());
    }
}
```

### 7.7 守护线程

- 线程分为用户线程和守护线程
- 虚拟机必须保证用户线程执行完毕
- 虚拟机不用等待守护线程执行完毕
- 守护线程如：后台记录操作日志、监控内存、垃圾回收等待等...

```java
package com.dreamin.threadtest;

//守护线程测试
//上帝守护
public class DaemonTest {
    public static void main(String[] args) {
        God god=new God();
        People you=new People();
        Thread thread=new Thread(god);
        thread.setDaemon(true);//默认是false，表示用户线程
        thread.start();
        new Thread(you).start();
    }

}

class God implements Runnable{

    @Override
    public void run() {
        while (true){
            System.out.println("God Bless You...");
        }
    }
}

class People implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 3650; i++) {
            System.out.println("得过且过又一天");
        }
        System.out.println("======升入天堂...======");
    }
}
```

## 8、线程同步

- 并发:同一个对象被多个线程同时操作

- 线程同步：synchronized 方法和 synchronized  块。

### 8.1 同步方法

- synchronized 方法控制对”对象“的访问，每个对象对应一把锁，每个synchronized 方法都必须获得调用该方法的对象的锁才能执行，否则线程会阻塞。方法一旦执行，就独占该锁，直到该方法返回才释放锁，后面被阻塞的线程才能获得这个锁，继续执行。
- synchronized 会降低性能和效率。
- 方法里需要有需要修改的内容才需要锁。

买票问题：（未同步可能产生负数票数）

```java
package com.dreamin.threadtest;

public class ThreadTest3 implements Runnable {

    private static int tickets = 20;
    boolean flag = true;

    @Override
    public  void run() {
        while (flag) {
            buy();
        }
    }

    public synchronized void buy() {
        if (tickets <= 0) {
            flag=false;
            return;
        }
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + tickets-- + "张票");
    }

    public static void main(String[] args) {
        ThreadTest3 threadTest3 = new ThreadTest3();
        new Thread(threadTest3, "小明").start();
        new Thread(threadTest3, "老师").start();
        new Thread(threadTest3, "黄牛").start();

    }
}
```

### 8.2 同步块

- synchronized (Obj) {}
- Obj称之为同步监视器
  -   Obj可以是任何对象，但推荐使用共享资源作为同步监视器
  - 同步方法中无需指定同步监视器，因为同步方法的同步监视器就是this，就是这个对象本身，或者是class
- 同步监视器执行过程
  - 1、第一个线程访问，锁定同步监视器，执行其中代码
  - 2、第二个线程访问，发现同步监视器被锁定，无法访问
  - 3、第一个线程访问完毕，解锁同步监视器
  - 4、第二个线程访问，发现同步监视器没有锁，然后锁定并访问

取钱问题：（不同步可能产生负数余额）

```java
package com.dreamin.threadtest;

public class ThreadTest3 implements Runnable {

    private static int tickets = 20;
    boolean flag = true;

    @Override
    public  void run() {
        while (flag) {
            buy();
        }
    }

    public synchronized void buy() {
        if (tickets <= 0) {
            flag=false;
            return;
        }
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + tickets-- + "张票");
    }

    public static void main(String[] args) {
        ThreadTest3 threadTest3 = new ThreadTest3();
        new Thread(threadTest3, "小明").start();
        new Thread(threadTest3, "老师").start();
        new Thread(threadTest3, "黄牛").start();

    }
}
```

线程集合问题：（不同步可能产生覆盖）

```java
package com.dreamin.threadtest;

import java.util.ArrayList;
import java.util.List;

//线程集合
public class MyList {

    public static void main(String[] args) {

        List<String> list=new ArrayList<String>();
        for (int i = 0; i < 10000; i++) {
            new Thread(()->{
                synchronized (list){
                    list.add(Thread.currentThread().getName());
                }
            }).start();
        }
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(list.size());

    }

}
```

### 8.3 死锁

- 死锁指多个线程因竞争资源而造成的一种僵局（互相等待），若无外力作用，这些进程都将无法向前推进。

```java
package com.dreamin.threadtest;

//死锁测试
public class DeadLock {
    public static void main(String[] args) {
        Makeup m1=new Makeup(0,"灰姑娘");
        Makeup m2=new Makeup(1,"白雪公主");
        new Thread(m1).start();
        new Thread(m2).start();
    }
}

//口红
class Lipstick{

}

//镜子
class Mirror{

}

class Makeup implements Runnable{

    //需要的资源只有一份，用static来保证只有一份
    static Lipstick lipstick=new Lipstick();
    static Mirror mirror=new Mirror();

    int choice;//选择
    String girlName;//使用化妆品人

    Makeup(int choice,String girlName){
        this.choice=choice;
        this.girlName=girlName;
    }



    @Override
    public void run() {
        //化妆
        try {
            makeup();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    //化妆，互相持有对方的锁，就是互相持有对方的资源
    private void makeup() throws InterruptedException {
        if(choice==0){
            synchronized (lipstick){
                System.out.println(this.girlName+"获得口红的锁");
                Thread.sleep(1000);
//                synchronized (mirror){
//                    System.out.println(this.girlName+"获得镜子的锁");
//                }
            }
            synchronized (mirror){
                System.out.println(this.girlName+"获得镜子的锁");
            }
        }else{
            synchronized (mirror){
                System.out.println(this.girlName+"获得镜子的锁");
                Thread.sleep(2000);
//                synchronized (lipstick){
//                    System.out.println(this.girlName+"获得口红的锁");
//                }
            }
            synchronized (lipstick){
                System.out.println(this.girlName+"获得口红的锁");
            }
        }
    }
}

```

### 8.4 Lock锁

- 在Java多线程中，可以使用synchronized关键字实现线程之间的同步互斥，在jdk1.5后新增的ReentrantLock类同样可达到此效果，且在使用上比synchronized更加灵活。
- Lock是显式锁，需要手动开启和关闭。
- 使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。

```java
package com.dreamin.threadtest;

import java.util.concurrent.locks.ReentrantLock;

//测试Lock锁
public class LockTest {
    public static void main(String[] args) {
        Tickets tickets=new Tickets();
        new Thread(tickets).start();
        new Thread(tickets).start();
        new Thread(tickets).start();
    }
}

class Tickets implements Runnable{

    int tickets=10;
    //定义lock锁
    private final ReentrantLock reentrantLock=new ReentrantLock();

    @Override
    public void run() {
        try {
            //加锁
        reentrantLock.lock();
            while(true){
                if(tickets>0){
                    try {
                        Thread.sleep(200);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName()+"获得了第"+tickets--+"张票");
                }else{
                    break;
                }
            }
        }finally {
            //解锁
            reentrantLock.unlock();
        }

    }
}
```

## 9、线程协作

### 9.1 生产者消费者问题

解决线程通信问题的方法：

| 方法名             | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| wait()             | 表示线程一直等待，直到其他线程通知，与sleep不同，会释放锁    |
| wait(long timeout) | 指定等待的毫秒数                                             |
| notify()           | 唤醒一个处于等待状态的线程                                   |
| notifyAll()        | 唤醒同一个对象上所有调用wait()方法的线程，优先级别高的线程优先调度 |

均是Object类的方法，都只能在同步方法或者同步代码块中使用，否则会抛出异常。

### 9.2 管程法

```java
package com.dreamin.threadtest;

//生产者消费者问题-->利用缓冲区解决：管程法
public class TestPC {
    public static void main(String[] args) {
        SynContainer synContainer=new SynContainer();
        Producer producer=new Producer(synContainer);
        Consumer consumer=new Consumer(synContainer);
        new Thread(producer).start();
        new Thread(consumer).start();
    }
}

//生产者
class Producer implements Runnable{

    SynContainer synContainer;

    public Producer(SynContainer synContainer) {
        this.synContainer = synContainer;
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("生产者生产了第"+i+"号产品");
            synContainer.push(new Product(i));
        }
    }
}

//消费者
class Consumer implements Runnable{

    SynContainer synContainer;

    public Consumer(SynContainer synContainer) {
        this.synContainer = synContainer;
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("消费者消费了"+(synContainer.pop().id+1)+"号产品");
        }
    }
}

//产品
class Product{
    int id;

    public Product(int id) {
        this.id = id;
    }
}

//缓冲区
class SynContainer{
    //容器大小
    Product[] products=new Product[10];
    //容器计数器，统计容器内产品数量
    int count=0;

    //生产者放入产品
    public synchronized void push(Product product){

        //如果容器已满,即count=10，就必须先等待消费者消费
        if(count==products.length){
            //通知消费者消费，生产者需要等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        //容器未满，生产者放入产品,即在容器中放入传过来的product,此时count计数+1
        products[count]=product;
        count=count+1;
        System.out.println("count："+count+"-----");

        //可以通知消费者消费了
        this.notifyAll();
    }

    //消费者取出产品
    public synchronized Product pop(){
        //如果容器为空，消费者就必须等待生产者生产
        if(count==0){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        //如果容器不为空，即可以消费，count--
        count--;

        //消费完成，通生产者生产
        this.notifyAll();

        //返回取出的产品信息
        Product product=products[count];
        return product;
    }
}

```

### 9.3 信号灯法

```java
package com.thread.gaoji;

//测试生产者消费者问题2：信号灯法，通过标志位解决

public class TestPC2 {
    public static void main(String[] args) {
        TV tv = new TV();
        new Player(tv).start();
        new Watcher(tv).start();
    }
}

//生产者-->演员
class Player extends Thread {
    TV tv;

    public Player(TV tv) {
        this.tv = tv;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            if (i % 2 == 0) {
                this.tv.play("快乐大本营播放中");
            } else {
                this.tv.play("抖音：记录美好生活");
            }
        }
    }
}

//消费者-->观众
class Watcher extends Thread {
    TV tv;

    public Watcher(TV tv) {
        this.tv = tv;
    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            tv.watch();
        }
    }
}

//产品-->节目
class TV {
    //演员表演，观众等待 T
    //观众观看，演员等待 F
    String voice; // 表演的节目
    boolean flag = true;


    //表演
    public synchronized void play(String voice) {

        if (!flag) {
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("演员表演了：" + voice);
        //通知观众观看
        this.notifyAll();
        this.voice = voice;
        this.flag = !this.flag;
    }

    //观看
    public synchronized void watch() {
        if (flag) {
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("观看了：" + voice);
        //通知演员表演
        this.notifyAll();
        this.flag = !this.flag;
    }
}
```

## 10、线程池

- 背景：经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大

- 思路：提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。可以避免频繁创建销毁、实现重复利用。类似生活中公共交通工具。

- 好处：

  - ​	提高响应速度(减少了创建线程的时间)

  - 降低资源的消耗(重复利用线程池中线程，不需要每次都创建)

  - 便于线程管理(...)

corePoolSize：核心池的大小

maximumPoolSize：最大线程数

keepAliveTime：线程没有任务时最多保持多长时间后会终止

1. JDK 5.0起提供了线程池相关API：ExecutorService 和 Executors
2. ExecutorService：真正的线程池接口。常见子类ThreadPoolExecutor

- void execute(Runnable command)：执行任务/命令，没有返回值，一 般用来执行Runnable
- <T>Future<T> submit(Callable<T> task)：执行任务，有返回值，一般用来执行Callable
- void shutdown()：关闭连接池

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

//测试线程池
public class TestPool {

    public static void main(String[] args) {
        //1.创建服务，创建线程池
        //newFixedThreadPool(10); 参数为线程池的大小
        ExecutorService service = Executors.newFixedThreadPool(10);

        //执行
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());

        //2.关闭连接
        service.shutdown();
    }
}

class MyThread implements Runnable {

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
    
}
```

