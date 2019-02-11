#### 查看官方文档--怎么使用repository接口
https://docs.spring.io/spring-data/data-document/docs/current/reference/html/#repositories

#### 一篇比较精简的mogodb example
https://www.mkyong.com/spring-boot/spring-boot-spring-data-mongodb-example/

#### postman 404
controller不在启动文件所在但包内，没有显示地让服务器知道。

可以用scanBasePackages，或者@ComponentScan({"package2", "package2"})，但更好的方法是更改整个项目的目录结构，把其他包放在启动文件所在包内，就可以扫描到所有的包内的文件了。

#### 405 Request method 'POST' not supported （应该也是和上个问题类似的情况）

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
#### 在controller里面定义main 函数， Exception in thread "main" java.lang.NullPointerException

发现这个null其实是因为
```java
private Repository repository;
// repository is null
```

https://stackoverflow.com/questions/19896870/why-is-my-spring-autowired-field-null

#### 一个简单的登陆例子（但花了我很长时间= =）
```java
@RestController
@RequestMapping("/user")
public class UserController {
	
  @Autowired
  private UserRepository repository;
	
  // login
 	@RequestMapping(value="/login", method=RequestMethod.POST, consumes=MediaType.APPLICATION_JSON_VALUE)
 	@ResponseStatus(value=HttpStatus.OK)
 	@ResponseBody
 	public String login(@RequestBody JSONObject jsonObject) {
 		String email = (String) jsonObject.get("email");
 		String password = (String) jsonObject.get("password");
 		
 		User user = repository.findByEmail(email);
 		if (user == null) {
 			return "not found, failed";
 		}
 		
 		String p = user.getPassword();
 		if (p.equals(password)) {
 			return user.getUsername();
 		}
 		return "wrong password, failed!";
 	}
```
@RestController，相当于@Controller + @ResponseBody

    @ResponseBody annotation and, therefore, writes directly to the response body versus view resolution and rendering with an HTML template.

    有@Controller的地方隐含了这个类是一个web组件。用于让程序识别出这个类，并将它自动注册到bean里。（？）
    The @Controller stereotype allows for auto-detection, aligned with Spring general support for detecting @Component classes in the classpath and auto-registering bean definitions for them. It also acts as a stereotype for the annotated class, indicating its role as a web component.

@RequestMapping注入了url地址

@RequestBody，会让程序自己map到HttpRequestd body，并解析成java的对象/类型。
    The body of the request is passed through an HttpMessageConverter to resolve the method argument depending on the content type of the request. Optionally, automatic validation can be applied by annotating the argument with @Valid.

    如果不加这个注解，在接受json的时候，无法解析request的内容，报错。

findByEmail是接口UserRepository中的方法。UserRepository直接继承了MongoRepository，可以按照MongoRepository定义的规则定义自己的函数。@Autowired后自动注入了User，不需要再显示的实现接口中的函数了。注意⚠️，这不是customized interface，如果要自己新定义函数，需要在定义一个接口，被UserRepository继承，并在UserController中实现

⚠️ “==”会比较地址

#### 为什么会出现optional <product>
