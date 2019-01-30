---
title: 更改CSS样式
date: 2018-02-21 16:42:30
tags: CSS
categories: CSS
---

* 更改CSS样式

1. toggle()

```
<style>
    li{
        list-style:none;
    }
    .change{
        color:red;
        text-decoration:line-through;
    }
</style>
<div>
    <ul>
        <li>abc</li>
        <li>abc</li>
        <li>abc</li>
    </ul>
</div>
<script>
let lis=document.getElementsByName('li');
for (let li of lis){
    li.addEventListener("click",function(eve){
        this.classList.togle('change');
    })
}
</script>
```