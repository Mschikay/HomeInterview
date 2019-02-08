#### 查看官方文档--怎么使用repository接口
https://docs.spring.io/spring-data/data-document/docs/current/reference/html/#repositories

#### 一篇比较精简的mogodb example
https://www.mkyong.com/spring-boot/spring-boot-spring-data-mongodb-example/

#### postman 404
controller不在启动文件所在但包内，没有显示地让服务器知道。

可以用scanBasePackages，但更好的方法是，把其他包放在启动文件所在包内，就可以扫描到所有的包内的文件了。

#### 405 Request method 'POST' not supported


#### 'Field required a bean of type that could not be found.' error spring restful API using mongodb

在继承接口时定义函数有问题。不能随便定义，可能与泛型有关？

#### autowired
With many beans in project, this process became tedious. So then in later version Spring introduced annotation based wiring using @Autowired annotation. This needs to be placed directly in Java file just above member defintion. This member is an interface member. Spring automatically then would initialize this member with the object of the implementor of this interface. If there are multiple implementations then qualifier attribute can be added to autowired annotation. No need to define these dependencies through xml. Eg.

会自动地实现接口
```java
Class Transport{
 
  @Autowired
  @Qualifier("car") 
  Public Vehicle v;
  ...... 
 
} 
```

