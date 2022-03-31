# JVM

jvm理解

jdk8的永久区变为原空间

OOM,栈溢出StackOverFlow,OutOfMemory,NullOfPoint.......

jvm常用调优参数

内存快照如何抓起，如何分析Dump文件

jvm中类加载器的认识	rt-jar ext application

别怕，至少基本都是理论

不知道就对了



1. JVM位置

   首先是硬件，再之上那必然是运行在操作系统(Windows,Linux,Mac)上的，JVM用C写的（Java叫"C++--"），再之上java的运行环境JRE--JVM，

2. JVM体系结构

   .java-->.class-->类加载器Class Loader-->运行时数据区(Runtime Data Area)[方法区Method Area，堆(Heap)，Java栈(Stack)，本地方法栈(Native Method Stack)，程序计数器]<-- [本地方法接口<--本地方法库，接口引擎]

   GC垃圾回收在堆Heap与方法区中，而jvm调优就几乎都在堆中

   ![image-20220328202415815](C:\Users\L\AppData\Roaming\Typora\typora-user-images\image-20220328202415815.png)

   详细完整版：

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621224433653.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

3. 类加载器

   - 作用：加载Class文件——如果new Student();（具体实例在堆里，引用变量名放栈里） 。

   BootStrap-->Extension class Loader --> Appliacation class Loader

   类就是一个模板，当我们New一个对象时候就会生成一个该类其中一个具体对象，并将对象的地址存在Java Stack栈中，而具体实例存在于Java Heap堆中

4. 双亲委派机制

   就是一个类被加载时，由下向上发送委托请求，然后再自上而下地进行加载，如果BootStrap能够加载就加载，否则抛出异常让子类再进行。

   可以通过getClass（）获取这个类的唯一的模板，也可以通过getClassLoader获取这个类的类加载器(App)，再.getParents()获取再上一层的(Exention)。

   双亲委派机制目的:安全  // 1. APP–>EXC—B0OT(最终执行)  // B0OT  // EXC  // APP  1.类加载器收到类加载的请求  2.将这个请求向上委托给父类加载器去完成，一 直向上委托，知道启动类加载器  3.启动加载器检查是否能够加载当前这个类，能加载就结束， 使用当前的加载器，否则， 抛出异常，通知子加载器进行加载  4.重复步骤3  Class Not Found异常就是这么来的  Java早期的名字：C+±-  Java = C++:去掉繁琐的东西，指针，内存管理~

   - 类是模板，对象是具体的，通过new来实例化对象。car1，car2，car3，名字在栈里面，真正的实例，具体的数据在堆里面，栈只是引用地址。

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210621224455814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

5. 沙箱安全机制  防止源码被破坏

   就是进行权限，类加载器在加载类时会对字节码进行校验，授予对应可操作的权限，当然核心类不需要。

   jdk1.0模型

   ![image-20220329214425188](C:\Users\L\AppData\Roaming\Typora\typora-user-images\image-20220329214425188.png)

   jdk1.1

   ![image-20220329214453669](C:\Users\L\AppData\Roaming\Typora\typora-user-images\image-20220329214453669.png)

   jdk1.2

   ![image-20220329214517231](C:\Users\L\AppData\Roaming\Typora\typora-user-images\image-20220329214517231.png)

   jdk1.6引入Domain，域，不同域有不同的操作权限，就好像Linux分root,guest,user一样

   他的组件：

   ![image-20220329214647274](C:\Users\L\AppData\Roaming\Typora\typora-user-images\image-20220329214647274.png)

   ![image-20220329214837976](C:\Users\L\AppData\Roaming\Typora\typora-user-images\image-20220329214837976.png)

   

6. Native

   用于修饰方法或者变量，涉及native时就已经是java操作不了的需要C++编写的本地方法来进行更底层的线程级操作，这也是为什么JVM外还有JNI（Java Native Interface）以及本地方法库。而在线程Thread的源码中就有native相关方法：

   

7. PC寄存器

8. 方法区

9. 栈

10. 三种JVM：HotSpot,xx,xx

11. 堆

12. 新生区，老年区

13. 永久区

14. 堆内存调优

15. GC（垃圾回收器）

    1. 常用算法

16. JMM

17. 总结



单点登录（架构思想）| SSO



