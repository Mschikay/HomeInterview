#### java的三层架构
https://juejin.im/post/5929259b44d90400642194f3

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

