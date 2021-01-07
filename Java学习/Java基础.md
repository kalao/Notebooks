





# **第一章** 入门

## 1.1  java的一些重要版本

- [ ] 2004       Java5
- [ ] 2014       Java8
- [ ] 2017       Java9
- [ ] 2018       Java10

## 1.2 进制转换

`**`

```java
public class XsystemToXsystem {
    /***
     *
     * @param x
     * @param sys_source 2 8 16
     * @param sys_target 2 8 16
     * @return
     */
    public static String translate(String x,int sys_source,int sys_target){
        //1 得到十进制数
        //2 将其转为target进制
        //
        char[] tx=x.toCharArray();
        int sum=0;
        for (int i = 0,j=1; i < tx.length; i++,j*=sys_source) {

            if(tx[tx.length-i-1]>='A'){
                sum+=(10+(tx[tx.length-i-1]-'A'))*j;
            }else{
                sum+=(tx[tx.length-i-1]-'0')*j;
            }
        }
        char[] res=new char[tx.length];

        int count=0;
        while(sum>0){
            int t=sum%sys_target;
            if(t>=10){
                res[count++]=(char)(t-10+'A');
            }else{
                res[count++]=(char)(t+'0');
            }
            sum-=(t);
            sum/=sys_target;

        }
        char[] rres=new char[count+1];
        for(int i=0;i<count;++i){
//            char t=res[i];
//            res[i]=res[count-i-1];
//            res[count-i-1]=t;
            rres[count-i-1]=res[i];
        }
        rres[count]='\n';
        return String.valueOf(rres);
    }
    public static void main(String[] args) {
        System.out.println(translate("10101", 2, 8));
    }
}
```

python的进制转换

|                        | More Actions**2进制** | More Actions**8进制** | More Actions**10进制** | More Actions**16进制** |
| ---------------------- | --------------------- | --------------------- | :--------------------: | ---------------------- |
| More Actions**2进制**  |                       | bin(int(n,8))         |     bin(int(n,10))     | bin(int(n,16))         |
| More Actions**8进制**  | oct(int(n,2))         |                       |     oct(int(n,10))     | oct(int(n,16))         |
| More Actions**10进制** | int(n,2)              | int(n,8)              |                        | int(n,16)              |
| More Actions**16进制** | hex(int(n,2))         | hex(int(n,8))         |     hex(int(n,10))     |                        |

idea 快捷键:

1. Alt+7 列出类的所有方法
2. Ctrl+H 列出所有实现类
3. Ctrl+Alt+U列出类的依赖情况

idea插件:

1. translation插件

# 第二章   面向对象

面向对象的方式使得程序易于理解,且易于找到bug.

## 1.1 类和对象

### 1.1.1 类

> 类是事物的模板,是抽象的.事物有属性(成员变量)和行为(成员方法).
>
> 对象是事物的实例,拥有具体的属性.

#### 1.1.1.1 类之间的关系

- 依赖 ("uses-a") 一个类的状态会影响另一个类
- 聚合("has-a")一个类包含在另一个类中,是聚合关系.
- 继承("is-a")

```
  在有些地区日期表示为月 / 日 / 年 , 而另一些地区表示为日 / 月 / 年 。 语言设计者是否能够预见这些问题呢? 如果没有处理好这类问题 , 语言就有可能陷入混乱, 对此感到不满的程序员也会丧失使用这种语言的热情 。 如果使用类 , 这些设计任务就交给了类库的设计者 。 如果类设计的不完善 , 其他的操作员可以很容易地编写自己的类, 以便增强或替代 ( replace ) 系统提供的类 (作为这个问题的印证 : Java 的日期类库有些混乱, 已经重新设计了两次 ) 。
```

###  1.2.1 对象

- **创建对象**其实就是<u>仅仅向一块内存区域写入与类元数据对应的各种字段的值</u>，当然对象类型的值是一个引用，访问一个对象的字段的值， 是通过定位这个字段在这个对象起始地址的相对偏移量。确定相对偏移量就是在字段解析阶段完成的。 

- 对象的行为,状态,标识,对象的状态不能完全描述一个对象,只有**对象的标识**是不一样的.

####   1.2.1.1 对象的内存图

![image-20201121163649217](https://github.com/kalao/Images/blob/master/Java基础.md/20201121163649217.png)

## 1.3 属性 

### 1.3.1 局部变量和成员变量

区别:

1. **定义的位置**不一样,局部变量在方法内,成员变量在方法外,类中.

2. **作用范围**不一样,一个是在方法内,一个在整个对象的每个方法都可以用,有this引用

3. **初始化**不一样,局部变量必须初始化,而成员变量的初始化可以有默认值.

   首先明文规定：局部变量是指类方法中的变量，必须初始化。

   理由：局部变量运行时被分配在栈中，量大，生命周期短，如果虚拟机给每个局部变量都初始化一下，是一笔很大的开销，但变量不初始化为默认值就使用是不安全的。出于速度和安全性两个方面的综合考虑，解决方案就是虚拟机不初始化，但要求编写者一定要在使用前给变量赋值。

```
interface skill{
	void release();
}
class hero{
	private Skill skill;
    public void setSkill(Skill skill){
    	this.skill=skill;
    }
    public void release(){
    	skill.release()
    }
}
main{
	hero h=new hero();
	h.setSkill(new Skill(){
		 void  release(){	
		 }
	});
	h.release();
}
```

## 1.4 面向对象三大特性

### 1.4.1 封装

**封装的目的**是为了实现高内聚,低耦合,防止相互依赖造成相互影响**.面向对象的封装**是指把一个对象的属性和行为封装在一个模块中,也就是一个类中.

```java
封装是保证软件部件具有优良的模块性的基础，封装的目标就是要实现软件部件的“高内聚、低耦合”，防止程序相互依赖性而带来的变动影响。在面向对象的编程语言中，对象是封装的最基本单位，面向对象的封装比传统语言的封装更为清晰、更为有力。面向对象的封装就是把描述一个对象的属性和行为的代码封装在一个“模块”中，也就是一个类中，属性用变量定义，行为用方法进行定义，方法可以直接访问同一个对象中的属性。
```

### 1.4.2 继承 

**继承主要解决的问题**:共性抽取.

#### 1.4.2.1 区分子类方法中重名的三个变量

- 局部变量: 直接写
- 本类的成员变量:this
- 父类的成员变量:super

#### 1.4.2.2 重写,重载

重写:方法名称一样,参数列表一样 @override,可写可不写,

- **返回值**必须小于等于父类的方法.
- **子类方法的权限**必须大于等于父类方法的权限修饰符.public>protected>default>private ,default是什么都不写

Q:为什么要重写?

```
从工程上的设计原则,对于已经投入使用的类,尽量不要修改,而是重复利用其中共性的东西,并且添加新的东西.
```

重载:

- 方法名称一样,参数列表不一样

继承关系中,父子类构造方法的方法的访问特点.

- <u>子类构造方法有一个默认隐含的super调用</u>,所以一定是先调用父类构造.
- <u>子类构造可以通过super关键字来调用父类重载构造</u>

### 1.4.3 多态

#### 对象访问成员变量的两种方式

1. 直接通过对象名称访问成员变量,看等号左边是谁,优先用谁,没有则向上找.

   ```java
   class A{
       int i=1;
   }
   class B extends A{
       
   }
   class C extends B{
       int i=3;
   }
   public class test1 {
       public static void main(String[] args) {
           B a=new C();
           System.out.println(a.i);
       }
   }
   output:1
   ```

2. 间接通过成员方法访问,那么看这个方法属于谁,就调用谁的方法.

#### 对象访问成员方法

1. 编译看左边(编译是否成功看左边是否有对应的方法),运行看右边(new的是谁)是否有该方法,如果没有则向上找.

```java
class A{
    int i=1;
    public void func(){
        System.out.println("A");
    }
}
class B extends A{
    public void func(){
        System.out.println("B");
    }
}
class C extends B{
    int i=3;
}
public class test1 {
    public static void main(String[] args) {
        A a=new C();
        a.func();
    }
}
output:B
```

#### 多态的好处

无论右边new的时候换成哪个子类对象,等号左边调用方法都不会变化.

#### 多态的向上与向下转型

向上转型

1. 向上转型是安全的,因为从小范围到大范围.(越来越抽象的层面)
2. 一旦转型为父类就无法使用子类的方法,但是可以用向下转型还原.(还原成原来的类)

向下转型

#### 1.4.3.3 instanceof 

用于判断某个对象是否是某个类的实例, (对象) instanceof (类)

```
A a =new B();
a.fun();
a=new C();
if(a instanceof B){
    B b=(B)a;
    b.fun();
}else if(a instanceof C){
    C c=(C)a;
    c.fun();
}
```

#### 1.4.3.4 小demo

问题描述:

usb接口规范,需要实现打开设备和关闭设备.

现需要鼠标和键盘实现usb接口规范,以及完成电脑类实现打开usb设备的功能.

分析:

鼠标,键盘实现usb接口,电脑类的打开功能的参数设置为接口参数

<img src="https://github.com/kalao/Images/blob/master/Java基础.md/20201207204142611.png" alt="image-20201207204142611" style="zoom:50%;" />

```
package 多态;
interface usb{
    void powerOn();
    void powerOff();
}
class mouse implements usb{
    public void click(){

    }
    @Override
    public void powerOn() {
        System.out.println("连接鼠标...");
    }

    @Override
    public void powerOff() {

    }
}
class keyBoard implements usb{
    public void click(){

    }
    @Override
    public void powerOn() {
        System.out.println("连接键盘...");

    }

    @Override
    public void powerOff() {

    }
}

class computer{
    public void powerOn() {

    }
    public void powerOff() {

    }
    public void openDevice(usb device){
        device.powerOn();
    }
}
public class test{
    public static void main(String[] args) {
        computer c=new computer();
        c.powerOn();
        c.openDevice(new mouse());
        c.openDevice(new keyBoard());

    }
}
```

## 1.5 关键词

### 1.5.1 private 关键词

对于一个对象的字段,无法阻止其他调用者对其字段的任意修改,使用private关键词修饰.

#### 1.5.1.1 get和set方法的作用

安全,方便调试,可以轻易改变内部实现(可以执行错误检查)

```
1.安全
比如People类的属性name可以设置为只读域，即只有get方法，没有set方法，当在构造方法中设置完毕，就没有任何一个办法可以对它进行修改，这样来确保name域不会受到外界的破坏。

2.方便调试
People类的属性age虽然不是只读域，但是只能通过setAge (int age)方法进行修改，一旦这个属性值出现了问题，只要调试这个方法就可以了。如果age属性是public修饰，破坏这个属性值的捣乱者则可能出现在任何地方。

3.可以轻易改变内部实现
如果将存储名字的属性改为

    String firstName;
    String lastName;

那么getName方法可以改为返回
firstName+" "+lastName，只需要修改getName()方法的内部实现，不会影响到其他代码。

4.可以执行错误检查
比如setAge()方法我们可以在设置之前判断参数age是否大于0。

在《码出高效》中对于封装的好处是这么描述的：封装是由俭入奢易，由奢入俭难。属性值的访问与修改需要使用相应的getter/setter方法，而不是对public的属性进行读取和修改。
如果有一天，类的提供方想在修改属性的setter方法上进行鉴权控制，日志记录，这是在直接访问属性的情形中无法做到的。若是将已经公开的属性和行为直接暴力修改为private，则依赖模块都会编译出错。所以，在不知道什么样的访问控制权限合适的时候，优先推荐使用private控制级别
```

### 1.5.2 this关键字



- 在本类的成员方法中调用成员变量,

  由于允许方法使用成员变量,但是如果成员变量和局部变量重名,那么采用就近原则,使用局部变量,那么需要使用this来区分.

- 在本类的成员方法中访问另一个本类的成员方法.

- 在本类的构造方法中调用另一个构造方法 this(...)必须是第一句,并且不存在循环调用

### 1.5.3 static关键字



### 1.5.4 静态代码块

特点:

1. 当第一次使用本类的时候,静态代码块执行唯一的一次
2. 静态代码块比构造方法先执行

用法:

1. 一般用于一次性对静态变量进行赋值

### 1.5.5 super关键字

- 在子类的成员方法中访问父类的成员变量和成员方法. 
- 在子类的构造函数中调用父类的构造函数,super(...),一般子构造器默认调用父类无参构造器,同样你可以调用其他有参构造器,必须第一行.







### 1.5.6 final关键字

用法:

1. 修饰一个类
2. 修饰一个方法
3. 修饰一个局部变量
4. 修饰一个成员变量

1. 当修饰一个类的时候,不能有子类的时候,那么用final修饰这个类.

2. 当修饰一个方法的时候,表明方法体是最终的形态,不允许重写.

3. 当修饰一个局部变量时候,那么这个变量"一经赋值,终生不变".对于基本类型,是值不变;对于引用类型,是地址不变.

4. 当修饰一个成员变量时候,一旦赋值,也不能改变,但由于成员变量如果未赋值,会给其默认赋值,因此需要对其手动赋值或者构造器赋值(二者选其一).

   

   





### 1.5.7 抽象

Q:为什么需要抽象概念?

因为抽象的概念往往其行为不是具体的.

抽象方法 :就是加上abstract关键字,然后去掉大括号,直接分号结束,而含有抽象方法的类应该是抽象类.

抽象类:在类前加上abstract关键词.

- 抽象类不能创建对象
- 抽象类可以有构造方法,是供子类创建对象时,初始化父类成员使用.
- 抽象类可以没有抽象方法,但是含有抽象方法必须是抽象类.
- 抽象类的子类必须重写父类中

```
import com.sun.xml.internal.ws.api.addressing.WSEndpointReference;

import java.util.ArrayList;
import java.util.List;
import java.util.Objects;
import java.util.Random;

abstract  class Person{
    Person(){this.account=new Double(0);}
    Person(double account){this.account=new Double(account);}
    protected Double account;
    @Override
    public String toString() {
        return "Person{" +
                "account=" + account +
                '}';
    }

    public Double getAccount() {
        return account;
    }

    public void setAccount(Double account) {
        this.account = account;
    }
}
class master extends Person{
    master(){}
    master(double account){
        super(account);
    }
    public Boolean reduce(Double account){
        if(account>this.account)return false;
        this.account-=account;
        return true;
    }
}
class other extends Person{
    other(){}
    other(double account){
        super(account);
    }
    public void add(Double account){
        this.account+=account;
    }

}
/***
 * 发红包,n , m  x+m*y=z y>0 (z-x)/m>0 x
 *
 */

public class ExtendTest {
    private static ArrayList<other> others;
    private static  master m;
    public static void sendRedPackage(double money) throws Exception {
        Random random=new Random();
        if(m==null||others==null)return ;
        double sum=money;
        for(int i=0;i<4;++i){
            other o=others.get(i);
            double rv;
            do{
                rv=random.nextDouble()*sum;
            }while((sum-rv)/(5-i)<=0);
            o.add(rv);
            m.reduce(rv);
            sum-=rv;
        }
        others.get(others.size()-1).add(sum);
        m.reduce(sum);

    }

    public static void main(String[] args) {
           m=new master(10);
           others=new ArrayList<other>();
           for(int i=0;i<5;++i){
              others.add(new other(0));
           }
        try {
            sendRedPackage(10);
            for (other o:others) {
                System.out.println(o);
            }
            System.out.println(m);
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}

```

```
import com.sun.xml.internal.ws.api.addressing.WSEndpointReference;

import java.util.*;

abstract  class Person{
    Person(){this.account=new Double(0);}
    Person(double account){this.account=new Double(account);}
    protected Double account;
    @Override
    public String toString() {
        return "Person{" +
                "account=" + account +
                '}';
    }

    public Double getAccount() {
        return account;
    }

    public void setAccount(Double account) {
        this.account = account;
    }
}
class master extends Person{
    master(){}
    master(double account){
        super(account);
    }
    public Boolean reduce(Double account){
        if(account>this.account)return false;
        this.account-=account;
        return true;
    }

    /***
     * 发红包
     * @param account :发送的金额
     * @param count :发送的人数 ,尽量不要以类为参数,这样写死了,如果改一个对其他类发红包的需求呢
     * @return 发的金额列表
     */
    public ArrayList<Double> send(double account,int count){
       Random random=new Random();
       ArrayList<Double> res=new ArrayList<Double>();
       double sum=account;
       double money;
       for(int i=0;i<count-1;++i){
           do{
               money=random.nextDouble();
           }while((sum-money*sum)/(count-i)<=0);
           res.add(money*sum);
           sum-=money*sum;
       }
       res.add(sum);
       return res;
    }
}
class other extends Person{
    other(){}
    other(double account){
        super(account);
    }
    public void add(Double account){
        this.account+=account;
    }

    /***
     * 收到的金额
     * @param account
     */
    public void receive(double account){
        add(account);
    }
}
/***
 * 发红包,n , m  x+m*y=z y>0 (z-x)/m>0 x
 *
 */

public class ExtendTest {
    public static void main(String[] args) {
       master m= new master(10);
       ArrayList<other> others=new ArrayList<>();
       for(int i=0;i<5;++i)
           others.add(new other(0));
        ArrayList<Double> distributes = m.send(10, 5);
        for(int i=0;i<5;++i){
            others.get(i).add(distributes.get(i));
            System.out.println(others.get(i));
        }
    }
}

```

## 1.6 权限修饰符

#### 1.6.1  包

1. 只有java.lang下的包内容才不需要导包
2. 还有同一个包下的不需要,其余都需要import(包括子包下的)

#### 1.6.2 对于不同修饰符对包的访问权限

|                    | public | protected | default | private |
| ------------------ | ------ | --------- | ------- | ------- |
| 同一个包下同一个类 | 1      | 1         | 1       | 1       |
| 同一个包下不同类   | 1      | 1         | 1       | 0       |
| 不同的包下子类     | 1      | 1         | 0       | 0       |
| 不同的包下不是子类 | 1      | 0         | 0       | 0       |

##### 

## 1.7 内部类

用法:

1. 成员内部类
2. 局部内部类(匿名内部类)

**成员内部类:**

特点:

1. 内部用外部随意 个人理解:外部类对象先于内部类对象
2. 外部用内部,需要内部类对象

访问内部类方式:

1. 间接方式:在外部类的方法中,间接的使用内部类,然后在main调用外部类的方法

2. 直接方式:

   ```
   外部类.内部类 name=new 外部类().new 内部类()
   ```

内部类访问变量的方式

1. 访问内部类方法的局部变量

   ```
   变量
   ```

   

2. 访问内部类的成员方法

   ```
   this.变量
   ```

   

3. 访问外部类的成员方法

   ```
   外部类.this.变量
   ```

**局部内部类**

```
public Outer{
	public void funcO(){
		class Inner{
			public void funcI(){
			
			}
		}
		Inner inner=new Inner();
		inner.funcI();
	}
}
```

特点:

1. 只能在方法内部使用

2. 对于局部内部类访问方法内部的变量,必须是final类型(或者保证不可变)

   原因是:

   - new出来的对象在堆中,局部变量是在栈内存中,那么局部变量在方法结束之后会消失,所以当内部类对象访问的时候就无法访问了,因此将其放在常量池中,保证对象能够访问到.

定义一个类的时候的权限修饰符

1. 外部类: public /default ,由于private,和protected必须是类内部的.
2. 成员内部类:都有
3. 局部内部类:什么都不写,因为它只在方法内部被使用,所以不需要权限修饰

**匿名内部类**

特点:

1. 如果接口的实现类只需要使用唯一的一次,那么这种情况就能省略该类的定义.

   ```
   匿名内部类的定义
   接口名称 对象名=new 接口名称(){
   	覆盖重写所有的抽象方法
   }
   
   ```

2. 无需类的定义

   

   

   



# 第三章 常用类



## 1.0 包装类

### 1.0.1 Integer

1. Integer.valueOf(10)将基本数据类型转换为包装类

2. intValue()将包装类转换成基本数据类型

   自动装箱

   ```
   Integer i=100;
   //相当于 Integer i=Integer.valueOf(100);
   ```

   自动拆箱

   ```
   int j=i;
   //int j=i.intValue();
   ```

3. .toString()转换为字符串

4. 常见的常量 MAX_VALUE ;MIN_VALUE

Q:如下示例,下面情况的原因

```
        Integer i_1=Integer.valueOf(-128);
        Integer j_1=Integer.valueOf(-128);
        System.out.println(i_1==j_1);
        Integer i_2=Integer.valueOf(128);
        Integer j_2=Integer.valueOf(128);
        System.out.println(i_2==j_2);
```

A:Integer的valueOf将[-128,127]的Integer进行缓存

<img src="https://github.com/kalao/Images/blob/master/Java基础.md/20210102205805287.png" alt="image-20210102205805287" style="zoom:80%;" />

<img src="https://github.com/kalao/Images/blob/master/Java基础.md/20210102205145815.png" alt="image-20210102205145815" style="zoom:80%;" />







## 1.1 Scanner /Radnom

## 1.2 ArrayList

## 1.3 String

1. 字符串的内容永不可变
2. 因为不可变,所以可共享 
3. 效果上相当于char[],底层是byte[]

##### 构造函数

`String()`初始化新创建的 `String`对象，使其表示空字符序列。

String(byte[] bytes)通过使用平台的默认字符集解码指定的字节数组来构造新的 `String` 。

String(char[] value) 分配一个新的 `String` ，以便它表示当前包含在字符数组参数中的字符序列。

字符串常量池:

程序中直接写上双引号字符串就放在常量池中,new的不在常量池当中

![image-20201124205144927](https://github.com/kalao/Images/blob/master/Java基础.md/20201124205144927.png)

##### equals方法

"str".equals(obj) 推荐   obj.equals("str")不推荐,以防obj为null.

equalsIgnoreCase 忽略大小写比较

##### concat方法 将字符串与参数字符串拼接形成新的字符串

##### length方法 返回字符串的长度

##### charAt方法 获得指定索引位置单个字符

​	public char charAt(int index)

##### indexOf方法  查找参数字符串在字符串的首次出现的索引位置,如果没有返回-1

​	public int indexOf(String str)

##### substring 截取方法

public String substring(int index)从参数位置到结尾

public String substring(int begin,int end)截取从begin到end,左闭右开

##### 字符串转换方法

```
public char[] toCharArray()
public byte[] getBytes()
public String replace(CharSequence old,Charsequence new)
```

##### 分割字符串的方法

```
public String[] split(String regex)//参数是正则表达式
```

Q:由于编译器优化导致的比较结果差异

```
String a="hello"+"world";
String b="helloworld";
System.out.println(a==b);
String c="hello";
String d="world";
String e=c+d;
System.out.println(a==e);
```

```
true
false
```

A:a在进行优化后就是同一对象,而e编译器不知道具体的值.

### 1.4 可变StringBuilder和StringBuffer

String的char[]是final的,而StringBuilder不是final的

<img src="https://github.com/kalao/Images/blob/master/Java基础.md/20210102211340023.png" alt="image-20210102211340023" style="zoom:80%;" />

```
        StringBuilder a=new StringBuilder("abcd");
        System.out.println(a.hashCode());
        a=a.append("ss");
        System.out.println(a.hashCode());
        String b="dff";
        System.out.println(b.hashCode());
        b=b+"ss";
        System.out.println(b.hashCode());
```

```
1528902577
1528902577
99364
95492484
```

常用方法:

1. obj.reverse 倒序
2. obj.setCharAt(index,value)修改
3. obj.insert(index,value)插入
4. obj.delete(start,end)删除

### 1.5 时间类 

#### Date类(时间类)

![image-20210102213521101](https://github.com/kalao/Images/blob/master/Java基础.md/20210102213521101.png)

自1970年1月1日 开始,以此时间点为0,往后为正,往前为负.可以通过

```
        System.currentTimeMillis()
```

以此为核心的Date类:

```
Date x=new Date(时间戳)
```

obj.getTime()可获得毫秒数

#### Calender类(日期类)

> ```
> GregorianCalendar是Calender的实现类
> ```

创建对象:

```
Calendar gregorianCalendar = new GregorianCalendar(2020, 10, 1);
```

获取相关的信息:

```
gregorianCalendar.get(Calendar.YEAR)
```

设置相关信息:

```
gregorianCalendar.add(Calendar.YEAR,1);
```

日期和时间类的相互转化

```
gregorianCalendar.setTime(时间对象);
```

注意:

月份是从0开始的

#### DateFormat和SimpleDateFormat

> DateFormat用于将字符串和时间类相互转换

![image-20210102220137476](https://github.com/kalao/Images/blob/master/Java基础.md/20210102220137476.png)

示例: 将时间对象按照指定格式输出为字符串

```
        DateFormat yy = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String format = yy.format(new Date(System.currentTimeMillis()));
        System.out.println(format);
```

示例:

```
        try {
            DateFormat yy = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
            Date parse = yy.parse("2020-12-01 00:00:00");     
        } catch (ParseException e) {
            e.printStackTrace();
        }
```

示例:**

```
完成一个简单日历
System.out.println(firstDayOfMonth.getActualMaximum(Calendar.DATE));
System.out.println(firstDayOfMonth.get(Calendar.DAY_OF_WEEK));
```

### 1.6 File类

常用方法:

1. File是否存在
2. File是否是目录
3. ~是否是文件
4. ~最后修改时间
5. ~大小
6. ~文件名
7. 目录路径
8. 创建文件/文件夹

示例:

```
递归遍历文件夹
```

### 1.7 枚举

>  将一些字串定义为有意义的常量
>

```
enum x{
    MONDAY,
}
```



### 1.8Arrays 数组工具类

```
public static String toString(数组)将参数数组变成字符串,{元素1,元素2}
public static void sort(数组)按照默认升序对数组的元素进行排序
如果是自定义的类型,那么这个自定义的类需要有Comparable或者Comparator
```



### 1.9 Math类

```
public static double abs(double num)
public static double ceil(double num)
public static double floor(double num)
public static long round(double num)

```

## 2.0 流

InputStream 

![image-20201230195403836](https://github.com/kalao/Images/blob/master/Java基础.md/20201230195403836.png)

# 第四章 集合

##  1.1 自动装箱,自动拆箱

java1.5后支持,将基本类型转为包装类型,反之也可以.

# 第五章 接口

定义:

> ​    接口就是一种公共的规范标准,只要符合标准,就可以通用.(接口中就是定义了通用的规范)

- java7,接口中可以定义**常量**和**抽象方法**


- java8,接口中可以定义**默认方法**和**静态方法**.


- java9,接口中可以定义**私有方法**


java定义接口:

- 抽象方法
- 成员变量
- 默认方法
- 静态方法
- 私有方法

1. 接口中的抽象方法,必须是public abstract
2. 接口中的成员变量只能是public  static final,而且必须赋值.在命名的时候建议大写.字母之间用_分隔
3. 接口虽然有常量,但是**没有静态代码块**,**没有构造方法**.
4. 可以实现多个接口
5. 如果实现的多个接口当中,存在重复的抽象方法,那么只需覆盖重写一次即可.
6. 如果实现类没有重写所有的抽象方法,那么实现类就必须是一个抽象类.
7. 如果实现的多个接口中存在多个重复的默认方法,那么实现类就必须对其进行重写.
8. 如果继承的父类和实现的接口的默认方法有相同,那么优先考虑父类的方法.
9. 接口可以作为参数类型,返回值类型.(多态写法)

子接口继承注意:

1. 抽象方法如果有冲突是没有关系的.
2. 但是默认方法如果有冲突,子接口就必须对其进行重写.



实现一个接口:

1. (implements)关键词实现
2. 实现必须重写抽象方法

java8可以定义默认方法的原因:

- java8中的默认方法是用于解决接口的升级问题,当接口需要新增规范,那么把之前投入使用的实现进行修改不现实,那么可以使用默认方法.这样保证了兼容性.

java9可以定义私有方法的原因:

> 用于解决两个默认方法之间重复代码的问题,并且这个共有方法不应该让实现类来使用,应该是私有的.

1. 普通私有方法
   解决多个默认方法之间的重复

   ```
   private  
   ```

2. 静态私有方法

   解决多个静态方法之间的重复

   ```
   private static 
   ```



![image-20201205201115354](https://github.com/kalao/Images/blob/master/Java基础.md/20201205201115354.png)

# 第六章 异常



![image-20210106143858084](/home/kalao/.config/Typora/typora-user-images/image-20210106143858084.png)

![image-20210106143936930](/home/kalao/.config/Typora/typora-user-images/image-20210106143936930.png)

- 逻辑代码和错误代码放在一起

- 程序员本身需要考虑的例外情况较为复杂,对程序员要求较高!

  ![image-20210106144257596](/home/kalao/.config/Typora/typora-user-images/image-20210106144257596.png)

![image-20210106145040082](/home/kalao/.config/Typora/typora-user-images/image-20210106145040082.png)

派生于RuntimeException的异常,如被0除,数组下标越界,强制类型转换,空指针,其产生比较频繁,处理麻烦,<u>如果显式的声明或捕获将会对程序可读性和运行效率影响很大,因此由系统自动检测并将它们交给缺省的异常处理程序.</u>(不是很懂)

解决上述异常需要修改代码:判断是否为0或者null(修改逻辑代码)

```
if(obj==null){
}
```

强制类型转换

```
if(a instanceof 类){
	类 b=(类)a;
}
```

不在业务代码考虑的范围之内的:

try...catch 异常一般子类在前,父类在后

![image-20210106193558236](/home/kalao/.config/Typora/typora-user-images/image-20210106193558236.png)

Tip:

> 1 在实际开发中,会将代码进行分层,专门有层用来处理下层传来的异常.
>
> 2 要避免使用异常处理代替错误处理,这样只会降低程序的清晰度
>
> 3 不要进行小粒度的异常处理,应该将整个任务包装在一个try语句块中.

![image-20210106195049469](/home/kalao/.config/Typora/typora-user-images/image-20210106195049469.png)