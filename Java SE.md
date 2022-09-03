Java SE

### **疑点回顾**

#### 重写了equals()为什么还要重写hashCode()

首先来说说hashCode()，也就是散列值，任意的输入通过哈希函数会得到一个唯一的输出，这就是哈希值。equals比较的是两个对象是否是属性上的一致

如果有3个Person类对象

未重写Person的equals,hashCode的时候，即使值一样，equals仍然是false（因为对于Object的equals比较的还是地址）

这时候重写equals变为属性一样就为true

偶然看到，Integer的hashCode就是他的value，equals也就是比一下int值一样不

重写过程

```java
//参考String重写的equals
public boolean equals(Object anObject){
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof Person) {
            Person person = (Person) anObject;
            if(person.name.equals(this.name) && person.age == this.age)
                return true;
        }
        return false;
    }


```

那么现在equals一样了，但是hashCode却不一样，

重写一下hashCode()

```java
public int hashCode() {
        final int prime = 31;  // 基数
        int result = 1;
        result = prime * result + age;
        result = prime * result + ((name == null) ? 0 : name.hashCode());
        return result;            // 相同的数据，hashCode也相同。所以age与name相同时，result计算结果也相同。
    }
```

现在值一样的对象，hashcode都一样，然后地址也一样，Integer.toHexString(hashCode())了

**hashCode**与**equals**

现在是可能感觉，是不是只要重写hashCode而不用equals就好了？也不对，因为hashCode并不可靠，有时候hashCode意外一样，而值并不完全一样的情况也是有的。但是hashCode可以辅助equals效率，如果两个对象hashCode不一样，那么这两个对象肯定不一样。

Java中规定，**两个内容相同(equals()为true)的对象必须具有相等的hashCode**。因为如果equals()为true而两个对象的hashcode不同;那在整个存储过程中就发生了悖论。所以我们在重写equals时，也需要重写hashCode方法。

总之，我来说就是Object中equals和== 比较的都是地址，但是我们希望equals比较值，以值来区分对象是不是一个，所以我们重写了equals并进行比较值，hashCode此时也需要重写，不然会出现地址不同，但是equals却为true,而java规定，**两个内容相同(equals()为true)的对象必须具有相等的hashCode**。不然在整个存储过程中就发生了悖论，然后是hashCode重写后，这样地址和值都一样了。比如Integer，它的hashCode就是直接int的value，equals就直接比较value是否一样。一般我们自己new了一个对象可以通过各属性得到一个hashCode，然后在equals时先比较地址，hash值是否一样，一样的话直接为true增加效率，false再慢慢一个个比值。

lombok的@Data帮我们简化了new一个对象后，需要重写hashCode和equals，toString（本来是输出一个对象的十六进制化的内存地址，现在我们希望得到它的属性详细信息）。@Data相当于@Getter @Setter @RequiredArgsConstructor @ToString @EqualsAndHashCode这5个注解的合集, 和@EqualsAndHashCode默认是false(就是只用自己的属性来生成hashcode)。为true的话就是用自己的属性和从父类继承的属性来生成hashcode.

这里注意一下：比如一个父类，一个子类都有很多属性，然后都有@Data，此时equals比较的是子类的属性，如果都一样就都一样，这里我们可以通过@EqualsAndHashCode设置为true来解决



javadoc -encoding utf-8 -charset utf-8 Test.java

可产生说明文档

<<带符号位移动，+-不变

<<<不带符号位移动，可能变

&与 |或 ^异 ~取反

‘\u0041’

**类的加载顺序**

(1) 父类静态对象和静态代码块

(2) 子类静态对象和静态代码块

(3) 父类非静态对象和非静态代码块

(4) 父类构造函数

(5) 子类 非静态对象和非静态代码块

(6) 子类构造函数

常用类如:Object,Date,String,Math,Random,File等

集合框架：大类Collection , Map

**Collection**

- List
  - ArrayList
  - LinkedList
- Set
  - TreeSet
  - HashSet



Map

- HashMap
- TreeMap
- LinkedHashMap

**值传递与引用传递**

java只有值传递

 int ,double等基本数据类型在参数传递时并没有传进变量本身，而是创建了一个新的相同数值的变量, 函数修改这个新变量并没有影响原来变量的数值,这也是按值传递的特点

那为什么对象a的数据就改变了呢?

因为虽然也是按值传递, 复制了一份新的引用但是指向的对象是同一个,修改后会影响原对象

![img](C:\Users\L\Desktop\文档\photo\70.png)

这种方式假如在函数内修改 a=null; 只是把复制的引用与对象的联系断开,不影响函数外与实际对象

这就是按值传递, 即使传的是引用也不是引用传递,

因为引用传递是不复制的,直接使用参数,如下图:

这时候函数把指针a=null就指针就置空了,函数外也无法再通过指针访问对象了




### 1.数据类型

##### 基本数据类型：

- byte 8

可以通过string.getBytes()得到bytes数组

也可以通过Base64.getEncoder().encodeToString(bytes)变String

​								.getDecoder().decode(encoded)变byte[]

- char 16
  ```java
  //使用一下判断一个字符是否为小写
  Character.isLowerCase(a)
  //或者
  (a>='a' && a <= 'z')
  ```

- short 16

- int 32

  (关于java里的编码)

  1 就是 000...00(31位)1

  -1 就是 先同上,再有数据部分取反，再去前面0把剩余都取反就变1111111111..11(32个1)

- float 32

- long 64

- double 64

- boolean ~，1bit 就可以表示他的值true or false，JVM会把boolean 变成 int ,0 为 false , 1 为true.JVM 支持 boolean 数组，但是是通过读写 byte 数组来实现的。

##### 包装类型

- 基本类型都有对应包装类型,

如Integer Double Long ，

赋值用装箱与拆箱，

Integer x = 2;//(Integer.valuseOf(2))

int y = x;// (x.intvalue())

##### 缓存池

new Integer 与 Integer.valueOf()不同

前者每次都会创建一个新的对象，而valueOf则会用缓存池中的对象，多次调用取得同一个引用

对于valueOf的实现方法，先判断是否在缓存池中，是的话返回

```java
public static Integer valueOf(int i){
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (- IntegerCache.low)];
    return new Integer(i);
}
```

否则创建一个integer对象包装

上个示例中的缓存池大小IntegerCache默认为-128~127

所以

```java
Integer x = 125;
Integer y = 125;
System.out.println(x == y)// true
```

有了以上示例后，我们就会理解如下

基本类型对应的缓存池:

- boolean values true and false
- all byte values
- short valuse between -128 ~ 127
- int values -128~127
- char range \u0000 to \u007F

在缓存池中的对象，在适用其对应包装类型时可以直接引用

当然，对于Integer 的上限，在jdk1.8中是可以改的，可以通过 -XX:AutoBoxCacheMax=<size> 来指定这个缓冲池的大小

### 2.String

申明为final ，故不可继承，其他包装类也一样不可以继承

Java 8 中 ，String 内部使用 char[]存数据

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    private final char value[];
}
```

Java9后，String类改用byte[]来存数据

同时使用 byte 类型的 coder 标记使用的哪种编码

可以看见存储过程中 value是final 的，所以他不可变

##### 不可变当然有很多好处

- 比如HashMap<String , Object>,这样他的key用String类型,String不可变，hash的值也不可变
- String Pool：

如果一个String对象已经被创建过，就会从String Pool中获得引用，只有String不可变，才可以使用String Pool

![img](C:\Users\L\Desktop\文档\photo\68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303139313231303030343133323839342e706e67.png)

String 的不可变也让其具备线程安全，可以在多线程中使用

##### String ,StringBuffer与StringBulider三者关系

- 对于String来说，它不可变，所以线程安全
- 而String Builder 可变，所以线程不安全，用于字符串单线程的更改
- StringBuffer 也可变，但是内部可以使用synchronized进行同步，用于多线程字符串的更改与使用

##### StringPool

当一个String使用intern()时，经过.equals()发现已经存在一个值一样的String,那么就会返回它在String Pool中字符串的引用，否则就再创建一个新的对象

如以下示例

```java
String s1 = new String("love");
String s2 = new String("lover");
System.out.println(s1 == s2);//false
String s3 = s1.intern();
String s4 = s2.intern();
System.out.println(s3 == s4);//true
//当然，若使用
String s5 = "lover";
String s6= "lover";//会自动调用intern()
//所以
System.out.println(s5 == s6);//true
```

再Java7 前,String Pool在运行时常量池中，而在之后，他被放到了堆中，因为大量使用字符串会导致OutOfMemoryError（内存溢出）

String 作为对象在创建另一个作为字符串的对象时，会把自己的value指向另一个字符串的value，所以

new String("abcd");//实际上创建了两个String对象（如果String Pool 中还没有"abcd"这个对象的话）

### 3.运算

##### 参数传递

在java中参数是以值形式传入方法中，而非引用

把一个参数传入对象实际是把对象的地址以值传入形参中

那么，如果在方法中改变的是原来对象的这个字段的值，他就会真的改变，因为他们是引用的同一个对象

而对于在方法中改变的若是另一个对象的字段，对于原对象字段不会发生变化

```java
public static void main(String[] args){
    Dog dog = new Dog("A");
    System.out.println(dog); //Dog@4554617c
    func(dog);
    System.out.println(dog.getObjectAddress()); // Dog@4554617c
        System.out.println(dog.getName());          // A
    }

    private static void func(Dog dog) {
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        dog = new Dog("B");
        System.out.println(dog.getObjectAddress()); // Dog@74a14482
        System.out.println(dog.getName());          // B
    }
}
```

##### float 与 double 

java不可以执行隐式向下转型，

```java
float = 1.1; //不被允许 1.1是double类型，精度更高
float = 1.1f; //可以 因为1.1f是float
short s1 = 1;/不行
s1 = s1 +1;
s1 ++; //行
s1 = (short)(s1 + 1);
```

##### switch

Java7 后,switch 可以使用String，不支持long,float,double

### 4.关键字

##### final

- 对于数据，final可以使其数值不变，对于引用类型，final使其引用不变
- 对于void ,那么其就不可以被子类重写
- 对于类，那他就不可以被继承

##### static

- 静态变量，说明他属于类，这个类所有实例都可以共享静态变量，可以通过类名，直接访问,在内存中只存在一份

  ```java
  Dog dog = new Dog("511");//static String sex = "男";
  System.out.println(Dog.sex);
  ```

  实例变量，

  private String num;//这就是一个实例变量

- 静态方法

  static void func(){}

  他在类加载时候就存在，不依赖于任何实例

  很显然，静态方法和变量中不可以有this和super

  因为这俩和具体实例相关

- 静态语句块

  static { }

  在类初始化的时候运行一次

- 静态内部类，

非静态内部类需要依赖外部类实例，而静态内部类不需要，可以直接用

- 初始化顺序

自然是静态化语句与数据先，他俩谁先看代码里的顺序

最后才是构造函数的初始化。

存在继承的情况下，初始化顺序为：

- 父类（静态变量、静态语句块）
- 子类（静态变量、静态语句块）
- 父类（实例变量、普通语句块）
- 父类（构造函数）
- 子类（实例变量、普通语句块）
- 子类（构造函数）

### 5.Object的通用方法

![image-20211107144728929](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20211107144728929.png)

```java
public native int hashCode()

public boolean equals(Object obj)

protected native Object clone() throws CloneNotSupportedException

public String toString()

public final native Class<?> getClass()

protected void finalize() throws Throwable {}

public final native void notify() //锁相关，唤醒

public final native void notifyAll() //锁相关，唤醒

public final native void wait(long timeout) throws InterruptedException //锁相关

public final void wait(long timeout, int nanos) throws InterruptedException //锁相关

public final void wait() throws InterruptedException
```

其中，equals（）比较的是两个对象的地址是否一样

##### equals

###### **1. 等价关系**

两个对象具有等价关系，需要满足以下五个条件：

Ⅰ 自反性

```java
x.equals(x); // true
```

Ⅱ 对称性

```java
x.equals(y) == y.equals(x); // true
```

Ⅲ 传递性

```java
if (x.equals(y) && y.equals(z))
    x.equals(z); // true;
```

Ⅳ 一致性

多次调用 equals() 方法结果不变

```java
x.equals(y) == x.equals(y); // true
```

Ⅴ 与 null 的比较

对任何不是 null 的对象 x 调用 x.equals(null) 结果都为 false

```java
x.equals(null); // false;
```

###### **2. 等价与相等**

- 对于基本类型， == 判断两个值是否相等，基本类型没有equals（）方法
- 对于引用类型， == 判断两个值是否引用同一个对象，而equals判断两个两个变量是否等价相等

###### 3.实现

- 检查是否为同一个对象的引用，如果是直接返回 true；
- 检查是否是同一个类型，如果不是，直接返回 false；
- 将 Object 对象进行转型；
- 判断每个关键域是否相等。

```java
public class EqualExample {

    private int x;
    private int y;
    private int z;

    public EqualExample(int x, int y, int z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        EqualExample that = (EqualExample) o;

        if (x != that.x) return false;
        if (y != that.y) return false;
        return z == that.z;
    }
}
```

###### hashCode()

hashCode() 返回哈希值，而 equals() 是用来判断两个对象是否等价。等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价，这是因为计算哈希值具有随机性，两个值不同的对象可能计算出相同的哈希值。

在覆盖 equals() 方法时应当总是覆盖 hashCode() 方法，保证等价的两个对象哈希值也相等。

HashSet 和 HashMap 等集合类使用了 hashCode() 方法来计算对象应该存储的位置，因此要将对象添加到这些集合类中，需要让对应的类实现 hashCode() 方法。

下面的代码中，新建了两个等价的对象，并将它们添加到 HashSet 中。我们希望将这两个对象当成一样的，只在集合中添加一个对象。但是 EqualExample 没有实现 hashCode() 方法，因此这两个对象的哈希值是不同的，最终导致集合添加了两个等价的对象。

```java
EqualExample e1 = new EqualExample(1, 1, 1);
EqualExample e2 = new EqualExample(1, 1, 1);
System.out.println(e1.equals(e2)); // true
HashSet<EqualExample> set = new HashSet<>();
set.add(e1);
set.add(e2);
System.out.println(set.size());   // 2
```

理想的哈希函数应当具有均匀性，即不相等的对象应当均匀分布到所有可能的哈希值上。这就要求了哈希函数要把所有域的值都考虑进来。可以将每个域都当成 R 进制的某一位，然后组成一个 R 进制的整数。

R 一般取 31，因为它是一个奇素数，如果是偶数的话，当出现乘法溢出，信息就会丢失，因为与 2 相乘相当于向左移一位，最左边的位丢失。并且一个数与 31 相乘可以转换成移位和减法：`31*x == (x<<5)-x`，编译器会自动进行这个优化。

```
@Override
public int hashCode() {
    int result = 17;
    result = 31 * result + x;
    result = 31 * result + y;
    result = 31 * result + z;
    return result;
}
```

###### toString()

默认返回 ToStringExample@4554617c 这种形式，其中 @ 后面的数值为散列码的无符号十六进制表示。

```java
public class ToStringExample {

    private int number;

    public ToStringExample(int number) {
        this.number = number;
    }
}
ToStringExample example = new ToStringExample(123);
System.out.println(example.toString());
ToStringExample@4554617c
```

###### clone()

**1. cloneable**

clone() 是 Object 的 protected 方法，它不是 public，一个类不显式去重写 clone()，其它类就不能直接去调用该类实例的 clone() 方法。

```java
public class CloneExample {
    private int a;
    private int b;
}
CloneExample e1 = new CloneExample();
// CloneExample e2 = e1.clone(); // 'clone()' has protected access in 'java.lang.Object'
```

重写 clone() 得到以下实现：

```
public class CloneExample {
    private int a;
    private int b;

    @Override
    public CloneExample clone() throws CloneNotSupportedException {
        return (CloneExample)super.clone();
    }
}
CloneExample e1 = new CloneExample();
try {
    CloneExample e2 = e1.clone();
} catch (CloneNotSupportedException e) {
    e.printStackTrace();
}
java.lang.CloneNotSupportedException: CloneExample
```

以上抛出了 CloneNotSupportedException，这是因为 CloneExample 没有实现 Cloneable 接口。

应该注意的是，clone() 方法并不是 Cloneable 接口的方法，而是 Object 的一个 protected 方法。Cloneable 接口只是规定，如果一个类没有实现 Cloneable 接口又调用了 clone() 方法，就会抛出 CloneNotSupportedException。

```
public class CloneExample implements Cloneable {
    private int a;
    private int b;

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

**2. 浅拷贝**

拷贝对象和原始对象的引用类型引用同一个对象。

```
public class ShallowCloneExample implements Cloneable {

    private int[] arr;

    public ShallowCloneExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
    }

    public void set(int index, int value) {
        arr[index] = value;
    }

    public int get(int index) {
        return arr[index];
    }

    @Override
    protected ShallowCloneExample clone() throws CloneNotSupportedException {
        return (ShallowCloneExample) super.clone();
    }
}
ShallowCloneExample e1 = new ShallowCloneExample();
ShallowCloneExample e2 = null;
try {
    e2 = e1.clone();
} catch (CloneNotSupportedException e) {
    e.printStackTrace();
}
e1.set(2, 222);
System.out.println(e2.get(2)); // 222
```

**3. 深拷贝**

拷贝对象和原始对象的引用类型引用不同对象。

```java
public class DeepCloneExample implements Cloneable {

    private int[] arr;

    public DeepCloneExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
    }

    public void set(int index, int value) {
        arr[index] = value;
    }

    public int get(int index) {
        return arr[index];
    }

    @Override
    protected DeepCloneExample clone() throws CloneNotSupportedException {
        DeepCloneExample result = (DeepCloneExample) super.clone();
        result.arr = new int[arr.length];
        for (int i = 0; i < arr.length; i++) {
            result.arr[i] = arr[i];
        }
        return result;
    }
}
DeepCloneExample e1 = new DeepCloneExample();
DeepCloneExample e2 = null;
try {
    e2 = e1.clone();
} catch (CloneNotSupportedException e) {
    e.printStackTrace();
}
e1.set(2, 222);
System.out.println(e2.get(2)); // 2
```

**4. clone() 的替代方案**

使用 clone() 方法来拷贝一个对象即复杂又有风险，它会抛出异常，并且还需要类型转换。Effective Java 书上讲到，最好不要去使用 clone()，可以使用拷贝构造函数或者拷贝工厂来拷贝一个对象。

```java
public class CloneConstructorExample {

    private int[] arr;

    public CloneConstructorExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
    }

    public CloneConstructorExample(CloneConstructorExample original) {
        arr = new int[original.arr.length];
        for (int i = 0; i < original.arr.length; i++) {
            arr[i] = original.arr[i];
        }
    }

    public void set(int index, int value) {
        arr[index] = value;
    }

    public int get(int index) {
        return arr[index];
    }
}
CloneConstructorExample e1 = new CloneConstructorExample();
CloneConstructorExample e2 = new CloneConstructorExample(e1);
e1.set(2, 222);
System.out.println(e2.get(2)); // 2
```

### 6.继承

##### 访问权限

Java 中有三个访问权限修饰符：private、protected 以及 public，如果不加访问修饰符，表示包级可见。

可以对类或类中的成员（字段和方法）加上访问修饰符。

- 类可见表示其它类可以用这个类创建实例对象。
- 成员可见表示其它类可以用这个类的实例对象访问到该成员；

protected 用于修饰成员，表示在继承体系中成员对于子类可见，但是这个访问修饰符对于类没有意义。

设计良好的模块会隐藏所有的实现细节，把它的 API 与它的实现清晰地隔离开来。模块之间只通过它们的 API 进行通信，一个模块不需要知道其他模块的内部工作情况，这个概念被称为信息隐藏或封装。因此访问权限应当尽可能地使每个类或者成员不被外界访问。

如果子类的方法重写了父类的方法，那么子类中该方法的访问级别不允许低于父类的访问级别。这是为了确保可以使用父类实例的地方都可以使用子类实例去代替，也就是确保满足里氏替换原则。

字段决不能是公有的，因为这么做的话就失去了对这个字段修改行为的控制，客户端可以对其随意修改。例如下面的例子中，AccessExample 拥有 id 公有字段，如果在某个时刻，我们想要使用 int 存储 id 字段，那么就需要修改所有的客户端代码。

```
public class AccessExample {
    public String id;
}
```

可以使用公有的 getter 和 setter 方法来替换公有字段，这样的话就可以控制对字段的修改行为。

```
public class AccessExample {

    private int id;

    public String getId() {
        return id + "";
    }

    public void setId(String id) {
        this.id = Integer.valueOf(id);
    }
}
```

但是也有例外，如果是包级私有的类或者私有的嵌套类，那么直接暴露成员不会有特别大的影响。

```
public class AccessWithInnerClassExample {

    private class InnerClass {
        int x;
    }

    private InnerClass innerClass;

    public AccessWithInnerClassExample() {
        innerClass = new InnerClass();
    }

    public int getValue() {
        return innerClass.x;  // 直接访问
    }
}
```

##### 抽象类与接口

**1. 抽象类**

抽象类和抽象方法都使用 abstract 关键字进行声明。如果一个类中包含抽象方法，那么这个类必须声明为抽象类。

抽象类和普通类最大的区别是，抽象类不能被实例化，只能被继承。

```
public abstract class AbstractClassExample {

    protected int x;
    private int y;

    public abstract void func1();

    public void func2() {
        System.out.println("func2");
    }
}
public class AbstractExtendClassExample extends AbstractClassExample {
    @Override
    public void func1() {
        System.out.println("func1");
    }
}
// AbstractClassExample ac1 = new AbstractClassExample(); // 'AbstractClassExample' is abstract; cannot be instantiated
AbstractClassExample ac2 = new AbstractExtendClassExample();
ac2.func1();
```

**2. 接口**

接口是抽象类的延伸，在 Java 8 之前，它可以看成是一个完全抽象的类，也就是说它不能有任何的方法实现。

从 Java 8 开始，接口也可以拥有默认的方法实现，这是因为不支持默认方法的接口的维护成本太高了。在 Java 8 之前，如果一个接口想要添加新的方法，那么要修改所有实现了该接口的类，让它们都实现新增的方法。

接口的成员（字段 + 方法）默认都是 public 的，并且不允许定义为 private 或者 protected。从 Java 9 开始，允许将方法定义为 private，这样就能定义某些复用的代码又不会把方法暴露出去。

接口的字段默认都是 static 和 final 的。

```
public interface InterfaceExample {

    void func1();

    default void func2(){
        System.out.println("func2");
    }

    int x = 123;
    // int y;               // Variable 'y' might not have been initialized
    public int z = 0;       // Modifier 'public' is redundant for interface fields
    // private int k = 0;   // Modifier 'private' not allowed here
    // protected int l = 0; // Modifier 'protected' not allowed here
    // private void fun3(); // Modifier 'private' not allowed here
}
public class InterfaceImplementExample implements InterfaceExample {
    @Override
    public void func1() {
        System.out.println("func1");
    }
}
// InterfaceExample ie1 = new InterfaceExample(); // 'InterfaceExample' is abstract; cannot be instantiated
InterfaceExample ie2 = new InterfaceImplementExample();
ie2.func1();
System.out.println(InterfaceExample.x);
```

**3. 比较**

- 从设计层面上看，抽象类提供了一种 IS-A 关系，需要满足里式替换原则，即子类对象必须能够替换掉所有父类对象。而接口更像是一种 LIKE-A 关系，它只是提供一种方法实现契约，并不要求接口和实现接口的类具有 IS-A 关系。
- 从使用上来看，一个类可以实现多个接口，但是不能继承多个抽象类。
- 接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。
- 接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限。

**4. 使用选择**

使用接口：

- 需要让不相关的类都实现一个方法，例如不相关的类都可以实现 Comparable 接口中的 compareTo() 方法；
- 需要使用多重继承。

使用抽象类：

- 需要在几个相关的类中共享代码。
- 需要能控制继承来的成员的访问权限，而不是都为 public。
- 需要继承非静态和非常量字段。

在很多情况下，接口优先于抽象类。因为接口没有抽象类严格的类层次结构要求，可以灵活地为一个类添加行为。并且从 Java 8 开始，接口也可以有默认的方法实现，使得修改接口的成本也变的很低。

- [Abstract Methods and Classes](https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html)
- [深入理解 abstract class 和 interface](https://www.ibm.com/developerworks/cn/java/l-javainterface-abstract/)
- [When to Use Abstract Class and Interface](https://dzone.com/articles/when-to-use-abstract-class-and-intreface)
- [Java 9 Private Methods in Interfaces](https://www.journaldev.com/12850/java-9-private-methods-interfaces)

##### super

- 访问父类的构造函数：可以使用 super() 函数访问父类的构造函数，从而委托父类完成一些初始化的工作。应该注意到，子类一定会调用父类的构造函数来完成初始化工作，一般是调用父类的默认构造函数，如果子类需要调用父类其它构造函数，那么就可以使用 super() 函数。
- 访问父类的成员：如果子类重写了父类的某个方法，可以通过使用 super 关键字来引用父类的方法实现。

```
public class SuperExample {

    protected int x;
    protected int y;

    public SuperExample(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public void func() {
        System.out.println("SuperExample.func()");
    }
}
public class SuperExtendExample extends SuperExample {

    private int z;

    public SuperExtendExample(int x, int y, int z) {
        super(x, y);
        this.z = z;
    }

    @Override
    public void func() {
        super.func();
        System.out.println("SuperExtendExample.func()");
    }
}
SuperExample e = new SuperExtendExample(1, 2, 3);
e.func();
SuperExample.func()
SuperExtendExample.func()
```

[Using the Keyword super](https://docs.oracle.com/javase/tutorial/java/IandI/super.html)

##### 重写与重载

**1. 重写（Override）**

存在于继承体系中，指子类实现了一个与父类在方法声明上完全相同的一个方法。

为了满足里式替换原则，重写有以下三个限制：

- 子类方法的访问权限必须大于等于父类方法；
- 子类方法的返回类型必须是父类方法返回类型或为其子类型。
- 子类方法抛出的异常类型必须是父类抛出异常类型或为其子类型。

使用 @Override 注解，可以让编译器帮忙检查是否满足上面的三个限制条件。

下面的示例中，SubClass 为 SuperClass 的子类，SubClass 重写了 SuperClass 的 func() 方法。其中：

- 子类方法访问权限为 public，大于父类的 protected。
- 子类的返回类型为 ArrayList<Integer>，是父类返回类型 List<Integer> 的子类。
- 子类抛出的异常类型为 Exception，是父类抛出异常 Throwable 的子类。
- 子类重写方法使用 @Override 注解，从而让编译器自动检查是否满足限制条件。

```
class SuperClass {
    protected List<Integer> func() throws Throwable {
        return new ArrayList<>();
    }
}

class SubClass extends SuperClass {
    @Override
    public ArrayList<Integer> func() throws Exception {
        return new ArrayList<>();
    }
}
```

在调用一个方法时，先从本类中查找看是否有对应的方法，如果没有再到父类中查看，看是否从父类继承来。否则就要对参数进行转型，转成父类之后看是否有对应的方法。总的来说，方法调用的优先级为：

- this.func(this)
- super.func(this)
- this.func(super)
- super.func(super)

```
/*
    A
    |
    B
    |
    C
    |
    D
 */


class A {

    public void show(A obj) {
        System.out.println("A.show(A)");
    }

    public void show(C obj) {
        System.out.println("A.show(C)");
    }
}

class B extends A {

    @Override
    public void show(A obj) {
        System.out.println("B.show(A)");
    }
}

class C extends B {
}

class D extends C {
}
public static void main(String[] args) {

    A a = new A();
    B b = new B();
    C c = new C();
    D d = new D();

    // 在 A 中存在 show(A obj)，直接调用
    a.show(a); // A.show(A)
    // 在 A 中不存在 show(B obj)，将 B 转型成其父类 A
    a.show(b); // A.show(A)
    // 在 B 中存在从 A 继承来的 show(C obj)，直接调用
    b.show(c); // A.show(C)
    // 在 B 中不存在 show(D obj)，但是存在从 A 继承来的 show(C obj)，将 D 转型成其父类 C
    b.show(d); // A.show(C)

    // 引用的还是 B 对象，所以 ba 和 b 的调用结果一样
    A ba = new B();
    ba.show(c); // A.show(C)
    ba.show(d); // A.show(C)
}
```

**2. 重载（Overload）**

存在于同一个类中，指一个方法与已经存在的方法名称上相同，但是参数类型、个数、顺序至少有一个不同。

应该注意的是，返回值不同，其它都相同不算是重载。

```
class OverloadingExample {
    public void show(int x) {
        System.out.println(x);
    }

    public void show(int x, String y) {
        System.out.println(x + " " + y);
    }
}
public static void main(String[] args) {
    OverloadingExample example = new OverloadingExample();
    example.show(1);
    example.show(1, "2");
}
```

### 七、反射

每个类都有一个 **Class** 对象，包含了与类有关的信息。当编译一个新类时，会产生一个同名的 .class 文件，该文件内容保存着 Class 对象。

类加载相当于 Class 对象的加载，类在第一次使用时才动态加载到 JVM 中。也可以使用 `Class.forName("com.mysql.jdbc.Driver")` 这种方式来控制类的加载，该方法会返回一个 Class 对象。

反射可以提供运行时的类信息，并且这个类可以在运行时才加载进来，甚至在编译时期该类的 .class 不存在也可以加载进来。

Class 和 java.lang.reflect 一起对反射提供了支持，java.lang.reflect 类库主要包含了以下三个类：

- **Field** ：可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段；
- **Method** ：可以使用 invoke() 方法调用与 Method 对象关联的方法；
- **Constructor** ：可以用 Constructor 的 newInstance() 创建新的对象。

**反射的优点：**

- **可扩展性** ：应用程序可以利用全限定名创建可扩展对象的实例，来使用来自外部的用户自定义类。
- **类浏览器和可视化开发环境** ：一个类浏览器需要可以枚举类的成员。可视化开发环境（如 IDE）可以从利用反射中可用的类型信息中受益，以帮助程序员编写正确的代码。
- **调试器和测试工具** ： 调试器需要能够检查一个类里的私有成员。测试工具可以利用反射来自动地调用类里定义的可被发现的 API 定义，以确保一组测试中有较高的代码覆盖率。

**反射的缺点：**

尽管反射非常强大，但也不能滥用。如果一个功能可以不用反射完成，那么最好就不用。在我们使用反射技术时，下面几条内容应该牢记于心。

- **性能开销** ：反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化。因此，反射操作的效率要比那些非反射操作低得多。我们应该避免在经常被执行的代码或对性能要求很高的程序中使用反射。
- **安全限制** ：使用反射技术要求程序必须在一个没有安全限制的环境中运行。如果一个程序必须在有安全限制的环境中运行，如 Applet，那么这就是个问题了。
- **内部暴露** ：由于反射允许代码执行一些在正常情况下不被允许的操作（比如访问私有的属性和方法），所以使用反射可能会导致意料之外的副作用，这可能导致代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。
- [Trail: The Reflection API](https://docs.oracle.com/javase/tutorial/reflect/index.html)
- [深入解析 Java 反射（1）- 基础](http://www.sczyh30.com/posts/Java/java-reflection-1/)

### 八、异常

Throwable 可以用来表示任何可以作为异常抛出的类，分为两种： **Error** 和 **Exception**。其中 Error 用来表示 JVM 无法处理的错误，Exception 分为两种：

- **受检异常** ：需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复；
- **非受检异常** ：是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。

[![img](https://camo.githubusercontent.com/8b2dfb0bfaeaf7ea0f0ae387ce2cbb0da1ec8d88a23929623cafc6bcbff044af/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f50506a77502e706e67)](https://camo.githubusercontent.com/8b2dfb0bfaeaf7ea0f0ae387ce2cbb0da1ec8d88a23929623cafc6bcbff044af/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f50506a77502e706e67)



- [Java Exception Interview Questions and Answers](https://www.journaldev.com/2167/java-exception-interview-questions-and-answersl)
- [Java提高篇——Java 异常处理](https://www.cnblogs.com/Qian123/p/5715402.html)

### 九、泛型

```
public class Box<T> {
    // T stands for "Type"
    private T t;
    public void set(T t) { this.t = t; }
    public T get() { return t; }
}
```

- [Java 泛型详解](https://www.cnblogs.com/Blue-Keroro/p/8875898.html)
- [10 道 Java 泛型面试题](https://cloud.tencent.com/developer/article/1033693)

十、注解

Java 注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。

[注解 Annotation 实现原理与自定义注解例子](https://www.cnblogs.com/acm-bingzi/p/javaAnnotation.html)

### 十一、特性

##### Java 各版本的新特性

##### **New highlights in Java SE 8**

1. Lambda Expressions

   没有使用Lambda的老方法：

   ```java
   button.addActionListener(``new` `ActionListener(){``  ``public` `void` `actionPerformed(ActionEvent actionEvent){``    ``System.out.println(``"Action detected"``);``  ``}``});
   ```

   使用Lambda：

   ```java
   button.addActionListener( actionEvent -> { ``  ``System.out.println(``"Action detected"``);``});
   ```

2. Pipelines and Streams

3. Date and Time API

4. Default Methods

5. Type Annotations

6. Nashhorn JavaScript Engine

7. Concurrent Accumulators

8. Parallel operations

9. PermGen Error Removed

##### **New highlights in Java SE 7**

1. Strings in Switch Statement
2. Type Inference for Generic Instance Creation
3. Multiple Exception Handling
4. Support for Dynamic Languages
5. Try with Resources
6. Java nio Package
7. Binary Literals, Underscore in literals
8. Diamond Syntax

- [Difference between Java 1.8 and Java 1.7?](http://www.selfgrowth.com/articles/difference-between-java-18-and-java-17)
- [Java 8 特性](http://www.importnew.com/19345.html)

##### Java 与 C++ 的区别

- Java 是纯粹的面向对象语言，所有的对象都继承自 java.lang.Object，C++ 为了兼容 C 即支持面向对象也支持面向过程。
- Java 通过虚拟机从而实现跨平台特性，但是 C++ 依赖于特定的平台。
- Java 没有指针，它的引用可以理解为安全指针，而 C++ 具有和 C 一样的指针。
- Java 支持自动垃圾回收，而 C++ 需要手动回收。
- Java 不支持多重继承，只能通过实现多个接口来达到相同目的，而 C++ 支持多重继承。
- Java 不支持操作符重载，虽然可以对两个 String 对象执行加法运算，但是这是语言内置支持的操作，不属于操作符重载，而 C++ 可以。
- Java 的 goto 是保留字，但是不可用，C++ 可以使用 goto。

[What are the main differences between Java and C++?](http://cs-fundamentals.com/tech-interview/java/differences-between-java-and-cpp.php)

##### JRE or JDK

- JRE：Java Runtime Environment，Java 运行环境的简称，为 Java 的运行提供了所需的环境。它是一个 JVM 程序，主要包括了 JVM 的标准实现和一些 Java 基本类库。
- JDK：Java Development Kit，Java 开发工具包，提供了 Java 的开发及运行环境。JDK 是 Java 开发的核心，集成了 JRE 以及一些其它的工具，比如编译 Java 源码的编译器 javac 等。

### IO流

![img](C:\Users\L\Desktop\文档\photo\iostream2xx.png)

```java
FileInputStream fileInputStream = new FileInputStream(new File("E:\\IDEA\\tests\\high-test\\net_test\\test.txt"));
        ByteArrayOutputStream baos = new ByteArrayOutputStream();

//        byte[] buffer = new byte[1024];
//        int len;
//        while((len=fileInputStream.read(buffer))!=-1){
//            baos.write(buffer,0,len);
//        }
//        System.out.println(baos.toString());
        
        FileOutputStream fileOutputStream = new FileOutputStream("E:\\IDEA\\tests\\high-test\\net_test\\test2.txt");
        byte[] buffer = new byte[1024];
        int len;
        while((len=fileInputStream.read(buffer))!=-1){
            fileOutputStream.write(buffer,0,len);
        }
```

读取文件输入流放到缓存区输出到内存管道流或通过输出流写入到文件。



## SE REVIEW 3RD

- JAVA SE
- JAVA ME --Android 
- JAVA EE

JDK 开发者工具包

JRE 运行环境

JVM Java虚拟机

手写个helloworld

```java
public class hello{
    public static void main(String[] args){
        System.out.println("Hello World~!");
    }
}
```

基本语法之

**注释**

- 行内注释//
- 多行注释/**/
- 文档注释/** */

**关键字**

abstract assert boolean break byte case catch char class const continue default do double else enum extends final finally float for goto if implements import instanceof int interface long native new package private protected public return strictfp short static super switch synchronized this throw throws transient try void volatile while

##### **不常用关键字**

**才要记**

assert 断言 为了方便调试程序 用JUnit进行替代

运行Java程序时可增加参数-enableassertions或者-ea打开断言。可通过-disableassertions或者-da关闭断言(默认情况,可有可无)。

**const** Java预留关键字，用于后期扩展用

**goto** 保留字以备扩充

**enum** 枚举

jdk1.5之前定义常量都是public static final,现在可以用枚举

```java
public class EnumTest {
    Signal color = Signal.RED;

    public void change() {
        switch (color) {
            case RED:
                color = Signal.GREEN;
                break;
            case YELLOW:
                color = Signal.RED;
                break;
            case GREEN:
                color = Signal.YELLOW;
                break;
        }
    }
    void Print(){
        System.out.println(this.color);
    }

//RED
//GREEN
    public static void main(String[] args) {
        EnumTest enumTest = new EnumTest();
        enumTest.Print();
        enumTest.change();
        enumTest.Print();
    }
}
enum Signal{
    RED, YELLOW, GREEN;
}
```

**instanceOf** 比较是否是某个类（或者子类，实现接口）的实例

**native** 设计这个关键词就是java解决不了需要去调用本地方法库(C,C++)写的

**strictfp** ***\*strict float point (精确浮点)\****

strictfp 关键字可应用于类、接口或方法。使用 strictfp 关键字声明一个方法时，该方法中所有的float和double表达式都严格遵守FP-strict的限制,符合IEEE-754规范。当对一个类或接口使用 strictfp 关键字时，该类中的所有代码，包括嵌套类型中的初始设定值和代码，都将严格地进行计算。严格约束意味着所有表达式的结果都必须是 IEEE 754 算法对操作数预期的结果，以单精度和双精度格式表示。

**transient** 

当串行化某个对象时，如果该对象的某个变量是transient，那么这个变量不会被串行化进去。也就是说，假设某个类的成员变量是transient，那么当通过

ObjectOutputStream把这个类的某个实例

保存到磁盘上时，实际上transient变量的值是不会保存的。因为当从磁盘中读出这个对象的时候，对象的该变量会没有被赋值。

**volatile (那必然要提到JMM了)** 

JMM是java内存模型，抽象为一个主内存存储共享变量，其他每个线程会独占一部分本地内存，线程里面会有所需使用的主内存的共享变量的副本，然后线程在自己的区域内操作副本，当然这可能会导致在线程还没有把自己改变完的变量写入到主内存时候，然后其他线程不知道情况下使用了主内存老的数据的副本进行缓存，使用volatile可以保证单个共享变量读写可见性。

同synchronized相比（synchronized通常称为重量级锁），volatile更轻量级

读时：会设置本地线程变量为无效，强制到主内存中读取。

写时：会把本地线程变量强制刷到主内存。

它可以保证在线程自己的内存里对变量进行写入操作后可以强制在主内存进行刷新，让其他线程的缓存失效。

它也禁止指令重排序

它只能保证单个共享变量读写原子性，并不能保证num++这种复合操作。复合操作需要用到并发包里的原子操作类JUC，通过循环CAS方式cpu支持，3变量(内存值，旧值，新值)java用unsafe类实现，以保证原子性。

```java
public static AtomicInteger num = new AtomicInteger(0);
```

volatile不支持i++这种复合操作的解决方案：

- 使用重量级锁，synchronized,ReentranLock，让线程排队
- 通过CAS方式，让其等会儿再来，java.util.concurrent包下的atomic包就是封装好的解决方案，它通过unsafe类(全部都是native操作，内存级)来完成CAS方式，现代CPU广泛支持一种对内存中的共享数据进行操作的一种特殊指令（**CAS**）有3个操作数，内存值V，旧的预期值A，要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则返回V

##### 数据类型

- 基本数据类型

  int4 short2  long8

  0b(2进) 0x(16进) 0(八进)

  double8 float4 

   byte1 char2(ascii128 utf-8 Unicode '\u0000' '\b'转义 \t \n \\ \' \") boolean1(0 or 1 if(flag))

- 引用类型

  栈指向堆

  - 类
  - 接口
  - 数组

##### 类型转换

- 自动类型转换

  - 低转高，子转父

    在子类创建前需要父类先创建，当父类只有有参构造而没有无参构造时，子类必须要有显示构造父类的有参构造方法，否则会报错

- 强制类型转换

  - 高转低，Object转其他就(Person)Object;

- 变量与常量

  type name [=value]

  - 作用域

    - 类变量
    - 实例变量
    - 局部变量

  - 常量 final static LALALA_MAX_A = 100;

  - 命名规范驼峰命名，见名知意

    类需要比如HelloController首字母大写

    常量：大写+下划线

- 运算符

  - 算术运算符

    +-*/% ++ --

  - 赋值运算符

    = += -= *= /=

  - 关系运算符

    < > != >= <= instaceof ==

  - 逻辑运算符

    || && !

  - 位运算符

    | & ^ ~ >> << >>> <<<(三个<为无符号版)

  - 条件运算符

    ? : 

    a>b? a: b

  - 扩展运算符

    += -= *= /=

- 包机制

  - public>protected>default>private
  - 域名倒写
  - package
  - import

- JavaDoc

  - JDK帮助文档就是 javadoc
    - @author
    - @Version
    - @Since
    - @Description
    - @param
    - @return
    - @throws

- 流程控制

  - Scanner 用户交互

    System.in nextlinexxxx

    System.out.println();

  - 顺序 （自上而下）

  - 选择  if单选择 ，if-else if-elseif-else switch jdk支持String,case不加break后面都会执行下去，default,这时候enum枚举就简化操作

  - 循环 while do-while for

- break & continue

  break跳出循环，continue结束本轮循环

  return结束方法运行、

##### 方法

- 修饰符 返回值 方法名 (参数名) (return xxx);

  public static void main(String[] args){}

- 方法调用

  - 类名+方法
  - 对象+方法

- 方法重载

  - 名字相同，参数列表不同

- 命令行传参，给main方法传参

  ```bash
  C:\Users\L\Desktop>java Computea lgd lgd1 lgd2
  lgd
  lgd1
  lgd2
  ```

- 可变长参数

  - ...必须放在最后一个参数

- 递归

  自己调用自己方法，记得给自己一个出口不然就是死循环Leecode

##### 数组

- 定义

  - new int[5]
  - {1,2,3,,4,5}
  - 同类型

- 数组使用

  - 通过下标拿到intsa[0]

  - ArrayIndexOutOfBounds

  - 增强for循环遍历

    for(int:num nums[]){

    ​	sout(num);

    }

  - 二维数组

    - int[ ][ ]
    - Arrays工具类
    - 排序算法
      - 冒泡
      - 快速排序
      - 选择排序
      - 插入排序
      - 归并
      - 希尔
      - 堆排序
      - 基数排序

##### 面向对象

- 类与对象

  类是对象的抽象，对象是类的具体，类是Class,一个模板，根据模板实例化一个对象

- 构造方法

  - 构造的重载
  - 有参无参(默认无参，如果手动定义了有参就必须手动再加无参构造，单例模式需要构造器私有，不能让人家new出来)

- 栈存放引用，堆存放具体对象，引用指向堆里对应对象的地址

- 封装

  属性私有，提供setter和getter以进行操作内部属性

- 继承

  Object

  extends

  子类拥有父类全部特性

  方法重写

  java为单继承

  this

  super

- 多态

  左父右子

  父类的引用指向子类的对象

  Person person = new Student();

  instanceof 如果匹配，可以进行类型间的转换，判断是否为实例的类，子类或者实现方法

- 修饰符

  - public
  - protected
  - private
  - static 
  - final
  - abstract

- 接口

  - interface

    只能定义方法名，子类必须重写其中的方法只有一个方法的接口叫做函数式接口可以使用lambda表达式

    new Thread(->{

    xxx

    }).start();

  - 接口比抽象类更加抽象

  - 可是实现多个接口

    public class a implements b,c,d{}

- 内部类

  - 局部内部类
  - 静态内部类
  - 匿名内部类*

##### 异常

- Throwable
  - Execption
    - 运行时异常
      - 1/0
      - ClassNotFound
      - NullPoint
      - UnkonwType
      - ArraysOutOfBound
    - 检查时异常
  - Error
    - AWT
    - JVM
      - StackOverFlower(栈溢出)
      - OutOfMemory(内存溢出)
- 五个关键字
  - try-catch(先小后大)
  - try-finally
  - try-catch-finally
  - throw-内部手动抛出异常
  - throws方法抛出异常
- 自定义异常
  - 继承Exception类

##### 常用类

- Object

  - hashcode()

  - toString()

  - clone()

    在未实现Cloneable接口的实例上调用 Object 的 clone 方法会导致抛出异常CloneNotSupportedException 

  - getClass()

  - nofity()

  - notifyAll()

  - wait()

  - equals()

    对于基本数据类型，用==可以比较他们的值

    对于复合数据类型(类)，比较的是引用地址，但在一些类库当中这个方法被覆盖掉了，如String,Integer,Date在这些类当中equals有其自身的实现，而不再是比较类在堆内存中的存放地址了。

    地址和内容都比较，String,Integer进行了重写，只比内容value

  - finalize

    - jvm垃圾回收相关
    - 当一个堆空间中的对象没有被栈空间变量指向的时候，这个对象会等待被java回收

- String

  当String创建时会现在缓冲区里找有没有一样的，有的话就指向它，没有就new一个。当出现第二个要创建相同值的对象时候，如果没有再new一个的要求（浪费资源，因为String一旦new出来就是final，class和value[]都 不可变，这里是jdk8所以value[]仍然是char），就指向缓冲区里的对象。

  .intern()方法的返回值还是字符串"abc"，检查字符串池里是否存在"abc"这么一个字符串，如果存在，就返回池里的字符串；如果不存在，该方法会 把"abc"添加到字符串池中，然后再返回它的引用。

  常用一些方法比如

  trim():去掉两边空格

  subString():截取字符串

  charAt(int index);//返回指定索引index位置上的字符，索引范围从0开始

  indexOf()

  split()比如string.split("@")可以得到一个String[] 这个数组是按照@进行拆分开得到的

  ```java
  String address="上海@上海市@闵行区@吴中路";
  String[] splitAddr=address.split("@");
  System.out.println(splitAddr [0]+splitAddr [1]+splitAddr [2]+splitAddr [3]);
  ```

  

  lastIndexOf(String str);查找最后一次出现的位置

  equalsIgnoreCase() //忽略大小写的情况下判断内容是否相同

  equals() //判断内容是否相同

  toLowerCase();//变小写

  toCharArray();将字符串转换成字符串数组

  valueOf(int i);//其他数据类型转String

  - StringBuffer继承自AbstractStringBuffer,实现了appendable

    源码里45个synchronized

    可变，线程安全，但是效率不高

  - StringBuilder,实现了appendable

    可变，不安全，效率相对高

- Math

  - Random	生成随机数 UUID

- **File**

  - 创建文件
  - 查看文件
  - 修改文件
  - 删除文件

- 包装类

  - 自动装箱，拆箱，valueOf
  - 八大基本类型不是类，而java面向对象所以把他们封装成类

- Date

  - Date

  - SimpleDateFormat

    yyyy-MM-dd HH-mm-ss

  - Calendar建议使用

##### 集合

![image-20220728114437310](C:\Users\L\Desktop\文档\photo\image-20220728114437310.png)

```java
			Iterator it = list.iterator(); //获取迭代器对象
			while (it.hasNext()) {         //判断是否还有元素
				Object obj = it.next();    //取出元素
				System.out.println(obj);
			}
```



- Collection

  - List

    有序，可重复

    - **ArrayList**

      基于数组，插入与删除的效率不高，随机读取快。Iterator，迭代器

      add

      remove

      contaions

      size

    - **LinkedList**

      getFirst()

      getLast()

      removeFirst()

      addFirst()

      链表，插入删除效率相对快，随机读取不如ArrayList快，就看读多还是写多用谁了。

    - Vector

    - Stack

  - Set

    无序，不可重复

    - **HashSet**

      

    - TreeSet

      

- Map

  - **HashMap** 重点

    jdk1.7是数组+链表

    容易产生死锁与环锁

    jdk1.8是数组+链表+红黑树(>8)变

    负载因子为0.75，也就是一开始默认16个容量，当数目达到12个以后扩容。如果大了查询效率低，如果小了，空间浪费。

    为什么>8会转红黑树呢？

    HashMap

    一开始是一些数组，桶，

    当重复时候就链表补上。

    数组必须为2的幂源码中是1<<4为初始值16，为了实现高存储，数据尽可能均匀，那就用hash%length(hash为奇素数如31)，这样位运算开销小于其他一些运算。

  - TreeMap

- Collections 工具类

- 泛型 jdk1.5 

  主要起约束作用，避免类型转换之间的问题

IO流

一切IO流一个进来一个出去，中间一个处理流

- InputStream
- OutputStream

字符流

- Writer
- Reader

buffer

filter过滤

Convering转换 inputstreamRead 、 O W

ObjectInputStream

DataInputSteam

Print

- 字节流 OutputStream InputStream

- 字符流 Reader Writer

- 节点流 CharArrayReader Writer inputstram outputstram

- 管道流 pipe pipedOutpuStream

- 处理流 buffer 

  - data
  - 转换流 InputStreamReader OutputStreamWriter
  - Filter
  - print

- **序列化 Serializable serialVersionUID**

  ```java
  //例如这种 较大且为static final
  private static final long erialVersionUID = 2611556444074013268L;
  ```
  
  
  
  transient 透明的 序列化就不考虑这个属性
  
  Exrernalizable

##### 多线程

- 进程与线程

- run(),start()

- 线程创建方式

  - extends Thread  start() native，java无权调用，底层交给本地方法完成

  - Runnable 函数式接口 可以用lambda表达式

    ```java
    new Thread(()->{
        System.out.print()
    }).start();
    ```

  - Callable

    可以有返回值JUC里

  - 静态代理

    对类的增强

    就是 new Thread(Runnable).start();

  - 动态代理

    利用反射 , Proxy与invocationHandler

  - Lambda表达式

    函数式编程，避免内部类定义过多，不便阅读

- 线程状态

  - 新建
  - 就绪
  - 运行
  - 阻塞
  - 死亡

- 常用方法

  - sleep
  - join
  - yield
  - isLive
  - start
  - 优先级 setPriority
  - interrupt()

- 线程同步

  多个线程操作同一个操作，并发

  - 队列+锁

  - synchronized

    - 同步方法

      弊端，锁太多了

    - **同步代码块**

      synchronized(类(默认this)){

      }

    - 第一个线程拿到锁，后面线程排队，直到这个人释放锁后面人拿到

    - 死锁：

      - 互斥
      - 请求与保持
      - 不剥夺
      - 循环等待

  - Lock(优先级高)----ReentranLock

    - lock()
    - trylock()
    - unlock()

- 线程通信

  - 缓冲区:消息队列
  - 标志位：红绿灯 flag
  - wait()
  - nofifyAll()

- 线程池

  池化技术，进来就给你先创建好几个连接池

  - 池的大小
  - 最大连接数
  - 等待时间

  ```java
  ExecutorService service = Executors.newFixedThreadPool(5);
  
  public static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory) {
          return new ThreadPoolExecutor(nThreads, nThreads,
                                        0L, TimeUnit.MILLISECONDS,
                                        new LinkedBlockingQueue<Runnable>(),
                                        threadFactory);
      }
  ```
  
  线程池参数
  
  ThreadPoolExecutor 类中参数最多的构造方法：
  
  1）corePoolSize（核心线程池大小）：核心线程池大小， 新的任务到线程池后，若线程池已创建的线程数小于corePoolSize，即便存在空闲线程，线程池会创建新的线程，直到核心线程池已满。
  
  2）maximumPoolSize（最大线程池大小）：线程池所允许的最大线程个数。当队列满了，且已创建的线程数小于maximumPoolSize，则线程池会创建新的线程来执行任务。另外，对于无界队列，可忽略该参数。
  
  3）keepAliveTime（线程保持存活时间）当线程池中线程数大于核心线程数时，线程的空闲时间如果超过线程存活时间，那么这个线程就会被销毁，直到线程池中的线程数小于等于核心线程数。
  
  4）unit（参数的时间单位）线程存活时间的单位
  
  5）workQueue（任务队列）：用于传输和保存等待执行任务的阻塞队列。
  
  6）threadFactory（线程工厂）：创建新线程。主要用于给任务线程起个自定义名字
  
  7）handler（线程饱和策略）：当线程池和队列都满了，再加入线程时会执行此策略。
  
  ![img](C:\Users\L\Desktop\文档\photo\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNDAyODU0,size_16,color_FFFFFF,t_70.png)

网络编程

- URL
  - IP 
  - port
  - Socket
- TCP
  - 三次握手
  - 四次挥手
- UDP
  - 无连接的
  - 以packet发送Dataprogram
- 聊天通信
- 文件上传

GUI(FX)

- AWT
  - Frame
  - 事件
    - 鼠标
    - 键盘
    - 窗口
    - 动作事件
- Swing
  - 文本框
  - 标签
  - 按钮
  - 文本域
  - 面板
  - 布局方式
  - 关闭窗口
  - 组件

注解与反射

- 注解
  - 元注解
  - 内置注解
  - 自定义注解
  - 反射读取注解
- 反射
  - Class
    - new Instance();
  - 类加载机制
  - Method
  - 存在重载需要参数类型
  - Field
  - Constructs
    - newInstance()
    - 获取时候需要传递class类型
  - 破坏私有关键字
    - setAccessible(true);
  - 性能分析
    - 正常>检测关闭的反射>默认的反射
  - 反射获取注解，泛型
- 单例模式探究
- Stream
- ForkJoin
  - 效率对比

### Java8：

新特性：

- 接口默认方法（”类优先”原则,自己指定调用哪个方法）

  1.抽象方法必须为public或者protected，接口public或default

  缺省情况下默认都为public

  2.接口中的变量会被隐式地指定为public static final变量

  3.抽象类可以有构造器，接口不能有构造器

  4.抽象类可以有非抽象的方法 接口不能有非抽象方法（除default和static）

  5.抽象类使用extends来继承 单继承,接口使用implements来实现，多实现

  6.抽象类速度快，接口速度慢

  7.抽象类可以包含静态代码块，接口不能包含静态代码块（可以包含static方法）

- Lambda函数式编程(如下，简化代码，匿名内部类)

- Stream流式处理(如下，像sql一样操作数据)

  并行流：一个数据分成多个块，调用多个线程处理,并行parallel(),串行sequential()

- Optional类

  (Optional<T> 类(java.util.Optional) 是一个容器类，代表一个值存在或不存在，原来用 null 表示一个值不存在，现在 Optional 可以更好的表达这个概念。并且可以避免空指针异常。)

- 新Date API（使用 LocalDate、LocalTime、LocalDateTime，不可变对象）

- 多重注解

#### Lambda参考多线程里，我貌似写了

#### Stream流

利用函数式编程，在集合类上进行复杂操作，是以内部迭代的方式处理集合数据的操作，内部迭代可以将更多的控制权交给集合类。Stream 和 Iterator 的功能类似，只是 Iterator 是以外部迭代的形式处理集合数据的操作。

比如我以前操作一个List里多条数据，需要用Iterator进行筛选判断，而现在，我可以使用流，把筛查条件作为实现函数式接口的部分。

Java 8 新提供给开发者的一组操作集合的 API，将要处理的元素集合看作一种流， 流在管道中传输， 并且可以在管道的节点上进行处理， 比如筛选、排序、聚合等。元素流在管道中经过中间操作(intermediate operation)的处理，最后由终端操作 (terminal operation) 得到前面处理的结果。Stream 流可以极大的提高开发效率，也可以使用它写出更加简洁明了的代码。

主要用于查出来的数据进行一个查询筛选

创建流方法

- list.stream()
- parallelStream()
- 静态的Stream.of()、Stream.empty()

1.intermediate  operation 中间操作：中间操作的结果是刻画、描述了一个Stream，并没有产生一个新集合，这种操作也叫做惰性求值方法。

2.terminal operation 终止操作：最终会从Stream中得到值。

![image-20220602140738060](C:\Users\L\Desktop\文档\photo\image-20220602140738060.png)

常用Stream流操作

- 把Array->Stream:Stream.of(xxx[]).collect(toList());

  由Stream中的值生成一个List列表，也可用collect(toSet())

  ```java
  		String[] testStrings = { "java", "react", "angular", "vue" };
  		
  		List<String> list = Stream.of(testStrings).collect(Collectors.toList());
   
  		for (int i = 0, length = list.size(); i < length; i++) {
  			System.out.println(list.get(i));
  		}
  ```

- map中间操作

  ```java
  //全部转大写
          List<String> collect = Stream.of(testStrings).map(test -> test.toUpperCase()).collect(toList());
  ```

- filter筛选

  ```java
  //筛选
          long j = collect.stream().filter(p -> p.startsWith("J")).count();
  ```

- flatMap 中间操作

  可用 Stream 替换值，并将多个 Stream 流合并成一个 Stream 流。

- 终止操作,max,min,reduce

  ```java
  String[] testStrings = { "java", "react", "angular", "javascript", "vue" };
  		
  		Optional<String> max = Stream.of(testStrings).max((p1, p2) -> Integer.compare(p1.length(), p2.length()));
  
  int sum = Stream.of(5, 6, 7, 8).reduce(0,
                                         (accumulator, element) -> accumulator + element);
  System.out.println(sum);
  ```

  reduce方法的第一个参数值 0 是初始值，第二个lambda表达式参数 (accumulator, element) -> accumulator + element 是执行求和操作，其中 accumulator 是累加器，element 是每次迭代的当前元素数值。

##### 实战示例

测试数据

```java
public static Student student;
    public static List<Student> students = new ArrayList<>();

    static {
        students.add(new Student("lgd1","语文",100,1));
        students.add(new Student("lgd3","语文",300,2));
        students.add(new Student("lgd2","语文",90,1));
        students.add(new Student("lgd4","数学",100,1));
        students.add(new Student("lgd5","英文",130,1));
    }


//或者
		students.add(new Student("lgd1","语文",100,1));
        students.add(new Student("lgd3","语文",300,2));
        students.add(new Student("lgd2","语文",90,1));
        students.add(new Student("lgd4","数学",100,1));
        students.add(new Student("lgd5","英文",130,1));
        students.add(new Student("lgd11","英文",130,3));
```

- 分类(group by)

  ```java
  Map<String,Map<String, List<Student>>> collect2 = students.stream().collect(Collectors.groupingBy(i -> i.getName()
                  ,Collectors.groupingBy(i -> i.getCount() > 1 ? "首考" : "重修" )
          ));
  
          for(String groupName:collect.keySet()){
              System.out.println(groupName);
              for(Integer count:collect2.get(groupName).keySet()) {
                  System.out.println(count);
                  collect2.get(groupName).get(count).forEach(student -> System.out.println(student));
              }
          }
  ```

  效果如下

  ![image-20220602161722082](C:\Users\L\Desktop\文档\photo\image-20220602161722082.png)

- 过滤(where xxx and xxx )and 可以用&& ，or 可以用||

  ```java
  //全部name转大写
  long count = students.stream().map(student -> {
              student.setName(student.getName().toUpperCase());
              return null;
          }
  ).count();
  ```
  
  ![image-20220609102109683](C:\Users\L\Desktop\文档\photo\image-20220609102109683.png)
  
  筛选lgd1的分数大于100的数据
  
- list转map与map转list

  ```java
  //List转map, (k1,k2)->k2 避免键重复 k1-取第一个数据；k2-取最后一条数据
          Map<String, String> deviceMap = students.stream().collect(Collectors.toMap(i -> i.getName(), j -> j.getSubject(), (k1, k2) -> k1));
  
          System.out.println(deviceMap.get("lgd1"));
  
          //在.map里面构造数据 return什么数据就转成什么类型的list
          List<Student> collectStu = deviceMap.entrySet().stream().map(item -> {
              Student student = new Student();
              student.setName(item.getKey());
              student.setSubject(item.getValue());
              return student;
          }).collect(Collectors.toList());
  
          for (Student student:collectStu) {
              System.out.println(student);
          }
  ```

- 求和与极值

  ```java
  //在egyList里面求cols的和
  public static BigDecimal getSumBig(List<Map<String,Object>> egyList, String cols){
          BigDecimal consuBig = egyList.stream()
                  .filter((Map m)->StringUtils.isNotEmpty(m.get(cols)+"") && !"null".equals(String.valueOf(m.get(cols)))
                          && !"-".equals(String.valueOf(m.get(cols))))
                  .map((Map m)->new BigDecimal(m.get(cols)+""))
                  .reduce(BigDecimal.ZERO,BigDecimal::add);
          return consuBig;
  }
  
  //求和与获取极值
  Integer sum = students.stream().mapToInt(Student::getScore).sum();
  OptionalInt optionalMax = students.stream().mapToInt(Student::getScore).max();
  System.out.println(optionalMax.getAsInt());
  System.out.println(sum);
  ```

- 获取最大值或者最小值的对象

  ```java
  //找到有最大分数的对象
  Optional<Student> optional = students.stream().max(Comparator.comparing(Student::getScore));
          if (optional.isPresent()) { // 判断是否有值
              Student student = optional.get();
          }
          System.out.println(optional.orElse(new Student()));
  ```

  ![image-20220610145917263](C:\Users\L\Desktop\文档\photo\image-20220610145917263.png)

- 去重

  ```java
  List<String> strings = new ArrayList<>();
          strings.add("a");
          strings.add("b");
          strings.add("b");
          //去重
          //去重之后进行拼接:
          String s = strings.stream().distinct().collect(Collectors.joining(","));
          System.out.println(s);
  
          List<String> sIdList = strings.stream().distinct().collect(Collectors.toList());
  
          for (String s1:sIdList){
              System.out.println(s1);
          }
  		
  //去重，按照字段
  ArrayList<HashMap<String, Object>> disMaps = maps.stream().collect(Collectors.collectingAndThen(
                  Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(o -> (BigDecimal) o.get("ID")))), ArrayList::new));
  //多个字段
  ArrayList<HashMap<String, Object>> disMaps = maps.stream().collect(Collectors.collectingAndThen(
                  Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(o -> (BigDecimal) o.get("ID") ";" (String) o.get("NAME")))), ArrayList::new));
  ```

  ![image-20220610150726458](C:\Users\L\Desktop\文档\photo\image-20220610150726458.png)

- 排序

  ```java
  //Integer
  Collections.sort(students, Comparator.comparing(Student::getCount));
  
  //String
  Collections.sort(students, (p1, p2) -> {
              return Integer.parseInt(String.valueOf(p1.getName().compareTo(p2.getName() + "")));
          });
  ```

  ![image-20220610152528365](/image-20220610152528365.png)

- 拼接

  ```java
  //将某个字段,按照某个字符串拼接:  List<Map<String, Object>> deviceMapList 
  String sns = deviceMapList.stream()
       	.map((m)->m.get("sn")+"").collect(Collectors.joining(","));
  
  List<String> strs = Arrays.asList("a","b","cd");
   
          //连接所有内容
          String str = strs.stream().collect(Collectors.joining());
          System.out.println(str);
          //输出：abcd
   
          //连接所有内容,中间加一个逗号隔开
          String str1 = strs.stream().collect(Collectors.joining(","));
          System.out.println(str1);
          //输出：a,b,cd
   
          //连接所有内容,中间加一个逗号隔开，两边加上括号
          String str2 = strs.stream().collect(Collectors.joining(",","(",")"));
          System.out.println(str2);
          //输出：(a,b,cd)
  ```

- 统计

  ```java
  //统计：和、数量、最大值、最小值、平均值: List<Employee> list
          IntSummaryStatistics sumStatus = students.stream().collect(Collectors.summarizingInt(Student::getScore));
          System.out.println("和：" + sumStatus.getSum());
          System.out.println("数量：" + sumStatus.getCount());
          System.out.println("最大值：" + sumStatus.getMax());
          System.out.println("最小值：" + sumStatus.getMin());
          System.out.println("平均值：" + sumStatus.getAverage());
  
  OptionalDouble average = students.stream().mapToInt(Student::getScore).average();
          System.out.println(average.getAsDouble());
  ```

- 某个字段为对应值的个数

  ```java
  //List<Employee> list
  Map<Integer, Long> collect3 = students.stream().collect(Collectors.groupingBy(i -> i.getScore(),Collectors.counting()));
  for(Integer integer: collect3.keySet()){
      System.out.println(integer + " " + collect3.get(integer));
  }
  ```

  ![image-20220610155622707](./image-20220610155622707.png)

  比如这里，分数为130的有两个

- 分区

- 反转，再变Int[]

  ```java
  list.stream().sorted((pre, next) -> -1).mapToInt(Integer::intValue).toArray();
  ```

### java 锁

Java（1.6+）中锁的状态一共有四种，级别由低到高分别是：**无锁、偏向锁、轻量级锁、重量级锁**，这几个状态会随着竞争情况逐渐升级，其中锁可以升级，但是不能降级。Java中加锁的最简单方式就是加synchronized关键字。

早期synchronized锁就是重量级锁，但是JDK1.6之后，JVM为了提高锁的获取与释放效率，对synchronized进行了优化，引入了偏向锁和轻量级锁，根据线程竞争情况对锁进行升级，在线程竞争不激烈的情况避免使用重量级锁。

![图片](http://inews.gtimg.com/newsapp_bt/0/13846791707/641)

- 无锁：对象头中有31bit的空间来存储对象的hashcode，4bit用于存放对象分代年龄，1bit来表示是否是偏向锁，2bit存放锁标志位，偏向锁位与锁标志位合起来“001”就代表无锁。无锁就是没有对任何资源进行锁定，所有线程都能访问并修改资源。
- 偏向锁：对象头中记录了获得偏向锁的线程ID，偏向锁与锁标志位合起来“101”就代表偏向锁。有研究发现，在大多数情况下，锁很少被多个线程同时竞争，而且总是由同一个线程多次获得，因此只需要将获得锁的线程ID写入到锁对象Mark Word中，相当于告诉其他线程，这块资源已经被我占了。当线程访问资源结束后，不会主动释放偏向锁，当线程再次需要访问资源时，JVM就会通过Mark Word中记录的线程ID判断是否是当前线程，如果是，则继续访问资源。所以，在没有其他线程参与竞争时，锁就一直偏向被当前线程持有，当前线程就可以一直占用资源或者执行代码。
- **自旋锁**：一旦有另外一个线程参与锁竞争，偏向锁就会升级为自旋锁，此时撤销偏向锁，锁标志位变为“00”。竞争的两个线程都在各自的线程栈帧中生成一个Lock Record空间，用于存储锁对象目前Mark Word的拷贝，用CAS操作将Mark Word设置为指向自己这个线程的LR（Lock Record）指针，设置成功者获得锁，其他参与竞争的线程如果未获取到锁，则会一直处于自旋等待的状态，直到竞争到锁。
- 重量级锁：长时间的自旋操作是很消耗CPU资源的，为了避免这种盲目的消耗，JVM会在有线程超过10次自旋，或者自旋次数超过CPU核数的一半（JDK1.6以后加入了自适应自旋-Adaptive Self Spinning，由JVM自己控制自旋次数）时，会升级到重量级锁。重量级锁底层是依赖操作系统的mutex互斥锁，也就是有操作系统来负责线程间的调度。重量级锁减少了自旋锁带来的CPU消耗，但是由于操作系统调度线程带来的线程阻塞会使程序响应速度变慢。

#### 对象头：

![img](https://img.jbzj.com/file_images/article/202108/2021082715455956.jpg)

- 如果对象没有重写hashcode方法，那么默认是调用os::random产生hashcode,可以通过System.identityHashCode获取；os::random产生hashcode的规则为:next_rand = (16807seed) mod (2*31-1),因此可以使用31位存储;另外一旦生成了hashcode,JVM会将其记录在markword中；
- GC年龄采用4位bit存储，最大为15，例如MaxTenuringThreshold参数默认值就是15；
- 当处于轻量级锁、重量级锁时，记录的对象指针，根据JVM的说明，此时认为指针仍然是64位，最低两位假定为0;当处于偏向锁时，记录的为获得偏向锁的线程指针，该指针也是64位；

#### 锁升级状态：

![img](https://img.jbzj.com/file_images/article/202108/2021082715455957.jpg)

1、当没有被当做锁的时候，这就是个普通对象，锁标志位为01，是否偏向锁为0

2、当对象被当做同步锁时，一个线程A抢到锁时，锁标志位依然是01，是否偏向锁为1，前23位记录A线程的线程ID，此时锁升级为偏向锁

3、当线程A再次试图来获得锁时，JVM发现同步锁对象的标志位是01，是否偏向锁是1，也就是偏向状态，Mark Word中记录的线程id就是线程A自己的id，表示线程A已经获得了这个偏向锁，可以执行同步锁的代码，这也是偏向锁的意义

4、当一个线程B尝试获取锁，JVM发现当前的锁处于偏向状态，并且现场ID不是B线程的ID，那么线程B会先用CAS将线程id改为自己的，这里是有可能成功的，因为A线程一般不会释放偏向锁。如果失败，则执行5

5、偏向锁抢锁失败，则说明当前锁存在一定的竞争，偏向锁就升级为轻量级锁。JVM会在当前线程的现场栈中开辟一块单独的空间，里面保存指向对象锁Mark Word的指针，同时在对象锁MarkWord中保存指向这片空间的指针。上面的保存都是CAS操作，如果竞争成功，代表线程B抢到了锁，可以执行同步代码。如果抢锁失败，则继续执行6

6、轻量级锁抢锁失败，则JVM会使用自旋锁，自旋锁并非是一个锁，则是一个循环操作，不断的尝试获取锁。从JDK1.7开始，自旋锁默认开启，自旋次数由JVM决定。如果抢锁成功，则执行同步代码；如果抢锁失败，则执行7

7、自旋锁重试之后仍然未抢到锁，同步锁会升级至重量级锁，锁标志位改为10，在这个状态下，未抢到锁的线程都会被阻塞，由Monitor来管理，并会有线程的park与unpark，因为这个存在用户态和内核态的转换，比较消耗资源，故名重量级锁

### **NIO多路复用**

**BIO(blocking I/O) 同步阻塞**
JDK1.4之前,是面向流传输的, 传输效率慢, **客户端每次发起一个请求, 建立一个连接**, 都创建一个socket对象, 不管有没有流的传输, **服务器都创建一个线程**, 但有些线程不会执行流的读写操作是无效的. 线程多了会占用过多的资源,浪费堆的内存空间(可以采用线程池去管理线程, 但是如果线程池满的情况下, 再创建新线程会等待空余线程去复用)不适合高并发情况
io的阻塞是针对流是否可以同时读写来说的,stream只是支持单向的读写

![img](https://img-blog.csdnimg.cn/20200623085602613.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pa2V3dWhhbw==,size_16,color_FFFFFF,t_70)

**NIO (non-blocking I/O)同步非阻塞**
组成部分: 通道, 缓冲区, 选择器
JDK1.4开始支持, 引入的面向缓冲区的, 缓冲区buffer支持同时读写操作, 服务器不需要对每个请求都创建线程,
执行流程: 客户端发起请求, 请求通过chanel通道注册到selector连接器上, selector进行while(true)轮询, 当轮询到到有读写操作时,才创建线程. 效率提升.适用连接数量较多,但连接较短的操作, 比如聊天室。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200623085645209.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pa2V3dWhhbw==,size_16,color_FFFFFF,t_70)

**AIO (Asynchronous I/O) 异步非阻塞**

AIO的基本流程是：用户线程通过系统调用，告知kernel内核启动某个IO操作，用户线程返回。kernel内核在整个IO操作（包括数据准备、数据复制）完成后，通知用户程序，用户执行后续的业务操作。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200623095829175.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pa2V3dWhhbw==,size_16,color_FFFFFF,t_70)

**BIO/NIO/AIO使用场景**
并发连接数不多时采用BIO，因为它编程和调试都非常简单，但如果涉及到高并发的情况，应选择NIO或AIO，更好的建议是采用成熟的网络通信框架Netty。

NIO中select、poll 、 epoll模式
文件描述符fd:
在linux中,一切皆文件,fd是非负整数,用来标明每一个被进程所打开的文件
select模式
当客户端发起一个socket请求, 在linux系统下看做一个文件描述符fd, select模式的fd存放bitmap里, 支持fd的大数量是1024。
