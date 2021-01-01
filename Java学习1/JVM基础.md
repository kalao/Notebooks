







 



#  第一章 Java虚拟机

#### 1.0 引用JVM规范文档

> For the sake of security, the Java Virtual Machine imposes strong syntactic and
> structural constraints on the code in a class file.(为了安全起见,JVM会增强语法和结构约束)
>
> any language withfunctionality that can be expressed in terms of a valid class file can be hosted by
> the Java Virtual Machine.(任何可以被解释为class文件并遵循JVM规范的都可以被JVM支持)

#### 1.1 问题:

为什么Java没有指针,不支持多重继承,不支持操作符重载?



#### 1.2 基础

> The Java Virtual Machine is the cornerstone of the Java platform. It is the
> component of the technology responsible for its hardware- and operating system-
> independence, the small size of its compiled code, and its ability to protect users
> from malicious programs.
> The Java Virtual Machine is an abstract computing machine. Like a real computing
> machine, it has an instruction set and manipulates various memory areas at run time.
> It is reasonably common to implement a programming language using a virtual
> machine; the best-known virtual machine may be the P-Code machine of UCSD
> Pascal.

JVM是硬件和OS独立的关键技术

高级语言-(解释,编译)->汇编语言-->机器指令-->cpu



#### 1.3 Java和C++的比较

1. **Java 没有指针的概念**
2. **Java不支持多重继承。**
3. **Java 是完全面向对象的语言**，所有方法和数据都必须是**类**的一部分。除了**基本数据类型**之外，其余类型的数据都作为**对象型数据**。
4. **Java 自动进行无用内存回收操作，不再需要程序员进行手动删除。**
5. **Java 不支持操作符重载****，操作符重载被认为是 C++的突出特征。**

#### 1.4 JVM上的编程语言



#### 1.5 JVM虚拟机

JVM虚拟机就是二进制字节码的运行环境,负责将字节码转为相应平台的机器指令.

特点:

- 一次编译
- 自动内存管理
- 自动垃圾回收功能

![image-20201020172511804](https://github.com/kalao/Images/blob/master/JVM基础.md/20201020172511804.png)

#### 1.6 JVM的整体结构

- HotSpot VM是目前市面上高性能虚拟机的代表作之一
- 它采用解释器和即时编译器并存的架构
- 在今天,Java程序的运行性能有了很大提升

![image-20201020173338251](https://github.com/kalao/Images/blob/master/JVM基础.md/20201020173338251.png)

![image-20201020174251427](https://github.com/kalao/Images/blob/master/JVM基础.md/20201020174251427.png)

#### 1.7 JVM的架构模型

Java编译器输入的指令流基本上是一种基于栈的指令集架构,另一种基于寄存器的指令集架构

![image-20201020175319819](https://github.com/kalao/Images/blob/master/JVM基础.md/20201020175319819.png)

> Javap -v  test.class 对字节码文件进行反编译,可以 
>
> 零地址指令只有操作码，没有操作数。这种指令有两种情况：一是无需操作数，另一种是操作数为默认的（隐含的），默认为操作数在寄存器中，指令可直接访问寄存器。

![image-20201020200806454](https://github.com/kalao/Images/blob/master/JVM基础.md/20201020200806454.png)

#### 1.8 JVM的生命周期

 启动: JVM的启动通过引导类加载器(bootstrap class loader)创建一个初始类来完成的,这个类是由虚拟机的具体实现指定.

执行: 

一个运行中的JVM虚拟机的任务就是执行java程序

执行一个java程序,实际上真正执行的是一个**java虚拟机进程**

退出:

正常退出,因为中断退出,系统错误,

线程调用Runtime类或System类的exit方法,或Runtime的halt方法 .另外通过JNI规范描述了用JNI来加载或卸载java虚拟机时,JVM的退出情况.

#### 1.9 内存结构

![image-20201021191143831](https://github.com/kalao/Images/blob/master/JVM基础.md/20201021191143831.png)

![image-20201021191329300](https://github.com/kalao/Images/blob/master/JVM基础.md/20201021191329300.png)

![image-20201021191514712](https://github.com/kalao/Images/blob/master/JVM基础.md/20201021191514712.png)

#### 2.0 类加载器ClassLoader角色

![image-20201021193518417](https://github.com/kalao/Images/blob/master/JVM基础.md/20201021193518417.png)

![image-20201021193850629](https://github.com/kalao/Images/blob/master/JVM基础.md/20201021193850629.png)

#### 2.1 类加载过程

![image-20201021193959990](https://github.com/kalao/Images/blob/master/JVM基础.md/20201021193959990.png)

![image-20201021194107535](https://github.com/kalao/Images/blob/master/JVM基础.md/20201021194107535.png)

加载:

![image-20201021200318904](https://github.com/kalao/Images/blob/master/JVM基础.md/20201021200318904.png)

![image-20201021200454604](https://github.com/kalao/Images/blob/master/JVM基础.md/20201021200454604.png)

 链接:

![image-20201024162148226](https://github.com/kalao/Images/blob/master/JVM基础.md/20201024162148226.png)



```java
public class test {
    public static void main(String[] args) {
        System.out.println(1);
    }
}
```

参考具体解析:[https://blog.csdn.net/dreamytian/article/details/79605048]()

```
类的解析：
类的解析是将一个类的符号引用变为指向InstanceKlass对象的直接指针。指向这个对象的开头， 当创建对象的时候，这个指针会赋值给对象头中_kclass指针。 这样就定位到了该类的数据。访问类的元数据信息，是通过描述该类的类的对象实现的，当然 每个类只对应一个InstanceKlass对象。这就是类本身如何被描述的内存形态。

因为对象内部的数据在内存中的连续堆放的，当你访问一个类的某字段，是需要通过元数据InstanceKlass对象 来记录这个字段的与对象头的偏移量来获取。 当然调用对象的方法是定位到虚方法表 而不是定位到对象的内存区域
创建对象其实就是仅仅向一块内存区域写入与类元数据对应的各种字段的值，当然对象类型的值是一个引用，访问一个对象的字段的值， 是通过定位这个字段在这个对象起始地址的相对偏移量。确定相对偏移量就是在字段解析阶段完成的。

字段的解析：
字段的解析是确定一个对象的字段的访问地址，是计算相对对象起始地址的偏移量。
当第一次用getfield指令访问一个字段，字段的fieldref常量会最终解析成偏移量信息，放入cpc中，然后指令会被修改成fast_bgetfield来避免重复解析而是直接使用偏移量 以使用正确的类型 来访问字段。

方法的解析：
这里单独从元组jvm来分析 就是生成一个描述元数据的methodtable结构体。类似虚方法表。每一个类加载后，会对应一个虚方法表。
当第一次调用方法时，也就是执行invokevirtual指令，指令参数为 该方法的符号引用（包含了参数个数和类型信息，返回值类型，这样就区分了方法重载是不同的方法），也就是对应找到常量表中的methodref类型的项。（class文件中不同类型的项都有标记来标识，从而能够描述并得到这个的项的内部结构 而取到对应的值）。
解析methodref类型的项， 解析的过程通过该项的class_index项找到类信息，通过name_and_type_index项找到方法名和方法描述符，然后在ClassClass对象（类的元数据信息）中找到虚方法表，根据方法描述符找到对应指向匹配方法的下标，该下标指向methodblock*指针，也就是对应的方法内存地址入口，然后把虚方法表的下标和参数个数 写回到该类型为methodref的常量池项 比如是第二项#2。来取代之前的符号引用。也就是说符号引用变成了虚方法表的下标。这个下标就是一种直接引用的体现。
类的直接引用--> ClassClass--> methodtable - 下标 -> methodblock结构体(ClassClass)
第二次调用方法，这时候invokevirtual指令会变成invokevirtual_quick， 该指令的参数为虚方法表的下标（vtable index）和 方法的参数个数。 所以调用方法并不是直接调用方法块，而是先找到虚方法表，再去根据下标调用对应的方法块。
弄清类的加载机制必须知道class文件结构。

```



```
#14 = NameAndType        #6:#7          // "<init>":()V
#15 = Class              #21            // java/lang/System
#16 = NameAndType        #22:#23        // out:Ljava/io/PrintStream;
#17 = Class              #24            // java/io/PrintStream
#18 = NameAndType        #25:#26        // println:(I)V
#19 = Utf8               test
#20 = Utf8               java/lang/Object
#21 = Utf8               java/lang/System
#22 = Utf8               out
#23 = Utf8               Ljava/io/PrintStream;
#24 = Utf8               java/io/PrintStream
#25 = Utf8               println
#26 = Utf8               (I)V
```

初始化

![image-20201025105846610](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025105846610.png)

![image-20201025105704844](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025105704844.png)



```java
public class Clinit {
    public static void main(String[] args) {
        Runnable r=()->{
            System.out.println(Thread.currentThread().getName()+"开始");
            new DeadThread();
            System.out.println(Thread.currentThread().getName()+"结束");
        };
        Thread t1=new Thread(r,"线程一");
        Thread t2=new Thread(r,"线程二");
        t1.start();
        t2.start();
    }
}
class DeadThread{
    static {
        if(true){
            System.out.println("hi");
            while(true){}//clint会加锁
        }
    }
}
```

类加载器

![image-20201025114515077](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025114515077.png)

![image-20201025114929898](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025114929898.png)

![image-20201025163547358](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025163547358.png)

![image-20201025163627934](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025163627934.png)

```java
public class classLoader {
    public static void main(String[] args) {
        //获取系统类加载器
        ClassLoader systemLoader= ClassLoader.getSystemClassLoader();
        System.out.println(systemLoader);
        //获取系统类加载器的上层:扩展类加载器
        ClassLoader exClassLoader=systemLoader.getParent();
        System.out.println(exClassLoader);
        //获得bootstrapClassLoader
        ClassLoader bootstrapClassLoader =exClassLoader.getParent();
        System.out.println(bootstrapClassLoader);
        //获取用户自定义类的classloader,默认是系统加载器
        ClassLoader userClassLoader=classLoader.class.getClassLoader();
        System.out.println(userClassLoader);
        //String类的classloader,是bootstraploader---->java的核心类库都是用bootstrap类加载器加载的
        ClassLoader stringClassLoader=String.class.getClassLoader();
        System.out.println(stringClassLoader);
    }
}
out:
jdk.internal.loader.ClassLoaders$AppClassLoader@7c53a9eb
jdk.internal.loader.ClassLoaders$PlatformClassLoader@6debcae2
null
jdk.internal.loader.ClassLoaders$AppClassLoader@7c53a9eb
null

```

 

![image-20201025121325578](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025121325578.png)

验证bootstraploader的加载的路径

```java
//获取bootstrapclassloader能够加载的api路径
URL[] urls=sun.misc.Launcher.getBootstrapClassPath().getURLs();
for(URL element : urls){
    System.out.println(element.toExternalForm());
}
out:
        jdk1.8.0_251/jre/lib/resources.jar
        jdk1.8.0_251/jre/lib/rt.jar
        jdk1.8.0_251/jre/lib/sunrsasign.jar
        jdk1.8.0_251/jre/lib/jsse.jar
        jdk1.8.0_251/jre/lib/jce.jar
        jdk1.8.0_251/jre/lib/charsets.jar
        jdk1.8.0_251/jre/lib/jfr.jar
        jdk1.8.0_251/jre/classes
```



![image-20201025121959124](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025121959124.png)

![image-20201025155949163](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025155949163.png)

`

```java
System.out.println(Long.class.getClassLoader());
String extDirs=System.getProperty("java.ext.dirs");
for(String path :extDirs.split(":")){
    System.out.println(path);
}
out:
jdk1.8.0_251/jre/lib/ext
/usr/java/packages/lib/ext
```

`



![image-20201025151921841](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025151921841.png)

![image-20201025161028734](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025161028734.png)

![image-20201025161434681](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025161434681.png)

``

```java
public class CustomClassLoader extends ClassLoader {
    protected Class<?> findClass(String name) throws ClassNotFoundException {
            byte[] result=getClassFromCustomPath(name);
            if(result==null){
                throw new ClassNotFoundException();
            }else{
                return defineClass(name,result,0,result.length);
            }
    }
    public static byte[] getClassFromCustomPath(String name){
        //这里需要对源码进行解密的操作
        return null;
    }
    public static void main(String[] args) {
    }
}
```

``

![image-20201025164138551](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025164138551.png)

![image-20201025165701820](https://github.com/kalao/Images/blob/master/JVM基础.md/20201025165701820.png)

``

```java
ClassLoader c1=Thread.currentThread().getContextClassLoader();
System.out.println(c1);//sun.misc.Launcher$AppClassLoader@18b4aac2
ClassLoader c2=ClassLoader.getSystemClassLoader();
System.out.println(c2);//sun.misc.Launcher$AppClassLoader@18b4aac2
```

#### 2.2 双亲委派机制

![image-20201027201112850](https://github.com/kalao/Images/blob/master/JVM基础.md/20201027201112850.png)

![image-20201027202832343](https://github.com/kalao/Images/blob/master/JVM基础.md/20201027202832343.png)

![image-20201027203722760](https://github.com/kalao/Images/blob/master/JVM基础.md/20201027203722760.png)

这里rt.jar中的接口是引导类加载器加载,而相应的接口实现是由反向委托的线程上下文类加载器来加载

![image-20201027204409869](https://github.com/kalao/Images/blob/master/JVM基础.md/20201027204409869.png)

![image-20201027205135198](https://github.com/kalao/Images/blob/master/JVM基础.md/20201027205135198.png)

#### 2.3 沙箱安全机制

保证了远程代码不污染本地代码

#### 2.4 其他

![image-20201028091530486](https://github.com/kalao/Images/blob/master/JVM基础.md/20201028091530486.png)

![image-20201028092509684](https://github.com/kalao/Images/blob/master/JVM基础.md/20201028092509684.png)

# 第二章 运行时数据区的内存结构

1. 运行时数据区概述及线程
2. 程序计数器
3. 虚拟机栈
4. 本地方法接口
5. 本地方法栈
6. 堆
7. 方法区
8. 直接内存
9. 执行引擎

![image-20201028092935843](https://github.com/kalao/Images/blob/master/JVM基础.md/20201028092935843.png)

#### 2.1 运行时数据区概述及线程

![image-20201028094026534](https://github.com/kalao/Images/blob/master/JVM基础.md/20201028094026534.png)



![image-20201028094515580](https://github.com/kalao/Images/blob/master/JVM基础.md/20201028094515580.png)

![image-20201028093953613](https://github.com/kalao/Images/blob/master/JVM基础.md/20201028093953613.png)

![image-20201028095053942](https://github.com/kalao/Images/blob/master/JVM基础.md/20201028095053942.png)

![image-20201028095726287](https://github.com/kalao/Images/blob/master/JVM基础.md/20201028095726287.png)



![image-20201028100241514](https://github.com/kalao/Images/blob/master/JVM基础.md/20201028100241514.png)

#### 2.2 程序计数器

![image-20201101153342915](https://github.com/kalao/Images/blob/master/JVM基础.md/20201101153342915.png)

![image-20201101154118550](https://github.com/kalao/Images/blob/master/JVM基础.md/20201101154118550.png)

![image-20201101153851628](https://github.com/kalao/Images/blob/master/JVM基础.md/20201101153851628.png)

![image-20201101154240665](https://github.com/kalao/Images/blob/master/JVM基础.md/20201101154240665.png)

对于栈和pc,既没有GC也没有OOM



```
 Last modified Nov 1, 2020; size 417 bytes
  MD5 checksum 77702dd61b1cea7060685cfb1034fd23
  Compiled from "test.java"
public class test
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #6.#15         // java/lang/Object."<init>":()V
   #2 = String             #16            // abc
   #3 = Fieldref           #17.#18        // java/lang/System.out:Ljava/io/PrintStream;
   #4 = Methodref          #19.#20        // java/io/PrintStream.println:(I)V
   #5 = Class              #21            // test
   #6 = Class              #22            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               main
  #12 = Utf8               ([Ljava/lang/String;)V
  #13 = Utf8               SourceFile
  #14 = Utf8               test.java
  #15 = NameAndType        #7:#8          // "<init>":()V
  #16 = Utf8               abc
  #17 = Class              #23            // java/lang/System
  #18 = NameAndType        #24:#25        // out:Ljava/io/PrintStream;
  #19 = Class              #26            // java/io/PrintStream
  #20 = NameAndType        #27:#28        // println:(I)V
  #21 = Utf8               test
  #22 = Utf8               java/lang/Object
  #23 = Utf8               java/lang/System
  #24 = Utf8               out
  #25 = Utf8               Ljava/io/PrintStream;
  #26 = Utf8               java/io/PrintStream
  #27 = Utf8               println
  #28 = Utf8               (I)V
{
  public test();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 1: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=5, args_size=1
         0: bipush        10
         2: istore_1
         3: bipush        20
         5: istore_2
         6: iload_1
         7: iload_2
         8: iadd
         9: istore_3
        10: ldc           #2                  // String abc
        12: astore        4
        14: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
        17: iload_1
        18: invokevirtual #4                  // Method java/io/PrintStream.println:(I)V
        21: return
      LineNumberTable:
        line 3: 0
        line 4: 3
        line 5: 6
        line 6: 10
        line 7: 14
        line 9: 21
}

```

```
   10: ldc           		#2            // String abc,指向常量池#2
   #2 = String              #16           // abc,指向#16
   #16 = Utf8               abc
   
```

```
         0: bipush        10
         2: istore_1
         3: bipush        20
         5: istore_2
         6: iload_1
         7: iload_2
         8: iadd
         9: istore_3
        10: ldc           #2                  // String abc
        12: astore        4
        14: getstatic     #3                  // Field 
 这里的0,2,3,5...就是所谓的偏移量.从10到12含有两个小指令,寻找abc常量
```

![image-20201101192936383](https://github.com/kalao/Images/blob/master/JVM基础.md/20201101192936383.png)

```
执行引擎在pc寄存器上获得指令地址,然后去获得相应的操作指令.获得指令后会操作局部变量表,操作数栈.最后解析为机器指令.
```

```
pc寄存器的存储字节码指令的地址是因为由于并发的原因,cpu不断切换线程,因而必须有个可以记录线程执行到哪了(下一个应该执行哪个)
```

#### 2.3 虚拟机栈

1 虚拟机栈概述

2 栈的存储单位

3 局部变量表

4 操作数栈

5 代码追踪

6 栈顶缓存技术

7 动态链接

8 方法的调用:解析与分派

9 方法返回地址

10 一些附加信息

11 栈的相关面试

##### 2.1.1 虚拟机栈概述

```
由于JVM指令是根据栈来设计的,由于需要跨平台,而基于栈是比基于寄存器更容易实现,但是性能会下降,而且由于指令小,需要更多的指令.
```

![image-20201101200921355](https://github.com/kalao/Images/blob/master/JVM基础.md/20201101200921355.png)

```
Java虚拟机栈是每个线程在创建时都会创建一个虚拟机栈,其内部保存一个个栈帧,对应着一次次的JAVA方法的调用.(之前提到PC寄存器也是私有的),因而生命周期也是随着线程的,Java程序的运行需要栈这种存储结构,其主要形式是存储(栈帧)方法的局部变量,部分结果,并参与方法的调用和返回. 
```

![image-20201101202214911](https://github.com/kalao/Images/blob/master/JVM基础.md/20201101202214911.png)

![image-20201101202431939](https://github.com/kalao/Images/blob/master/JVM基础.md/20201101202431939.png)

> 栈中可能出现的异常,JVM规范允许Java的栈的大小是动态的或者固定不变的.

- 如果采用固定大小的Java虚拟机栈,那么每个虚拟机栈的大小在创建线程的时候就独立选定.如果线程请求分配超过Java虚拟机允许的最大容量,就会抛出StackOverflowError异常
- 如果JVM虚拟机允许动态扩展,在请求的时候没有申请到足够的内存或者在一开始创建线程的时候没有足够的内存去创建虚拟机栈,那么就会抛出OutOfMemoryError异常

例子

```Java
public class StackOverTest {
    /***
     * 默认情况是 count:10777
     * 设置栈的大小(run->Edit COnfigurations->Application->VM options) -Xss256k count:1825
     */
    private static int count=1;
    public static void main(String[] args) {
        System.out.println(count);
        count++;
        main(args);
    }
}
```

##### 2.1.2 栈的存储单位

![image-20201103102328028](https://github.com/kalao/Images/blob/master/JVM基础.md/20201103102328028.png)

 

![image-20201103102750981](https://github.com/kalao/Images/blob/master/JVM基础.md/20201103102750981.png)

  ![image-20201103103220572](https://github.com/kalao/Images/blob/master/JVM基础.md/20201103103220572.png)

![image-20201103103328152](https://github.com/kalao/Images/blob/master/JVM基础.md/20201103103328152.png)

> 不同的线程之间的栈帧是不允许相互引用的,线程只共享同一个进程下的堆空间和方法区域.
>
> GCIH可以实现不同的JVM实例进行通信. 
>
> 在Java中,方法的退出有两种方法,第一种方法是正常的退出,第二种方法是未处理的异常的抛出.

![image-20201103182957094](https://github.com/kalao/Images/blob/master/JVM基础.md/20201103182957094.png)

 ![image-20201103184358800](https://github.com/kalao/Images/blob/master/JVM基础.md/20201103184358800.png)

##### 2.1.3 栈帧

###### 2.1.3.1  局部变量表

![image-20201103184928378](https://github.com/kalao/Images/blob/master/JVM基础.md/20201103184928378.png)

![image-20201103192005931](https://github.com/kalao/Images/blob/master/JVM基础.md/20201103192005931.png)

![image-20201103192046937](https://github.com/kalao/Images/blob/master/JVM基础.md/20201103192046937.png)

![image-20201104141551326](https://github.com/kalao/Images/blob/master/JVM基础.md/20201104141551326.png)

LineNumberTable:代码行号与相对偏移的行号

![image-20201104144856825](https://github.com/kalao/Images/blob/master/JVM基础.md/20201104144856825.png)

局部变量表中的StartPC和Length共同构成这个变量的作用域

![image-20201104145223082](https://github.com/kalao/Images/blob/master/JVM基础.md/20201104145223082.png)

![image-20201104150110283](https://github.com/kalao/Images/blob/master/JVM基础.md/20201104150110283.png)

![image-20201104150602744](https://github.com/kalao/Images/blob/master/JVM基础.md/20201104150602744.png)

![image-20201104151301154](https://github.com/kalao/Images/blob/master/JVM基础.md/20201104151301154.png)

```java
public void fun(){
	int a=0;
    {
    	int b=0;
    	b=a+1;
    	
	}
	int c=a+1;
}
```

![image-20201104151902653](https://github.com/kalao/Images/blob/master/JVM基础.md/20201104151902653.png)



变量的分类

按照数据类型分:

- 基本的数据类型
- 引用数据类型

按照在类中声明的位置分

- 成员变量(在使用前都经历过默认的初始化赋值)
  - ​	类变量:链接的prepare阶段,给类变量默认赋值;initial阶段,给类变量赋值
  - 实例变量:随着对象的创建,会在堆中分配实例变量空间,进行默认赋值
- 局部变量
  - **在使用前必须显式赋值.**

![image-20201104152723203](https://github.com/kalao/Images/blob/master/JVM基础.md/20201104152723203.png)

###### 2.1.3.2   操作数栈

![image-20201107195348752](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107195348752.png)

![image-20201107195538311](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107195538311.png)

![image-20201107195810591](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107195810591.png)

```
字节码指令需要变成机器指令,然后操作操作数栈.
```

![image-20201107200519034](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107200519034.png)



![image-20201107201631607](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107201631607.png)



![image-20201107201717028](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107201717028.png)



![image-20201107201922558](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107201922558.png)

![image-20201107202000712](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107202000712.png)







```
bipush 压入操作数栈
istore_index 压入局部变量表中
iload_index 将索引为index的数据从局部变量表到操作数栈 

```





![image-20201107202358452](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107202358452.png)

![image-20201107202431254](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107202431254.png)

```
在数据push到操作数栈的时候,对于上图的m,当其在byte范围,它指令为bipush,之后将byte转换为int,当其在short范围(800),之后将其转为int,如果超过int,那么在编译的时候,就会报错.
```

![image-20201107203319436](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107203319436.png)

![image-20201107203336597](https://github.com/kalao/Images/blob/master/JVM基础.md/20201107203336597.png)

```
aload0:加载this
invokevirtual 调用实例方法   ???没有解释
```

######  2.1.3.3 栈顶缓存技术

![image-20201109091400002](https://github.com/kalao/Images/blob/master/JVM基础.md/20201109091400002.png)



###### 2.1.3.4  动态链接 

![image-20201109091832174](https://github.com/kalao/Images/blob/master/JVM基础.md/20201109091832174.png)

```
#13 ()V 空参数
#11 I int 类型  


```

![image-20201110204632427](https://github.com/kalao/Images/blob/master/JVM基础.md/20201110204632427.png)  

 

```
为什么需要运行时常量池?
代码在还没运行就保存一份副本在栈帧中,浪费内存空间.
```

###### 2.1.3.5 绑定方式

![image-20201111133910610](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111133910610.png)

```
动态还是静态在于在编译期是否确定.
```

![image-20201111134203351](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111134203351.png)

![image-20201111142619874](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111142619874.png)

![image-20201111143054078](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111143054078.png)

![image-20201111143002537](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111143002537.png)

![image-20201111142747894](/home/kalao/.config/Typora/typora-user-images/image-20201111142747894.png  )

###### 2.1.3.6  虚方法和非虚方法

![image-20201111143400883](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111143400883.png) 



```
虚方法成立的必要条件是类的继承和重写.
即如果条件之一不成立,那么肯定不是虚函数
```



![image-20201111145547794](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111145547794.png)

![image-20201111151652086](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111151652086.png)

![image-20201111151730545](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111151730545.png)

 

###### 2.1.3.7 动态类型语言和静态类型语言

![image-20201111152431053](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111152431053.png)

![image-20201111154545266](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111154545266.png)

![image-20201111154639839](https://github.com/kalao/Images/blob/master/JVM基础.md/20201111154639839.png)

###### 2.1.3.8 方法重写 的本质

![image-20201114112912110](https://github.com/kalao/Images/blob/master/JVM基础.md/20201114112912110.png)

```
当调用一个属性或者方法的时候,通过不断的往父类查找,查不到就是抽象方法或者接口,但是如果是抽象方法,那么本身就是抽像类,所以只能是接口. 
```

![image-20201114182707703](https://github.com/kalao/Images/blob/master/JVM基础.md/20201114182707703.png)



![image-20201114183709018](https://github.com/kalao/Images/blob/master/JVM基础.md/20201114183709018.png)





![image-20201114184021301](https://github.com/kalao/Images/blob/master/JVM基础.md/20201114184021301.png)

![image-20201114184412739](https://github.com/kalao/Images/blob/master/JVM基础.md/20201114184412739.png)

###### 2.1.3.9 方法返回地址

![image-20201114190325180](https://github.com/kalao/Images/blob/master/JVM基础.md/20201114190325180.png)

![image-20201114203708787](https://github.com/kalao/Images/blob/master/JVM基础.md/20201114203708787.png)

![image-20201114204117508](https://github.com/kalao/Images/blob/master/JVM基础.md/20201114204117508.png)



![image-20201114204505131](https://github.com/kalao/Images/blob/master/JVM基础.md/20201114204505131.png)

######  2.1.3.10 面试题

![image-20201115153824263](https://github.com/kalao/Images/blob/master/JVM基础.md/20201115153824263.png)

```
局部变量s1是否是线程安全的?
是否线程安全就是看是否被多个线程调用?
这里的s1作为返回值,这样有可能会被其他线程争用.
如果局部变量内部无多线程环境,且不作为返回值,那么局部变量是线程私有的,只会调用那个方法的线程私有.
```



#### 2.4 本地方法

##### 2.4.1 本地方法

![image-20201115160420905](https://github.com/kalao/Images/blob/master/JVM基础.md/20201115160420905.png)

![image-20201115155326700](https://github.com/kalao/Images/blob/master/JVM基础.md/20201115155326700.png)

![image-20201115155531354](https://github.com/kalao/Images/blob/master/JVM基础.md/20201115155531354.png)

![image-20201115160350642](https://github.com/kalao/Images/blob/master/JVM基础.md/20201115160350642.png)

##### 2.4.2 本地方法栈

![image-20201115161000220](https://github.com/kalao/Images/blob/master/JVM基础.md/20201115161000220.png)







# 附录.jvm指令集

​                       **JVM指令集（指令码、助记符、功能描述）**

| **指令码** | **助记符**          | **功能描述**                                                 |
| ---------- | ------------------- | ------------------------------------------------------------ |
| 0x00       | **nop**             | 无操作                                                       |
|            |                     |                                                              |
| 0x01       | **aconst_null**     | 指令格式： aconst_null 功能描述： null进栈。 指令执行前指令执行后栈底...... null栈顶 注意：JVM并没有为null指派一个具体的值。 |
|            |                     |                                                              |
| 0x02       | **iconst_m1**       | int型常量值-1进栈                                            |
| 0x03       | **iconst_0**        | int型常量值0进栈                                             |
| 0x04       | **iconst_1**        | int型常量值1进栈                                             |
| 0x05       | **iconst_2**        | int型常量值2进栈                                             |
| 0x06       | **iconst_3**        | int型常量值3进栈                                             |
| 0x07       | **iconst_4**        | int型常量值4进栈                                             |
| 0x08       | **iconst_5**        | int型常量值5进栈                                             |
|            |                     |                                                              |
| 0x09       | **lconst_0**        | long型常量值0进栈                                            |
| 0x0A       | **lconst_1**        | long型常量值1进栈                                            |
|            |                     |                                                              |
| 0x0B       | **fconst_0**        | float型常量值0进栈                                           |
| 0x0C       | **fconst_1**        | float型常量值1进栈                                           |
| 0x0D       | **fconst_2**        | float型常量值2进栈                                           |
|            |                     |                                                              |
| 0x0E       | **dconst_0**        | double型常量值0进栈                                          |
| 0x0F       | **dconst_1**        | double型常量值1进栈                                          |
|            |                     |                                                              |
| 0x10       | **bipush**          | 将一个byte型常量值推送至栈顶                                 |
| 0x11       | **sipush**          | 将一个short型常量值推送至栈顶                                |
|            |                     |                                                              |
| 0x12       | **ldc**             | 将int、float或String型常量值从常量池中推送至栈顶             |
| 0x13       | **ldc_w**           | 将int、float或String型常量值从常量池中推送至栈顶（宽索引）   |
| 0x14       | **ldc2_w**          | 将long或double型常量值从常量池中推送至栈顶（宽索引）         |
|            |                     |                                                              |
| 0x15       | **iload**           | 指定的int型局部变量进栈                                      |
| 0x16       | **lload**           | 指定的long型局部变量进栈                                     |
| 0x17       | **fload**           | 指定的float型局部变量进栈                                    |
| 0x18       | **dload**           | 指定的double型局部变量进栈                                   |
| 0x19       | **aload**           | 指令格式： aload index 功能描述： 当前frame的局部变量数组中下标为      index的引用型局部变量进栈 指令执行前指令执行后栈底...... objectref栈顶 index ： 无符号一byte整型。和wide指令联用，      可以使index为两byte。 |
|            |                     |                                                              |
| 0x1A       | **iload_0**         | 第一个int型局部变量进栈                                      |
| 0x1B       | **iload_1**         | 第二个int型局部变量进栈                                      |
| 0x1C       | **iload_2**         | 第三个int型局部变量进栈                                      |
| 0x1D       | **iload_3**         | 第四个int型局部变量进栈                                      |
|            |                     |                                                              |
| 0x1E       | **lload_0**         | 第一个long型局部变量进栈                                     |
| 0x1F       | **lload_1**         | 第二个long型局部变量进栈                                     |
| 0x20       | **lload_2**         | 第三个long型局部变量进栈                                     |
| 0x21       | **lload_3**         | 第四个long型局部变量进栈                                     |
|            |                     |                                                              |
| 0x22       | **fload_0**         | 第一个float型局部变量进栈                                    |
| 0x23       | **fload_1**         | 第二个float型局部变量进栈                                    |
| 0x24       | **fload_2**         | 第三个float型局部变量进栈                                    |
| 0x25       | **fload_3**         | 第四个float型局部变量进栈                                    |
|            |                     |                                                              |
| 0x26       | **dload_0**         | 第一个double型局部变量进栈                                   |
| 0x27       | **dload_1**         | 第二个double型局部变量进栈                                   |
| 0x28       | **dload_2**         | 第三个double型局部变量进栈                                   |
| 0x29       | **dload_3**         | 第四个double型局部变量进栈                                   |
|            |                     |                                                              |
| 0x2A       | **aload_0**         | 指令格式：aload_0 该指令的行为类似于aload指令index为0的情况。 |
| 0x2B       | **aload_1**         | 同上                                                         |
| 0x2C       | **aload_2**         | 同上                                                         |
| 0x2D       | **aload_3**         | 同上                                                         |
|            |                     |                                                              |
| 0x2E       | **iaload**          | 指定的int型数组的指定下标处的值进栈                          |
| 0x2F       | **laload**          | 指定的long型数组的指定下标处的值进栈                         |
| 0x30       | **faload**          | 指定的float型数组的指定下标处的值进栈                        |
| 0x31       | **daload**          | 指定的double型数组的指定下标处的值进栈                       |
| 0x32       | **aaload**          | 指令格式： aaload 功能描述： 栈顶的数组下标（index）、数组引用      （arrayref）出栈，并根据这两个数值      取出对应的数组元素值（value）进栈。 抛出异常： 如果arrayref的值为null，会抛出      NullPointerException。      如果index造成数组越界，会抛出      ArrayIndexOutOfBoundsException。 指令执行前指令执行后栈底......arrayrefvalueindex 栈顶 index   ： int类型arrayref  ： 数组的引用 |
| 0x33       | **baload**          | 指定的boolean或byte型数组的指定下标处的值进栈                |
| 0x34       | **caload**          | 指定的char型数组的指定下标处的值进栈                         |
| 0x35       | **saload**          | 指定的short型数组的指定下标处的值进栈                        |
|            |                     |                                                              |
| 0x36       | **istore**          | 将栈顶int型数值存入指定的局部变量                            |
| 0x37       | **lstore**          | 将栈顶long型数值存入指定的局部变量                           |
| 0x38       | **fstore**          | 将栈顶float型数值存入指定的局部变量                          |
| 0x39       | **dstore**          | 将栈顶double型数值存入指定的局部变量                         |
| 0x3A       | **astore**          | 指令格式： astore index 功能描述： 将栈顶数值（objectref）存入当前      frame的局部变量数组中指定下标      （index）处的变量中，栈顶数值出栈。 指令执行前指令执行后栈底......objectref 栈顶 index ： 无符号一byte整数。该指令和wide联      用，index可以为无符号两byte整数。 |
|            |                     |                                                              |
| 0x3B       | **istore_0**        | 将栈顶int型数值存入第一个局部变量                            |
| 0x3C       | **istore_1**        | 将栈顶int型数值存入第二个局部变量                            |
| 0x3D       | **istore_2**        | 将栈顶int型数值存入第三个局部变量                            |
| 0x3E       | **istore_3**        | 将栈顶int型数值存入第四个局部变量                            |
|            |                     |                                                              |
| 0x3F       | **lstore_0**        | 将栈顶long型数值存入第一个局部变量                           |
| 0x40       | **lstore_1**        | 将栈顶long型数值存入第二个局部变量                           |
| 0x41       | **lstore_2**        | 将栈顶long型数值存入第三个局部变量                           |
| 0x42       | **lstore_3**        | 将栈顶long型数值存入第四个局部变量                           |
|            |                     |                                                              |
| 0x43       | **fstore_0**        | 将栈顶float型数值存入第一个局部变量                          |
| 0x44       | **fstore_1**        | 将栈顶float型数值存入第二个局部变量                          |
| 0x45       | **fstore_2**        | 将栈顶float型数值存入第三个局部变量                          |
| 0x46       | **fstore_3**        | 将栈顶float型数值存入第四个局部变量                          |
|            |                     |                                                              |
| 0x47       | **dstore_0**        | 将栈顶double型数值存入第一个局部变量                         |
| 0x48       | **dstore_1**        | 将栈顶double型数值存入第二个局部变量                         |
| 0x49       | **dstore_2**        | 将栈顶double型数值存入第三个局部变量                         |
| 0x4A       | **dstore_3**        | 将栈顶double型数值存入第四个局部变量                         |
|            |                     |                                                              |
| 0x4B       | **astore_0**        | 指令格式： astore_0 功能描述： 该指令的行为类似于astore指令index      为0的情况。 |
| 0x4C       | **astore_1**        | 同上                                                         |
| 0x4D       | **astore_2**        | 同上                                                         |
| 0x4E       | **astore_3**        | 同上                                                         |
|            |                     |                                                              |
| 0x4F       | **iastore**         | 将栈顶int型数值存入指定数组的指定下标处                      |
| 0x50       | **lastore**         | 将栈顶long型数值存入指定数组的指定下标处                     |
| 0x51       | **fastore**         | 将栈顶float型数值存入指定数组的指定下标处                    |
| 0x52       | **dastore**         | 将栈顶double型数值存入指定数组的指定下标处                   |
| 0x53       | **aastore**         | 指令格式： aastore 功能描述： 根据栈顶的引用型数值（value）、数组下      标（index）、数组引用（arrayref）出      栈，将数值存入对应的数组元素中。 抛出异常： 如果value的类型和arrayref所引用      的数组的元素类型不兼容，会抛出抛出      ArrayStoreException。      如果index造成数组越界，会抛出      ArrayIndexOutOfBoundsException。      如果arrayref值为null，会抛出      NullPointerException。 指令执行前指令执行后栈底......arrayref index value 栈顶 arrayref  ： 必须是对数组的引用index   ： int类型value   ： 引用类型 |
| 0x54       | **bastore**         | 将栈顶boolean或byte型数值存入指定数组的指定下标处            |
| 0x55       | **castore**         | 将栈顶char型数值存入指定数组的指定下标处                     |
| 0x56       | **sastore**         | 将栈顶short型数值存入指定数组的指定下标处                    |
|            |                     |                                                              |
| 0x57       | **pop**             | 栈顶数值出栈 (该栈顶数值不能是long或double型)                |
| 0x58       | **pop2**            | 栈顶的一个（如果是long、double型的)或两个（其它类型的）数值出栈 |
|            |                     |                                                              |
| 0x59       | **dup**             | 复制栈顶数值，并且复制值进栈                                 |
| 0x5A       | **dup_x1**          | 复制栈顶数值，并且复制值进栈2次                              |
| 0x5B       | **dup_x2**          | 复制栈顶数值，并且复制值进栈2次或3次                         |
| 0x5C       | **dup2**            | 复制栈顶一个（long、double型的)或两个（其它类型的）数值，并且复制值进栈 |
| 0x5D       | **dup2_x1**         |                                                              |
| 0x5E       | **dup2_x2**         |                                                              |
|            |                     |                                                              |
| 0x5F       | **swap**            | 栈顶的两个数值互换(要求栈顶的两个数值不能是long或double型的) |
|            |                     |                                                              |
| 0x60       | **iadd**            | 栈顶两int型数值相加，并且结果进栈                            |
| 0x61       | **ladd**            | 栈顶两long型数值相加，并且结果进栈                           |
| 0x62       | **fadd**            | 栈顶两float型数值相加，并且结果进栈                          |
| 0x63       | **dadd**            | 栈顶两double型数值相加，并且结果进栈                         |
|            |                     |                                                              |
| 0x64       | **isub**            | 栈顶两int型数值相减，并且结果进栈                            |
| 0x65       | **lsub**            | 栈顶两long型数值相减，并且结果进栈                           |
| 0x66       | **fsub**            | 栈顶两float型数值相减，并且结果进栈                          |
| 0x67       | **dsub**            | 栈顶两double型数值相减，并且结果进栈                         |
|            |                     |                                                              |
| 0x68       | **imul**            | 栈顶两int型数值相乘，并且结果进栈                            |
| 0x69       | **lmul**            | 栈顶两long型数值相乘，并且结果进栈                           |
| 0x6A       | **fmul**            | 栈顶两float型数值相乘，并且结果进栈                          |
| 0x6B       | **dmul**            | 栈顶两double型数值相乘，并且结果进栈                         |
|            |                     |                                                              |
| 0x6C       | **idiv**            | 栈顶两int型数值相除，并且结果进栈                            |
| 0x6D       | **ldiv**            | 栈顶两long型数值相除，并且结果进栈                           |
| 0x6E       | **fdiv**            | 栈顶两float型数值相除，并且结果进栈                          |
| 0x6F       | **ddiv**            | 栈顶两double型数值相除，并且结果进栈                         |
|            |                     |                                                              |
| 0x70       | **irem**            | 栈顶两int型数值作取模运算，并且结果进栈                      |
| 0x71       | **lrem**            | 栈顶两long型数值作取模运算，并且结果进栈                     |
| 0x72       | **frem**            | 栈顶两float型数值作取模运算，并且结果进栈                    |
| 0x73       | **drem**            | 栈顶两double型数值作取模运算，并且结果进栈                   |
|            |                     |                                                              |
| 0x74       | **ineg**            | 栈顶int型数值取负，并且结果进栈                              |
| 0x75       | **lneg**            | 栈顶long型数值取负，并且结果进栈                             |
| 0x76       | **fneg**            | 栈顶float型数值取负，并且结果进栈                            |
| 0x77       | **dneg**            | 栈顶double型数值取负，并且结果进栈                           |
|            |                     |                                                              |
| 0x78       | **ishl**            | int型数值左移指定位数，并且结果进栈                          |
| 0x79       | **lshl**            | long型数值左移指定位数，并且结果进栈                         |
|            |                     |                                                              |
| 0x7A       | **ishr**            | int型数值带符号右移指定位数，并且结果进栈                    |
| 0x7B       | **lshr**            | long型数值带符号右移指定位数，并且结果进栈                   |
| 0x7C       | **iushr**           | int型数值无符号右移指定位数，并且结果进栈                    |
| 0x7D       | **lushr**           | long型数值无符号右移指定位数，并且结果进栈                   |
|            |                     |                                                              |
| 0x7E       | **iand**            | 栈顶两int型数值按位与，并且结果进栈                          |
| 0x7F       | **land**            | 栈顶两long型数值按位与，并且结果进栈                         |
|            |                     |                                                              |
| 0x80       | **ior**             | 栈顶两int型数值按位或，并且结果进栈                          |
| 0x81       | **lor**             | 栈顶两long型数值按位或，并且结果进栈                         |
|            |                     |                                                              |
| 0x82       | **ixor**            | 栈顶两int型数值按位异或，并且结果进栈                        |
| 0x83       | **lxor**            | 栈顶两long型数值按位异或，并且结果进栈                       |
|            |                     |                                                              |
| 0x84       | **iinc**            | 指定int型变量增加指定值                                      |
|            |                     |                                                              |
| 0x85       | **i2l**             | 栈顶int值强转long值，并且结果进栈                            |
| 0x86       | **i2f**             | 栈顶int值强转float值，并且结果进栈                           |
| 0x87       | **i2d**             | 栈顶int值强转double值，并且结果进栈                          |
| 0x88       | **l2i**             | 栈顶long值强转int值，并且结果进栈                            |
| 0x89       | **l2f**             | 栈顶long值强转float值，并且结果进栈                          |
| 0x8A       | **l2d**             | 栈顶long值强转double值，并且结果进栈                         |
| 0x8B       | **f2i**             | 栈顶float值强转int值，并且结果进栈                           |
| 0x8C       | **f2l**             | 栈顶float值强转long值，并且结果进栈                          |
| 0x8D       | **f2d**             | 栈顶float值强转double值，并且结果进栈                        |
| 0x8E       | **d2i**             | 栈顶double值强转int值，并且结果进栈                          |
| 0x8F       | **d2l**             | 栈顶double值强转long值，并且结果进栈                         |
| 0x90       | **d2f**             | 栈顶double值强转float值，并且结果进栈                        |
| 0x91       | **i2b**             | 栈顶int值强转byte值，并且结果进栈                            |
| 0x92       | **i2c**             | 栈顶int值强转char值，并且结果进栈                            |
| 0x93       | **i2s**             | 栈顶int值强转short值，并且结果进栈                           |
|            |                     |                                                              |
| 0x94       | **lcmp**            | 比较栈顶两long型数值大小，并且结果（1，0，-1）进栈           |
| 0x95       | **fcmpl**           | 比较栈顶两float型数值大小，并且结果（1，0，-1）进栈；当其中一个数值为NaN时， -1进栈 |
| 0x96       | **fcmpg**           | 比较栈顶两float型数值大小，并且结果（1，0，-1）进栈；当其中一个数值为NaN时，1进栈 |
| 0x97       | **dcmpl**           | 比较栈顶两double型数值大小，并且结果（1，0，-1）进栈；当其中一个数值为NaN时，-1进栈 |
| 0x98       | **dcmpg**           | 比较栈顶两double型数值大小，并且结果（1，0，-1）进栈；当其中一个数值为NaN时，1进栈 |
|            |                     |                                                              |
| 0x99       | **ifeq**            | 当栈顶int型数值等于0时跳转                                   |
| 0x9A       | **ifne**            | 当栈顶int型数值不等于0时跳转                                 |
| 0x9B       | **iflt**            | 当栈顶int型数值小于0时跳转                                   |
| 0x9C       | **ifge**            | 当栈顶int型数值大于等于0时跳转                               |
| 0x9D       | **ifgt**            | 当栈顶int型数值大于0时跳转                                   |
| 0x9E       | **ifle**            | 当栈顶int型数值小于等于0时跳转                               |
| 0x9F       | **if_icmpeq**       | 比较栈顶两int型数值大小，当结果等于0时跳转                   |
| 0xA0       | **if_icmpne**       | 比较栈顶两int型数值大小，当结果不等于0时跳转                 |
| 0xA1       | **if_icmplt**       | 比较栈顶两int型数值大小，当结果小于0时跳转                   |
| 0xA2       | **if_icmpge**       | 比较栈顶两int型数值大小，当结果大于等于0时跳转               |
| 0xA3       | **if_icmpgt**       | 比较栈顶两int型数值大小，当结果大于0时跳转                   |
| 0xA4       | **if_icmple**       | 比较栈顶两int型数值大小，当结果小于等于0时跳转               |
| 0xA5       | **if_acmpeq**       | 比较栈顶两引用型数值，当结果相等时跳转                       |
| 0xA6       | **if_acmpne**       | 比较栈顶两引用型数值，当结果不相等时跳转                     |
|            |                     |                                                              |
| 0xA7       | **goto**            | 无条件跳转                                                   |
|            |                     |                                                              |
| 0xA8       | **jsr**             | 跳转至指定16位offset位置，并将jsr下一条指令地址压入栈顶      |
| 0xA9       | **ret**             | 返回至局部变量指定的index的指令位置（通常与jsr、jsr_w联合使用） |
| 0xAA       | **tableswitch**     | 用于switch条件跳转，case值连续（可变长度指令）               |
| 0xAB       | **lookupswitch**    | 用于switch条件跳转，case值不连续（可变长度指令）             |
|            |                     |                                                              |
| 0xAC       | **ireturn**         | 当前方法返回int                                              |
| 0xAD       | **lreturn**         | 当前方法返回long                                             |
| 0xAE       | **freturn**         | 当前方法返回float                                            |
| 0xAF       | **dreturn**         | 当前方法返回double                                           |
| 0xB0       | **areturn**         | 指令格式： areturn 功能描述： 从方法中返回一个对象的引用。 抛出异常： 如果当前方法是`synchronized`方法，      并且当前线程不是改方法的锁的拥有者，      会抛出      IllegalMonitorStateException。     指令执行前指令执行后栈底... objectref 栈顶 objectref ： 被返回的对象引用。 |
| 0xB1       | **return**          | 当前方法返回void                                             |
|            |                     |                                                              |
| 0xB2       | **getstatic**       | 获取指定类的静态域，并将其值压入栈顶                         |
| 0xB3       | **putstatic**       | 为指定的类的静态域赋值                                       |
| 0xB4       | **getfield**        | 获取指定类的实例域，并将其值压入栈顶                         |
| 0xB5       | **putfield**        | 为指定的类的实例域赋值                                       |
|            |                     |                                                              |
| 0xB6       | **invokevirtual**   | 调用实例方法                                                 |
| 0xB7       | **invokespecial**   | 调用超类构造方法、实例初始化方法、私有方法                   |
| 0xB8       | **invokestatic**    | 调用静态方法                                                 |
| 0xb9       | **invokeinterface** | 调用接口方法                                                 |
|            |                     |                                                              |
| 0xBA       | ---                 | 因为历史原因，该码点为未使用的保留码点                       |
|            |                     |                                                              |
| 0xBB       | **new**             | 创建一个对象，并且其引用进栈                                 |
| 0xBC       | **newarray**        | 创建一个基本类型数组，并且其引用进栈                         |
| 0xBD       | **anewarray**       | 指令格式： anewarray index1 index2 功能描述： 栈顶数值（count）作为数组长度，创建      一个引用 型数组。栈顶数值出栈，数组引      用进栈。 抛出异常： 如果count小于0，会抛出      NegativeArraySizeException 指令执行前指令执行后栈底......countarrayref栈顶 count   ： int类型。arrayref  ： 对所创建的数组的引用。 |
| 0xBE       | **arraylength**     | 指令格式： arraylength 功能描述： 栈顶的数组引用（arrayref）出栈，该      数组的长度进栈。 抛出异常： 如果arrayref的值为null，会抛出      NullPointerException。 指令执行前指令执行后栈底......arrayreflength栈顶 arrayref  ： 数组引用length   ： 数组长度 |
|            |                     |                                                              |
| 0xBF       | **athrow**          | 指令格式： athrow 功能描述： 将栈顶的数值作为异常或错误抛出 抛出异常： 如果栈顶数值为null，则使用      NullPointerException代替栈顶数      值抛出。      如果方法是的，则有可能抛出      IllegalMonitorStateException。 指令执行前指令执行后栈底...objectrefobjectref 栈顶 objectref ： Throwable或其子类的实例的引用。 |
| 0xC0       | **checkcast**       | 类型转换检查，如果该检查未通过将会抛出ClassCastException异常 |
| 0xc1       | **instanceof**      | 检查对象是否是指定的类的实例。如果是，1进栈；否则，0进栈     |
|            |                     |                                                              |
| 0xC2       | **monitorenter**    | 获得对象锁                                                   |
| 0xC3       | **monitorexit**     | 释放对象锁                                                   |
|            |                     |                                                              |
| 0xC4       | **wide**            | 用于修改其他指令的行为                                       |
|            |                     |                                                              |
| 0xC5       | **multianewarray**  | 创建指定类型和维度的多维数组（执行该指令时，栈中必须包含各维度的长度值），并且其引用值进栈 |
|            |                     |                                                              |
| 0xC6       | **ifnull**          | 为null时跳转                                                 |
| 0xC7       | **ifnonnull**       | 不为null时跳转                                               |
| 0xC8       | **goto_w**          | 无条件跳转（宽索引）                                         |
| 0xC9       | **jsr_w**           | 跳转至指定32位offset位置，并且jsr_w下一条指令地址进栈        |
|            |                     |                                                              |
| 0xCA       | **breakpoint**      |                                                              |
|            |                     |                                                              |
| 0xFE       | **impdep1**         |                                                              |
| 0xFF       | **impdep2**         |                                                              |
