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

### synchronized 底层实现原理？

答：synchronized 同步代码块的实现是通过 monitorenter 和 monitorexit 指令，其中 monitorenter 指令指向同步代码块的开始位置，monitorexit 指令则指明同步代码块的结束位置。当执行 monitorenter 指令时，线程试图获取锁也就是获取 monitor(monitor对象存在于每个Java对象的对象头中， synchronized 锁便是通过这种方式获取锁的，也是为什么Java中任意对象可以作为锁的原因) 的持有权。 其内部包含一个计数器，当计数器为0则可以成功获取，获取后将锁计数器设为1也就是加1。相应的在 执行 monitorexit 指令后，将锁计数器设为0，表明锁被释放。如果获取对象锁失败，那当前线程就要阻塞等待，直到锁被另外一个线程释放为止

 synchronized 修饰的方法并没有 monitorenter 指令和 monitorexit 指令，取得代之的确实是 ACC_SYNCHRONIZED 标识，该标识指明了该方法是一个同步方法，JVM 通过该 ACC_SYNCHRONIZED 访问标志来辨别一个方法是否声明为同步方法，从而执行相应的同步调用。

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

synchronized版 wait nofity:synchronized版本，这次用juc版

等待，业务，通知

> ​	生产者与消费者问题Synchronized

下次一定

### 5.8锁的现象

- 同步代码块（synchronized(this)，synchronized(类实例对象)，锁是小括号()中的实例对象）
- 同步非静态方法（synchronized method），锁的是当前对象的实例对象

获取类锁

- 同步代码块（synchronized(类.class)），锁是最小括号 () 中的类对象（Class对象）
- 同步静态方法（synchronized static method），锁是当前对象的类对象（Class 对象）

总结

- 有线程访问对象的同步代码块时，另外的线程可以访问该对象的非同步代码块
- 若锁住的是同一个对象，一个线程在访问对象的同步代码块时，另一个访问对象的同步代码块的线程会被阻塞。
- 若锁住的是同一个对象，一个线程在访问对象的同步方法时，另一个访问对象的同步方法的线程会被阻塞。
- 若锁住的是同一个对象，一个线程在访问对象的同步代码块时，另一个访问对象同步方法的线程会被阻塞，反之亦然。
- 同一个类的不同对象的锁互不干扰
- 类锁由于也是一种特殊的对象锁，因此表现和上述一致，而由于一个类只有一把对象锁，所以同一个类的不同对象使用类锁将会是同步的
- 类锁和对象锁互不干扰

其实就是加深一下到底锁的是什么

```java
/**
 * @Description: 8锁，就是锁的8个问题
 *                 1、标准情况下，两个线程先后
 *                 当然并不是先调用就先执行，而是由于锁的存在
 *                 由于一个对象只有一个锁，所以谁先拿到锁，谁先执行
 *
 * @author: LGD
 * @date:2022/6/23 14:05
 */
public class Test1 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        new Thread(()->{
            phone.sendSms();
        },"A").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        }catch (InterruptedException e){
            e.printStackTrace();
        }

        new Thread(()->{
            phone.call();
        },"B").start();
    }

}

class Phone{
    //被synchronized修饰的对象是锁的调用者
    //现在两个方法用的是同一把锁(一个对象只可以有一把锁)，谁先拿到，谁执行
    public  synchronized void sendSms(){
        try {
            TimeUnit.SECONDS.sleep(4);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("sendSms");
    }

    public  synchronized void call(){
        System.out.println("call");
    }
}

/**
 * @Description: 3.增加一个普通void先执行哪个？
 *                  这里没有锁，就不是同步方法，不受锁的影响
 *                  4.两个对象，两把锁，都被两位拿到，所以都可以执行
 *                  所以synchronized锁的是this.Class这个对象
 *
 * @author: LGD
 * @date:2022/6/23 14:05
 */
class Phone2{
    //被synchronized修饰的对象是锁的调用者
    //现在两个方法用的是同一把锁(一个对象只可以有一把锁)，谁先拿到，谁执行
    public  synchronized void sendSms(){
        try {
            TimeUnit.SECONDS.sleep(4);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("sendSms");
    }

    public  synchronized void call(){
        System.out.println("call");
    }

    //这里没有锁，就不是同步方法，不受锁的影响
    public void hello(){
        System.out.println("hello");
    }
}

/**
 * @Description: 5.两个静态同步方法，只有一个对象
 *              6.两个对象，调用一个类的两个static synchronized方法
 *                  那么此时，类在ClassLoader之后得到一个全局唯一的一个Class模板
 *                  之后synchronized所的就是这个Class类模板
 *
 * @author: LGD
 * @date:2022/6/23 14:05
 */

class Phone3{
    //被synchronized修饰的对象是锁的调用者
    //现在两个方法用的是同一把锁(一个对象只可以有一把锁)，谁先拿到，谁执行
    //static 静态方法，在类加载的时候就有的了，Class模板
    //而phone3唯一的一个class对象，此时锁的就是class模板
    public static  synchronized void sendSms(){
        try {
            TimeUnit.SECONDS.sleep(4);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("sendSms");
    }

    public static synchronized void call(){
        System.out.println("call");
    }
}

/**
 * @Description: 7.一个对象，两个同步方法，一个静态一个普通
 *                 这次两个方法锁的不是一个，上面static锁的是class模板
 *                 而普通synchornized锁的是当前类对象
 * @author: LGD
 * @date:2022/6/23 14:05
 */

class Phone4{
    //静态同步锁的是Class类模板
    public static synchronized void sendSms(){
        try {
            TimeUnit.SECONDS.sleep(4);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("sendSms");
    }
    //普通同步锁的是当前类对象this.class
    public synchronized void call(){
        System.out.println("call");
    }

    public void hello(){
        System.out.println("hello");
    }
}
```

> 综合一下：

- 两个synchronized 锁当前对象
- 一个synchronized ，一个普通void,void不受影响
- 两个static synchronized,锁Class模板
- 一个synchronized,一个static synchronized锁的东西并不一样，一个类模板，一个当前对象，所以互不影响

new 出来 this 是一个具体的对象

static ClassLoader得到的一个全局唯一的类模板

### 6.集合类不安全

在集合类，并发环境下进行修改就会出现

java.util.ConcurrentModificationException

面试问你一些经典异常，欸，为了体现咱们确实是干过活的，所以可以这么说

比如**OutOfMemory**:堆内存溢出，一般是new了太多东西，或者两个过大的集合类进行操作的时候会出现

**StackOverFlow**:栈溢出，这个也挺经典，因为我在JDBC学习的时候碰到过preparement的sql里只有3个？，而你setXXX了4个就会出现。

还有这个**ConcurrentModificationException**，并发修改，在多线程

List	add()最早1.2，Vector是1.0时候就有了，懂吧，我记得为什么由Vector(add 里面有synchronized)到ArrayList的原因，就是synchronized同步慢了。

```java
//并发下ArrayList并不安全，所以需要解决方案
        //1.老集合就是Vector
        //2.使用Collections.synchronizedList(new ArrayList<>())
        //3.JUC包下ConCurrentXXX(并发的xxx)
//        List<String> strings = Collections.synchronizedList(new ArrayList<>());
        List<String> strings = new CopyOnWriteArrayList<>();
```

为什么JUC下的CopyOnWriteArrayList可以保证并发呢

```java
/** The array, accessed only via getArray/setArray. */
    private transient volatile Object[] array;
//点进CopyOnWriteArrayList，可以发现它通过transient跟volatile实现
```

##### CopyOnWriteArrayList(写入时复制)

SE里面我有写，那再来一遍

**transient** 

当串行化某个对象时，如果该对象的某个变量是transient，那么这个变量不会被串行化进去。也就是说，假设某个类的成员变量是transient，那么当通过

ObjectOutputStream把这个类的某个实例

保存到磁盘上时，实际上transient变量的值是不会保存的。因为当从磁盘中读出这个对象的时候，对象的该变量会没有被赋值。

**volatile (那必然要提到JMM了)** 

JMM是java内存模型，抽象为一个主内存存储共享变量，其他每个线程会独占一部分本地内存，线程里面会有所需使用的主内存的共享变量的副本，然后线程在自己的区域内操作副本，当然这可能会导致在线程还没有把自己改变完的变量写入到主内存时候，然后其他线程不知道情况下使用了主内存老的数据的副本进行缓存，使用volatile可以保证单个共享变量读写可见性。

同synchronized相比（synchronized通常称为重量级锁），volatile更轻量级

它可以保证在线程自己的内存里对变量进行写入操作后可以强制在主内存进行刷新，让其他线程的缓存失效。

它也禁止指令重排序

它只能保证单个共享变量读写原子性，并不能保证num++这种复合操作。复合操作需要用到并发包里的原子操作类JUC，通过循环CAS方式保证原子性。

```java
public static AtomicInteger num = new AtomicInteger(0);
```

//CopyOnWriteArrayList(写入时复制) 	COW计算机程序设计领域的一种优化策略，多个线程调用的时候,list,读取的时候，

多个线程调用时，

//读写分离	写入的时候复制一个数组，写入完再进行插入 保证线程安全

//为什么用CopyOnWriteArrayList 而不怎么用Vector

**Vector**用了synchronized的方式实现，相对效率较低

```java
/**
 * Sets the size of this vector. If the new size is greater than the
 * current size, new {@code null} items are added to the end of
 * the vector. If the new size is less than the current size, all
 * components at index {@code newSize} and greater are discarded.
 *
 * @param  newSize   the new size of this vector
 * @throws ArrayIndexOutOfBoundsException if the new size is negative
 */
public synchronized void setSize(int newSize) {
    modCount++;
    if (newSize > elementCount) {
        ensureCapacityHelper(newSize);
    } else {
        for (int i = newSize ; i < elementCount ; i++) {
            elementData[i] = null;
        }
    }
    elementCount = newSize;
}
```

而**CopyOnWriteArrayList** 用的lock锁，效率相对高，如下，先复制copyof，再写入setArray

```java
 /** * Appends the specified element to the end of this list. * * @param e element to be appended to this list * @return {@code true} (as specified by {@link Collection#add}) */public boolean add(E e) {    final ReentrantLock lock = this.lock;    lock.lock();    try {        Object[] elements = getArray();        int len = elements.length;        Object[] newElements = Arrays.copyOf(elements, len + 1);        newElements[len] = e;        setArray(newElements);        return true;    } finally {        lock.unlock();    } [ListTest.java](E:\IDEA\tests\Small-Tests\stream-test\src\main\java\com\lgd\juc\unsafe\ListTest.java) }
```

##### CopyOnWriteArraySet

欸，有list,有Set，那肯定还有map。

Set跟List，BlockingQuere(阻塞队列)都继承自Collection

Set无序不可重复

HashSet底层就是HashMap;

add其本质就是

```java
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```

使用 Collections.synchronizedSet 方法来“包装” set

再来补补短板

Object中equals和== 比较的都是地址，但是我们希望equals比较值，以值来区分对象是不是一个，所以我们重写了equals并进行比较值，hashCode此时也需要重写，不然会出现地址不同，但是equals却为true,而java规定，**两个内容相同(equals()为true)的对象必须具有相等的hashCode**。不然在整个存储过程中就发生了悖论，然后是hashCode重写后，这样地址和值都一样了。比如Integer，它的hashCode就是直接int的value，equals就直接比较value是否一样。一般我们自己new了一个对象可以通过各属性得到一个hashCode，然后在equals时先比较地址，hash值是否一样，一样的话直接为true增加效率，false再慢慢一个个比值。

lombok的@Data帮我们简化了new一个对象后，需要重写hashCode和equals，toString（本来是输出一个对象的十六进制化的内存地址，现在我们希望得到它的属性详细信息）。@Data相当于@Getter @Setter @RequiredArgsConstructor @ToString @EqualsAndHashCode这5个注解的合集, 和@EqualsAndHashCode默认是false(就是只用自己的属性来生成hashcode)。为true的话就是用自己的属性和从父类继承的属性来生成hashcode.

这里注意一下：比如一个父类，一个子类都有很多属性，然后都有@Data，此时equals比较的是子类的属性，如果都一样就都一样，这里我们可以通过@EqualsAndHashCode设置为true来解决

##### ConcurrentHashMap

HashMap首先初始化容量16(1 << 4位运算快)

加载因子0.75(默认)，= =忘了，回头SE去看看

Map不安全。

Collections.synchronizedMap(new HashMap)

new ConcurrentHashMap();



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