## 一、Java集合

<img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWRmaWxlcy5ub3djb2Rlci5jb20vaW1hZ2VzLzIwMjAwMjE1LzEwMzQ4ODRfMTU4MTczODE5OTE2Ml81MDEyNUQ1MzhDMjQ3NDhGQ0IxMDMyQ0IzOEVFRTBBOQ?x-oss-process=image/format,png" alt="img" style="zoom:;" />



Java集合两大接口：Collection和Map

Collection的子接口：

​				----List：存储的元素有序、可重复

​				----Set：存储的元素无序、不可重复

​				----Queue

**线程安全(Thread-safe)的集合对象（内部方法都使用了synchronized修饰）：**

- Vector 线程安全：
- HashTable 线程安全：
- StringBuffer 线程安全：
- Stack
- Enumeration

**非线程安全的集合对象：**

- ArrayList ：
- LinkedList：
- HashMap：
- HashSet：
- TreeMap：
- TreeSet：
- StringBulider：

### 1、List

List的常见实现类：ArrayList、LinkedList、Vector

#### ArrayList

底层用**动态Object[ ]数组**实现。数组是连续的内存空间，因此ArrayList随机访问（查询）某个元素的速度较**快**，时间复杂度为O(1)。但在指定位置 i 插入或删除某个元素的速度较**慢**，因为第 i 个及之后的元素都要执行向后/向前移一位的操作。ArrayList的**初始容量为10**，容量不足时会扩容为原来的**1.5倍**。（创建一个1.5倍容量的新数组并将原有数据拷贝到新数组）

#### LinkedList

底层用**双向链表**实现。LinkedList只能顺序访问，查询速度慢，按序号索引数据需要进行前向或后向遍历。但是插入删除数据时只需要记录本项的前后项即可，所以**插入删除速度较快**。LinkedList比ArrayList更占内存，因为LinkedList为每一个节点存储了两个引用，一个指向前一个元素，一个指向下一个元素。

LinkedList实现了Deque接口，具有add/get/remove+First/Last的操作方法，可以用作栈、队列、双端队列。

#### Vector

底层用动态Object[ ]数组实现， Vector中的相关方法前添加了**synchronized**关键字修饰，实现了**线程安全**，但性能比ArrayList差。每次扩容为原来的2倍。



### 2、Set

Set中的方法和Collection相同，没有新方法。

#### HashSet

内部基于**HashMap**实现，内部元素的位置是由哈希码来决定的。

通过存储对象的**hashCode方法和equals方法**来保证集合中元素的唯一性：1）如果传入对象的hashCode值不同，说明不是相同元素，直接存储到集合中，不用判断equals方法。2）如果传入对象的hashCode值相同，再判断对象的equals方法，如果为true，视为相同元素，不存入。如果为false，视为不同因素，存入集合。

#### TreeSet

底层使用了**红黑树**实现。如何保证集合中元素的唯一性：方法一：让传入对象实现**Comparable接口**并自定义compareTo( )比较方法；方法二：创建一个比较器的类实现**Comparator接口**并自定义compare( )比较方法，将该类对象传入TreeSet的构造函数。

#### LinkedHashSet

底层使用**哈希表和链表**实现，元素有序。



### 3、Map



#### HashMap

##### 底层数据结构：

JDK1.7：数组+链表（拉链法解决哈希冲突）；

 JDK1.8：数组+链表/红黑树（链表长度大于8时转为红黑树，小于6时转为链表，如果数组长度小于64，则会优先选择数组扩容，而不是转为红黑树）。

**HashMap的初始容量为16，负载因子为0.75，每次扩容为原来的2倍，而且哈希表（数组）的长度总是2的幂次方。**



##### HashMap存取原理：

**put方法：**key的hashcode值经过hash方法处理后得到hash值，然后通过 (length - 1) & hash得出元素应被存放的桶的位置。如果该位置没有元素就直接插入，如果该位置有元素，则遍历比较已有元素和插入元素的key是否相同，**key相同就直接覆盖**，不同就用头插法插入元素。

**get方法：**根据key的hashcode计算出元素存放的位置，然后根据**equals方法**在链表/红黑树中找到对应的值。



##### HasMap的扩容步骤：

HashMap里面默认的负载因子大小为0.75，也就是说，当Map中的元素个数**（包括数组，链表和红黑树中）**超过了16*0.75=12之后开始扩容。将会创建原来HashMap大小的两倍的bucket数组，来重新调整map的大小，并将原来的对象放入新的bucket数组中。这个过程叫作**rehashing**，因为它调用hash方法找到新的bucket位置。

但是，需要注意的是在**多线程环境**下，HashMap扩容可能会造成元素之间形成循环链表导致**死循环**。（HashMap线程不安全的体现）



##### HashMap的长度为什么是2的幂次方？

答案：HashMap的长度为2的幂次方是为了散列更均匀，**减少哈希碰撞**，提高空间利用率，**提高HashMap的存取效率**。

详细原因：

- 我们将一个键值对插入HashMap中，这个存放位置的数组下标的计算方法是

  “ `(length - 1) & hash`”（hash%length==hash&(length-1)的前提是 length 是 2 的 n 次方）。通过**将Key的hash值与length-1进行&运算**，实现了当前Key的定位，2的幂次方可以减少冲突（碰撞）的次数，提高HashMap查询效率

- **如果length为2的幂次方**，则length-1 转化为二进制必定是11111……的形式，在与h的二进制与操作效率会非常的快，而且空间不浪费

- **如果length不是2的幂次方**，比如length为15，则length-1为14，对应的二进制为1110，在与h与操作，最后一位都为0，而0001，0011，0101，1001，1011，0111，1101这几个位置永远都不能存放元素了，空间浪费相当大，更糟的是这种情况中，数组可以使用的位置比数组长度小了很多，这意味着进一步增加了碰撞的几率，减慢了查询的效率！这样就会造成空间的浪费。



#### fail-fast和fail-safe

**快速失败(fail-fast)** 是 Java 集合的一种错误检测机制。**在使用迭代器对集合进行遍历**的时候，**多个线程对集合进行结构上的改变的操作**可能就会触发 fail-fast 机制，导致抛出 `ConcurrentModificationException` 异常。

采用**安全失败机制(fail-safe)**的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，**在拷贝的集合上进行遍历**。所以，在遍历过程中对原集合所作的修改并不能被迭代器检测到，故不会抛 `ConcurrentModificationException` 异常。





## 二、Java基础

#### String, StringBuilder, StringBuffer

**1. 可变性**

- String 不可变（底层字符数组用final关键字修饰）
- StringBuffer 和 StringBuilder 可变（底层字符数组没有用final关键字修饰）

**2. 线程安全**

- String 不可变，因此是线程安全的
- StringBuilder 不是线程安全的（没有加同步锁）
- StringBuffer 是线程安全的，内部使用 synchronized 进行同步



#### 重载、重写

**重载就是同一个类中多个同名方法根据不同的传参来执行不同的逻辑处理。**

**重写就是子类对父类方法的重新改造，外部样子不能改变，内部逻辑可以改变**

| 区别点     | 重载方法 | 重写方法                                                     |
| ---------- | -------- | ------------------------------------------------------------ |
| 发生范围   | 同一个类 | 子类                                                         |
| 参数列表   | 必须修改 | 一定不能修改                                                 |
| 返回类型   | 可修改   | 子类方法返回值类型应比父类方法返回值类型更小或相等           |
| 异常       | 可修改   | 子类方法声明抛出的异常类应比父类方法声明抛出的异常类更小或相等； |
| 访问修饰符 | 可修改   | 一定不能做更严格的限制（可以降低限制）                       |
| 发生阶段   | 编译期   | 运行期                                                       |



#### 抽象类、接口：

抽象方法：只有声明没有实现(没有方法体)，需要被abstract修饰。

一个类如果包含一个抽象方法，这必然是个抽象类，而抽象类是不能实例化的，它必须通过被继承来实现其中的抽象方法；

抽象类可以没有抽象方法，也可以抽象方法和非抽象方法共存，抽象类不可以被实例化；
抽象类的子类必须重写其  所有 抽象方法后，才可以实例化，否则这个子类还是抽象类。

构造方法，类方法（用 static 修饰的方法）不能声明为抽象方法。



接口是抽象方法的集合，不能有任何方法的实现，但Java 8开始接口也可以有默认的方法实现和静态方法。

接口的变量默认修饰符为public static final，接口方法的默认修饰符为public abstract。



区别：1、接口的方法不能有实现（Java 8之前），而抽象类可以有非抽象方法。

2、接口中只能有 static final变量，而抽象类可以有其他类型变量。

3、一个类只能继承一个抽象类，但可以实现多个接口。接口本身可以通过extends继承多个接口。

4、接口的成员修饰符只能是public的，而抽象类的成员可以有public、protected 和 default多种访问权限（抽象类的变量可以为private，但抽象方法不能为private，因为抽象方法就是为了被重写的，而私有方法不能被子类继承）

有可能此对象已经不使用了，但是还有其它对象保持着此对象的引用，就会导致 GC 不能回收此对象，这种情况下就会出现内存泄漏。

#### final关键字
1.final是一个修饰符，可以修饰类，方法，变量
2.final修饰的类不能被继承
3.final修饰的方法不能被子类重写
4.final修饰的变量是一个常量，只能赋值一次



#### static关键字

静态方法只能访问所属类的静态字段和静态方法，不能访问非静态成员，实例方法则无此限制。

静态方法中不能有 this 和 super 关键字，因为这两个关键字与具体对象关联。



静态代码块：static{} 随着类的加载而执行，而且只执行一次。用于给类初始化。静态代码块先于主函数执行。

构造代码块：可以给对象进行共性初始化。

构造函数：给对象进行特性初始化。

初始化顺序：静态代码块 > 构造代码块 > 构造函数

静态变量和静态语句块优先于实例变量和普通语句块，静态变量和静态语句块的初始化顺序取决于它们在代码中的顺序。

存在继承的情况下，初始化顺序为：

- 父类（静态变量、静态代码块）
- 子类（静态变量、静态代码块）
- 父类（实例变量、普通代码块、构造函数）
- 子类（实例变量、普通代码块、构造函数）





#### Object类常用方法

##### 方法概览

```java
public final native Class<?> getClass()//native方法，用于返回当前运行时对象的Class对象，使用了final关键字修饰，故不允许子类重写。

public native int hashCode() //native方法，用于返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap。
public boolean equals(Object obj)//用于比较2个对象的内存地址是否相等，String类对该方法进行了重写用户比较字符串的值是否相等。

protected native Object clone() throws CloneNotSupportedException//naitive方法，用于创建并返回当前对象的一份拷贝。一般情况下，对于任何对象 x，表达式 x.clone() != x 为true，x.clone().getClass() == x.getClass() 为true。Object本身没有实现Cloneable接口，所以不重写clone方法并且进行调用的话会发生CloneNotSupportedException异常。

public String toString()//返回类的名字@实例的哈希码的16进制的字符串。建议Object所有的子类都重写这个方法。

public final native void notify()//native方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。

public final native void notifyAll()//native方法，并且不能重写。跟notify一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。

public final native void wait(long timeout) throws InterruptedException//native方法，并且不能重写。暂停线程的执行。注意：sleep方法没有释放锁，而wait方法释放了锁 。timeout是等待时间。

public final void wait(long timeout, int nanos) throws InterruptedException//多了nanos参数，这个参数表示额外时间（以毫微秒为单位，范围是 0-999999）。 所以超时的时间还需要加上nanos毫秒。

public final void wait() throws InterruptedException//跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念

protected void finalize() throws Throwable { }//实例被垃圾回收器回收的时候触发的操作
```



##### ==与equals()

* 对于基本数据类型，==判断两个值是否相等。
* 对于引用数据类型，==判断两个对象的内存地址是否相等，即判断是否同一个对象。

**equals()** : 它的作用也是判断两个对象是否相等。但它一般有两种使用情况：

- 情况 1：类没有覆盖 equals() 方法。则通过 equals() 比较该类的两个对象时，等价于通过“==”比较这两个对象。（因为 Object 的 equals 方法是用==比较对象的内存地址）
- 情况 2：类覆盖了 equals() 方法。一般，我们都覆盖 equals() 方法来比较两个对象的<span style="color:red">内容</span>是否相等；若它们的内容相等，则返回 true (即，认为这两个对象相等)。String类就重写了该方法，比较的是对象的值。



##### hashCode()与equals()

hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个 int 整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。说通俗一点：Java中的hashCode方法就是根据一定的规则将与对象相关的信息（比如对象的存储地址，对象的字段等）映射成一个数值，这个数值称作为散列值。

**hashCode() 在散列表中（如HashMap，Hashtable，HashSet）才有用，在其它情况下没用。**在散列表中hashCode() 的作用是获取对象的散列码，确定该类的每一个对象在散列表中的位置。

1. 如果两个对象相等，则hashCode()值一定相同，调用equals()返回true。
2. 两个对象的hashCode()值相同，它们也不一定相等，还需要通过equals()判断（可能存在哈希冲突）。但如果两个对象的hashCode()值不等，则必定是两个不同的对象。
3. **equals 方法被覆盖过，则 hashCode 方法也必须被覆盖.**



#### 异常

在 Java 中，所有的异常都有一个共同的祖先 java.lang 包中的 **Throwable 类**。Throwable： 有两个重要的子类：**Exception（异常）** 和 **Error（错误）** ，二者都是 Java 异常处理的重要子类，各自都包含大量子类。

**Error（错误）:是JVM无法处理的错误**

**Exception（异常）:是JVM程序本身可以处理的异常**。Exception 分为两种：

- **受检异常** ：需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复；
- **非受检异常** ：是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。

![img](https://snailclimb.gitee.io/javaguide/docs/java/images/Java%E5%BC%82%E5%B8%B8%E7%B1%BB%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84%E5%9B%BE.png)

![img](https://snailclimb.gitee.io/javaguide/docs/java/images/Java%E5%BC%82%E5%B8%B8%E7%B1%BB%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84%E5%9B%BE2.png)



**try-catch-finally**

- **try 块：** 用于捕获异常。其后可接零个或多个 catch 块，如果没有 catch 块，则必须跟一个 finally 块。
- **catch 块：** 用于处理 try 捕获到的异常。
- **finally 块：** 无论是否捕获或处理异常，finally 块里的语句都会被执行。当在 try 块或 catch 块中遇到 return 语句时，finally 语句块将在方法返回之前被执行。



#### 反射

newInstance()：使用Class对象的newInstance()方法来创建Class对象对应类的实例。

getMethods()：返回某个类的所有公用（public）方法，包括其继承类的公用方法。

getDeclaredMethods()：方法返回类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。

getMethod()：返回一个特定的方法，其中第一个参数为方法名称，后面的参数为方法的参数对应Class的对象。

getFiled()：访问公有的成员变量

getDeclaredField()：所有已声明的成员变量，但不能得到其父类的成员变量

`getFileds` 和 `getDeclaredFields` 方法用法同上（参照 Method）。

```Java
// 例子
package org.ScZyhSoft.common;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class test1 {
	public static void test() throws IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException {
	        Class<?> c = methodClass.class;
	        Object object = c.newInstance();
	        Method[] methods = c.getMethods();
	        Method[] declaredMethods = c.getDeclaredMethods();
	        //获取methodClass类的add方法
	        Method method = c.getMethod("add", int.class, int.class);
	        //getMethods()方法获取的所有方法
	        System.out.println("getMethods获取的方法：");
	        for(Method m:methods)
	            System.out.println(m);
	        //getDeclaredMethods()方法获取的所有方法
	        System.out.println("getDeclaredMethods获取的方法：");
	        for(Method m:declaredMethods)
	            System.out.println(m);
	    }
    }
class methodClass {

    public final int fuck = 3;
    public int add(int a,int b) {
        return a+b;
    }
    public int sub(int a,int b) {
        return a+b;
    }
}
```

程序运行的结果如下:

```
getMethods获取的方法：
public int org.ScZyhSoft.common.methodClass.add(int,int)
public int org.ScZyhSoft.common.methodClass.sub(int,int)
public final void java.lang.Object.wait() throws java.lang.InterruptedException
public final void java.lang.Object.wait(long,int) throws java.lang.InterruptedException
public final native void java.lang.Object.wait(long) throws java.lang.InterruptedException
public boolean java.lang.Object.equals(java.lang.Object)
public java.lang.String java.lang.Object.toString()
public native int java.lang.Object.hashCode()
public final native java.lang.Class java.lang.Object.getClass()
public final native void java.lang.Object.notify()
public final native void java.lang.Object.notifyAll()
getDeclaredMethods获取的方法：
public int org.ScZyhSoft.common.methodClass.add(int,int)
public int org.ScZyhSoft.common.methodClass.sub(int,int)
```

```java
// invoke()例子
public class test1 {

    public static void main(String[] args) throws IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException {
        Class<?> klass = methodClass.class;
        //创建methodClass的实例
        Object obj = klass.newInstance();
        //获取methodClass类的add方法
        Method method = klass.getMethod("add",int.class,int.class);
        //调用method对应的方法 => add(1,4)
        Object result = method.invoke(obj,1,4);
        System.out.println(result);
    }

}

class methodClass {

    public final int fuck = 3;
    public int add(int a,int b) {
        return a+b;
    }
    public int sub(int a,int b) {
        return a+b;
    }
}
```



**反射机制优缺点**

- **优点：** 运行期类型的判断，动态加载类，提高代码灵活度。
- **缺点：** 1,性能瓶颈：反射相当于一系列解释操作，通知 JVM 要做的事情，性能比直接的 java 代码要慢很多。2,安全问题，让我们可以动态操作改变类的属性同时也增加了类的安全隐患。

**反射的应用场景**

1. 我们在使用 JDBC 连接数据库时使用 `Class.forName()`通过反射加载数据库的驱动程序；
2. **Spring 框架的 IOC（动态加载管理 Bean）创建对象以及 AOP（动态代理）功能都和反射有联系；**
3. 动态配置实例的属性；





#### 动态代理

代理模式是一种比较好的理解的设计模式。简单来说就是 **我们使用代理对象来代替对真实对象(real object)的访问，这样就可以在不修改原目标对象的前提下，提供额外的功能操作，扩展目标对象的功能。**

**代理模式的主要作用是扩展目标对象的功能，比如说在目标对象的某个方法执行前后你可以增加一些自定义的操作。**



**JDK 动态代理类使用步骤**

1. 定义一个接口及其实现类；
2. 自定义 `InvocationHandler` 并重写`invoke`方法，在 `invoke` 方法中我们会调用原生方法（被代理类的方法）并自定义一些处理逻辑；
3. 通过 `Proxy.newProxyInstance(ClassLoader loader,Class[] interfaces,InvocationHandler h)` 方法创建代理对象；



**CGLIB 动态代理类使用步骤**

1. 定义一个类；
2. 自定义 `MethodInterceptor` 并重写 `intercept` 方法，`intercept` 用于拦截增强被代理类的方法，和 JDK 动态代理中的 `invoke` 方法类似；
3. 通过 `Enhancer` 类的 `create()`创建代理类；



**JDK 动态代理和 CGLIB 动态代理对比**

1. **JDK 动态代理只能代理实现了接口的类，而 CGLIB 可以代理未实现任何接口的类。** 另外， CGLIB 动态代理是通过生成一个被代理类的子类来拦截被代理类的方法调用，因此不能代理声明为 final 类型的类和方法。
2. 就二者的效率来说，大部分情况都是 JDK 动态代理更优秀，随着 JDK 版本的升级，这个优势更加明显。



#### 多线程

##### **进程与线程**

1. 进程是**资源分配**的基本单位；线程是**程序执行**的基本单位（CPU上真正运行的是线程）。
2. 一个进程可以包含多个线程。
3. 进程拥有自己的资源空间，每启动一个进程，系统就会为它分配地址空间；而线程与CPU资源分配无关，一个进程中的多个线程**共享地址空间和资源**。
4. 线程上下文的切换比进程上下文切换要快很多。



##### **Java中线程的6个状态：**

* NEW：新建状态，还没有调用start()
* RUNNABLE：运行状态
* BLOCKED：阻塞状态
* WAITING：等待状态
* TIME_WAITING：超时等待状态
* TERMINATED：销毁状态

线程创建之后它将处于 **NEW（新建）** 状态，调用 `start()` 方法后开始运行处于 **RUNNABLE（运行）** 状态，当线程执行 `wait()`方法之后，线程进入 **WAITING（等待）**状态。进入等待状态的线程需要依靠其他线程的通知（notify）才能够返回到运行状态，而 **TIME_WAITING(超时等待)** 状态相当于在等待状态的基础上增加了超时限制，比如通过 `sleep（long millis）`方法或 `wait（long millis）`方法可以将 Java 线程置于 **TIME_WAITING**状态。当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态。当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到 **BLOCKED（阻塞）** 状态。线程在执行 Runnable 的`run()`方法之后将会进入到 **TERMINATED（终止）** 状态。

![Java线程状态变迁](https://my-blog-to-use.oss-cn-beijing.aliyuncs.com/19-1-29/Java%20%E7%BA%BF%E7%A8%8B%E7%8A%B6%E6%80%81%E5%8F%98%E8%BF%81.png)

**sleep()和wait()区别**

1. sleep()为Thread的静态方法，wait()为Object的方法

2. sleep() 没有释放锁，而wait() 释放了锁

3. sleep() 方法执行完成后，线程会自动苏醒。

   wait() 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 notify() 或者 notifyAll() 方法。或者可以使用 wait(long timeout)超时后线程会自动苏醒。





##### 线程死锁

死锁是最常见的一种线程活性故障。死锁的起因是多个线程之间相互等待对方的资源释放，导致多个线程被无限期地阻塞。死锁产生的**四个必要条件**：

- **资源互斥**：一个资源每次只能被一个线程使用
- **请求与保持条件**：一个线程因请求资源而阻塞时，对已获得的资源保持不放
- **不剥夺条件**：线程已经获得的资源，在未使用完之前，不能强行剥夺
- **循环等待条件**：若干线程之间形成一种头尾相接的循环等待资源关系



避免死锁：

- **锁排序法：（破坏循环等待条件）**

**指定获取锁的顺序**，比如某个线程只有获得A锁和B锁，才能对某资源进行操作，在多线程条件下，如何避免死锁？

通过指定锁的获取顺序，比如规定，只有获得A锁的线程才有资格获取B锁，按顺序获取锁就可以避免死锁。这通常被认为是解决死锁很好的一种方法。



## 三、Spring



#### Bean的作用域

**singleton(默认)**： 在**创建容器**时就同时自动创建了一个bean的对象，不管你是否使用，他都存在了，每次获取到的对象都是同一个对象。在spring IoC容器仅存在一个Bean实例，Bean以单例方式存在。

**prototype**：Prototype是原型类型，它在我们创建容器的时候并没有实例化，而是当我们获取bean的时候才会去创建一个对象，而且我们每次获取到的对象都不是同一个对象。每次从容器中**调用**Bean时，都返回一个新的实例。

request : 每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP request内有效。

session : 每一次HTTP请求都会产生一个新的 bean，该bean仅在当前 HTTP session 内有效。

global-session： 全局session作用域，仅仅在基于portlet的web应用中才有意义，Spring5已经没有了。

#### IoC

(Inverse of Control:控制反转)，IoC是一种设计思想，就是把创建和管理bean对象的控制权交给Spring的IoC容器。**IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value）,Map 中存放的是各种对象。**

**Spring IoC的初始化过程：**

![Spring IoC的初始化过程](https://my-blog-to-use.oss-cn-beijing.aliyuncs.com/2019-7/SpringIOC%E5%88%9D%E5%A7%8B%E5%8C%96%E8%BF%87%E7%A8%8B.png)

IoC容器的初始化过程：

* **Resource定位：**

  加载XML配置文件并解析为Resource（每个文件是一个 resource）

* **BeanDefinition的载入：**

  解析Resource定位的资源（转换成DOM树解析它的</bean>等标签），根据配置文件中用户定义好的Bean**生成对应的BeanDefinition**

* **BeanDefinition的注册：**

  将BeanDefinition保存到BeanDefinitionMap中



#### BeanFactory与ApplicationContext:

`BeanFactory`是Spring里面最顶层的接口，负责读取Bean配置文档，管理Bean的加载、实例化，控制Bean的生命周期，维护Bean之间的依赖关系。

`ApplicationContext` 是 `BeanFactory` 的子接口，它简化了与 AOP 的整合、消息机制、事件机制，以及对 Web 环境的扩展（ `WebApplicationContext` 等），`BeanFactory` 是没有这些扩展的。

`ApplicationContext` 主要扩展了以下功能：

- AOP 的支持（ `AnnotationAwareAspectJAutoProxyCreator` 作用于 Bean 的初始化之后 ）

- 资源文件的管理访问（ `Resource` 抽象 ）

- 事件发布机制（ `ApplicationEvent` 、`ApplicationListener` ）

- 继承了MessageSource，消息资源处理（支持国际化）（ `LocaleResolver` ）

- 载入不同应用层的特定上下文

  

* **BeanFactory采用延迟加载**(lazy-init=true)形式来注入Bean，只有在第一次使用某个Bean时容器才调用getBean()，对该Bean进行实例化。**启动快，运行慢。**

  **优点**：应用启动的时候占用资源很少，对资源要求较高的应用，比较有优势；

  **缺点**：运行速度会相对来说慢一些。而且有可能会出现空指针异常的错误，

  

* **ApplicationContext，它是在容器启动时，一次性创建了所有的单实例Bean。启动慢，运行快，占内存**。

  **优点**：所有的Bean在启动的时候都进行了加载，系统运行的速度快；在系统启动的时候，可以发现系统中的配置问题。

  **缺点**：把费时的操作放到系统启动中完成，所有的对象都可以预加载，缺点就是内存占用较大。





#### Spring循环依赖

一个完整的对象包含两部分：当前对象实例化和对象属性的实例化。

在Spring中，对象的实例化是通过反射实现的，而对象的属性则是在对象实例化之后通过一定的方式设置的。

![img](https://pic1.zhimg.com/80/v2-666ec714ceda5e5be889b37c361d806d_720w.jpg)



- Spring是通过递归的方式获取目标bean及其所依赖的bean的；
- Spring实例化一个bean的时候，是分两步进行的，首先实例化目标bean，然后为其注入属性。

结合这两点，也就是说，Spring在实例化一个bean的时候，是首先**递归的实例化其所依赖的所有bean，直到某个bean没有依赖其他bean，此时就会将该实例返回，然后反递归的将获取到的bean设置为各个上层bean的属性的。**

例子：A和B两个Bean互相依赖。Spring尝试通过ApplicationContext.getBean()获取A的实例，不存在就创建A对象，创建时发现A的属性依赖了B对象，就递归地调用getBean()去获取创建B对象，此时发现B对象依赖了属性A，此时的A还是个**半成品**（其属性b还未初始化），仍将A返回，则B的属性a设置完成，再递归地将对象B返回给一开始的A，此时半成品的A对象也就变成了成品的A对象，因为其属性b已经设置完成了。

<img src="https://picb.zhimg.com/80/v2-abe8d96f198a33fcfd51bb2108b00004_720w.jpg" alt="img" style="zoom: 125%;" />

**总结：**

1. **构造器注入的循环依赖**：不能解决，会抛异常。你想想，对象都还在创建中，vm都还没返回给你实例，怎么注入。
2. **setter注入的循环依赖**：可以循环依赖，Spring采用了**三级缓存**解决。因为都是先初始化了实例再填充字段值的。（已实例化但未初始化的Bean放在二级缓存）
3. **非单例Bean循环依赖**：对于“prototype”作用域bean, Spring 容器无法完成依赖注入，因为Spring 容器不进行缓 存“prototype”作用域的bean ，因此无法提前暴露一个创建中的bean 。



#### Bean的生命周期

**实例化，填充属性，name，factory，applicationContext，before-init，init，after-init，调用，destory**

![Spring Bean 生命周期](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-9-17/5496407.jpg)

1. 实例化 Bean

2. 设置对象属性（依赖注入）

3. 注入Aware接口

   （如果Bean实现了`BeanNameAware`接口→调用setBeanName( )，`BeanFactoryAware`接口→setBeanFactory( )，`ApplicationContextAware`接口→setApplicationContext( )，后两个接口都是为了获取Spring容器）

4. BeanPostProcessor

   `postProcessBeforeInitialzation(Object bean, String beanName)`当前正在初始化的 Bean 对象会被传递进来，我们就可以对这个 Bean 作任何处理。这个函数会先于`InitialzationBean`执行，因此称为前置处理。

   `postProcessAfterInitialzation(Object bean, String beanName)`这个函数会在`InitialzationBean`完成后执行，因此称为后置处理。

5. InitializingBean与init-method

   如果Bean实现了`InitializingBean`接口，执行`afterPropertiesSet()`方法。

   如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。

6. 销毁Bean

   如果Bean实现了`DisposableBean`接口，会调用其实现的destroy方法

   如果这个Bean的Spring配置中配置了destroy-method属性，会自动调用其配置的销毁方法





#### SpringMVC工作原理

用户发送请求到前端控制器DispatcherServlet，再调用HandlerMapping解析出对应的Handler，再由HandlerAdapter调用该Handler并返回ModelAndView，ViewSolver解析返回视图对象View，DispatcherServlet给View渲染数据，返回给用户浏览器

**简单来说：**

客户端发送请求-> 前端控制器 DispatcherServlet 接受客户端请求 -> 找到处理器映射器 HandlerMapping 解析请求对应的 Handler-> HandlerAdapter 会根据 Handler 调用真正的处理器来处理请求，并处理相应的业务逻辑 -> 处理器返回一个模型视图 ModelAndView -> 视图解析器进行解析 -> 返回一个视图对象->前端控制器 DispatcherServlet 渲染数据（Moder）->将得到视图对象返回给用户

**如下图所示：** ![SpringMVC运行原理](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-10-11/49790288.jpg)

上图的一个笔误的小问题：Spring MVC 的入口函数也就是前端控制器 DispatcherServlet 的作用是接收请求，响应结果。

**流程说明（重要）：**

（1）客户端（浏览器）发送请求，直接请求到 DispatcherServlet。

（2）DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler。

（3）解析到对应的 Handler（也就是我们平常说的 Controller 控制器）后，开始由 HandlerAdapter 适配器处理。

（4）HandlerAdapter 会根据 Handler 来调用真正的处理器开处理请求，并处理相应的业务逻辑。

（5）处理器处理完业务后，会返回一个 ModelAndView 对象，Model 是返回的数据对象，View 是个逻辑上的 View。

（6）ViewResolver 会根据逻辑 View 查找实际的 View。

（7）DispaterServlet 把返回的 Model 传给 View（视图渲染）。

（8）把 View 返回给请求者（浏览器）









## 四、数据库

#### 事务四大特性(ACID)

1. **原子性（Atomicity）**：事务被视为不可分割的最小单元，事务的所有操作要么全部提交成功，要么全部失败回滚；（MySQL通过回滚日志保证原子性）
2. **一致性（Consistency）**：执行事务前后，数据保持一致，多个事务对同一个数据读取的结果是相同的；
3. **隔离性（Isolation）**：并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；一个事务所做的修改在最终提交以前，对其它事务是不可见的；
4. **持久性（Durability）：**一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。



#### 并发事务产生的问题

1. **丢失修改**

   指一个事务的更新操作被另外一个事务的更新操作替换；

2. **脏读**

   一个事务读取到了另一个事务修改但未提交的数据，那么该事务读到的这个数据是“脏数据”；

3. **不可重复读**

   不可重复读指在一个事务内多次读取同一数据。在这一事务还未结束前，另一事务也访问了该数据并做了修改。在第一个事务中的两次读数据之间，由于第二个事务的修改，第一次事务的两次读取的数据可能不一致。

4. **幻读**

    幻读与不可重复读类似。它发生在一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录，就好像发生了幻觉一样，所以称为幻读。

不可重复读的重点是修改比如多次读取一条记录发现其中某些**列的值被修改**，幻读的重点在于新增或者删除比如多次读取一条记录发现**记录增多或减少**了。

导致以上问题的原因是破坏了事物的隔离性



#### 事务隔离级别

* **读未提交（READ UNCOMMITTED）**：

  允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**。

* **读已提交（READ COMMITTED)**

  允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**。

* **可重复读（REPEATABLE READ)**

  对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生**。

* **可串行化（SERIALIZABLE）**

  最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。

| 隔离级别 | 脏读 | 幻影读 | 不可重复读 |
| -------- | ---- | ------ | ---------- |
| 读未提交 | √    | √      | √          |
| 读已提交 | ×    | √      | √          |
| 可重复读 | ×    | √      | ×          |
| 可串行化 | ×    | ×      | ×          |

MySQL InnoDB 存储引擎的默认事务隔离级别是 **REPEATABLE-READ（可重复读）**。







## 五、计算机网络

### TCP

#### 三次握手

**第一个报文（SYN报文）**：一开始，客户端和服务端都处于 `CLOSED` 状态。先是服务端主动监听某个端口，处于 `LISTEN` 状态。客户端随机生成一个**初始序列号ISN**，并将ISN置于TCP首部的"序号"字段中，再把标志位SYN置为1，表示SYN报文。报文发出之后客户端处于 `SYN-SENT` 状态。

**第二个报文（SYN+ACK报文）**：服务端也随机生成自己的**初始序列号**并填入TCP首部的"序号"字段，将收到的**客户端ISN + 1**填入 TCP首部的 "确认号" 字段， 并将SYN和ACK标志位置为1，最后把该报文发给客户端。之后服务端处于 `SYN-RCVD` 状态。

**第三个报文（ACK报文）**：客户端将收到的**服务端ISN + 1**填入 TCP首部的 "确认号" 字段， 并将ACK标志位置为1表示确认收到，序号为客户端ISN + 1，报文发送后客户端处于 `ESTABLISHED` 状态，服务端收到客户端的确认报文以后也进入`ESTABLISHED` 状态。前两次握手的报文不能携带数据，而第三个ACK报文是可以携带数据的。如果不携带数据则不消耗序列号。

Tips：前两个报文都要消耗掉一个序号，需要对端确认。前两个报文都不能携带数据。



**为什么是三次握手而不是两次的原因**： 

* 三次握手才可以阻止重复历史连接的初始化（主要原因） 

* 三次握手才可以同步双方的初始序列号：双方发送的携带"初始序列号"的SYN报文都需要对方回一个ACK应答报文，才能确保双方的初始序列号能被可靠的同步。









## 六、JVM

#### 堆空间：

堆空间常用参数：

-XX:+PrintFlagsInitial ：查看所有参数的默认初始值

-XX:+PrintFlagsFinal ：查看所有参数的最终值（初始值可能被修改过）

具体查看某个参数的指令：jps：查看当前运行中的线程

​												jinfo -flag SurvivorRatio 进程id

-Xms（memory start）：堆空间的初始内存（默认为物理内存的1/64）

-Xmx ：堆空间（年轻代+老年代）的最大内存（默认为物理内存的1/4）

-XX:NewRatio ：设置新生代与老年代的比例，默认是2（新:老=1:2）

-XX:SurvivorRatio ：设置新生代中Eden区与Survivor区的比例，默认是8（Eden:S0:S1=8:1:1）

-XX:MaxTenuringThreshold：设置新生代垃圾的最大年龄（默认15）

-XX:+PrintGCDetails：输出详细的GC处理日志

-XX:+PrintGC：打印GC简要信息

-XX:HandlePromotionFailure：是否设置空间分配担保（JDK7开始不再使用）

空间分配担保策略（JDK7开始）：在发生Minor GC 之前，虚拟机会检查，如果老年代的可用空间小于 **新生代所有对象的总大小** 或小于 **历次晋升到老年代的对象的平均大小**，则改为进行Full GC，否则正常执行Minor GC。

-XX:-UseAdaptiveSizePolicy ：关闭自适应的内存分配策略

-Xmn ：设置新生代的空间大小





**Minor GC / Yonung GC** 触发机制：当年轻代（指Eden区）空间不足时，就会触发Minor GC ，Survivor满不会引发GC。（每次 Minor GC 会清理Eden和Survivor）

**Major GC / Old GC** 触发机制：老年代空间不足时，会先触发Minor GC ，如果之后空间还不足，则会触发 Major GC 。如果Major GC 之后内存还不足，就报OOM

**Full GC** 触发机制：五种触发情况：

1. 调用System.gc() 时，系统建议执行Full GC ，但是不必然执行；
2. 老年代空间不足
3. 方法区空间不足
4. 通过Minor GC 后进入老年代的对象大于老年代可用内存
5. 由Eden区、S(from)区向S(to)区复制时，对象大于S(to)区可用内存，则把该对象转存到老年代，但对象仍大于老年代可用内存，则需要进行Full GC



Major GC 和 Full GC 的stw暂停时间是Minor GC 的10倍以上



Eden区满的时候触发YoungGC/Minor GC，S0和S1满的时候不会触发YGC，Eden触发YGC的时候会一起对Eden区和S区进行垃圾回收

针对幸存者S0,S1区的总结：复制之后有交换，谁空谁是to

关于垃圾回收：频繁在新生代收集，很少在老年代收集，几乎不在永久代/元空间收集

内存分配策略（对象提升规则）：

* 优先分配到Eden区(普通对象分配到TLAB上，如果对象较大则分配到Eden其他位置上)
* 经过YGC之后Eden区一定是空的（幸存对象放到Survivor区）
* Eden区和 S (from space) 区经过YGC之后的幸存对象若在S (to space) 区放不下，则直接晋升老年代
* 超大对象直接分配到老年代
* 长期存活的对象（默认年龄15）分配到老年代
* 动态对象年龄判断：如果S区中相同年龄对象的大小总和 > 1/2 S区空间大小，则大于等于该年龄的对象可以直接进入老年代。



-XX:+DoEscapeAnalysis ：启用逃逸分析（默认开启）

判断对象是否逃逸的关键就在于对象是否会在**方法外**被调用，如return 对象、给成员变量赋值 this.obj = 对象、实例引用传递

**逃逸分析**(代码优化）：

1. 栈上分配（通过标量替换实现？）:没有发生逃逸的对象，则可以分配到栈上，随着方法执行的结束，栈空间就被移除。
2. 同步省略（即锁消除）：如果同步块使用的锁对象只能被一个线程访问，那么JIT编译器在编译时会取消这部分代码的同步
3. 标量替换（将对象打散分配到栈上）：用标量值代替聚合对象的属性值，比如对象拥有 id 和 name 两个字段，那么这两个字段将会被视为两个独立的局部变量进行分配

堆是分配对象存储的唯一选择，因为标量替换





#### 方法区：

方法区在JDK 7以前的落地实现是永久代，从**JDK 8**开始的落地实现是**元空间**。

元空间的本质和永久代类似，都是对JVM规范中方法区的实现。

最大的区别在于：**元空间使用本地内存，不在虚拟机设置的内存中**。

-XX:MetaspaceSize=100m：设置元空间初始内存大小(windows下默认约21M)

-XX:MaxMetaspaceSize：设置元空间最大内存	(默认-1，表示不限制大小)



StringTable为什么要调整位置？

因为永久代的回收频率很低，只有在full gc 时（老年代或永久代空间不足）才会触发。而开发过程中会创建大量字符串，回收效率低会导致永久代内存不足。放到堆里能及时回收内存。



|         | 静态变量、字符串常量池（StringTable) | 运行时常量池、类信息 |
| ------- | ------------------------------------ | -------------------- |
| JDK 1.6 | 永久代                               | y                    |
| JDK 1.7 | 堆                                   | 永久代               |
| JDK 1.8 | 堆                                   | 元空间               |



方法区的垃圾收集主要回收两部分内容：常量池中废弃的常量和不再使用的类型。





**创建对象的步骤（宋红康)：**

1. **检查该对象对应的类是否已经被加载过**（如果还没加载，则通过类加载器查找对应的class文件，找到文件就进行类加载，找不到文件就抛出ClassNotFoundException异常）
2. **为对象分配内存**（如果内存规整----指针碰撞，如果内存不规整----空闲列表）
3. **处理并发安全问题**（两种方式：CAS失败重试、每个线程预先分配一块TLAB）
4. **在分配到的内存中为对象属性进行默认初始化**（零值初始化）
5. **设置对象的对象头**
6. **执行init方法（构造方法）进行初始化**（属性显示初始化 / 代码块初始化、构造器初始化）

ps：类具有静态变量或静态代码块时才会有clinit方法，且clinit方法是在类加载过程中的初始化阶段执行。





**对象在JVM/堆中是怎么存储的？/ Java对象头里有什么？**

对象实例在堆内存中的布局：

1. **对象头（Header)**：

   * 运行时元数据：哈希值HashCode（内存地址）、GC分代年龄（新生代晋升老年代依据）、锁状态标志（偏向锁、轻量级锁、重量级锁）、线程持有的锁、偏向线程 ID、偏向时间戳等
   * 类型指针：指向类的元数据**instanceKlass** （位于方法区）
   * 数组长度（只有数组对象才有）

2. **实例数据**：

   对象中的字段和对应的值，从父类继承的字段排在子类字段前面

3. **对齐填充字节：**

   因为JVM要求java的对象占的内存大小应该是8字节的倍数，所以后面有几个字节用于把对象的大小补齐至8字节的倍数

![image-20210215204951960](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210215204951960.png)



![image-20210215185251297](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210215185251297.png)







### 今日：MySQL事务、完善简历投jd、wph

MySQL、计算机网络、JVM



动态规划：dp数组多开一位有时候可以省去数组第一位初始化的问题，这种情况下dp数组是从第1位开始算起，而数据数组中仍是从第0位开始，则在for循环中dp数组和数据数组 (或字符串) 共用一个索引如 i 时，要注意

eg: 

```java
for(int i = 1; i <= nums.length; i++){
	
}
// 剑指 Offer 47. 礼物的最大价值
class Solution {
    public int maxValue(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[][] dp = new int[m + 1][n + 1]; // df数组多开一行一列的空间可以省去初始化问题(即以下注释)
        // dp[0][0] = grid[0][0];
        // for(int i = 1; i < n; i++){
        //     dp[0][i] = dp[0][i - 1] + grid[0][i];
        // }
        // for(int i = 1; i < m; i++){
        //     dp[i][0] = dp[i - 1][0] + grid[i][0];
        // }
        for(int i = 1; i <= m; i++){
            for(int j = 1; j <= n; j++){
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]) + grid[i - 1][j - 1];
            }
        }
        return dp[m][n];
    }
}
```





###  今日任务2020年12月13日

* [ ] LeetCode 完成动态规划部分
* [ ] LeetCode 刷5题
* [ ] 计算机网络TCP部分
* [ ] MySQL掘金小册 -- 优化部分
* [ ] 20个单词



#### MySQL 锁：

按粒度分：

* 表级锁：
  1. 表级别的S锁（共享锁/读锁）、X锁（排他锁/写锁）
  2. 表级别的IS锁（意向共享锁）、IX锁（意向排他锁），在给记录加锁时需要在表上加意向锁
  3. 表级别的自增锁
* 行级锁：
  1. record 锁 （只锁记录）
  2. gap 锁 (只锁记录前的间隙)
  3. next-key 锁（本质上是 record锁 + gap 锁）
  4. 插入意向锁
  5. 隐式锁

MySQL实现的锁都是悲观锁













说白了是没有掌握研究方法哎，把问题是什么，引言讲清楚5页，为什么要这么做，前人已经做了什么，你基于哪些东西想到，把所有理论方法写上10页，你自己怎么做，把握每个细节10页，做好了一个方法怎么评价5页，转述一个前人的方法作为对比5页，对比实验解释配图表格5页，将两种方法结合作一个新的方法10页，三种方法对比实验5页，最后还剩5页搞什么结论建议展望。以上读够了文献，认真做了学问，应该可以实现。毕设跟投稿还是不一样，投稿只需要你怎么做那10页加上实验对比5页，毕设还是要把整个东西的前世今生讲全的



背景、意义、国内外发展、前景

自我表达与沟通交流是人类与生俱来的情感需求，人与人之间通过输出交流自己的情绪与思想，找到与自身契合的朋友和同好，进而构建起自己的社会关系网络。而随着计算机与网络技术的快速发展，出现了社交网络这一概念。社交网络是人际关系在互联网世界的延续与扩展，随着网络的发展，网民花费在社交网络上的时间越来越多。根据德国市场和消费者数据公司Statista发布的一份报告的数据显示，全球互联网用户平均每天在社交媒体上花费2小时22分钟，但各国的趋势有很大不同。菲律宾人花在社交网络上的时间最多，每天花在社交媒体上的时间平均为3小时50分钟，将近4个小时。其次为尼日利亚、印度、美国。中国以1小时57分钟位列第五[1]。

我国网民在社交网络上投入的时间较长，这一现象在我国高校校园中同样存在。在互联网技术发展日新月异的今天，各种社交网络相关的应用层出不穷，而在高校校园中，论坛便是一种供学生沟通交流、结交朋友的重要社交网络应用和渠道。本系统尝试以校园论坛为基础，开发一个适应当代高校校园使用需求的社交网络平台。

\2. 国内外现状和发展趋势

440000

445200

445281

```mysql
select id,concat(substr(id,1,4),'00') from t_wcenter_dim_area where area_type is null ;

select
        id,
        substr(id,5,2),
        (case when substr(id,5,1) = '0' then substr(id,6,1)
        else substr(id,5,2) end ) 
from t_wcenter_dim_area order by id ;

update t_wcenter_dim_area set parent_id = '000000',area_type = 1 where id like '%0000';

update t_wcenter_dim_area set sort_no = (case when substr(id,5,1) = '0' then substr(id,6,1)
        else substr(id,5,2) end );
```



主要关注问题  1.选题依据，回答为什么选这个题目   2. 做什么，论文的主要内容或功能模块有哪些  3.准备怎么做，用到哪些技术或方法   4.特色和创新   讲讲如何进行创新，5.具体工作计划和安排，要求时间上合理

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210111000104085.png" alt="image-20210111000104085" style="zoom:50%;" />