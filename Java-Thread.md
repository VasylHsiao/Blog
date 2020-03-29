  * [JAVA线程的实现方式及常用方法](#java线程的实现方式及常用方法)
      * [1.继承Thread类创建线程类](#1继承thread类创建线程类)
      * [2.通过实现Runnable接口创建线程类](#2通过实现runnable接口创建线程类)
      * [3.通过实现Callable创建线程](#3通过实现callable创建线程)
      * [4.运用Executor框架来创建线程池](#4运用executor框架来创建线程池)
      * [5.常用方法](#5常用方法)
   * [进程状态及转变](#进程状态及转变)
      * [1. 新建( new )](#1-新建-new-)
      * [2. 可运行( runnable )](#2-可运行-runnable-)
      * [3. 运行( running )](#3-运行-running-)
      * [4. 阻塞( block )](#4-阻塞-block-)
      * [5. 死亡( dead )](#5-死亡-dead-)
      * [重要注意！！！](#重要注意)
   * [线程同步与线程安全](#线程同步与线程安全)
      * [线程同步](#线程同步)
         * [同步实现方式1：](#同步实现方式1)
         * [同步实现方式2：](#同步实现方式2)
         * [同步实现方式3：](#同步实现方式3)

# JAVA线程的实现方式及常用方法
1.继承Thread类，重写run方法；
2.实现Runnable接口，重写run方法，但是比继承Thread类好用，实现接口还可以继承类，避免了单继承带来的局限性；
3.实现callable接口，重写call方法，有返回值，与run相比可抛出异常；
4.使用实现了Executor框架来创建线程池。

## 1.继承Thread类创建线程类
（1）定义Thread类的子类，并重写该类的run方法，该run方法的方法体就代表了线程要完成的任务。因此把run()方法称为执行体。
（2）创建Thread子类的实例，即创建了线程对象。
（3）调用线程对象的start()方法来启动该线程。
```java
//通过继承Thread类创建线程

public class MyThreadOne extends Thread {
    <a href="/profile/992988" data-card-uid="992988" class="js-nc-card" target="_blank">@Override
    public void run() {
        for(int i = 0; i < 100; i++){
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
    }
    public static void main(String[] args){
        new MyThreadOne().start();
        new MyThreadOne().start();
    }
}
```
## 2.通过实现Runnable接口创建线程类
（1）定义runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。
（2）创建 Runnable实现类的实例（注意 ，这里声明的是实现类，构造方法也是实现类的构造方法），并依此实例作为Thread构造方法的参数来创建Thread对象，该Thread对象才是真正的线程对象。
（3）调用线程对象的start()方法来启动该线程。
```java
// 通过实现Runnable接口创建线程

public class MyThreadTwo implements Runnable{
    <a href="/profile/992988" data-card-uid="992988" class="js-nc-card" target="_blank">@Override
    public void run() {
        for(int i = 0; i < 100; i++){
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
    }
    public static void main(String[] args){
        MyThreadTwo myThreadTwo = new MyThreadTwo();
        Thread th1 = new Thread(myThreadTwo, "thread-1");
        Thread th2 = new Thread(myThreadTwo, "thread-2");
        th1.start();
        th2.start();
    }
}
```
## 3.通过实现Callable创建线程
（1）创建Callable接口的实现类，并实现call()方法，该call()方法将作为线程执行体，并且有返回值。
（2）创建Callable实现类的实例（注意 ，这里声明的是实现类，构造方法也是实现类的构造方法），并依此实例作为FutureTask构造方法的参数来创建FutureTask对象，同时该FutureTask对象封装了该Callable对象的call()方法的返回值。
（3）使用FutureTask对象（需要泛型<V>）作为Thread构造方法（需要泛型<V>）的参数来创建Thread对象，该Thread对象才是真正的线程对象。
（4）调用线程对象的start()方法来启动该线程。
（5）调用FutureTask对象的get()方法来获得子线程执行结束后的返回值
```java
/*
 * 通过实现Callable接口创建线程
 * 1】创建Callable接口的实现类，并实现call()方法，然后创建该实现类的实例
 * 2】使用FutureTask类来包装Callable对象，该FutureTask对象封装了Callable对象的call()方法的返回值
 * 3】使用FutureTask对象作为Thread对象的target创建并启动线程（因为FutureTask实现了Runnable接口）
 * 4】调用FutureTask对象的get()方法来获得子线程执行结束后的返回值
 */
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class MyThreadThree implements Callable<Integer> {
    public static void main(String[] args) {
        MyThreadThree myThreadThree = new MyThreadThree();
        FutureTask<Integer> ft = new FutureTask<>(myThreadThree);
        FutureTask<Integer> ft1 = new FutureTask<>(myThreadThree);
        System.out.println(Thread.currentThread().getName() + "===== ");
 
        new Thread(ft, "thread-1").start();
        new Thread(ft1, "thread-2").start();
        try {
            System.out.println("return thread-1'result ：" + ft.get());
            System.out.println("return thread-2'result ：" + ft1.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
 
    <a href="/profile/992988" data-card-uid="992988" class="js-nc-card" target="_blank">@Override
    public Integer call() throws Exception {
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
        return 20;
    }
}
```

	1.Callable的call()方法类似于Runnable接口中run()方法，都定义任务要完成的工作，实现这两个接口时要分别重写这两个方法，主要的不同之处是call()方法是有返回值的，而且call()方法可以抛出异常，run方法不可以。
	2.运行Callable对象（实现类，也可以叫任务）可以拿到一个Future对象（注意future只是个泛型接口，我们用的是他的实现类FutureTask），表示异步计算的结果。FutureTask类同时实现了两个接口，Future和Runnable接口，所以它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值。

第三种用callable和future的方法我用的比较少，只是了解，最常用还是第二种方法，相对于第一种方法，使用**runnable接口的优点**在于：
1.由于java单继承多实现的特点，实现了runnable接口的同时，我还可以继承其他父类；
2.提高了代码的复用性，在这种方式下，多个线程可以共享一个runnable对象，所以非常适合多个相同线程来处理同一份资源的情况。

但是也有**缺点**：
1.Thread子类获取线程时，用this.即可，而runnable接口则需要Thread.currentThread（）这个静态方法。
## 4.运用Executor框架来创建线程池

```java
//Executor执行Runnable任务

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class MyThreadFour implements Runnable {
    <a href="/profile/992988" data-card-uid="992988" class="js-nc-card" target="_blank">@Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " is called");
    }
    public static void main(String[] args){
        ExecutorService executorService = Executors.newCachedThreadPool();
        for(int i = 0; i < 5; i++){
            executorService.execute(new MyThreadFour());
        }
        executorService.shutdown();
    }
}
```
```java
//Executor执行Callable任务

import java.util.ArrayList;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class MyThreadFive {
 
    // 业务线程
    private class AThread implements Callable<String> {
        private int id;
        public AThread(int id){
            this.id = id;
        }
        <a href="/profile/992988" data-card-uid="992988" class="js-nc-card" target="_blank">@Override
        public String call() {
            System.out.println("线程:" + id + " 运行..");
            try {
                Thread.sleep(3000);
            } catch (Exception e) {
                e.printStackTrace();
            }
 
            System.out.println("线程:" + id + " -> 结束.");
            return "返回的字符串" + id;
        }
    }
 
    public void ExecutorService() {
        ExecutorService pool = Executors.newFixedThreadPool(4);
        ArrayList<Future<String>> futures = new ArrayList<>();
 
        for (int i = 0; i < 10; i++) {
            AThread t = new AThread(i);
            // Runnable -> execute()
            // Callable -> submit()
            Future<String> f = pool.submit(t);
            futures.add(f);
        }
        System.out.println("....开始获取结果中...");
        // Future的get方法在获取结果时候将进入阻塞，阻塞直到Callable中的call返回。
        for (Future<String> f : futures) {
            try {
                System.out.println(f.get());
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        System.out.println(".....结束得到结果....");
        pool.shutdown();
    }
    public static void main(String[] args) {
        new MyThreadFive().ExecutorService();
    }
}
```
## 5.常用方法
 1.start()：启动线程并执行相应的run()方法
 2.run():子线程要执行的代码放入run()方法中
 3.currentThread()：静态的，调取当前的线程
 4.getName():获取此线程的名字
 5.setName():设置此线程的名字
 6.yield():调用此方法的线程释放当前CPU的执行权（很可能自己再次抢到资源）
 7.join():在A线程中调用B线程的join()方法，表示：当执行到此方法，A线程停止执行，直至B线程执行完毕，A线程再接着join()之后的代码执行
 8.isAlive():判断当前线程是否还存活
 9.sleep(long l):显式的让当前线程睡眠l毫秒  (只能捕获异常，因为父类run方法没有抛异常)
10.线程通信（方法在Object类中）：wait()   notify()  notifyAll()
11.getPriority()：返回线程优先值
12.setPriority(int newPriority)：改变线程的优先级

# 进程状态及转变
![进程转化示意图](https://img-blog.csdnimg.cn/20200324234258419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NDU2MDU3,size_16,color_FFFFFF,t_70#pic_center)
## 1. 新建( new )
新创建了一个线程对象。
## 2. 可运行( runnable )
线程对象创建后，其他线程(比如 main 线程）调用了该对象 的 start ()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获 取 cpu 的使用权 。
## 3. 运行( running )
可运行状态( runnable )的线程获得了 cpu 时间片（ timeslice ） ，执行程序代码。
## 4. 阻塞( block )
阻塞状态是指线程因为某种原因放弃了 cpu 使用权，也即让出了 cpu timeslice ，暂时停止运行。直到线程进入可运行( runnable )状态，才有 机会再次获得 cpu timeslice 转到运行( running )状态。阻塞的情况分三种：
(1). 等待阻塞：运行( running )的线程执行 o . wait ()方法， JVM 会把该线程放 入等待队列( waitting queue )中。
(2). 同步阻塞：运行( running )的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则 JVM 会把该线程放入锁池( lock pool )中。
(3). 其他阻塞: 运行( running )的线程执行 Thread . sleep ( long ms )或 t . join ()方法，或者发出了 I / O 请求时， JVM 会把该线程置为阻塞状态。            当 sleep ()状态超时、 join ()等待线程终止或者超时、或者 I / O 处理完毕时，线程重新转入可运行( runnable )状态。
## 5. 死亡( dead )
线程 run ()、 main () 方法执行结束，或者因异常退出了 run ()方法，则该线程结束生命周期。死亡的线程不可再次复生。


## 重要注意！！！

1.sleep和yield的区别：sleep可以使优先级低的线程得到执行的机会,  而yield只能使同优先级的线程有执行的机会.

2.**wait()与sleep()的区别**

(1) sleep()方法正在执行的线程主动让出CPU（然后CPU就可以去执行其他任务），在sleep指定时间后CPU再回到该线程继续往下执行(注意：sleep方法只让出了CPU，而并不会释放同步资源锁！！！)；wait()方法则是指当前线程让自己暂时退让出同步资源锁，以便其他正在等待该资源的线程得到该资源进而运行，只有调用了notify()方法，之前调用wait()的线程才会解除wait状态，可以去参与竞争同步资源锁，进而得到执行；
(2) sleep()是线程线程类（Thread）的方法，调用会暂停此线程指定的时间，但监控依然保持，不会释放对象锁，到时间自动恢复；wait()是Object的方法，调用会放弃对象锁，进入等待队列，待调用notify()/notifyAll()唤醒指定的线程或者所有线程，才会进入锁池，不再次获得对象锁才会进入运行状态；
(3)sleep()方法可以在任何地方使用；waite()和notify()必须在synchronized函数或synchronized block中进行调用。如果在non-synchronized函数或non-synchronized　block中进行调用，虽然能编译通过，但在运行时会发生IllegalMonitorStateException的异常。

3.**interrupt()不会中断一个正在运行的线程**，它做的**仅仅是要改变该线程的中断状态标志（布尔类型变量）由False变为True，引起线程注意，至于后面由线程自己决定应对处理**。
线程如果正在运行的过程中, 去调用此方法仅仅是将中断状态标志置为True，线程的运行状态和具体情况由线程自己应对处理，不会受到直接的影响；
线程如果被阻塞（即当线程调用了Object.wait(), th.join(), th.sleep()等），再调用interrupt方法，**没有占用CPU运行的线程是不可能给自己的中断状态置位的，这就会产生一个InterruptedException异常，使得线程得以退出阻塞状态**，这时中断状态为False，不会改变。

# 线程同步与线程安全
参考：
https://blog.csdn.net/qq_38872310/article/details/79922848?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task

## 线程同步
**线程同步：在多线程下，多线程共享临界资源，通过合理安排各线程对共享资源的竞争（互斥）访问，保证数据完整性**。

线程安全：在多线程下，采用加锁机制，当一个线程访问数据时，进行保护，……
线程不安全：不提供数据保护，产生脏数据……

### 同步实现方式1：synchronized

### 同步实现方式2：ReentrantLock

参考：https://blog.csdn.net/xlgen157387/article/details/78197583

### 同步实现方式3：


# 线程通信
参考：
https://blog.csdn.net/qq_38872310/article/details/79922848?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task

## wait()/notify()/notifyall()
## await()/signal()/signalall()

