---
typora-root-url: photo
typora-copy-images-to: photo
---

# JUC

前言：面试收官~全过~，(要是寒假的时候就狠狠心开始面就更好了)，也算是对从去年暑假开始一路学习之旅考验的一个肯定和结果吧。

体会来说，对于实习生问的主要还是以**Java基础(SE,集合，多线程)与数据结构(栈，链表，二叉树)，简单排序算法，Spring，Mysql及sql语句编写**为主，

= =，实习也真是折腾啊，不过住房，做抉择，时间安排之类也是迟早的，那么开始补JUC了~(面试问到**synchronized,jdk8新特性之stream流，Callable为什么可以有返回值，可重入锁**之类面试里问到了，不过我还没学~),And,之前用Typora,图片都直接是本地资源里没有放到同文件夹下的photo文件夹里(巨蠢，以后知道了)

多线程进阶=>JUC并发编程

### 1.什么是JUC

源码+官方文档，jdk1.8文档早下好啦~

- java.util.concurrent
- java.util.concurrent.atomic
- java.util.concurrent.locks
- java.util.concurrent.function

![image-20220417150117418](/image-20220417150117418.png)

Runnable: 没有返回值，效率相对callable低，企业里还是callable多= =，ReentranLock，就叫可重入锁= =

### 2.进程和线程

>线程，进程

进程：一个程序，一个进程包含多个线程，操作系统分配的最小单位，java默认有2个线程 main线程+gc线程

线程：是cpu调度的最小单位

java:  Thread,Runnable,Callable

java当然做不了线程级别操作，需要用native修饰的start0，也就是调用了本地方法(C,C++写的)

```java
public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }

	private native void start0();
```

>并发，并行

并行：多个人一起走，多核，多个线程同时执行

并发：多个线程操作一个资源，CPU一核，模拟出多条线程，快速切换交替

```java
public static void main(String[] args) {
        System.out.println(Runtime.getRuntime().availableProcessors());//获取cpu
    }

//16(核心)
```

并发编程本质初衷就是为了：**充分利用CPU资源**

>线程有几个状态

- NEW(新生)
- RUNNABLE(运行时)
- BLOCKED（阻塞）
- WATTING(死等)
- TIMED_WATTING(超时等待)
- TERMINATED(终止)

>wait/sleep

1. **来自不同类**

   wait=>object

   sleep=>Thread

   企业中用TimeUnit.DAYS.sleep();

   ```java
   long l = System.currentTimeMillis();
           try {
               TimeUnit.SECONDS.sleep(10);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           long l1 = System.currentTimeMillis();
           System.out.println(l1-l);
   
   //10013差不多就是10s
   ```

2. **关于锁的释放**

   wait会释放锁，sleep不释放资源抱着锁睡

3. **使用范围不同**

   wait只能在同步代码块中用

   sleep可以在任何地方使用，因为在Object下

4. wait ——InterruptedException,需要被nofity()/notifyAll(),唤醒

   sleep 必须要捕获异常

### 3.Lock锁(重点)

>传统Synchronized

线程就是一个单独的资源类，没有任何附属的操作

```java
public class Ticket_Test {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();
//        ExecutorService service = Executors.newFixedThreadPool(5);
//        service.execute(ticket);
//        service.execute(ticket);
//        service.execute(ticket);
//        service.shutdown();
        new Thread(() -> {
            for (int i = 0; i < 50; i++) {
                ticket.buy();
            }
        },"小明").start();
        new Thread(() -> {
            for (int i = 0; i < 50; i++) {
                ticket.buy();
            }
        },"小红").start();
        new Thread(() -> {
            for (int i = 0; i < 50; i++) {
                ticket.buy();
            }
        },"小兰").start();
    }
}
class Ticket {
    private int tickets = 100;

    public synchronized void buy(){
        if (tickets > 0) {
            System.out.println(Thread.currentThread().getName() + "拿到了最后第" + tickets-- + "张票");
        }
    }
}
```

synchronized本质是排队，锁住对象跟Class,但是如果锁住多个代码块后再解锁需要反向释放。并不灵活。

`Lock`实现提供比使用`synchronized`方法和语句可以获得的更广泛的锁定操作。  它们允许更灵活的结构化，可能具有完全不同的属性，并且可以支持多个相关联的对象Condition  。 

没有块结构化锁定会删除使用`synchronized`方法和语句发生的锁的自动释放。  在大多数情况下，应使用以下惯用语： 

```java
Lock l = ...; l.lock(); try { // access the resource protected by this lock 
}
    finally { l.unlock(); }
```

常用(ReentranLock)可重入锁

- java.util.concurrent.locks.ReentrantReadWriteLock.ReadLock 读锁
- java.util.concurrent.locks.ReentrantReadWriteLock.WriteLock 写锁

NofairSync——非公平锁，可以插队(默认)，涉及cpu调度，耗时少的优先

FairSync——公平锁，十分公平先来后到

```java
class Ticket2 {
    private int tickets = 100;
    private Lock lock = new ReentrantLock();

    public void buy(){
        lock.lock();
        try{//放业务代码
            if (tickets > 0) {
                System.out.println(Thread.currentThread().getName() + "拿到了最后第" + tickets-- + "张票");
            }
        }catch (Exception e){
            e.printStackTrace();
            System.out.println("好像出现了异常捏");
        }finally {
         lock.unlock();
        }
    }
}
```

主要就是先lock();

然后try里放业务代码，

finally解锁unlock()

```java
/**
 * Creates an instance of {@code ReentrantLock}.
 * This is equivalent to using {@code ReentrantLock(false)}.
 */
public ReentrantLock() {
    sync = new NonfairSync();
}

/**
 * Creates an instance of {@code ReentrantLock} with the
 * given fairness policy.
 *
 * @param fair {@code true} if this lock should use a fair ordering policy
 */
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

那么

>synchronized 与 ReentranLock

1. synchronized为java内置关键字，Lock为java类

2. synchronized无法判断锁的状态，Lock可以判断是否获取到了锁

3. synchronized会自动释放锁，隐式的，lock必须要手动释放锁lock() and unlock();如果不释放锁就会出现死锁，相对灵活性也高

4. synchronized 线程1(获得锁，如果阻塞)，线程2(一直等待)；

   lock锁就不一定会等待下去了，因为其lock.tryLock();

5. synchronized 为可重入锁(一个线程已经获得了某个锁，可以再次获取锁而不出现死锁，当然释放也要释放多次)，不可以中断，非公平，Lock是一个类，可重入的，可以判断锁，非公平(可以自己设置)源码通过ReentrantLock(boolean fair)

6. synchronized 适合锁少量代码同步，Lock适合锁大量的同步代码

那么

>锁是什么，怎么看锁的是谁？



### 4.生产者和消费者

synchronized wait nofity:synchronized版本，这次用juc版

等待，业务，通知

### 5.8锁的现象

### 6.集合类不安全

### 7.Callable

### 8.CountDownLatch,CyclicBarrier,Semaphore

### 9.读写锁

### 10.阻塞队列

### 11.线程池

### 12.四大函数式接口

### 13.Stream流式计算

### 14.分支合并

### 15.异步回调

### 16.JMM

### 17.volatile

### 18.深入单例模式

### 19.深入理解CAS

### 20.源自引用

### 21.可重入锁，公平锁，非公平锁，自旋锁，死锁...