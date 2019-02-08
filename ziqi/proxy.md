## 一个完整的代理请求过程为：
#### 客户端首先与代理服务器创建连接，接着根据代理协议，请求对目标服务器创建连接、或者获得目标服务器的指定资源（如：文件）。如果客户端所要获取的资源在代理服务器的缓存之中，则代理服务器并不会向目标服务器发送请求，而是直接传回已缓存的资源。
#### 一些代理协议允许代理服务器改变客户端的原始请求、目标服务器的原始响应，以满足代理协议的需要。

## Functions
A proxy server uses a network addressing scheme to present one organization-wide IP address to the internet.proxy servers make it much more difficult for hackers to get internal addresses and details of a private network.

prevent inside users from reaching specific internet resources

A proxy server can also be one of the components of a firewall.

cache web pages

## 与NAT的不同
NAT，Network Address Translation 
####  A proxy server connects to, responds to, and receives traffic from the internet, acting on behalf of the client computer, while a NAT device transparently changes the origination address of traffic coming through it before passing it to the internet.

#### proxy server works on the transport layer (layer 4) or higher of the OSI model, whereas a NAT works on the network layer (layer 3).

## Forward Proxy
e.g. VPN 

#### it is on behalf of clients and protects clients.

A forward proxy provides proxy services to a client or a group of clients. Oftentimes, these clients belong to a common internal network like the one shown below.

A forward proxy is typically used in tandem with a firewall to enhance an internal network's security by controlling traffic originating from clients in the internal network that are directed at hosts on the Internet. Thus, from a security standpoint, a forward proxy is primarily aimed at enforcing security on client computers in your internal network.

## Reverse Proxy
#### A reverse proxy accepts requests from external clients on behalf of servers stationed behind it. 

#### In effect, whereas a forward proxy hides the identities of clients, a reverse proxy hides the identities of servers. 

The client is oblivious to the file transfer servers behind the proxy, which are actually providing those services. 

#### it can be
load balancers

set up to work alongside one or two firewalls to control traffic and requests directed to your internal servers. 


##### Both types of proxy servers relay requests and responses between source and destination machines. But in the case of reverse proxy servers, client requests that go through them normally originate from the Internet, while, in the case of forward proxies, client requests normally come from the internal network behind them.

