---
title: CSS
date: 2018-02-21 16:42:30
tags: CSS
categories: CSS
---

* 单位
    1. 百分比
    2. px
    3. em
    4. rem


* 块级元素和内联元素
    * 块级元素
    > 块级元素是
    
    
    
* 外边距合并（高度坍缩）
> 块级元素的上外边距和下外边距有时会合并（或折叠）为一个外边距，其大小取其中的最大者。
   <br>浮动元素和绝对定位元素的外边距不会折叠。
    
    * 相邻元素之间
    * 父元素与其第一个或最后一个子元素之间
    * 空的块级元素
    
* 格式化上下文BFC
>

1. float的值不为none。
2. overflow的值不为visible。
3. position的值不为relative和static。
4. display的值为table-cell, table-caption, inline-block

* CSS选择器优先级
    1. !important>内联>id>class>标签(p、a、div)>通配符(*)>浏览器自定义
    