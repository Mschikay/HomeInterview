---
title: XMLHttpRequest
date: 2018-02-21 16:42:30
tags: Ajax XSS
categories: Ajax和Web安全
---

# Ajax和Web安全
## Ajax
> Ajax——异步javascript和xml，允许Web页面迅速的更新无需刷新的页面，可以让客户端只向服务器传输更新过的内容，借助客户端的javascript处理服务器响应

* Ajax必须严格遵守浏览器同源策略
> 同源是指：同域名（不仅仅是顶级域名相同，同级域名也必须相同），同端口，同协议

### XMLHttpRequest对象
> Ajax技术的核心，创建一个XMLHttpRequest对象的方法如下：

```
var XMLHttpReq=false;   /*创建XHR对象*/
    function creatXMLHttpRequest(){
        if(window.XMLHttpRequest){  /*非IE*/
            XMLHttpReq=new XMLHttpRequest();
        }else if(window.ActiveXobject){ /*IE*/
            try{
                XMLHttpReq=new ActiveXobject("Msxml2.XMLHTTP");
            }catch(e){
                try{
                    XMLHttpReq=new ActiveXobject("Microsoft.XMLHTTP");
                }catch(e){
                    XMLHttpReq=new false;
                }
            }
        }
    }
```

* XMLHttpRequest对象的方法
1. `abord()`:终止当前请求
2. `getAllResponseHeaders()`：把HTTP请求的所有相应首部作为键/值对返回
3. `getResponseHeaders("header")`：返回指定首部的串值
4. `open("method","url",[,async,username,password])`：建立对服务器的调用
5. `send(content)`：向服务器发送请求
6. `setRequestHead("header","value")`：把指定首部设置为所提供的值，在设置任何首部之前必须先调用open()方法

* XMLHttpRequest对象的属性
1. `onreadyState`：每个状态改变时都会触发这个属性，通常会调用一个JS函数
2. `readyState`：请求状态，**0未初始化，1打开一个请求，2发送一个请求，3正在接受响应，4响应加载完成**
3. `responseText`：文本格式的响应
4. `responseXML`：XML格式的响应，可以继续被作为合法的XML对象处理
5. `status`：服务器的HTTP响应状态码，200成功，301永久重定向,302临时重定向，304未修改，404未找到,500服务器内部错误,505HTTP版本不支持
6. `statusText`：HTTP状态的对应文本

###  HTTP请求
* GET请求

```
var xml=new creatXMLHttpRequest();  /*创建XHR对象*/
xml.open("GET","url.php?"+data,true);   /*初始化请求，设置请求方法，请求地址，是否异步*/
xml.send(); /*与服务器建立连接并发送数据*/
```

* POST请求

```
var xml=new creatXMLHttpRequest();  /*创建XHR对象*/
data="id=123&username=abc&password=123456"; /**/
xml.open("POST","url.php?",true);   /*初始化请求，设置请求方法，请求地址，是否异步*/
xml.setRequestHeader("Connect-Type","application/x-www-form-urlencode"); /*设置Connect-Type首部，告知服务器应该如何解析发送的数据*/
xml.send(data); /*与服务器建立连接并发送数据*/
```

* GET和POST请求的区别
    1. `GET` 会在URL中显示， `POST` 写在HTTP请求的body中；
    2. `GET` 请求发送数据量小， `POST` 请求发送数据量大
    3. `GET` 请求需要注意缓存， `POST` 请求不需要
### 简单的例子

```
var xml= new XMLHttpRequest(); /*初始化xhr请求对象*/ 
xml.open("GET","url",true); /*初始化请求，设置请求方法，请求地址*/ 
xml.onreadystatechange=function(){  /*设置文档更新后调用的函数*/ 
    if(xml.readyState==4&&xml.status==200){ /*判断对象的的状态*/ 
        /*xml.responseXML*/ 
        /*xml.responseText*/ 
        xml=null
    }
}；
xml.send();
```

## XSS Worm

