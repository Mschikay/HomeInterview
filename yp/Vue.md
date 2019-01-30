---
title: Vue学习
date: 2018-02-21 16:42:30
tags: Javascript 
categories: Vue
---

# Vue学习
## 数据渲染，模块显示隐藏，渲染循环列表

```
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>vue1</title>
    <!--引入vue-->
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <style>
        .show {
            width: 100px;
            height: 100px;
            background: red;
        }
    </style>
</head>

<body>
<div id="app">
    <!--数据渲染"v-title"和"v-html"-->
    <p v-text="message"></p>
    <p v-html="message"></p>
    <p v-html="title"></p>
    <p v-text="title"></p>

    <!--控制模块显示隐藏"v-if"和"v-show"-->
    <p class="show" v-if="isShow"></p>
    <p class="show" v-show="isShow"></p>

    <!--渲染循环列表"v-for"-->
    <ul>
        <li v-for="item in arr">
            {{item}}
        </li>
    </ul>
</div>

</body>

<script>
    var app = new Vue({
        el: "#app",
        data: {
            message: "Hello Word!",
            title: "<h2>指令学习</h2>",
            isShow: false,
            arr: ['1 html', '2 css', '3 javascript']
        }
    })
</script>
</html>
```

## 事件绑定，样式绑定，属性绑定

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>vue1</title>
    <!--引入vue-->
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <style>
        .show {
            width: 100px;
            height: 100px;
            background: red;
        }

        .vlew {
            width: 300px;
            height: 30px;
            border: 1px solid gray;
            margin: 20px auto;
            line-height: 30px;
            text-align: center;
        }

        .active {
            background: red;
            color: white;
        }
    </style>
</head>

<body>
<div id="app">
    <!--事件绑定-->
    <p class="show" v-on:click="speak"></p>
    <p class="show" @click="speak"></p>
    <!--属性绑定"v-bind"-->
    <img v-bind:src="imgName">
    <img :src="imgName">
    <!--样式绑定'v-bind'-->
    <div class="vlew" v-bind:class={active:isActive}>   //如果isActive为真，则添加active属性
        {{message}}
    </div>
</div>

</body>

<script>
    var app = new Vue({
        el: "#app",
        data: {
            imgName: 'right.jpeg',
            message: 'vue学习',
            isActive: true,
        },
        methods: {
            speak: function () {
                alert("hello");
            }

        }
    })
</script>
</html>
```

## 数据双向绑定

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>vue1</title>
        <!--引入vue-->
        <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    </head>

    <body>
        <div id="app">
            <!--数据双向绑定-->
            <input v-model="title">
            <p>{{title}}</p>
        </div>

    </body>

    <script>
        var app = new Vue({
            el: "#app",
            data:{
                title:'你好',
            },
            methods:{

            }
        })
</script>
</html>
```



## Vue组件的重要选项

```
 <script>
        var app = new Vue({
            el: "#app",     //挂载点
            data:{          //数据
                title:'你好',
            },
            methods:{       //方法
                add:function(){},
                change:function(){}
            },
            computed:{      //计算属性

            },
            watch:{         //监听属性

            }
        })
</script>
```