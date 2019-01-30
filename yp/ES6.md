---
title: ES6笔记
date: 2018-03-21 16:42:30
tags: ES6
categories: Javascript
---

# ES6笔记

## var,let和const
* `var` 属于函数作用域；可以变量提升；在同一个作用域中可以重复声明；值可以更改；
* `let` 属于块级作用域；可以变量提升，但会形成暂时性死区TDZ；在同一个作用域中不可以重复声明；值可以更改；
* `const` 属于块级作用域；可以变量提升，但会形成暂时性死区TDZ；在同一个作用域中不可以重复声明；常量，值不可以更改；

## 变量的结构赋值
```
let [a,b,c]=[1,2,3];
let [a,[b,c]] = [1,[2,3]];
let [a, ...b] = [1, 2, 3, 4];
a // 1
b // [2, 3, 4]
```

## 箭头函数
* 箭头函数可以隐式返回（不加return）
* 箭头函数没有自己的this值，他的this值继承自父级作用域
* 箭头函数没有arguments对象
* 不适用于：
    1. 构造函数或作为一个需要绑定到对象的方法
    2. 需要使用arguments对象
    3. 需要使用this时

## 模板字符串

```
const name='Boris';
    const age=9;
    const sentence=`${name} is ${age}`;
    console.log(sentence);
    const templlate=`<div><h1>nihao</h1></div>`;
    console.log(templlate);
```
* TODO List
```
 const Boris={
        name:'Boris',
        date:'2018-05-24',
        todos:[
            {name:'Go school',completed:false},
            {name:'Go home',completed:true}
        ]
    };
    const templete=`
            <ul>
                ${Boris.todos.map(todo=>`
                    <li>
                        ${todo.name} ${todo.completed? '完成':'未完成'}
                    </li>`).join('')}
            </ul>`
    console.log(templete);
    document.body.innerHTML=templete;
```

* 模板字面量的拼接

```
    function sanitize(strings,...values){
        console.log(strings[0]);
        console.log(strings[1]);
        console.log(strings[2]);
        const dirty=strings.reduce((prev,curr,i)=>`${prev}${curr}${values[i]||''}`,'');     //reduce()方法拼接数组中的元素
    }
    document.body.innerHTML=sanitize`<div class="comment-header">${user}</div><div class="comment-body">${textarea.value}</div>`;
```

## 字符串方法
* string.prototype.startWith
```
const str='1234567890';
console.log(str.startWith('123'));  //true
console.log(str.startWith('456',3));    //true
console.log(str.startWith('456',5));    //false
```

* string.prototype.endWith
```
const str='1234567890';
console.log(str.endWith("890"));    //true
console.log(str.endWith("89"));    //flase
console.log(str.endWith("89",9));    //true
```

* string.prototype.includes
```
const str="Hello word";
str.includes("hello");  //false
str.includes("Hello");  //true
```

* string.prototype.repeat
```
const str="ha";
str.repeat(4);  //hahahaha
const heading=`${'='.repeat(5)} ${str.repeat(4)} ${'='.repeat(5)}`;     //"=====hahahaha====="
```

## 解构赋值
> 解构赋值语法是一个 `Javascript` 表达式，这使得可以将值从数组或属性从对象提取到不同的变量中。

### 对象解构

```
const Tom={
        name:'Tom Smith',
        age:'19',
        family:{
            mother:'Lily Smith',
            father:'Jon Smith',
            brother:'Sam Smith',
            sister:undefined
        }
    };
    const {father,mother,brother,sister='sister is undefined'}=Tom.family;
    const {name,age}=Tom;
    console.log(brother);   //Sam Smith
    <!-- 只有值明确为undefined时才会使用默认值，null、false、0都不会使用默认值 -->
    console.log(sister);    //sister is undefined
    console.log(name);  //Tom Smith
    console.log(age);   //19
    const {mother:m}=Tom.family;
    console.log(mother);    //Lily Smith
    console.log(m);    //Lily Smith
    const f='father';
    const {father:f}=Tom.family;    //error
    console.log(father);    //Identifier 'f' has already been declared
    console.log(f);     //Identifier 'f' has already been declared
```

### 数组解构

```
const[a,b]=[10,20];
console.log(a); //a=10
console.log(b); //=20
const [c,d,...rest]=[40,50,1,2,3,4];
console.log(c); //c=40
console.log(d); //d=50
console.log(rest);  //rest=[1,2,3,4]
const [q,,w]=[11,12,13];
console.log(q); //q=11
console.log(w); //w=13
<!-- 交换变量的值 -->
let e=9;
let r=0;
[e,r]=[r,e];
console.log(e,r);  //0,9
```

## for of循环
### forEach循环
> 不能终止循环或跳过循环

```
  const arr=['a','b','c','d'];
    arr.forEach(arry=>{
        console.log(arry);      //a,b,c,d
        if(arry==='c'){
            break;      //Error!!!
        }
    })
```

### for in循环
> for in循环遍历的是属性名而不是属性值；会遍历所有可枚举属性（原型和之后添加的）；不支持break和continue。

```
Array.prototype.first=function(){return this[0]};
    const arr=['a','b','c','d'];
    arr[4]='e';
    for (let index in arr){
        console.log(index);     //0,1,2,3,4,first
        console.log(arr[index]);    //a,b,c,d,ƒ (){return this[0]}
    }
```

### for of循环
> for of不会遍历非数字属性

```
Array.prototype.first=function(){return this[0]};
    const arr=['a','b','c','d'];
    arr[4]='e';
    arr.second='m';
    for (let arry of arr){
        console.log(arry);     //a,b,c,d,e
    }
```

##
##
##
##
##
##
##
##
