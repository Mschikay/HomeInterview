# 主要的参考链接

[github](https://github.com/Snailclimb/JavaGuide/tree/master/Java%E7%9B%B8%E5%85%B3)

[java高并发系列](http://www.importnew.com/21312.html)

[java的三层架构](https://juejin.im/post/5929259b44d90400642194f3)

[java 序列化](https://www.hollischuang.com/archives/1140)

[public protected private](https://blog.csdn.net/justloveyou_/article/details/61672133)

[HashMap, HashTable, ConcurrentHashMap](http://www.yuanrengu.com/index.php/2017-01-17.html)

# 多线程
[良心的多线程套餐](https://blog.csdn.net/qq_34337272/article/details/79640870)
[中断](https://blog.csdn.net/canot/article/details/51087772)

[掘金java多线程](https://juejin.im/entry/58f1d35744d904006cf14b17)

### <font color="00ffff">等待/通知机制 </font>

<font color="red">生产和消费者关系</font>：第一个线程（生产者）做相应的操作然后第二个线程（消费者）感知到了变化又进行相应的操作

会有一个问题：如果轮询时间的间隔太小会浪费CPU资源，轮询时间的间隔太大，就可能取不到自己想要的数据。所以用等待/通知（wait/notify）机制来解决这两个矛盾。

synchronized关键字可以将任何一个Object对象作为同步对象来看待，而Java为每个Object都实现了等待/通知（wait/notify）机制的相关方法，它们必须用在synchronized关键字同步的Object的临界区内。通过调用wait()方法可以使处于临界区内的线程进入等待状态，同时<font color="red">释放</font>被同步对象的锁。而notify()方法可以唤醒一个因调用wait操作而处于阻塞状态中的线程，使其进入就绪状态。<font color="red">被重新唤醒的线程会视图重新获得临界区的控制权也就是锁</font>，并继续执行wait方法之后的代码。如果发出notify操作时没有处于阻塞状态中的线程，那么该命令会被忽略。

在没有同步锁的情况下：
* 一个生产者判断条件，比如发现buffer满了，需要等待

* 同时消费者消费，改变了条件

* 消费者要通知生产者条件发生了改变，但是这个时候生产者线程还没有在等待的状态，所以通知不到它

* 之后生产者不知道条件已经改变进入了等待的状态，可能之后就一直收不到通知了





# JVM
Boolean类型在编译后在JVM中是int类型，一般是1个字节大小




# 若干道面试题

## Java 基础
### <font color=#00ffff>方法内联</font>
方法内联就是把被调用方函数代码"复制"到调用方函数中，来减少因函数调用开销的技术。

但是一个方法就算被JVM标注成为热点方法，JVM仍然不一定会对它做方法内联优化。其中有个比较常见的原因就是这个方法体太大了，分为两种情况。

* 如果方法是经常执行的，默认情况下，方法大小小于325字节的都会进行内联（可以通过** -XX:MaxFreqInlineSize=N**来设置这个大小）

* 如果方法不是经常执行的，默认情况下，方法大小小于35字节才会进行内联（可以通过** -XX:MaxInlineSize=N **来设置这个大小）

我们可以通过增加这个大小，以便更多的方法可以进行内联；但是除非能够显著提升性能，否则不推荐修改这个参数。因为更大的方法体会导致代码内存占用更多，更少的热点方法会被缓存，最终的效果不一定好。

[private static 有助于内敛优化。内敛优化是通过将被调用的函数复制到调用方函数内来减少函数调用的资源消耗](https://www.toutiao.com/a6530421233337500164/?tt_from=weixin&utm_campaign=client_share&timestamp=1520505006&app=news_article&utm_source=weixin&iid=27056549301&utm_medium=toutiao_ios&wxshare_count=1)
### <font color=#00ffff>Class.forName</font>
[为什么要使用；它的作用；new和newInstance的对比](http://www.cnblogs.com/springcsc/archive/2010/03/03/1676977.html)
### <font color=#00ffff>包的作用</font>
protected 在包内可以访问，如果不是同一个包，子类可以访问父类的方法，但不能访问父类实例的方法

1. 把功能相似或相关的类或接口组织在同一个包中，方便类的查找和使用。

2. 如同文件夹一样，包也采用了树形目录的存储方式。同一个包中的类名字是不同的，不同的包中的类的名字是可以相同的，当同时调用两个不同包中相同类名的类时，应该加上包名加以区别。因此，包可以避免名字冲突。

3. 包也限定了访问权限，拥有包访问权限的类才能访问某个包中的类。

Java 使用包（package）这种机制是为了防止命名冲突，访问控制，提供搜索和定位类（class）、接口、枚举（enumerations）和注释（annotation）等。

包语句的语法格式为：
```java
package pkg1[．pkg2[．pkg3…]];
```

### <font color=#00ffff>JDK 和 JRE 有什么区别？</font>
JDK是Java Development Kit，它是功能齐全的Java SDK。它拥有JRE所拥有的一切，还有<font color=#ffff>编译器（javac）</font>和<font color=#ffff>工具</font>（如javadoc和jdb）。它能够创建和编译程序。

JRE 是 Java运行时环境。它是运行已编译 Java 程序所需的所有内容的集合，包括 <font color=#ffff>Java虚拟机（JVM），Java类库，java命令和其他的一些基础构件。但是，它不能用于创建新程序</font>。

如果你只是为了运行一下 Java 程序的话，那么你只需要安装 JRE 就可以了。如果你需要进行一些 Java 编程方面的工作，那么你就需要安装JDK了。但是，这不是绝对的。有时，即使您不打算在计算机上进行任何Java开发，仍然需要安装JDK。例如，如果要使用JSP部署Web应用程序，那么从技术上讲，您只是在应用程序服务器中运行Java程序。那你为什么需要JDK呢？因为应用程序服务器会将 JSP 转换为 Java servlet，并且需要使用 JDK 来编译 servlet。

### <font color=#00ffff>== 和 equals 的区别是什么？</font>
**==** : 它的作用是判断两个对象的地址是不是相等。即，判断两个对象是不是同一个对象。(基本数据类型==比较的是值，引用数据类型==比较的是内存地址)

**equals()**: 它的作用也是判断两个对象是否相等。但它一般有两种使用情况：

* 情况1：类没有覆盖 equals() 方法。则通过 equals() 比较该类的两个对象时，等价于通过“==”比较这两个对象。

* 情况2：类覆盖了 equals() 方法。一般，我们都覆盖 equals() 方法来两个对象的内容相等；若它们的内容相等，则返回 true (即，认为这两个对象相等)。

```java
public class test1 {
    public static void main(String[] args) {
        String a = new String("ab"); // a 为一个引用
        String b = new String("ab"); // b为另一个引用,对象的内容一样
        String aa = "ab"; // 放在常量池中
        String bb = "ab"; // 从常量池中查找
        if (aa == bb) // true
            System.out.println("aa==bb");
        if (a == b) // false，非同一对象
            System.out.println("a==b");
        if (a.equals(b)) // true
            System.out.println("aEQb");
        if (42 == 42.0) { // true
            System.out.println("true");
        }
    }
}
```

String 中的 equals 方法是被重写过的，因为 object 的 equals 方法是比较的对象的内存地址，而 String 的 equals 方法比较的是对象的值。

当创建 String 类型的对象时，虚拟机会在<font color=#ffff>常量池</font>中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个 String 对象。

### <font color=#00ffff>两个对象的 hashCode()相同，则 equals()也一定为 true，对吗？</font>

当你把对象加入 HashSet 时，HashSet 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashcode 值作比较，如果没有相符的hashcode，HashSet会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用 equals（）方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。（摘自我的Java启蒙书《Head first java》第二版）。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。

hashCode（）与equals（）的相关规定
* 如果两个对象相等，则hashcode一定也是相同的
* 两个对象相等,对两个对象分别调用equals方法都返回true
* 两个对象有相同的hashcode值，它们也不一定是相等的
* 因此，equals 方法被覆盖过，则 hashCode 方法也必须被覆盖
* hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

### <font color=#00ffff> final 在 java 中有什么作用？</font>
[关于 final 关键字的一些总结](https://github.com/Snailclimb/JavaGuide/blob/master/Java/Java%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md#31-%E5%85%B3%E4%BA%8E-final-%E5%85%B3%E9%94%AE%E5%AD%97%E7%9A%84%E4%B8%80%E4%BA%9B%E6%80%BB%E7%BB%93)
final关键字主要用在三个地方：变量、方法、类。

* 对于一个final变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。
* 当用final修饰一个类时，表明这个类不能被继承。final类中的所有成员方法都会被隐式地指定为final方法。
* 使用final方法的原因有两个。第一个原因是把方法锁定，以防任何继承类修改它的含义；第二个原因是效率。在早期的Java实现版本中，会将final方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升（现在的Java版本已经不需要使用final方法进行这些优化了）。类中所有的private方法都隐式地指定为final。
### <font color=#00ffff>java 中的 Math.round(-1.5) 等于多少？</font>
-1
round(1.5) = 2

### String 属于基础的数据类型吗？

[引用数据类型](https://blog.csdn.net/YuanMxy/article/details/74170775)

### <font color=#00ffff>java 中操作字符串都有哪些类？它们之间有什么区别？</font>

### <font color=#00ffff>String str="i"与 String str=new String("i")一样吗？</font>
第一个“i”会先在常量池里面找，如果没有，会在常量池里面开辟空间给变量，并用str指向它；如果有的话，str指向这个已经存在的变量。new会新开一块空间给str，str会指向一个新的对象空间

### <font color=#00ffff>如何将字符串反转？</font>
[反转的方法](https://josh-persistence.iteye.com/blog/2205772)
```java

public class TestReverse {
	public static void main(String[] args) {
		String str2 = "Hello";
		str2 = new StringBuffer(str2).reverse().toString();
		System.out.println(str2);
 
		String message = "Hello";
		StringBuilder rev = new StringBuilder();
		for (int i = message.length() - 1; i >= 0; i--)
			rev.append(message.charAt(i));
		System.out.println(rev.toString());
 
	}
```
### <font color=#00ffff>String 类的常用方法都有那些？</font>
[StringBuffer, StringBuilder](https://github.com/Snailclimb/JavaGuide/blob/master/Java/Java%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md#12-string-stringbuffer-%E5%92%8C-stringbuilder-%E7%9A%84%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88-string-%E4%B8%BA%E4%BB%80%E4%B9%88%E6%98%AF%E4%B8%8D%E5%8F%AF%E5%8F%98%E7%9A%84)

### <font color=#00ffff>抽象类必须要有抽象方法吗？</font>

[不需要](https://blog.csdn.net/sunshinestation/article/details/4224768)

抽象类可以没有抽象方法，但是如果你的一个类已经声明成了抽象类，即使这个类中没有抽象方法，它也不能再实例化，即不能直接构造一个该类的对象。

<font color=#ff0000>如果一个类中有了一个抽象方法，那么这个类必须声明为抽象类，否则编译通不过。

另外，内部静态类是没有指向外部类的引用的，不会随外部类的初始化而初始化</font>

### <font color=#00ffff>普通类和抽象类有哪些区别？</font>

[抽象类的⚠️](https://www.cnblogs.com/chenssy/p/3376708.html)
* 抽象类不能被实例化，实例化的工作应该交由它的子类来完成，它只需要有一个引用即可。
* 抽象方法必须由子类来进行重写。
* 只要包含一个抽象方法的抽象类，该方法必须要定义成抽象类，不管是否还包含有其他方法。
* 抽象类中可以包含具体的方法，当然也可以不包含抽象方法。
* 子类中的抽象方法不能与父类的抽象方法同名。
* abstract不能与final并列修饰同一个类。
* abstract 不能与private、static、final或native并列修饰同一个方法。
* 父类方法为抽象方法时，子类必须重写（实现）所有父类的<font color=#ffff>抽象</font>方法；如果子类没有实现父类的抽象方法，则必须将子类也定义为为abstract类。
* 父类方法为普通方法时，子类可以重写父类方法，也可以不重写。

### <font color=#00ffff> 静态特点 </font>
* 全局唯一，任何一次的修改都是全局性的影响
* 只加载一次，优先于非静态
* 使用方式上不依赖于实例对象。
* 生命周期属于类级别，从JVM 加载开始到JVM卸载结束。
### <font color=#00ffff> 抽象类能使用 final 修饰吗? </font>
不能，抽象类不是不可变的，是需要被继承的

### <font color=#00ffff> 静态内部类与非静态内部类</font>

* 内部静态类不需要有指向外部类的引用。但非静态内部类需要持有对外部类的引用。

* 非静态内部类能够访问外部类的静态和非静态成员。静态类不能访问外部类的非静态成员。他只能访问外部类的静态成员。

* 一个非静态内部类不能脱离外部类实体被创建，一个非静态内部类可以访问外部类的数据和方法，因为他就在外部类里面。

```java
/* 下面程序演示如何在java中创建静态内部类和非静态内部类 */
class OuterClass{
  private static String msg = "GeeksForGeeks";
  // 静态内部类
  public static class NestedStaticClass{
    // 静态内部类只能访问外部类的静态成员
    public void printMessage() {
     // 试着将msg改成非静态的，这将导致编译错误 
     System.out.println("Message from nested static class: " + msg); 
    }
  }
  // 非静态内部类
  public class InnerClass{
    // 不管是静态方法还是非静态方法都可以在非静态内部类中访问
    public void display(){
     System.out.println("Message from non-static nested class: "+ msg);
    }
  }
} 
class Main
{
  // 怎么创建静态内部类和非静态内部类的实例
  public static void main(String args[]){
    // 创建静态内部类的实例
    OuterClass.NestedStaticClass printer = new OuterClass.NestedStaticClass();
    // 创建静态内部类的非静态方法
    printer.printMessage();  

    // 为了创建非静态内部类，我们需要外部类的实例
    OuterClass outer = new OuterClass();    
    OuterClass.InnerClass inner = outer.new InnerClass();／／这样new出来的
    // 调用非静态内部类的非静态方法
    inner.display();
    // 我们也可以结合以上步骤，一步创建的内部类实例
    OuterClass.InnerClass innerObject = new OuterClass().new InnerClass();
    // 同样我们现在可以调用内部类方法
    innerObject.display();
  }
}

 
// 其实就是静态类不用先创建外部类。可以静态类看做外部类的静态变量，使用就不要外部类实例；而非静态就必须先实例化。
```

### <font color=#00ffff> 接口和抽象类有什么区别？ </font>
[Refer to ...](https://github.com/Snailclimb/JavaGuide/blob/master/Java/Java%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md#17-%E6%8E%A5%E5%8F%A3%E5%92%8C%E6%8A%BD%E8%B1%A1%E7%B1%BB%E7%9A%84%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)

* 接口的方法默认是 public，所有方法在接口中不能有实现(Java 8 开始接口方法可以有默认实现），抽象类可以有非抽象的方法(可以实现)
* 接口中的实例变量默认是 final 类型的，而抽象类中则不一定
* 一个类可以实现多个接口，但最多只能实现一个抽象类
* 一个类实现接口的话要实现接口的所有方法，而抽象类不一定（抽象类中有非抽象方法）
* 接口不能用 new 实例化，但可以声明，但是必须引用一个实现该接口的对象 从设计层面来说，抽象是对类的抽象，是一种模板设计，接口是行为的抽象，是一种行为的规范。
* 抽象类实现某个接口，可以不实现所有接口的方法，可以由它的子类实现。而普通类即非抽象类则必须实现接口里的全部方法。

<font color=#ff0000>备注:在JDK8中，接口也可以定义静态方法，可以直接用接口名调用。实现类和实现是不可以调用的。如果同时实现两个接口，接口中定义了一样的默认方法，必须重写，不然会报错。</font>

### <font color=#00ffff>怎么防止重写</font>

[3 Ways to Prevent Method Overriding in Java - Private, Static and Final](https://javarevisited.blogspot.com/2015/04/3-ways-to-prevent-method-overriding-in.html)

static private final 都可以用来防止重写。如果在子类中重写父类的static、private方法，不会报错，但是在调用的时候会调用到父类的方法。final是最好的防止重写的方法，它直接告诉鳄梨programmer，这个方法是不可以被重写的，但是在这个类内是可以读的（readability advantage）

用了private，隐含了可以在子类中隐藏这些方法。在子类中“重写”的操作被称为“hidden method”。对它的调用在编译时被解析；而调用被重写的方法，在运行时解析

static也是在编译时被解析和绑定的；但是重写是在运行时被解析的

<font color="red">We can not override private method in Java, just like we can not override static method in Java. Like static methods, private method in Java is also bonded during compile time using static binding by Type information and doesn't depends on what kind of object a particular reference variable is holding. Since method overriding works on dynamic binding, its not possible to override private method in Java. private methods are not even visible to Child class, they are only visible and accessible in the class on which they are declared. private keyword provides highest level of Encapsulation in Java. Though you can hide private method in Java by declaring another private method with same name and different method signature.</font>

```java
public class PrivateMethodCanNotBeOverriden{
  
    public static void main(String args[]) {
        //shows that private method can not be overridden in Java     
        Parent parent = new Child();
    }
  
  
}

class Parent{
  
    public Parent(){
        name();
        normal();
    }
  
    private void name(){
        System.out.printf("private method inside Parent class in Java %n");
    }
  
    public void normal(){
        System.out.println("non private method from Parent class can be overridden");
    }
  
}

class Child extends Parent{
  
    /*
     * Private method can not be overridden in Java, they can only be hidden
     */
    private void name(){
        System.out.printf("private method inside Child class in Java %n");
    }
  
    @Override
    public void normal(){
        System.out.println("non private overridden method from Child class ");
    }
  
}


// Output
// private method inside Parent class in Java
// non private overridden method from Child class
```

[Read more](http://www.java67.com/2012/08/can-we-override-private-method-in-java.html#ixzz5ikTOX2jR) 

### <font color=#00ffff> 为什么java不支持多继承</font>
* 钻石问题
* 设计会很复杂，构造函数链、casting
    
cast是造型的意思当一个对象不能直接类型转换。就需要造型。 例如： 一个int型能直接转换为double，就不需要造型。 一个double型不能直接转换为int，就需要造型

### <font color=#00ffff> java 中 IO 流分为几种？</font>

### <font color=#00ffff> BIO、NIO、AIO 有什么区别？</font>

### <font color=#00ffff> Files的常用方法都有哪些？</font>

二、容器

18.java 容器都有哪些？

19.Collection 和 Collections 有什么区别？

20.List、Set、Map 之间的区别是什么？

21.HashMap 和 Hashtable 有什么区别？

22.如何决定使用 HashMap 还是 TreeMap？

23.说一下 HashMap 的实现原理？

24.说一下 HashSet 的实现原理？

25.ArrayList 和 LinkedList 的区别是什么？

26.如何实现数组和 List 之间的转换？

27.ArrayList 和 Vector 的区别是什么？

28.Array 和 ArrayList 有何区别？

29.在 Queue 中 poll()和 remove()有什么区别？

30.哪些集合类是线程安全的？

31.迭代器 Iterator 是什么？

32.Iterator 怎么使用？有什么特点？

33.Iterator 和 ListIterator 有什么区别？

34.怎么确保一个集合不能被修改？

三、多线程

35.并行和并发有什么区别？

36.线程和进程的区别？

37.守护线程是什么？

38.创建线程有哪几种方式？

39.说一下 runnable 和 callable 有什么区别？

40.线程有哪些状态？

41.sleep() 和 wait() 有什么区别？

42.notify()和 notifyAll()有什么区别？

43.线程的 run()和 start()有什么区别？

44.创建线程池有哪几种方式？

45.线程池都有哪些状态？

46.线程池中 submit()和 execute()方法有什么区别？

47.在 java 程序中怎么保证多线程的运行安全？

48.多线程锁的升级原理是什么？

49.什么是死锁？

50.怎么防止死锁？

51.ThreadLocal 是什么？有哪些使用场景？

52.说一下 synchronized 底层实现原理？

53.synchronized 和 volatile 的区别是什么？

54.synchronized 和 Lock 有什么区别？

55.synchronized 和 ReentrantLock 区别是什么？

56.说一下 atomic 的原理？

四、反射

57.什么是反射？

58.什么是 java 序列化？什么情况下需要序列化？

59.动态代理是什么？有哪些应用？

60.怎么实现动态代理？

五、对象拷贝

61.为什么要使用克隆？

62.如何实现对象克隆？

63.深拷贝和浅拷贝区别是什么？

六、Java Web

64.jsp 和 servlet 有什么区别？

65.jsp 有哪些内置对象？作用分别是什么？

66.说一下 jsp 的 4 种作用域？

67.session 和 cookie 有什么区别？

68.说一下 session 的工作原理？

69.如果客户端禁止 cookie 能实现 session 还能用吗？

70.spring mvc 和 struts 的区别是什么？

71.如何避免 sql 注入？

72.什么是 XSS 攻击，如何避免？

73.什么是 CSRF 攻击，如何避免？

## 异常

### <font color=#00ffff>throw 和 throws 的区别？</font>
throw与throws两者都是消极处理异常的方式：抛出或者可能抛出异常，但是不会由函数去处理异常，真正的处理异常由函数的上层调用处理。
```java
class Math{
    public int div(int i,int j) throws Exception{    // 定义除法操作，如果有异常，则交给被调用处处理
        int temp = i / j ;    // 计算，但是此处有可能出现异常
        return temp ;
    }
};
public class ThrowsDemo01{
    public static void main(String args[]){
        Math m = new Math() ;        
        try{
            System.out.println("除法操作：" + m.div(10,2)) ;
        }catch(Exception e){
            e.printStackTrace() ;    
        }
    }
};
public class ThrowsDemo02{
    // 在主方法中的所有异常都可以不使用try...catch进行处理
    // 异常会抛给JVM
    public static void main(String args[]) throws Exception{
        Math m = new Math() ;        
            System.out.println("除法操作：" + m.div(10,0)) ;
    }
};
```
### <font color=#00ffff> final、finally、finalize 有什么区别？</font>

### <font color=#00ffff> try-catch-finally 中哪个部分可以省略？</font>

### <font color=#00ffff> try-catch-finally 中，如果 catch 中 return 了，finally 还会执行吗？ </font>

### <font color=#00ffff>常见的异常类有哪些？</font>
[Java异常类层次结构图
](https://github.com/Snailclimb/JavaGuide/blob/master/Java/Java%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md#32-java-%E4%B8%AD%E7%9A%84%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86)

八、网络

79.http 响应码 301 和 302 代表的是什么？有什么区别？

80.forward 和 redirect 的区别？

81.简述 tcp 和 udp的区别？

82.tcp 为什么要三次握手，两次不行吗？为什么？

83.说一下 tcp 粘包是怎么产生的？

84.OSI 的七层模型都有哪些？

85.get 和 post 请求有哪些区别？

86.如何实现跨域？

87.说一下 JSONP 实现原理？

九、设计模式

88.说一下你熟悉的设计模式？

89.简单工厂和抽象工厂有什么区别？

# Spring/Spring MVC

[接口只有一个实现类的注入方式](https://www.jianshu.com/p/3942cce05f71
)

[接口由多个实现类的注入方式](
https://blog.csdn.net/u010476994/article/details/80986435)

### <font color=#00ffff>为什么要使用 spring？</font>