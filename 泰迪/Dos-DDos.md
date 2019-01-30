---
title: Dos&DDos
date: 2017-09-24 08:09:31
tags: WEB安全
---
## Dos
### Dos分类
* D网络
     基于巨量的Flood耗尽目标网络的带宽资源
     ICMP Flood、UDP Flood
* D协议
     攻击协议漏洞发起的拒绝服务攻击
     如Syn Flood、Ping of Death、ARP、DNS、802.11、SSL
* D应用
     针对应用软件和操作系统的漏洞发起的拒绝服务攻击
     大量频繁访问消耗系统资源严重的应用（CC）
     通常表现为操作系统运行正常、网络流量不大、但是停止服务
     可以是一击致命，也可以耗尽目标资源


### 实例
* Smurf攻击（局域网）
世界上最古老的DDoS攻击技术
 向局域网地址发送伪造源地址的ICMP echo Request（icmp）包
 LAN所有计算机向伪造源地址返回响应包
 对现在操作系统几乎无效（不响应目标为广播的ping）

* Sockstress
2008年由Jack C Louis发现
针对TCP服务的拒绝服务攻击
 消耗被攻击目标系统资源
 与攻击目标建立大量的socket连接
 完成三次握手，最后ACK包 window 大小为0（客户端不接收数据）
 攻击者资源消耗小（CPU、内存、带宽）
 异步攻击、单机可拒绝服务高配置资源服务器
 Windos窗口实现的TCP流控

防御措施
 直到今天sockstress攻击仍然是一种很有效的DoS攻击方式
 由于建立完整的TCP三次握手，因此使用syn cookie防御无效
 根本的防御方法是采用白名单（不实际）
 折中对策：限制单位时间内每ip建的TCP连接数
      封杀每30秒与80端口连接超过10个的IP地址
      iptables -I INPUT -p tcp --dport 80 -m state --state NEW -m recent --set
     iptables -I INPUT -p tcp --dport 80 -m state --state NEW -m recent --update --seconds 30 --hitcount 10 -j DROP
  以上规则对DDoS无效

* DNS放大攻击
产生大流量的攻击方法
     单机的带宽优势
     巨大单机数量形成的流量汇聚
     利用协议特性实现放大效果的流量
DNS协议放大效果
     查询请求流量小，但响应流量非常巨大
     dig ANY hp.com @202.106.0.20(流量放大约8被)
攻击原理
     伪造源地址为被攻击目标地址，向递归域名查询服务器发起请求
     DNS服务器称为流量放大和实施攻击者，大量DNS服务器实现DDoS

* SYN洪水攻击
在建立TCP连接的过程中，如果在服务器返回SYN+ACK报文后，客户端没有对其进行确认，服务器就需要重传SYN+ACK报文，并等待客户端的确认报文直到TCP连接超时。

一篇不错的文章，写的比我详细
(传送门)[https://www.secpulse.com/archives/64088.html]


