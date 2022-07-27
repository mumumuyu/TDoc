# JVM

jvm理解

jdk8的永久区变为原空间

OOM,栈溢出StackOverFlow,OutOfMemory,NullOfPoint.......

jvm常用调优参数

内存快照如何抓起，如何分析Dump文件

jvm中类加载器的认识	rt-jar ext application

别怕，至少基本都是理论

不知道就对了



##### 1.JVM位置

首先是硬件，再之上那必然是运行在操作系统(Windows,Linux,Mac)上的，JVM用C写的（Java叫"C++--"），再之上java的运行环境JRE--JVM，

##### 2.JVM体系结构

.java-->.class-->类加载器Class Loader-->运行时数据区(Runtime Data Area)[方法区Method Area，堆(Heap)，Java栈(Stack)，本地方法栈(Native Method Stack)，程序计数器]<-- [本地方法接口<--本地方法库，接口引擎]

GC垃圾回收在堆Heap与方法区中，而jvm调优就几乎都在堆中

![image-20220328202415815](C:\Users\L\Desktop\文档\photo\image-20220328202415815-16511122258921.png)

详细完整版：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621224433653.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

##### 3.类加载器

- 作用：加载Class文件——如果new Student();（具体实例在堆里，引用变量名放栈里） 。

BootStrap-->Extension class Loader --> Appliacation class Loader

类就是一个模板，当我们New一个对象时候就会生成一个该类其中一个具体对象，并将对象的地址存在Java Stack栈中，而具体实例存在于Java Heap堆中

##### 4.双亲委派机制

就是一个类被加载时，由下向上发送委托请求，然后再自上而下地进行加载，如果BootStrap能够加载就加载，否则抛出异常让子类再进行。

可以通过getClass（）获取这个类的唯一的模板，也可以通过getClassLoader获取这个类的类加载器(App)，再.getParents()获取再上一层的(Exention)。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621224600525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

双亲委派机制目的:安全  // 1. APP–>EXC—B0OT(最终执行)  // B0OT  // EXC  // APP  1.类加载器收到类加载的请求  2.将这个请求向上委托给父类加载器去完成，一 直向上委托，知道启动类加载器  3.启动加载器检查是否能够加载当前这个类，能加载就结束， 使用当前的加载器，否则， 抛出异常，通知子加载器进行加载  4.重复步骤3  Class Not Found异常就是这么来的  Java早期的名字：C+±-  Java = C++:去掉繁琐的东西，指针，内存管理~

- 类是模板，对象是具体的，通过new来实例化对象。car1，car2，car3，名字在栈里面，真正的实例，具体的数据在堆里面，栈只是引用地址。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621224455814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

##### 5.沙箱安全机制  防止源码被破坏

就是进行权限，类加载器在加载类时会对字节码进行校验，授予对应可操作的权限，当然核心类不需要。

jdk1.0模型

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714083918143.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29rRm9ycmVzdDI3,size_16,color_FFFFFF,t_70#pic_center)

jdk1.1

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714084054727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29rRm9ycmVzdDI3,size_16,color_FFFFFF,t_70#pic_center)

jdk1.2

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714084127482.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29rRm9ycmVzdDI3,size_16,color_FFFFFF,t_70#pic_center)

jdk1.6引入Domain，域，不同域有不同的操作权限，就好像Linux分root,guest,user一样

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714084152611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29rRm9ycmVzdDI3,size_16,color_FFFFFF,t_70#pic_center)

**组成沙箱的基本组件**:

- `字节码校验器`(bytecode verifier)︰确保Java类文件遵循lava语言规范。这样可以帮助lava程序实现内存保护。但并不是所有的类文件都会经过字节码校验，比如核心类。

- 类装载器(class loader) :其中类装载器在3个方面对Java沙箱起作用：

  。它防止恶意代码去干涉善意的代码;
  。它守护了被信任的类库边界;
  。它将代码归入保护域，确定了代码可以进行哪些操作。

 虚拟机为不同的类加载器载入的类提供不同的命名空间，命名空间由一系列唯一的名称组成，每一个被装载的类将有一个名字，这个命名空间是由Java虚拟机为每一个类装载器维护的，它们互相之间甚至不可见。

**类装载器采用的机制是双亲委派模式。**

1.从最内层VM自带类加载器开始加载，外层恶意同名类得不到加载从而无法使用;

2.由于严格通过包来区分了访问域，外层恶意的类通过内置代码也无法获得权限访问到内层类，破坏代码就自然无法生效。

- 存取控制器(access controller)︰存取控制器可以控制核心API对操作系统的存取权限，而这个控制的策略设定，可以由用户指定。
- 安全管理器(security manager)︰是核心API和操作系统之间的主要接口。实现权限控制，比存取控制器优先级高。
- 安全软件包(security package) : java.security下的类和扩展包下的类，允许用户为自己的应用增加新的安全特性，包括:
  - 安全提供者
  - 消息摘要
  - 数字签名
  - 加密
  - 鉴别



##### 6.Native

用于修饰方法或者变量，涉及native时就已经是java操作不了的需要C++编写的本地方法来进行更底层的线程级操作，这也是为什么JVM外还有JNI（Java Native Interface）以及本地方法库。而在线程Thread的源码中就有native相关方法：

凡是带了native关键字的，说明java的作用范围达不到了，回去调用底层c语言的库!  会进入本地方法栈  调用本地方法本地接口 JNI (Java Native Interface)  JNI作用:开拓Java的使用，融合不同的编程语言为Java所用!最初: C、C++  Java诞生的时候C、C++横行，想要立足，必须要有调用C、C++的程序  它在内存区域中专门开辟了一块标记区域: Native Method Stack，登记native方法  在最终执行的时候，加载本地方法库中的方法通过JNI  例如：Java程序驱动打印机，管理系统，掌握即可，在企业级应用比较少  private native void start0();  //调用其他接口:Socket. . WebService~. .http~

##### 7.PC寄存器

程序计数器:每个线程都有一个，为线程私有，是一个指向方法区的指针，实际所占内存空间很；

用于记录各个现成的执行位置，方便CPU来回切换继续执行

##### 8.方法区

被线程所共享，所有构造方法，接口，函数，在此定义。为共享区间，static,final 类信息（构造方法，接口定义）运行时的常量池存在于方法区中。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621224842497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

##### 9.栈

数据结构中为后进先出。栈是线程级的

当方法执行时入栈，当这个方法需要用到其他方法时，把所需方法压入栈，并在自身栈区域内使用子帧以调用所需子程序，待子程序执行完毕后，再根据子程序区域内的父帧以找到原程序继续执行的位置。因此，正在执行的栈必然在栈顶。

- 存储一些基本类型的值、对象的引用、方法等。
- **栈的优势是，存取速度比堆要快，仅次于寄存器，栈数据可以共享。**



- 1、栈也叫栈内存，主管Java程序的运行，是在线程创建时创建，它的生命期是跟随线程的生命期，线程结束栈内存也就释放。
- 2、**对于栈来说不存在垃圾回收问题**，只要线程一旦结束，该栈就Over，生命周期和线程一致，是线程私有的。
- 3、当栈调用深度大于栈深度时就会出现StackOverFlowError的错误，程序会停下来。
- 4、若栈的深度是可扩展的（不够时申请内存）

![image-20220403210258221](C:\Users\L\Desktop\文档\photo\image-20220403210258221-16511122629592.png)

- **栈里面会放什么东西那？**

  - 8大基本类型 + 对象的引用 + 实例的方法

  > **栈运行原理**

  - Java栈的组成元素——栈帧。
  - 栈帧是一种用于帮助虚拟机执行方法调用与方法执行的数据结构。他是独立于线程的，一个线程有自己的一个栈帧。封装了方法的局部变量表、动态链接信息、方法的返回地址以及操作数栈等信息。
  - 第一个方法从调用开始到执行完成，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程。

栈区域如下图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621224937353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

对象的实例化过程

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621224952953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

##### 10.三种JVM：

Sun公司的**HotSpot** √本次所学

BEA JRockit（Oracle的OpenJDK，最快的jvm，适合财务办公，企业应用）

IBM j9 VM在Oracle开发的基础上开发的

##### 11.堆

- Heap，一个jvm只有一个堆内存，堆内存的大小是可以调整的vm options

- 类加载器读取了类文件后，需要把类，方法，常变量放到堆内存中，保存所有引用类型的真实信息，以方便执行器执行。
- 堆内存分为三部分：
  - 新生区 Young Generation Space Young/New 1/3
  - 养老区 Tenure generation space Old/Tenure  2/3
  - 永久区 Permanent Space Perm

- 堆内存逻辑上分为三部分：新生，养老，永久（元空间 : JDK8 以后名称）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621225034545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

- **GC**垃圾回收主要是在新生区和养老区，又分为轻GC 和 重GC，如果内存不够，或者存在死循环，就会导致堆内存满![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621225116754.png#pic_center)
- 在JDK8以后，永久存储区改了个名字(元空间)。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621225049387.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

##### 12.新生区，老年区

- PSYoungGen
- - eden 8/10
  - from （动态）1/10
  - to （动态）1/10
- OldGen
- Metaspace

YoungGen + OldGen = total

Metaspace属于堆但是是非堆。jdk1.8后分离元空间到电脑内存，不受jvm控制

Full GC，GC

**新生区**

- 类诞生，成长，甚至死亡的地方

- 伊甸园区，所有的对象都是在伊甸园区new出来的，如果在第一次GC回收后还存活，他就会到幸存者1区。

- 大部分new出来的对象，都是临时需要的对象。

- 每当伊甸园区满了，还需要new对象，就会进行一次垃圾回收(Minor GC)，有的对象还在指向引用对象，那他就会继续存活。将伊甸园中的剩余对象移动到幸存0区，若幸存0区也满了，再对该区进行垃圾回收，然后移动到1区，那如果1区也满了呢？（这里幸存0区和1区是一个互相交替的过程）再移动到养老区，若养老区也满了，那么这个时候将产生MajorGC（Full GC），进行养老区的内存清理，若养老区执行了Full GC后发现依然无法进行对象的保存，就会产生OOM异常 “OutOfMemoryError ”。如果出现 java.lang.OutOfMemoryError：java heap space异常，说明Java虚拟机的堆内存不够

  ```
  [GC (Allocation Failure) [PSYoungGen: 262144K->984K(305664K)] 999424K->738272K(1497088K), 0.0031445 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] //进行一次GC
  [GC (Allocation Failure) [PSYoungGen: 21955K->1264K(305664K)] 759243K->738560K(1709568K), 0.0055485 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] //进行一次GC
  [GC (Allocation Failure) [PSYoungGen: 1264K->1280K(305664K)] 738560K->738576K(1709568K), 0.0059387 secs] [Times: user=0.20 sys=0.00, real=0.01 secs] //进行一次GC
  [Full GC (Allocation Failure) [PSYoungGen: 1280K->0K(305664K)] [ParOldGen: 737296K->492547K(840192K)] 738576K->492547K(1145856K), [Metaspace: 4007K->4007K(1056768K)], 0.0407241 secs] [Times: user=0.41 sys=0.00, real=0.04 secs] //进行一次重度GC
  [GC (Allocation Failure) [PSYoungGen: 0K->0K(530944K)] 492547K->492547K(1934848K), 0.0041501 secs] [Times: user=0.20 sys=0.00, real=0.00 secs] //进行一次GC，结果释放的内存还是不够用，所以接下来
  [Full GC (Allocation Failure) [PSYoungGen: 0K->0K(530944K)] [ParOldGen: 492547K->492493K(969216K)] 492547K->492493K(1500160K), [Metaspace: 4007K->4007K(1056768K)], 0.0077331 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] //进行一次重度GC，也解决不了问题
  Heap
   PSYoungGen      total 530944K, used 23607K [0x0000000795300000, 0x00000007b9600000, 0x00000007c0000000)
    eden space 505856K, 4% used [0x0000000795300000,0x0000000796a0dc00,0x00000007b4100000)
    from space 25088K, 0% used [0x00000007b7d80000,0x00000007b7d80000,0x00000007b9600000)
    to   space 43520K, 0% used [0x00000007b4100000,0x00000007b4100000,0x00000007b6b80000)
   ParOldGen       total 1403904K, used 492493K [0x000000073f800000, 0x0000000795300000, 0x0000000795300000)
    object space 1403904K, 35% used [0x000000073f800000,0x000000075d8f35a8,0x0000000795300000)
   Metaspace       used 4040K, capacity 4568K, committed 4864K, reserved 1056768K
    class space    used 439K, capacity 460K, committed 512K, reserved 1048576K
  Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
  	at java.util.Arrays.copyOf(Arrays.java:3332)
  	at java.lang.AbstractStringBuilder.ensureCapacityInternal(AbstractStringBuilder.java:124)
  	at java.lang.AbstractStringBuilder.append(AbstractStringBuilder.java:596)
  	at java.lang.StringBuilder.append(StringBuilder.java:190)
  	at sun.misc.FloatingDecimal$BinaryToASCIIBuffer.appendTo(FloatingDecimal.java:309)
  	at sun.misc.FloatingDecimal.appendTo(FloatingDecimal.java:89)
  	at java.lang.AbstractStringBuilder.append(AbstractStringBuilder.java:736)
  	at java.lang.StringBuilder.append(StringBuilder.java:226)
  	at jvm.MemoryOut_test.main(MemoryOut_test.java:12)
  ```
  
  
  
  原因如下：
  
  - 1、Java虚拟机的堆内存设置不够，尝试扩大堆内存，可以通过参数 -Xms（初始值大小），-Xmx（最大大小）来调整。-XX:+PrintGCDetails （打印GC细节）
  
    扩展VM选项在这![image-20220404124317308](C:\Users\L\Desktop\文档\photo\image-20220404124317308-16511122706123.png)
  
  - 2、代码中创建了大量大对象，并且长时间不能被垃圾收集器收集（存在被引用）或者死循环。

##### 13.永久区

这个区域常驻内存，存放JDK自身携带的Class对象，Interface元数据，存储java运行时需要的基本环境。

加载太多第三方jar包，大量动态生成的反射类，不断被加载，直到内存满了就会出现OOM。

- jdk 1.6之前：永久代，常量池在方法区中
- 1.7： 永久代，慢慢退化，去永久化，常量池在堆中
- 1.8之后： 无永久代，常量池在元空间，方法区(非堆，与堆相区别)在堆中的元空间

获取并打印堆内存最大及初始化时候的大小

```java
long l = Runtime.getRuntime().maxMemory();
long l1 = Runtime.getRuntime().totalMemory();
System.out.println(l/(double)(1024*1024));
System.out.println(l1/(double)(1024*1024));
```

```
1828.0
981.5
Heap
 PSYoungGen      total 305664K, used 15729K [0x0000000795300000, 0x00000007aa800000, 0x00000007c0000000)
  eden space 262144K, 6% used [0x0000000795300000,0x000000079625c420,0x00000007a5300000)
  from space 43520K, 0% used [0x00000007a7d80000,0x00000007a7d80000,0x00000007aa800000)
  to   space 43520K, 0% used [0x00000007a5300000,0x00000007a5300000,0x00000007a7d80000)
 ParOldGen       total 699392K, used 0K [0x000000073f800000, 0x000000076a300000, 0x0000000795300000)
  object space 699392K, 0% used [0x000000073f800000,0x000000073f800000,0x000000076a300000)
 Metaspace       used 3441K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 373K, capacity 388K, committed 512K, reserved 1048576K
```



默认情况下：分配的总内存是电脑内存的1/4,而初始化的内存是1/64？？？，差不多吧

##### 14.堆内存调优

MAT（Eclipse），Jprofiler工具分析OOM原因

作用：

- 分析Dump内存文件，快速定位内存泄漏问题
- 获取堆中的数据
- 获取大的对象

-Xmx2056m -Xms1024m -XX:+HeapDumpOnOutOfMemoryError

![image-20220404143806508](C:\Users\L\Desktop\文档\photo\image-20220404143806508-16511122726544.png)

打开Dump文件进行分析

找到犯错位置

![image-20220404162344922](C:\Users\L\Desktop\文档\photo\image-20220404162344922-16511122738535.png)

##### 15.GC（垃圾回收器）

jvm在进行GC时，并非在三个区域统一回收，基本都是在新生代

- 新生代
- 幸存区（from, to）
- 老年区

GC两种类型为Minor GC & FULL GC(Major GC)

- -XX： -XX:MaxTenuringThreshold=15，根据架构中对象使用到的次数来设定，经历了这个数字后就会进入到老年代区中，JVM默认15次。
- 谁空谁是to，to和from会轮流交换(沙漏)，当对象经历15次GC回收后就进入养老区。
- 当第一次GC时，Eden进行一次清理，然后把存货下来的对象丢入To，From中存在的也丢入To,然后From，To标志对调。（好处就是没有内存的碎片了，坏处是浪费内存空间，多了一半的空间永远是to空的）

常用算法

- 标记清除法

  先扫描一遍空间，把没有标记的对象进行清楚。

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621225706360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

  好处是不需要额外的空间

  坏处是扫描严重浪费时间，会产生内存碎片

- 标记压缩

  在标记清除基础上再优化，向一端移动存活对象，整合内存碎片，扩大可使用空间。

  当然这样会再增加时间。

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021062122575235.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621225801551.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

- 复制算法

  - 当对象在Eden（包括一个Survivor区域，这里假设是From区域）出生后，在经过一次Minor GC后，如果对象还存活，并且能够被另外一块Survivor区域所容纳 （上面已经假设为from区域，这里应为to区域，即to区域有足够的内存空间来存储Eden 和 From 区域中存活的对象），则使用**复制算法**将这些仍然还活着的对象复制到另外一块Survivor区域（即 to 区域）中，然后清理所使用过的Eden 以及Survivor 区域（即form区域），并且将这些对象的年龄设置为1，以后对象在Survivor区，每熬过一次MinorGC，就将这个对象的年龄 + 1，当这个对象的年龄达到某一个值的时候（默认是15岁，通过- XX:MaxTenuringThreshold 设定参数）这些对象就会成为老年代。

    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621225611642.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

  - HotSpot JVM 把年轻代分为了三部分：一个 Eden 区 和 2 个Survivor区（from区 和 to区）。默认比例为 8:1:1，一般情况下，新创建的对象都会被分配到Eden区（一些大对象特殊处理），这些对象经过第一次Minor GC后，如果仍然存活，将会被移到Survivor区，对象在Survivor中每熬过一次Minor GC ， 年龄就会增加1岁，当它的年龄增加到一定程度时，就会被移动到年老代中，因为年轻代中的对象基本上 都是朝生夕死，所以在年轻代的垃圾回收算法使用的是复制算法！复制算法的思想就是将内存分为两块，每次只用其中一块，当这一块内存用完，就将还活着的对象复制到另外一块上面。复制算法不会产 生内存碎片！

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621225635376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

  当对象存活度较低时候，为最佳使用场景，所以我们在新生区使用。

- 引用计数器

  - 每个对象有一个引用计数器，当对象被引用一次则计数器加1，当对象引用失效一次，则计数器减1，对于计数器为0的对象意味着是垃圾对象，可以被GC回收。
  - 目前虚拟机基本都是采用可达性算法，从GC Roots 作为起点开始搜索，那么整个连通图中的对象边都是活对象，对于GC Roots 无法到达的对象变成了垃圾回收对象，随时可被GC回收。

  按照引用次数来算，没有引用过的就被垃圾回收，当然java并不用这种

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621225533583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)



##### 16.JMM

JMM：（java Memory Model的缩写）

作用：缓存一致性协议，用于定义数据读写的规则(遵守，找到这个规则)。

JMM定义了线程工作内存和主内存之间的抽象关系∶线程之间的共享变量存储在主内存(Main Memory)中，每个线程都有一个私有的本地内存（Local Memory)。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621225826231.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

解决共享对象可见性这个问题：volilate

JMM：抽象的概念，理论。

JMM对这八种指令的使用，制定了如下规则：

- 不允许read和load、store和write操作之一单独出现。即使用了read必须load，使用了store必须write。
- 不允许线程丢弃他最近的assign操作，即工作变量的数据改变了之后，必须告知主存。
- 不允许一个线程将没有assign的数据从工作内存同步回主内存。
- 一个新的变量必须在主内存中诞生，不允许工作内存直接使用一个未被初始化的变量。就是怼变量实施use、store操作之前，必须经过assign和load操作。
- 一个变量同一时间只有一个线程能对其进行lock。多次lock后，必须执行相同次数的unlock才能解锁。
- 如果对一个变量进行lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新load或assign操作初始化变量的值。
- 如果一个变量没有被lock，就不能对其进行unlock操作。也不能unlock一个被其他线程锁住的变量。
- 对一个变量进行unlock操作之前，必须把此变量同步回主内存。

　　JMM对这八种操作规则和对[volatile的一些特殊规则](https://www.cnblogs.com/null-qige/p/8569131.html)就能确定哪里操作是线程安全，哪些操作是线程不安全的了。但是这些规则实在复杂，很难在实践中直接分析。所以一般我们也不会通过上述规则进行分析。更多的时候，使用java的happen-before规则来进行分析。

##### 17.总结

- 内存效率：复制算法 > 标记清除算法 > 标记压缩算法 （时间复杂度）；
- 内存整齐度：复制算法 = 标记压缩算法 > 标记清除算法；
- 内存利用率：标记压缩算法 = 标记清除算法 > 复制算法；

 可以看出，效率上来说，复制算法是当之无愧的老大，但是却浪费了太多内存，而为了尽量兼顾上面所 提到的三个指标，标记压缩算法相对来说更平滑一些 ， 但是效率上依然不尽如人意，它比复制算法多了一个标记的阶段，又比标记清除多了一个整理内存的过程。

> 难道就没有一种最优算法吗？
>
> 答案： 无，没有最好的算法，只有最合适的算法 。 -----------> 分代收集算法

**年轻代：**（Young Gen）

- 年轻代特点是区域相对老年代较小，对象存活低。
- 这种情况复制算法的回收整理，速度是最快的。复制算法的效率只和当前存活对象大小有关，因而很适 用于年轻代的回收。而复制算法内存利用率不高的问题，通过hotspot中的两个survivor的设计得到缓解。

**老年代：**（Tenure Gen）

- 老年代的特点是区域较大，对象存活率高！
- 这种情况，存在大量存活率高的对象，复制算法明显变得不合适。一般是由标记清除或者是标记清除与标记整理的混合实现。Mark阶段的开销与存活对象的数量成正比，这点来说，对于老年代，标记清除或 者标记整理有一些不符，但可以通过多核多线程利用，对并发，并行的形式提标记效率。Sweep阶段的 开销与所管理里区域的大小相关，但Sweep “就地处决” 的 特点，回收的过程没有对象的移动。使其相对其他有对象移动步骤的回收算法，仍然是是效率最好的，但是需要解决内存碎片的问题。



##### JMM

**java虚拟机有自己的内存模型（Java Memory Model，JMM），JMM可以屏蔽掉各种硬件和操作系统的内存访问差异，以实现让java程序在各种平台下都能达到一致的内存访问效果。**

　　**JMM决定一个线程对共享变量的写入何时对另一个线程可见，JMM定义了线程和主内存之间的抽象关系：共享变量存储在主内存(Main Memory)中，每个线程都有一个私有的本地内存（Local Memory），本地内存保存了被该线程使用到的主内存的副本拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存中的变量。这三者之间的交互关系如下**

![img](https://images2015.cnblogs.com/blog/1024555/201703/1024555-20170318223812776-1081654170.png)

 

　　需要注意的是，JMM是个抽象的内存模型，所以所谓的本地内存，主内存都是抽象概念，并不一定就真实的对应cpu缓存和物理内存。当然如果是出于理解的目的，这样对应起来也无不可。

　　**大概了解了JMM的简单定义后，问题就很容易理解了，对于普通的共享变量来讲，比如我们上文中的status，线程A将其修改为true这个动作发生在线程A的本地内存中，此时还未同步到主内存中去；而线程B缓存了status的初始值false，此时可能没有观测到status的值被修改了，所以就导致了上述的问题。那么这种共享变量在多线程模型中的不可见性如何解决呢？比较粗暴的方式自然就是加锁，但是此处使用synchronized或者Lock这些方式太重量级了，有点炮打蚊子的意思。比较合理的方式其实就是volatile**

　　volatile具备两种特性，第一就是保证共享变量对所有线程的可见性。将一个共享变量声明为volatile后，会有以下效应：

　　　　**1.当写一个volatile变量时，JMM会把该线程对应的本地内存中的变量强制刷新到主内存中去；**

　　　　**2.这个写会操作会导致其他线程中的缓存无效。**

上面的例子只需将status声明为volatile，即可保证在线程A将其修改为true时，线程B可以立刻得知

```
 volatile boolean status = false;
```

留意复合类操作

　　但是需要注意的是，我们一直在拿volatile和synchronized做对比，仅仅是因为这两个关键字在某些内存语义上有共通之处，volatile并不能完全替代synchronized，它依然是个轻量级锁，在很多场景下，volatile并不能胜任。看下这个例子：

```java

import java.util.concurrent.CountDownLatch;

public class Counter {
    public static volatile int num = 0;
    //使用CountDownLatch来等待计算线程执行完
    static CountDownLatch countDownLatch = new CountDownLatch(30);
    public static void main(String []args) throws InterruptedException {
        //开启30个线程进行累加操作
        for(int i=0;i<30;i++){
            new Thread(){
                public void run(){
                    for(int j=0;j<10000;j++){
                        num++;//自加操作
                    }
                    countDownLatch.countDown();
                }
            }.start();
        }
        //等待计算线程执行完
        countDownLatch.await();
        System.out.println(num);
    }
}
```

执行结果：

```
224291
```

针对这个示例，一些同学可能会觉得疑惑，如果用volatile修饰的共享变量可以保证可见性，那么结果不应该是300000么?

问题就出在num++这个操作上，**因为num++不是个原子性的操作，而是个复合操作**。我们可以简单讲这个操作理解为由这三步组成:

　　1.读取

　　2.加一

　　3.赋值

　　**所以，在多线程环境下，有可能线程A将num读取到本地内存中，此时其他线程可能已经将num增大了很多，线程A依然对过期的num进行自加，重新写到主存中，最终导致了num的结果不合预期，而是小于30000。**

解决num++操作的原子性问题

　　针对num++这类复合类的操作，可以使用java并发包中的原子操作类原子操作类是通过循环CAS的方式来保证其原子性的。

```java
public class Counter {　　//使用原子操作类
    public static AtomicInteger num = new AtomicInteger(0);
    //使用CountDownLatch来等待计算线程执行完
    static CountDownLatch countDownLatch = new CountDownLatch(30);
    public static void main(String []args) throws InterruptedException {
        //开启30个线程进行累加操作
        for(int i=0;i<30;i++){
            new Thread(){
                public void run(){
                    for(int j=0;j<10000;j++){
                        num.incrementAndGet();//原子性的num++,通过循环CAS方式
                    }
                    countDownLatch.countDown();
                }
            }.start();
        }
        //等待计算线程执行完
        countDownLatch.await();
        System.out.println(num);
    }
}
```

执行结果

```
300000
```

关于原子类操作的基本原理，会在后面的章节进行介绍，此处不再赘述。

禁止指令重排序

volatile还有一个特性：**禁止指令重排序优化。**

**重排序是指编译器和处理器为了优化程序性能而对指令序列进行排序的一种手段。但是重排序也需要遵守一定规则：**

　　**1.重排序操作不会对存在数据依赖关系的操作进行重排序。**

　　　　比如：a=1;b=a; 这个指令序列，由于第二个操作依赖于第一个操作，所以在编译时和处理器运行时这两个操作不会被重排序。

　　**2.重排序是为了优化性能，但是不管怎么重排序，单线程下程序的执行结果不能被改变**

　　　　比如：a=1;b=2;c=a+b这三个操作，第一步（a=1)和第二步(b=2)由于不存在数据依赖关系，所以可能会发生重排序，但是c=a+b这个操作是不会被重排序的，因为需要保证最终的结果一定是c=a+b=3。

　　简单总结下，volatile是一种轻量级的同步机制，它主要有两个特性：一是保证共享变量对所有线程的可见性；二是禁止指令重排序优化。同时需要注意的是，volatile对于单个的共享变量的读/写具有原子性，但是像num++这种复合操作，volatile无法保证其原子性，当然文中也提出了解决方案，就是使用并发包中的原子操作类，通过循环CAS地方式来保证num++操作的原子性。关于原子操作类，会在后续的文章进行介绍。
