[互联网协议入门](http://www.ruanyifeng.com/blog/2012/05/internet_protocol_suite_part_i.html)

#  ***链路层***
# 以太网
确定0、1分组方式
# ***网络层***

# ARP
网络层协议

解析链路层以太网中的MAC地址

# ***传输层***
# ssl/tls
[基础安全专有名词解释](https://segmentfault.com/a/1190000011675421)

[微软详细介绍](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc785811(v=ws.10))

SSL 是个二进制协议，与 HTTP 完全不同，其流量是承载在另一个端口上的（443）

## 握手阶段：

1. 客户端请求
    
    版本号
    
    随机数
    
    session identifucation
    
    可以用的加密算法
    
    压缩方法

2. 服务器确认双方使用的加密方法，并给出数字证书、以及一个 服务器生成的随机数（Server random）。
    
    返回和客户端相同类型的数据，以及
    证书
    
    Server Key Exchange（当公钥无效时）
    
    Client Certificate Request（不一定有 看服务器要求）
    
    Server Hello Done

3. 客户端确认数字证书有效，基于之前的随机数生成一个新的随机数（Premaster secret），并使用数字证书中的公钥，加密这个随机数，发给服务器。

    Client Certificate（不一定需要）之后发送 Certificate Verify
    
    Client Key Exchange，确认服务器有私钥
    
    Change Cipher Spec. This message notifies the server that all messages that follow the Client Finished message will be encrypted using the keys and algorithms just negotiated.
    
    Client Finished. 

4. 服务器使用自己的私钥，获取爱丽丝发来的随机数（即Premaster secret）
    
    Change Cipher Spec Message. 
    
    Server Finished Message.

5. 双方根据约定的加密方法，使用前面的三个随机数，生成对话密钥（session key），用来加密接下来的整个对话过程。

## 握手阶段有三点需要注意：

1. 生成对话密钥一共需要三个随机数。
2. 握手之后的对话使用 对话密钥（session key） 加密（对称加密），服务器的公钥和私钥只用于加密和解密 对话密钥（session key）（非对称加密），无其他作用。
3. 整个通话的安全，只取决于 第三个随机数（Premaster secret）能不能被破解。
4. 服务器公钥放在服务器的数字证书之中。


# ***应用层***
# Socket

Socket是一组编程接口（API）。介于传输层和应用层，向应用层提供统一的编程接口。应用层不必了解TCP/IP协议细节。直接通过对Socket接口函数的调用完成数据在IP网络的传输。

它是网络通信过程中端点的抽象表示，包含进行网络通信必须的五种信息：连接使用的协议，本地主机的IP地址，本地进程的协议端口，远地主机的IP地址，远地进程的协议端口。

套接字之间的连接过程分为三个步骤：服务器监听，客户端请求，连接确认。

# push and pull
In push protocols, the client opens a connection to the server and keeps it constantly active. The **server** will send (push) all new events to the client using that single **always-on** connection. In other words, the server PUSHes the new events to the client.

In pull protocols, the client periodically connects to the server, checks for and gets (pulls) recent events and then closes the connection and disconnects from the server. The client repeats this whole procedure to get updated about new events. In this mode, the **clients periodically **PULLs the new events from the server.

# WebSocket VS polling VS long polling VS http streaming 

Duplex/directional:

    Uni-directional: HTTP poll, long poll, streaming.
    Bi-direcitonal: WebSockets, plugin networking

In order of increasing latency (approximate):

    WebSockets
    Plugin networking
    HTTP streaming
    HTTP long-poll
    HTTP polling

CORS (cross-origin support):

    WebSockets: yes
    Plugin networking: Flash via policy request (not sure about others)
    HTTP * (some recent support)

Native binary data (typed arrays, blobs):

    WebSockets: yes
    Plugin networking: not with Flash (requires URL encoding across ExternalInterface)
    HTTP *: recent proposal to enable binary type support

Bandwidth in decreasing efficiency:

    Plugin networking: Flash sockets are raw except for initial policy request
    WebSockets: connection setup handshake and a few bytes per frame
    HTTP streaming (re-use of server connection)
    HTTP long-poll: connection for every message
    HTTP poll: connection for every message + no data messages

Mobile device support:

    WebSocket: iOS 4.2 and up. Some Android via Flash emulation or using Firefox for Android or Google Chrome for Android which both provide native WebSocket support.
    Plugin networking: some Android. Not on iOS
    HTTP *: mostly yes
## polling
In short-polling, the client is constantly sending requests for data on a timer basis; in long-polling, the client makes one request and waits for the server to return any new data it has.

在短轮询情况下，客户端循环发送请求，服务器立即返回；长轮询：服务端接收请求后，如果响应没有变化，则将请求挂起，直到有变化或超过超时时间。服务器的响应是不规律的，取决于有没有数据更新。客户端一旦得到响应，再次发送新的请求。

在客户端大量的情况下，长轮询好于短轮询，不需要频繁地响应客户端；但如果更多，都不好，都会消耗服务端的资源。

## WebSocket

[阮一峰博客](http://www.ruanyifeng.com/blog/2017/05/websocket.html)

http是单向、半双工、轮询、无状态的

WebSocket 双向 全双工

在TCP和http之上。名称ws，（wss if tsl）

客户端通过更改http header，和服务端进行一次握手，建立全双工连接。It is persistent. To achieve compatibility, the WebSocket handshake uses the HTTP Upgrade header to change from the HTTP protocol to the WebSocket protocol. 

[为什么websocket在有代理、负载均衡的情况下会有问题](https://www.infoq.com/articles/Web-Sockets-Proxy-Servers)

## 代理
HTTP proxy servers — which were originally designed for document transfer — may choose to close streaming or idle WebSocket connections, because they appear to be trying to connect with an unresponsive HTTP server. This behavior is a problem with long-lived connections such as Web Sockets.

Additionally, proxy servers may also buffer unencrypted HTTP responses, thereby introducing unpredictable latency during HTTP response streaming.

代理会关闭一些长时间没有请求和响应的连接，但websocket应该是persistent的。而且可能会阻塞一些没有加密的响应，导致时延。

#### 另外，在两种场景explicit proxy和transparent proxy下，也会有问题

websocket协议是无法区分有没有防火墙或者代理的

**没有加密时，explicit**可以识别协议头：
```
CONNECT example.com:80 HTTP/1.1
Host: example.com 
```
然后可以建立一个的websocket握手

**没有加密时，对于transparent**代理，浏览器可能不会加上CONNECT，而且代理发给服务器的时候，可能会去掉一些websocket含义的报文头。服务器是需要配置的，不然它们可能识别不出这是websocket协议。另外```Connection: Upgrade```只在一跳中有用，如果是多跳的代理，那每一次转发之前都要加上一个Upgrade header

但在有tls的时候，transparent代理会直接让websocket的报文通过，99.99999999%会成功。

## 负载均衡
Since firewalls normally just enforce the rules for inbound traffic rejection and outbound traffic routing (for example, through the proxy server), there are usually no specific WebSocket traffic-related firewall concerns.

防火墙都规定好了路由的规则，所以一般不会影响websocket

负载均衡路由无法识别websocket


## Socket.io Library

It is event-driven communication（The communication between software components with asynchronously delivered data objects. A component emits the object, which is then delivered to all subscribed components.[diagram](https://en.wikipedia.org/wiki/Event-driven_messaging#/media/File:SOA_DP_Event-Driven_Messaging_B.JPG)）

一端注册事件，交给事件管理员。当有事件发生时发送给管理员，管理员发送消息给订阅了这个事件的另一端。



[websocket vs socket.io library](https://www.educba.com/websocket-vs-socket-io/)

## http streaming
不用80端口，用一个新的端口

# DHCP
应用层协议，建立在UDP协议之上

# 冲突域 广播域
## 冲突域

连接在同一导线上的所有工作站的集合，或者说是同一物理网段上所有节点的集合或**以太网上竞争同一带宽**的节点集合。这个域代表了冲突在其中发生并传播的区域，这个区域可以被认为是共享段。在OSI模型中，冲突域被看作是**第一层的概念**，连接同一冲突域的设备有Hub，Reperter或者其他进行简单复制信号的设备。

## 广播域

接收同样广播消息的节点的集合。如：在该集合中的任何一个节点传输一个广播帧，则所有其他能收到这个帧的节点都被认为是该广播帧的一部分。由于许多设备都极易产生广播，所以如果不维护，就会消耗大量的带宽，降低网络的效率。由于广播域被认为是OSI中的**第二层概念**，所以像Hub，交换机等第一，第二层设备连接的节点被认为都是在同一个广播域。而**路由器，第三层交换机则可以划分广播域，即可以连接不同的广播域**。

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


