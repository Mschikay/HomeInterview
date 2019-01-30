---
title: 
date: 2018-02-21 16:42:30
tags: cookie
categories: cookie
---

# cookie

> cookie 就是浏览器储存在用户电脑上的一小段文本文件。cookie 是纯文本格式，不包含任何可执行的代码。
> cookie会随着用户的http请求被发送给服务器，服务器会根据cookie识别不同的用户

## 创建cookie
Web 服务器通过发送一个称为 Set-Cookie 的 HTTP 消息头来创建一个 cookie，Set-Cookie 消息头是一个字符串，其格式如下（中括号中的部分是可选的）：  
`Set-Cookie: name=value[; expires=date][; domain=domain][; path=path][; secure]`