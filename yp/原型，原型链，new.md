---
title: 原型 原型链 new的过程
date: 2018-04-21 16:42:30
tags: 原型 原型链 new的过程
categories: Javascript
---

# 原型，继承，new的过程

## 原型
> 除了null之外，每个JS对象都和另一个对象（原型）关联。
1. 所有通过对象直接量创建的对象都具有同一个原型Object.prototype
2. 通过new和构造函数创建的对象的原型是构造函数的prototype

## 继承
> 一个对象除了其自有属性外，还可以从其原型继承属性

## 原型链
> **对象的原型属性构成了一条 *原型链*，对象的属性通过原型链得到继承。**

> 查询某个对象的属性，如果该对象没有该属性，就去这个对象的原型查询，如果对象的原型也没有该属性，就去查找原型的原型，直到查找到Object.prototype。

1. 删除属性delete：只能删除对象的可操作自有属性（非只读），不能操作原型的属性。
2. 检测属性
      a. `hasOwnProperty()` 只检测自身属性  
      b. `propertyIsEnumberable()` 只检测可枚举自身属性   
      c. `...in...` 左边属性名，右边对象，自有/原型属性都检测  
      d. `isPropertyOf()` 自有/原型属性都检测
3. 枚举属性`for...in...`

### 原型链详解
如果有：

```
function People();  /*构造函数*/
var foo=new People();   /*实例*/
```

则有如下关系：

```
foo._proto_=People().prototype;

foo.custrom=People();
People().prototype.custrom=People();

People().prototype._proto_=Object().prototype;
People().prototype.custrom=Object();

```


## new的过程
 > new用来实例化一个对象，从而在内存中分配一个实例对象

   `var result=new Person();`

1. 创建一个实例对象

    `var obj={};`

2. 使用`instanceOf`让obj继承原型

    `obj instanceOf person`即：

    ```
    var L=obj._proto_;
    var R=Person.prototype;
    if(L===R){
    return true;
    }
    ```

3. 改变this的指向

    ` var result=Person.call(obj);`