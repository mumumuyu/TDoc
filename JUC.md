---
typora-root-url: photo
typora-copy-images-to: photo
---

# JUC

前言：面试收官~全过~2022/4/21，(要是寒假的时候就狠狠心开始面就更好了)，也算是对从去年暑假开始一路学习之旅考验的一个肯定和结果吧。

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

##### Key 和 Value 不支持 null 的原因？

我们先说 为何 value 不能为 null。因为 ConcurrentHashMap 是用于多线程的，如果 get(key) 方法到了 null，就无法判断存在的 value 是 null，还是没有找到对应的 key 而为 null，就有了二义性。

### 7.Callable

类似于Runnable，Runnable不返回结果也不能抛出被检查的异常

而Callabel<V>可以抛出异常，可以有返回值<V>泛型参数就是方法返回值类型

FutureTask实现了Runnable接口的子接口RunnableFuture

```java
public class CallabelTest {
    public static void main(String[] args) {
        //Callable,FutureTask实现了Runnable接口的子接口RunnableFuture
//        RunnableFuture;
//        FutureTask;
        MyThread myThread = new MyThread();
        //适配类 FutrueTask
        FutureTask futureTask = new FutureTask(myThread);

        new Thread(futureTask,"threadA").start();

        try {
            String result = (String) futureTask.get();
            System.out.println(result);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
class MyThread implements Callable<String>{

    @Override
    public String call() throws Exception {
        System.out.println("call");
        return "success";
    }
}
```

执行完可以拿到返回结果,那为什么我们需要这个结果呢，因为我们需要这个方法执行后的一些参数或者出现的异常。

由于这个get()方法可能产生阻塞，所以需要try,catch(InterruptedException)

### 8.常用辅助类

##### CountDownLatch

```java
public static void main(String[] args) {
    CountDownLatch countDownLatch = new CountDownLatch(8);

    for (int i = 1; i < 9; i++) {
        new Thread(()->{
            countDownLatch.countDown();
            System.out.println(Thread.currentThread().getName() + "go");
        },String.valueOf(i)).start();
    }

    try {
        countDownLatch.await();//等待计数器归零,说明上述所有线程都执行完毕,再执行后面的操作
    } catch (InterruptedException e) {
        e.printStackTrace();
    }

    System.out.println("Close door");
}
```

countDowLatch.countDown();数量-1

countDownLatch.await();//等待计数器归零后进行唤醒，程序继续执行

##### CyclicBarrier

```java
CyclicBarrier cyclicBarrier = new CyclicBarrier(9,()->{
    System.out.println("成功升至九五至尊");
});

for (int i = 1; i < 10; i++) {
    new Thread(()->{
        System.out.println(Thread.currentThread().getName()+"个");
        try {
            cyclicBarrier.await();//等待
        } catch (InterruptedException | BrokenBarrierException e) {
            e.printStackTrace();
        }
    },String.valueOf(i)).start();
}
```

指定个数线程执行完毕后再执行操作

加法计数器

##### Semaphore

用途：多个共享资源互斥使用，限流

同一时间最多有X个数量得到线程;acquire

信号量：

- 当n>0时表示还有n个可用进程
- n=0表示可用进程为0
- n<0表示资源占满，有n个在等待。(OS中解决互斥同步问题)

当一个进程申请资源时：如果n>0,则进行count--再使用,如果n<0则进行count--则进行排队。

当一个进程释放资源时候：如果n>0,则进行count++,如果n<0则进行count++再唤醒进程。

```java
public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);
        for (int i = 1; i <= 6; i++) {
            final int temp = i;
            new Thread(()->{
                try {
                    semaphore.acquire();
                    System.out.println(temp + "抢到了");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(temp + "离开了");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();
                }
            }).start();
        }
    }
```

参考效果

![image-20220714162906781](/image-20220714162906781.png)

### 9.读写锁

**ReadWriteLock**:读的时候可以有多个线程去同时读，但是写的时候只可以有一个线程去写。

```java
class MyCacheLock {
    private volatile Map<String,Object> map = new HashMap<>();
    private ReadWriteLock lock = new ReentrantReadWriteLock();

    //写只能一个线程写
    void put(String key,Object value){
        lock.writeLock().lock();//写入锁
        try {
            System.out.println(Thread.currentThread().getName() + "写入" + key);
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "写入完成");
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.writeLock().unlock();
        }
    }

    //读可以多个线程去读
    void get(String key){
        System.out.println(Thread.currentThread().getName() + "读取" +key);
        map.get(key);
        System.out.println(Thread.currentThread().getName() + "读取完成");
    }
}
```

### 10.阻塞队列

阻塞：队列满了需要阻塞等待,如果队列是空的，必须阻塞等待生产

队列

- Collection
  - Set
  - List
  - Queue
    - Deque(双端队列)
    - AbstractQueue(非阻塞队列)
    - BlockingQueue（阻塞队列）
      -  LinkedBlockingQueue
      - ArrayBlockingQueue 

在 **多线程并发处理** **线程池** 情况下我们会使用阻塞队列

四组API

1. 抛出异常

   //Queue full,满了抛出异常

   //NoSuchElementException没有了还remove抛出异常

2. 不抛出异常

   //false不抛出异常，返回false

   // 取不到值返回null

3. 阻塞 等待

4.  超时等待

| 方式     | 抛出异常 | 有返回值 | 阻塞等待 | 超时等待               |
| -------- | -------- | -------- | -------- | ---------------------- |
| 添加     | add()    | offer()  | put()    | offer(e,x,TimeUnit.XX) |
| 移除     | remove() | poll()   | take()   | poll(x,TimeUnit.XX)    |
| 获取队首 | element  | peek     | --       | --                     |

> SynchronizedQueue

容量为0

进去一个元素必须等取出来才能再次放入

### 11.线程池

三种方法，七大参数，四种策略

> ​	池化技术

程序的运行，本质就是占用系统i资源，所以我们要优化资源=>池化技术

线程池，连接池，内存池，对象池。。。

池化技术：

一般我们会设置事先准备好很多资源(防止不断地连接与关闭消耗资源)，有人要用就给，用完还给我。

![image-20220715161126535](/image-20220715161126535.png)

**线程池创建核心7参数**

- corePoolSize,核心线程数量，核心线程不会被回收，当线程池线程数小于核心数时会进行创建。

- maximumPoolSize:线程池最大线程，当线程数量达到corePoolSize，且workQueue(队列)塞满任务后继续创建线程

  如何定义最大线程数(自己定)：

  1、CPU 密集型 ：CPU核心数

  2、 IO 密集型 ： 判断十分耗IO的线程数

- keepAliveTime: 超过corePoolSize后”临时线程“的空闲存活时间

- unit: keepAliveTime单位比如TimeUnit有SECONDS,DAY...

- WorkQueue: 线程数超过corePoolSize后新任务会处于等待状态并存于workQueue中

- ThreadFactory: 创建线程的工厂类

- Handler: 线程池拒绝策略，线程数达到最大线程数，且WorkQueue也被塞满后线程池会调用Handler拒绝策略来处理请求

  - AbortPolicy：（默认）丢弃任务并抛出异常
  - DiscardPolicy：丢弃任务但不抛出异常
  - DiscardOldestPolicy：丢弃最老任务，把最新任务加入队列执行
  - CallerRunsPolicy：将任务分配给执行execute方法的当前线程处理

**创建线程池的几种方法**

Executors.newSingleThreadExecutor();   即只创建唯一的工作线程来执行任务，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

Executors.newFixedThreadPool(5);    创建一个指定工作线程数量的线程池。

Executors.newCachedThreadPool();   创建一个可缓存线程池。

Executors.newScheduleThreadPool()；创建一个定长的线程池，而且支持定时的以及周期性的任务执行。

**线程池的线程复用**

**一个线程可以不断地从阻塞队列中获取任务来执行**，其核心原理在于线程池对Thread进行了一个封装，而不用每次去调用Thread.start方法创建新线程，这样就破坏掉了线程池存在的意义。而是让每个线程去执行一个“**循环任务**”，它会直接去调用任务的run方法，一直去判断有没有要执行的任务，把它当成普通的方法来执行。

**execute**()与**submit**()

execute针对于类似于runnable这种不需要返回值的任务，无法被判断是否成功执行

submit类似于Callabel这种有返回值的任务，可以去判断执行成功与否并通过get获取线程返回值V(入参即返回值类型),使用get(xx,TimeUnit.SECODES)可以设置最长等待时间，超过这个时间立即返回，此时任务没有执行完

**线程池底层工作原理**

线程池通过Executor框架实现，主要时ThreadPoolExecutor类，成员变量AtomicInteer通过CAS(Compare and set)达到无锁并发，效率高，竞争少

ThreadPoolExecutor又是通过LinkedBlockingQueue阻塞队列来实现

当调用execute添加一个任务后，线程先进行判断当前线程数是否小于corePoolSize

小于则新建一个线程去执行

大于等于的话就会把任务放入等待队列中：

- 若等待队列满了，且当前线程数小于最大线程数Max，则创建临时线程去执行任务
- 如果队列满了而且当前线程数大于等于Max最大线程数，则进行饱和拒绝策略(四种)

当线程池里的线程执行完一个任务后会到等待队列中取任务，来执行

当当前线程数大于核心线程数而且一个线程已经执行完一个任务后空闲了超过keepAliveTime，则线程会把它回收以达到corePoolSize

线程阻塞队列：

ArrayBlockingQueue:数组+一把锁+两个条件，基于数组实现，对象入队出队都用一把锁，入队或出队高并发情况下，不需要扩容(也造成指定大小后大小有限)

LinkedBlockingQueue:单向链表+两把锁+两个条件，基于链表实现，两把锁一把入队一把出队，在入队与出队都高并发情况下由于ArrayBlockingQueue，容量无限。

阻塞队列可用于生产者与消费者模型(低级方法通过notify(),wait()实现线程间的通信)

**不允许使用Executors直接创建(允许的请求队列最大长度为Integer.MAX_VALUE，21以容易导致OOM)**，而应通过ThreadPoolExecutor方式，

### 12.四大函数式接口

- Lambda
- 链式编程
- 函数式接口
- Stream流式计算

@FunctionalInternal

四大函数式接口 Consumer Function Predicate Suppiler

- Function<T , R>(函数式接口):T 传入参数，T 返回类型 R apply(T t);

  ```java
  Function<String, String> function = s -> s+"lalala";
  
  //同理
  new Function<String, String>() {
              @Override
              public String apply(String s) {
                  return s;
              }
          };
  ```

- Predicate(断定型接口)有一个输入参数，返回值只能是布尔值类型

  ```java
  Predicate<String> predicate = s -> s.isEmpty();
  
  Predicate<String> predicate = new Predicate<String>(){
      @Override
      public boolean test(String str){
          return str.isEmpty();
      }
  };
  ```

- Consumer 消费型接口(只有输入，没有返回值)

  ```java
  Consumer<String> consumer = s -> System.out.println(s);
  
  consumer.accept("sss");
  ```

- Suppiler 供应型接口(只有输出，没有参数)

  ```java
  Supplier<String> supplier = () -> "ssss";
  System.out.println(supplier.get());
  
  Supplier<String> supplier = new Supplier<String>() {
              @Override
              public String get() {
                  return "ssss";
              }
          };
  ```

### 13.Stream流式计算

这里说一下，Stream流操作中有许多是用到函数式接口 结合lambda表达式，所以写起来非常简单，同时如果不经常用人家来看可能一下子也看不懂,hhh

JAVA8新特性Stream参考SE里写的，已经很全了

### 14.分支合并 ForkJoin

> 分支合并 ForkJoin

jdk1.7之后出来，并行执行任务以提高效率，大数据量

大数据 Map Reduce，把大任务拆分成很多小任务，由很多个线程去并行执行，比如Stream的parellel并行流。

特点：**工作窃取**(一个线程执行地快，另一个线程执行地慢，快线程就会去窃取慢线程地任务来执行以加快效率)。这里面维护的双端队列Deque.

### 15.异步回调

> Future 设计初衷：对将来的某个事件的结果进行建模

Ajax

CompletableFuture

@Async

执行成功回调

失败回调

```java
	CompletableFuture<Void> future = CompletableFuture.runAsync (()->{
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("延迟两秒后输出");
        });

        System.out.println("先输出");

        try {
            System.out.println(future.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

	CompletableFuture<Integer> async = CompletableFuture.supplyAsync(() -> {
            return 1024;
        });
        try {
            System.out.println(async.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }


		CompletableFuture<Integer> async = CompletableFuture.supplyAsync(() -> {
            int i = 10/0;
            return 200;
        });
        try {
            System.out.println(async.whenComplete((t,v)->{
                System.out.println(t + " " + v );
            }).exceptionally((e)->{
                System.out.println(e.getMessage());
                return 500;
            }).get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

```

类似于B/S端，成功200，失败500返回值

### 16.JMM

**指令并行的重排：** 现代处理器采用了指令级并行技术来将多条指令重叠执行。如果不存在数据依赖性(即后一个执行的语句无需依赖前面执行的语句的结果)，处理器可以改变语句对应的机器指令的执行顺序。

参考SE里写的

线程解锁前，必须把共享变量刷到主内存

线程加锁前，必须读取主存中的最新值到工作内存中

加锁和解锁是同一把锁

JMM内存分为工作内存与主内存（共享变量）区域

![在这里插入图片描述](https://img-blog.csdnimg.cn/039b03e0909c461bbc060ba966d38130.png#pic_center)

线程有私有的工作内存

![在这里插入图片描述](https://img-blog.csdnimg.cn/5a224b47ab244e60b6f152f45a4c56e3.png#pic_center)



- 原子性：一个操作是不可中断的，即使是在多线程环境下，一个操作一旦开始就不会被其他线程影响。比如对于一个静态变量int i = 0，两条线程同时对他赋值，线程A操作为 i = 1，而线程B操作为 i = 2，不管线程如何运行，最终 i 的值要么是1，要么是2，线程A和线程B间的操作是没有干扰的，这就是原子性操作，不可被中断的特点。
- 可见性：当一个线程修改了某个共享变量的值，其他线程是否能够马上得知这个修改的值
- 有序性：对于单线程的执行代码，我们总是认为代码的执行是按顺序依次执行的，这样的理解如果是放在单线程环境下没有问题，毕竟对于单线程而言确实如此，代码由编码的顺序从上往下执行

数据同步八大原子操作：

数据同步八大原子操作
（1）lock(锁定)：作用于主内存的变量，把一个变量标记为一条线程独占状态
（2）unlock(解锁)：作用于主内存的变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定
（3）read(读取)：作用于主内存的变量，把一个变量值从主内存传输到线程的工作内存中，以便随后的load动作使用
（4）load(载入)：作用于工作内存的变量，它把read操作从主内存中得到的变量值放入工作内存的变量副本中
（5）use(使用)：作用于工作内存的变量，把工作内存中的一个变量值传递给执行引擎
（6）assign(赋值)：作用于工作内存的变量，它把一个从执行引擎接收到的值赋给工作内存的变量
（7）store(存储)：作用于工作内存的变量，把工作内存中的一个变量的值传送到主内存中，以便随后的write的操作
（8）write(写入)：作用于工作内存的变量，它把store操作从工作内存中的一个变量的值传送到主内存的变量中

![在这里插入图片描述](https://img-blog.csdnimg.cn/c23b59f917b0477caae6f99ab0bf91e3.png#pic_center)

### 17.volatile

我们希望线程知道主内存的变量已经被修改，让线程重新读取

是Java虚拟机提供的轻量级的同步机制

- 保证可见性，有序性（有序性是通过禁止指令重排来实现的）
- **不保证原子性** (不可分割)线程A，不可被打扰，也不可被分割。要么同时成功，要么同时失败。使用**AtomicInteger**()弥补(使用native底层方法直接搞CPU操作系统,基于CAS实现)十分高效，人家java.concurrent.**atomic**甚至单独为他留了一个包出来编写。
- 禁止指令重排(java编译器会在生成指令系列时在适当的位置会插入内存屏障指令来禁止特定类型的处理器重排序)

JMM内存屏障分为四类：

![img](https://img-blog.csdnimg.cn/img_convert/4774036a5d5e2671094cd420d75f9e02.png)

java编译器会在生成指令系列时在适当的位置会插入内存屏障指令来禁止特定类型的处理器重排序。为了实现volatile的内存语义，JMM会限制特定类型的编译器和处理器重排序，JMM会针对编译器制定volatile重排序规则表：

![img](https://img-blog.csdnimg.cn/img_convert/50f32c1b1ceeca8b3b182eb70177f4cf.png)

"NO"表示禁止重排序。为了实现volatile内存语义时，编译器在生成字节码时，会在指令序列中插入内存屏障来禁止特定类型的处理器重排序。对于编译器来说，发现一个最优布置来最小化插入屏障的总数几乎是不可能的，为此，JMM采取了保守策略：

在每个volatile写操作的前面插入一个StoreStore屏障；
在每个volatile写操作的后面插入一个StoreLoad屏障；
在每个volatile读操作的前面插入一个LoadLoad屏障；
在每个volatile读操作的后面插入一个LoadStore屏障。
需要注意的是：volatile写是在前面和后面分别插入内存屏障，而volatile读操作是在后面插入两个内存屏障

StoreStore屏障：禁止上面的普通写和下面的volatile写重排序；

StoreLoad屏障：防止上面的volatile写与下面可能有的volatile读/写重排序

LoadLoad屏障：禁止下面所有的普通读操作和上面的volatile读重排序

LoadStore屏障：禁止下面所有的普通写操作和上面的volatile读重排序

**synchronized**

JMM关于synchronized的两条规定：

　　1）线程解锁前，必须把共享变量的最新值刷新到主内存中

　　2）线程**加锁**时，将**清空工作内存中共享变量**的值，从而使用共享变量时需要从主内存中重新获取最新的值

　　　（注意：加锁与解锁需要是同一把锁）

synchronized底层由于采用了字节码指令**monitorenter**和**monitorexit**来隐式地使用这lock和unlock两个操作，使得其操作具有原子性。

**unsafe**一个很神奇的类（全是本地方法），使用它可以实现CAS(自旋锁,乐观锁)操作(3个参数：待修改的内存地址，期望值，修改值)，ABA问题用AtomicRefrence进行版本号控制，当然自选过久也耗费CPU资源

### 18.深入单例模式

饿汉式

```java
class Hungry {

    private volatile static Hungry hungry;
    private Hungry() {
        System.out.println(Thread.currentThread().getName() + " ok ");
    }
    //双重检测锁模式
    public static Hungry getInstance(){
        if (hungry == null) {
            synchronized (Hungry.class) {
                if (hungry == null)
                /**
                 * new 的过程分为3步
                 * 1.分配内存
                 * 2.创建实例
                 * 3.指向地址
                 *
                 * 如果指令可以重排
                 * 132C操作，那么B拿到的可能是Null，因为虽然指向了地址，但是此时还没有创建实例
                 */
                    hungry = new Hungry();
            }
        }
        return hungry;
    }

}
public class HungryDemo {
    public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Hungry instance = Hungry.getInstance();
        Hungry instance2 = Hungry.getInstance();

        Constructor<EnumSingle> constructor = EnumSingle.class.getDeclaredConstructor(String.class,int.class);
        constructor.setAccessible(true);

        EnumSingle enumSingle = constructor.newInstance();

    }
}

enum EnumSingle {
    INSTANCE;

    public EnumSingle getInstance(){
        return  INSTANCE;
    }
}
```

### 19.深入理解CAS

> 什么是CAS

compareAndSet 比较当前工作内存值与主内存值，如果是期望的那就执行操作，否则不执行，由于do while会一直循环阻塞直到修改	期望是这个值就进行更新

**自旋锁**，不停循环，直到修改

缺点：循环耗时，底层CPU一次性只能保证一个共享变量的原子性，存在ABA问题

```java
/**
 * @Description: CAS compareAndSet 比较与交换
 * CAS 是CPU的并发原语 , java无法调用内存，但是可以用native调用C++，C++可以操作内存，通过Unsafe类可以操作内存
 * @author: LGD
 * @date:2022/7/20 9:00
 */
public class CASDemo {

    public static void main(String[] args) {
        AtomicInteger atomicInteger =  new AtomicInteger(2022);

        // public final boolean compareAndSet(int expect, int update) 期望，更新值 修改成功true
        atomicInteger.compareAndSet(2022,2033);
        System.out.println(atomicInteger.get());
    }
}
```

>CAS: ABA 问题

线程A:cas(1,2)

线程B: cas(1,3) cas(3,1)

线程B换过1，又换回1，线程A不知道，以为还是1

### AQS

AQS 加锁的原理？
先来看看 AQS 对象内部有哪些属性：

state：int 类型核心变量，代表了加锁的状态。初始状态下，state 为 0.

exclusiveOwnerThread：Thread 类型，用来记录当前加锁的是哪个线程，初始值为 null。

head：头结点，把它当做当前持有锁的线程可能更好理解。

tail：尾接单，每个新的节点进来，都插入到最后，也就形成了一个等待队列的链表。

接下来我们使用 ReentrantLock的lock 为例，当线程调用 ReentrantLock 的 lock() 方法尝试进行加锁，实际就是用 CAS 操作将 state 值从 0 变为 1，同时加锁成功后，将加锁线程设置成当前线程。

如何实现锁互斥

1）当另一个线程 B 加锁时，也会尝试使用 CAS 操作将 state 从 0 变为 1，但此时因为线程 A 已经获取到锁且 state 已经是 1，则线程 B 加锁失败。

2）接着线程 B 会检查加锁线程变量记录的加锁线程是否是自己。

3）如果不是，则线程 B 会将自己放入 AQS 中的一个等待队列，等待线程 A 释放锁后重新尝试加锁。



如何实现可重入

其实每次线程加锁时会判断一下当前加锁线程是否就是自己，那么是自己就可以可重入多次加锁，每次加锁就是把 state 的值给累加 1，释放锁时就递减 1。当 state 减到 0 时就彻底释放锁，即将加锁线程重置为 null。

ref

7、AQS 中公平锁和非公平锁？
在 AQS 中非公平体现在于当线程 A 释放锁，唤醒队列中第一个线程 B，此时刚好有线程 C 尝试抢锁，则 C 获取锁。

从代码实现来看，公平锁和非公平锁主要有两处不同：

1）非公平锁在调用 lock() 后，首先就会调用 CAS 进行一次抢锁，如果这个时候恰巧锁没有被占用，那么直接就获取到锁返回;

2）非公平锁在 CAS 失败后，和公平锁一样都会进入到 tryAcquire() 方法，在 tryAcquire() 方法中，如果发现锁这个时候被释放了（state == 0），非公平锁会直接 CAS 抢锁，但是公平锁会判断等待队列是否有线程处于等待状态，如果有则不去抢锁，乖乖排到后面。

如果这两次 CAS 都不成功，那么后面非公平锁和公平锁是一样的，都要进入到阻塞队列等待唤醒。

相对来说，非公平锁会有更好的性能，因为它的吞吐量比较大。当然，非公平锁让获取锁的时间变得更加不确定，可能会导致在阻塞队列中的线程长期处于饥饿状态。

### 20.原子引用

带版本号的原子操作，类似于Mysql的乐观锁

AtomicReference控制版本

注意这里比较装箱源码里比较的是地址 ==,而Integer缓存只有-128~127，超过了就会new一个对象造成尽管值一样，但是地址不一样还是为false

```java
AtomicStampedReference<Integer> stampedReference = new AtomicStampedReference<>(101, 1);

ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(2, 4, 5000, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
threadPoolExecutor.execute(()->{
    Integer reference = stampedReference.getReference();
    System.out.println("A1 " + stampedReference.getReference() + " "  + reference);

    try {
        TimeUnit.SECONDS.sleep(2);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }

    System.out.println("A2 "  +stampedReference.compareAndSet(101, 103, stampedReference.getStamp(), stampedReference.getStamp() + 1));
    System.out.println("A2 " + stampedReference.getReference() + " "+ stampedReference.getStamp());


    System.out.println("A3 "  +stampedReference.compareAndSet(103, 101, stampedReference.getStamp(), stampedReference.getStamp() + 1));
    System.out.println("A3 " + stampedReference.getReference()+ " " + stampedReference.getStamp());
});

threadPoolExecutor.execute(()->{
    Integer reference = stampedReference.getReference();
    System.out.println("B1 " + + stampedReference.getReference() + " "+ reference);

    try {
        TimeUnit.SECONDS.sleep(2);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }

    System.out.println("B2 "  + stampedReference.compareAndSet(101, 111, stampedReference.getStamp(), stampedReference.getStamp() + 1));
    System.out.println("B2 " + stampedReference.getReference() + " "+ stampedReference.getStamp());

});

threadPoolExecutor.shutdown();
```

这里可能A都成功，B不成功

A都不成功或者B都成功

### 21.可重入锁，公平锁，非公平锁，自旋锁，死锁

公平锁：很公平，队列不可以插队，必须先来后到

非公平锁：很不公平，可以插队(默认都是非公平锁)

**可重入锁**：(递归锁)，拿到外面的锁后就可以获得里面的锁(自动获得)

```java
/**
 *pool-1-thread-1sms
 *pool-1-thread-1call
 *pool-1-thread-2call
 */
public class lockDemo {
    public static void main(String[] args) {
        Phone phone = new Phone();
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(2, 5, 5, TimeUnit.SECONDS, new LinkedBlockingQueue<>());
        threadPoolExecutor.execute(()->{
            phone.sms();
        });
        threadPoolExecutor.execute(()->{
            phone.call();
        });
        threadPoolExecutor.shutdown();
    }
}

class Phone{
    public synchronized void sms(){
        System.out.println(Thread.currentThread().getName() + "sms");

        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        call();
    }

    public synchronized void call(){
        System.out.println(Thread.currentThread().getName() + "call");
    }

}
```

lock，**可重入锁**(获得一个锁后，可以继续获得锁而不会出现死锁，然后一层层解开，Lock锁必须配对，否则程序会死在里面)

```java
class Phone2{
    Lock lock = new ReentrantLock();
    public void sms(){
        lock.lock(); // 外面的锁
        try {
            System.out.println(Thread.currentThread().getName() + "sms");
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            call();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void call(){
        lock.lock();//里面的锁
        System.out.println(Thread.currentThread().getName() + "call");
        lock.unlock();
    }
}
```

**自旋锁**

```java
public final int getAndAddInt(Object var1, long var2, int var4){
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1,var2, var5 , var5 + var4));
}
```

自制自旋锁(加锁，不断循环以加锁)

```java
public class SpinlockDemo {

    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    // 加锁
    public void myLock(){
        Thread thread = Thread.currentThread();

        // 自旋锁
        while(!atomicReference.compareAndSet(null,thread)){

        }

        System.out.println(thread.getName() + " lock");
    }

    // 解锁
    public void myUnlock(){
        Thread thread = Thread.currentThread();
        atomicReference.compareAndSet(thread,null);
        System.out.println(thread.getName() + " unlock");
    }
}
```

**死锁**

> 死锁

- 互斥资源

- 不可剥夺
- 一直等待，不放弃资源

简简单单一个四锁

```java
class MyDeadThread implements Runnable{

    private static final String res1 = "res1";
    private static final String res2 = "res2";
    static int count = 1;
    @Override
    public void run() {
        if (count == 1) {
            count++;
            synchronized (res1) {
                System.out.println("拿到res1");
                try {
                    TimeUnit.SECONDS.sleep(5);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (res2){
                    System.out.println("拿到res2");
                    try {
                        TimeUnit.SECONDS.sleep(5);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }else{
            synchronized (res2) {
                System.out.println("拿到res2");
                try {
                    TimeUnit.SECONDS.sleep(5);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (res1){
                    System.out.println("拿到res1");
                    try {
                        TimeUnit.SECONDS.sleep(5);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}
```

解决问题

1. 使用jps 定位进程号

   `jps -l`

   获取进程

   ![image-20220720155213139](/image-20220720155213139.png)

2. 使用` jstack 进程号`查看具体信息

   ![image-20220720160335450](/image-20220720160335450.png)

面试，工作里排查问题：

1. 日志
2. 堆栈信息,Jprofile,jps -l, jstack 进程号

### 个人练习小实例：

使用雪花算法异步生成100000个ID

```java
	/**
     * 破案了，用的HashSet怪不得数字对不上，哈哈哈
     * 1.用future.isDone()
     * 2.使用辅助类CyclicBarrier,执行完N次做XXX
     */
    public static void main(String[] args) throws InterruptedException {
        SnowflakeIdWorker idWorker = new SnowflakeIdWorker(0, 0);
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(10, 16, 30, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
        CopyOnWriteArraySet<Long> sets = new CopyOnWriteArraySet<>();
        ArrayList<Future<AtomicInteger>> lists = new ArrayList<>(10);
        CyclicBarrier barrier = new CyclicBarrier(10,()->{
            System.out.println(" SUM " + sets.size());
        });

        for (int i = 0; i < 10; i++) {
            lists.add(runA(threadPoolExecutor, idWorker, sets,barrier));
        }

        threadPoolExecutor.shutdown();
/*        while(true){
            boolean flag = true;
            for(Future<AtomicInteger> future: lists) {
                if(!future.isDone())
                    flag = false;
            }
            if (flag) {
                //为正常之和说明没有重复   SUM 100000
                System.out.println(" SUM " + sets.size());
                break;
            }
            TimeUnit.SECONDS.sleep(3);
        }*/

    }

    public static Future<AtomicInteger> runA(ThreadPoolExecutor threadPoolExecutor,SnowflakeIdWorker idWorker,CopyOnWriteArraySet<Long> sets,CyclicBarrier barrier)  {
        return threadPoolExecutor.submit(() -> {
            AtomicInteger ai = new AtomicInteger(0);
            for (int i = 0; i < 10000; i++) {
                long id = idWorker.nextId();
                sets.add(id);
                ai.getAndAdd(1);
            }
            barrier.await();
            return ai;
        });
    }
```

结果，简简单单~

![image-20220811102836809](/image-20220811102836809.png)
