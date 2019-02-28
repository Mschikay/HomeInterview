## 从网上整理的资料
https://www.ibm.com/developerworks/cn/education/java/j-intserv/j-intserv.html

https://www.ibm.com/developerworks/cn/java/j-lo-servlet/index.html

https://www.ibm.com/developerworks/cn/java/j-lo-tomcat1/index.html

### Tomcat 
#### Tomcat容器模型
https://www.ibm.com/developerworks/cn/java/j-lo-servlet/image002.jpg
 
Container 是容器的父接口，所有子容器都必须实现这个接口，Container 容器的设计用的是典型的责任链的设计模式，它有四个子容器组件构成，分别是：Engine、Host、Context、Wrapper，这四个组件不是平行的，而是父子关系，Engine 包含 Host,Host 包含 Context，Context 包含 Wrapper。通常一个 Servlet class 对应一个 Wrapper，如果有多个 Servlet 就可以定义多个 Wrapper，如果有多个 Wrapper 就要定义一个更高的 Container 了。

Tomcat 的容器分为四个等级，真正管理 Servlet 的容器是 Context 容器，一个 Context 对应一个 Web 工程，在 Tomcat 的配置文件中可以很容易发现这一点

Tomcat7 也开始支持嵌入式功能，增加了一个启动类 org.apache.catalina.startup.Tomcat。创建一个实例对象并调用 start 方法就可以很容易启动 Tomcat，我们还可以通过这个对象来增加和修改 Tomcat 的配置参数，如可以动态增加 Context、Servlet 等。

#### 启动逻辑
Tomcat 的启动逻辑是基于观察者模式设计的，所有的容器都会继承 Lifecycle 接口，它管理容器的整个生命周期，所有容器的修改和状态的改变都会由它去通知已经注册的观察者（Listener），关于这个设计模式可以参考《 Tomcat 的系统架构与设计模式，第二部分：设计模式》。

#### 为什么要将 Servlet 包装成 StandardWrapper 而不直接是 Servlet 对象
这里 StandardWrapper 是 Tomcat 容器中的一部分，它具有容器的特征，而 Servlet 为了一个独立的 web 开发标准，不应该强耦合在 Tomcat 中。

除了将 Servlet 包装成 StandardWrapper 并作为子容器添加到 Context 中，其它的所有 web.xml 属性都被解析到 Context 中，所以说 Context 容器才是真正运行 Servlet 的 Servlet 容器。一个 Web 应用对应一个 Context 容器，容器的配置属性由应用的 web.xml 指定，这样我们就能理解 web.xml 到底起到什么作用了。

#### 初始化servlet
初始化 Servlet 在 StandardWrapper 的 initServlet 方法中，这个方法很简单就是调用 Servlet 的 init 的方法，同时把包装了 StandardWrapper 对象的 StandardWrapperFacade 作为 ServletConfig 传给 Servlet。

StandardWrapper 和 StandardWrapperFacade 都实现了 ServletConfig 接口，而 StandardWrapperFacade 是 StandardWrapper 门面类。所以传给 Servlet 的是 StandardWrapperFacade 对象，这个类能够保证从 StandardWrapper 中拿到 ServletConfig 所规定的数据，而又不把 ServletConfig 不关心的数据暴露给 Servlet。

#### servlet体系结构
https://www.ibm.com/developerworks/cn/java/j-lo-servlet/image010.jpg
从上图可以看出 Servlet 规范就是基于这几个类运转的，与 Servlet 主动关联的是三个类，分别是 ServletConfig、ServletRequest 和 ServletResponse。

这三个类都是通过容器传递给 Servlet 的，其中 ServletConfig 是在 Servlet 初始化时就传给 Servlet 了，而后两个是在请求达到时调用 Servlet 时传递过来的。

ServletConfig 接口中声明的方法都是为了获取这个 Servlet 的一些配置属性，而这些配置属性可能在 Servlet 运行时被用到。而 ServletContext 又是干什么的呢？ Servlet 的运行模式是一个典型的“握手型的交互式”运行模式。所谓“握手型的交互式”就是两个模块为了交换数据通常都会准备一个交易场景，这个场景一直跟随个这个交易过程直到这个交易完成为止。这个交易场景的初始化是根据这次交易对象指定的参数来定制的，这些指定参数通常就会是一个配置类。所以对号入座，交易场景就由 ServletContext 来描述，而定制的参数集合就由 ServletConfig 来描述。而 ServletRequest 和 ServletResponse 就是要交互的具体对象了，它们通常都是作为运输工具来传递交互结果。

Servlet 中能拿到的 ServletContext 的实际对象也是 ApplicationContextFacade 对象。ApplicationContextFacade 同样保证 ServletContex 只能从容器中拿到它该拿的数据，它们都起到对数据的封装作用，它们使用的都是门面设计模式。

#### Servlet 如何工作
当用户从浏览器向服务器发起一个请求，通常会包含如下信息：http://hostname: port /contextpath/servletpath，hostname 和 port 是用来与服务器建立 TCP 连接，而后面的 URL 才是用来选择服务器中那个子容器服务用户的请求。服务器是如何根据这个 URL 来达到正确的 Servlet 容器中的呢？

Tomcat7.0 中这件事很容易解决，因为这种映射工作有专门一个类来完成的，这个就是 org.apache.tomcat.util.http.mapper，这个类保存了 Tomcat 的 Container 容器中的所有子容器的信息，当 org.apache.catalina.connector. Request 类在进入 Container 容器之前，mapper 将会根据这次请求的 hostname 和 contextpath 将 host 和 context 容器设置到 Request 的 mappingData 属性中。所以当 Request 进入 Container 容器之前，它要访问那个子容器这时就已经确定了。

##### mapper 中怎么会有容器的完整关系?

MapperListener 类作为一个监听者加到整个 Container 容器中的每个子容器中，这样只要任何一个容器发生变化，MapperListener 都将会被通知，相应的保存容器关系的 MapperListener 的 mapper 属性也会修改。

##### 请求到达最终的 Servlet 还要完成一些步骤，必须要执行 Filter 链，以及要通知你在 web.xml 中定义的 listener。

Servlet 的确已经能够帮我们完成所有的工作了，但是现在的 web 应用很少有直接将交互全部页面都用 servlet 来实现，而是采用更加高效的 MVC 框架来实现。这些 MVC 框架基本的原理都是将所有的请求都映射到一个 Servlet，然后去实现 service 方法，这个方法也就是 MVC 框架的入口。

当 Servlet 从 Servlet 容器中移除时，也就表明该 Servlet 的生命周期结束了，这时 Servlet 的 destroy 方法将被调用，做一些扫尾工作。

#### Listener
整个 Tomcat 服务器中 Listener 使用的非常广泛，它是基于观察者模式设计的，Listener 的设计对开发 Servlet 应用程序提供了一种快捷的手段，能够方便的从另一个纵向维度控制程序和数据。

### Servlet
它驻留在 Web 服务器上，处理新来的请求和输出的响应。它与表示无关

它们与 Java 语言一样是与平台无关的

它们允许您完全访问整个 Java 语言 API，包括数据访问库（如 JDBC）

大多数情况下，它们内在地比 CGI 更高效，因为 servlet 为请求派生新的线程，而非不同的进程

#### 什么是servlet
在创建一个 Java servlet 时，一般需要子类 HttpServlet。该类中的方法允许您访问请求和响应包装器（wrapper），您可以用这个包装器来处理请求和创建响应。

Java servlet 类将那些低层的结构包装在 Java 类中，这些类所包含的便利方法使其在 Java 语言环境中更易于处理。正如您正使用的特定 servlet 容器的配置文件中所定义的，当用户通过 URL 发出一个请求时，这些 Java servlet 类就将之转换成一个 HttpServletRequest，并发送给 URL 所指向的目标。当服务器端完成其工作时，Java 运行时环境（Java Runtime Environment）就将结果包装在一个 HttpServletResponse 中，然后将原 HTTP 响应送回给发出该请求的客户机。在与 Web 应用程序进行交互时，通常会发出多个请求并获得多个响应。所有这些都是在一个会话语境中，Java 语言将之包装在一个 HttpSession 对象中。在处理响应时，您可以访问该对象，并在创建响应时向其添加事件。它提供了一些跨请求的语境。

容器（如 Tomcat）将为 servlet 管理运行时环境。您可以配置该容器，定制 J2EE 服务器的工作方式，而且您必须 配置它，以便将 servlet 暴露给外部世界。正如我们将看到的，通过该容器中的各种配置文件，您在 URL（由用户在浏览器中输入）与服务器端组件之间搭建了一座桥梁，这些组件将处理您需要该 URL 转换的请求。在运行应用程序时，该容器将加载并初始化 servlet，管理其生命周期。

#### web.xml
```java
<!DOCTYPE web-app PUBLIC '-//Sun Microsystems, Inc.//DTD 
    Web Application 2.3//EN' 'http://java.sun.com/dtd/web-app_2_3.dtd'>
<web-app>
     <servlet>
          <servlet-name>contacts</servlet-name>
          <servlet-class>com.roywmiller.contacts.model2.ContactsServlet</servlet-class>
     </servlet>
      
     <servlet-mapping>
         <servlet-name>contacts</servlet-name>
         <url-pattern>/index.htm</url-pattern>
     </servlet-mapping>
      
     <servlet-mapping>
         <servlet-name>contacts</servlet-name>
         <url-pattern>*.perform</url-pattern>
     </servlet-mapping>
 
     <servlet>
          <servlet-name>jspAssign</servlet-name>
          <servlet-class>org.apache.jasper.servlet.JspServlet</servlet-class>
          <init-param>
               <param-name>logVerbosityLevel</param-name>
               <param-value>WARNING</param-value>
          </init-param>
          <init-param>
               <param-name>fork</param-name>
               <param-value>false</param-value>
          </init-param>
          <load-on-startup>3</load-on-startup>
     </servlet>
      
     <servlet-mapping>
          <servlet-name>jspAssign</servlet-name>
          <url-pattern>/*.jsp</url-pattern>
     </servlet-mapping>
</web-app>
```

<servlet> 标签为 servlet 指定一个别名，我们将在该文件的别处使用它。它还告诉 Tomcat 实例化哪个类，以便在内存中创建 servlet。在我的 Eclipse 工作区中，我创建了 com.roywmiller.contacts.model2 包来保存该 servlet 类。无论需要什么，都可以调用我们的包，但是到 servlet 的路径必须匹配 <servlet-class> 元素中的内容。我们定义的第二个 servlet 是下载 Tomcat 时附带的，您不必修改它。它只是 JSP 正在处理的 servlet。

<servlet-mapping> 告诉 Tomcat 当某个 URL 到达服务器时，执行哪个 servlet。我们这里有三个映射。第一个将 Web 服务器查找的默认页面（<index.htm>）映射到 servlet。第二个告诉 Tomcat 将以 .perform 结尾的 URL 映射到 servlet。该形式的 URL 将告诉 servlet 实现哪个动作（稍后，我们将更详细地讨论其工作方式）。第三个映射告诉 Tomcat 使用 JSP servlet 来处理 JSP 页面。

