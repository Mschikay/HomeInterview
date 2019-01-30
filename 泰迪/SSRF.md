---
title: SSRF
date: 2017-09-20 12:02:58
tags: WEB安全
---

# SSRF
定义
> SSRF（Server-Side Request Forgery, 服务端请求伪造）利用漏洞可以发起网络请求来攻击内网服务。

##### SSRF类型
* 对外发起网络请求的地方
* 从远程服务器请求资源
* 数据库的内置功能
* Webmail收取其他邮箱邮件（POP3/IMAP/SMTP）
* 文件处理，编码处理，属性处理（ffpmg,ImageMagic,DOCX,PDF,XML）

##### 利用协议
* http
* 利用file协议查看任意文件
* dict
* gopher
* php支持的协议
 >  file:// — Accessing local filesystem
    http:// — Accessing HTTP(s) URLs
    ftp:// — Accessing FTP(s) URLs
    php:// — Accessing various I/O streams
    zlib:// — Compression Streams
    data:// — Data (RFC 2397)
    glob:// — Find pathnames matching pattern
    phar:// — PHP Archive
    ssh2:// — Secure Shell 2
    rar:// — RAR
    ogg:// — Audio streams
    expect:// — Process Interaction Streams

* java支持的协议
>http，https，file，ftp，mailto，jar，netdoc

##### 攻击目标
1. struct2
2. redis
3. elasticsearch (远程命令执行)
4. docker (2375 remote api)
5. zabix
6. Memcached(读取管理员cookie等)
7. FastCGI

##### SSRF绕过
1. ip限制绕过(xip.io,十进制，8进制，0=linux(127.0.0.1))
2. 协议限制绕过(302跳转, CRLF 注入)
3. dns
4. http://192.168.40.154&@23.244.65.6#@115.169.180.121
5. curl http://192.168.40.154 @23.244.65.6 (这样可以发起两次http请求)
6. 
... 未完续待
