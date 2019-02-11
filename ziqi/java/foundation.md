#### java的三层架构
https://juejin.im/post/5929259b44d90400642194f3

#### 包的作用
1. 把功能相似或相关的类或接口组织在同一个包中，方便类的查找和使用。

2. 如同文件夹一样，包也采用了树形目录的存储方式。同一个包中的类名字是不同的，不同的包中的类的名字是可以相同的，当同时调用两个不同包中相同类名的类时，应该加上包名加以区别。因此，包可以避免名字冲突。

3. 包也限定了访问权限，拥有包访问权限的类才能访问某个包中的类。

Java 使用包（package）这种机制是为了防止命名冲突，访问控制，提供搜索和定位类（class）、接口、枚举（enumerations）和注释（annotation）等。

包语句的语法格式为：
```java
package pkg1[．pkg2[．pkg3…]];
```

#### 接口可以多继承，类不可以
“Java其实是支持多继承的，具体看法如下:Java中的接口支持多继承（接口与接口之间为继承关系），因为接口不提供具体实现方式，只是一种规范，所以支持；Java的类是不支持多继承的，原因是Java是强类型语言，多继承会导致调用的不确定性，所以Java的类不支持。”

#### 抽象类实现某个接口，可以不实现所有接口的方法，可以由它的子类实现。而普通类即非抽象类则必须实现接口里的全部方法。

#### 接口只有一个实现类的注入方式
https://www.jianshu.com/p/3942cce05f71

@Service，@Autowired

#### 接口由多个实现类的注入方式
https://blog.csdn.net/u010476994/article/details/80986435

```java
@Service(xx)
class yy implements zz
@Resource(name=xx),
or @Qualifier(yy)
```

#### 3 Ways to Prevent Method Overriding in Java - Private, Static and Final
https://javarevisited.blogspot.com/2015/04/3-ways-to-prevent-method-overriding-in.html

#### List, ArrayList, Array
List is an interface in Java, which means that it may have multiple implementations. One of these implementations is ArrayList, which is a class that implements the behavior of the List interface using arrays as the data structure.

https://www.geeksforgeeks.org/array-vs-arraylist-in-java/

