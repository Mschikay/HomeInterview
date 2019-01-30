---
title: BOM
date: 2018-02-21 16:42:30
tags: html
categories: html基础
---

#BOM
> BOM的总结

* split("")
> 分割字符串,以""为界，变为数组
* decodeURICompontent()
> 解码

##location对象
* location.assign("URL")或window.location="URL"或location.href="URL"
> 给浏览器传入URL（会生成记录）
* location.replace("URL")
> 给浏览器传入URL（不会生成记录）
* location.reload(true)
> true直接从服务器加载，false优先从缓存加载

##window对象
* window.open()
> 有四个参数
* top对象
> 时钟指向最外围的框架，也就是整个浏览器
* parent对象
> 指向包含当前框架的框架
* self对象
> 回指window

##navigator对象
> 浏览器信息，不常用

##plugins数组
> 检测插件,**《JavaScript高级程序设计》P211**

##screen对象
> 客户端能力

##history对象
* history(-1)和history.back()
> 后退一页
* history(1)和history.forward()
> 前进一页
* history.go("baidu.com")
> 去最近的有baidu.com的页面
* history.length
> 打开过所有页面的数量

##超时调用和间歇调用
* setTimeout()超时调用
> 等待一定时间后执行。有两个参数。使用clearTimeout()清除。
* setInterval()间歇调用
> 间隔一段时间循环执行。有两个参数。使用clearInterval()清除。

以下两个例子功能相同

    var nam=0;
    var max=10;
    var intervalId=null;
    function incrementNumber(){
        num++;
        if (num==max){
            clearInterval(intervalId);
            alert("Done");
        }
    }
    intervalId=setInterval(incrementNumber,500);
--------------------------
    var num=0;
    var max=10;
    function increment(){
        num++;
        if(num<max){
            setTimeout(incrementNumber,500);
        }else{
            alert("Done");
        }
    }
    setTimeout(incrementNumber,500);
    

 

























