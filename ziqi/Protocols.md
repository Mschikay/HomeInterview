# HyperText Transfer Protocol
应用层协议

尽管TCP/IP协议是互联网上最流行的应用，HTTP协议中，并没有规定必须使用它或它支持的层。HTTP可以在任何互联网协议上，或其他网络上实现。HTTP假定其下层协议提供可靠的传输。因此，任何能够提供这种保证的协议都可以被其使用。因此也就是其在TCP/IP协议族使用TCP作为其传输层。

## 请求方法
#### GET
读
#### HEAD
与GET方法一样，都是向服务器发出指定资源的请求。只不过服务器将不传回资源的本文部分。它的好处在于，使用这个方法可以在不必传输全部内容的情况下，就可以获取其中“关于该资源的信息”（元信息或称元数据）。
#### POST
写
#### PUT
向指定资源位置上传其最新内容。
#### DELETE
请求服务器删除Request-URI所标识的资源（幂等，包括GET DELETE PUT TRACE OPTIONS）
#### TRACE
回显服务器收到的请求，主要用于测试或诊断。
#### OPTIONS
这个方法可使服务器传回该资源所支持的所有HTTP请求方法。用'*'来代替资源名称，向Web服务器发送OPTIONS请求，可以测试服务器功能是否正常运作。
#### CONNECT
HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。通常用于SSL加密服务器的链接（经由非加密的HTTP代理服务器）。

##状态码

1xx消息——请求已被服务器接收，继续处理

2xx成功——请求已成功被服务器接收、理解、并接受

3xx重定向——需要后续操作才能完成这一请求

4xx请求错误——请求含有词法错误或者无法被执行

5xx服务器错误——服务器在处理某个正确请求时发生错误

## 请求
https://pic002.cnblogs.com/images/2012/263119/2012022009421183.png

请求行（例如GET /images/logo.gif HTTP/1.1，表示从/images目录下请求logo.gif这个文件）

请求头（例如Accept-Language: en。所有的请求头，除Host外，都是可选的。


空行

其他消息体

```
GET / HTTP/1.1
Host: www.google.com
```

## 响应
https://pic002.cnblogs.com/images/2012/263119/2012022009373182.png

```
HTTP/1.1 200 OK
Content-Length: 3059
Server: GWS/2.0
Date: Sat, 11 Jan 2003 02:44:04 GMT
Content-Type: text/html
Cache-control: private
Set-Cookie: PREF=ID=73d4aef52e57bae9:TM=1042253044:LM=1042253044:S=SMCc_HRPCQiqy
X9j; expires=Sun, 17-Jan-2038 19:14:07 GMT; path=/; domain=.google.com
Connection: keep-alive
```


# SOCKS反向代理
#### SOCKS是会话层的协议，位于表示层与传输层之间。

SOCKS5是第五版，相对第四版作了大幅度的增强。首先，它增加了对UDP协议的支持；其次，它可以支持多种用户身份验证方式和通信加密方式；最后，修改了SOCKS服务器进行域名解析的方法，使其更加优雅。经过这次脱胎换骨的升级，SOCKS5于1996年被IETF确认为标准通信协议，RFC编号为1928。经过10余年的时间，大量的网络应用程序都支持SOCKS5代理。 

尽管从SOCKS4开始，就支持打开TCP监听端口，但是直到SOCKS5，也只允许这个端口接收一个客户端连接。因此网络服务提供者（如http服务器）不能使用SOCKS。实际上，很多SOCKS服务器的实现也不支持打开TCP监听端口

虽然说设计SOCKS协议的初衷是在保证网络隔离的情况下，提高部分人员的网络访问权限，但是国内似乎很少有组织机构这样使用。一般情况下，大家都会使用更新的网络安全技术来达到相同的目的。

