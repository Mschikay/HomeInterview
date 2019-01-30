---
title: JS中的this
date: 2018-02-21 16:42:30
tags: Javascript
categories: JS this
---

# JS中的this
## this指向的几种情况
* 在全局作用域中：指向全局作用域 `Window`
* 在函数内部：指向外层作用域
* 作为方法被调用：谁调用指向谁
* 使用apply/bind/call改变this指向

## 改变this指向
* apply()

    `var result=Person.apply(thisObj,[argArray]);   //传递的参数是数组,改变this并执行`

* bind()

    `var result=Person.bind(thisObj,[argArray])();    //传递的参数是数组，只改变this，不执行`

* call()

    `var result=Person.call(thisObj,Object);      //传递的参数是散队列,改变this并执行`

* 模拟call, apply的this替换

```
function Animal(name) {
   this.name = name;
   this.showName = function() {
     alert(this.name);
   };
 };
 function Cat(name) {
   this.superClass = Animal;
   this.superClass(name);
   delete superClass;
 }
 var cat = new Cat("Black Cat");
 cat.showName();
 ```
