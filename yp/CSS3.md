---
title: CSS3
date: 2018-02-21 16:42:30
tags: CSS
categories: CSS3基础
---

# CSS3
## 渐变（Gradients）

> CSS3 渐变（gradients）可以让你在两个或多个指定的颜色之间显示平稳的过渡。CSS3 定义了两种类型的渐变 *线性渐变（Linear Gradients）* 和 *径向渐变（Radial Gradients）*。

* 线性渐变 —— 向下/向上/向左/向右/对角方向

1. 以下分别是垂直方向（默认）、从左至右、从左上角至右下角的颜色渐变

    ```
   #grad1 {
		background: linear-gradient(red, blue);
	} 
	#grad2 {
		background: linear-gradient(to right, red , blue);
	}
	#grad3 {
		background: linear-gradient(to bottom right, red , blue);
	}
    ```

2. 使用角度

  >  角度是逆时针方向计算，0deg 将创建一个从下到上的渐变，90deg 将创建一个从右到左的渐变。
  
  ```
		background: linear-gradient(0deg, red, blue);
		background: linear-gradient(90deg, red, blue);
			background: linear-gradient(180deg, red, blue);
  ```

3. 使用多个颜色结点

	
  ```
#grad {
  background: -webkit-linear-gradient(red, green, blue); /* Safari 5.1 - 6.0 */
  background: -o-linear-gradient(red, green, blue); /* Opera 11.1 - 12.0 */
  background: -moz-linear-gradient(red, green, blue); /* Firefox 3.6 - 15 */
  background: linear-gradient(red, green, blue); /* 标准的语法 */
}
  ```

4. 使用透明度 **rgba()**

  >  为了添加透明度，我们使用 rgba() 函数来定义颜色结点。rgba() 函数中的最后一个参数可以是从 0 到 1 的值，它定义了颜色的透明度：0 表示完全透明，1 表示完全不透明。

  ```
#grad {
  background: linear-gradient(to right, rgba(255,0,0,0), rgba(255,0,0,1)); 
}
  ```

5. 重复的线性渐变 **repeating-linear-gradient()**

> repeating-linear-gradient() 函数用于重复线性渐变

```
#grad {
  background: repeating-linear-gradient(red, yellow 10%, green 20%);
}
```
     
  * 径向渐变——由它们的中心定义
> 为了创建一个径向渐变，你也必须至少定义两种颜色结点。颜色结点即你想要呈现平稳过渡的颜色。同时，你也可以指定渐变的中心、形状（圆形或椭圆形）、大小。默认情况下，渐变的中心是 center（表示在中心点），渐变的形状是 ellipse（表示椭圆形），渐变的大小是 farthest-corner（表示到最远的角落）。

1. 颜色结点均匀分布的径向渐变

  ```
#grad {
  background: radial-gradient(red, green, blue);
  ```

2. 颜色结点不均匀分布

  ```
#grad {
  background: radial-gradient(red 5%, green 15%, blue 60%);
}
  ```

3. 设置形状（circle 表示圆形，ellipse 表示椭圆形）
  
  ```
#grad {
  background: radial-gradient(circle, red, yellow, green);
}
  ```

## 圆角（border-radius）

> CSS3的 *border-radius* 属性可以给任何元素制作“圆角”

* 以下分别是给背景颜色、边框和背景图片指定圆角

  ```
#rcorners1 {
    border-radius: 25px;
    background: #8AC007;
    padding: 20px; 
    width: 200px;
    height: 150px; 
}
#rcorners2 {
    border-radius: 25px;
    border: 2px solid #8AC007;
    padding: 20px; 
    width: 200px;
    height: 150px; 
}
#rcorners3 {
    border-radius: 25px;
    background: url(paper.gif);
    background-position: left top;
    background-repeat: repeat;
    padding: 20px; 
    width: 200px;
    height: 150px; 
}
  ```

* 格式
	1. 四个值: 第一个值为左上角，第二个值为右上角，第三个值为右下角，第四个值为左下角。
	2. 三个值: 第一个值为左上角, 第二个值为右上角和左下角，第三个值为右下角
	3. 两个值: 第一个值为左上角与右下角，第二个值为右上角与左下角
	4. 一个值： 四个圆角值相同

* CSS3圆角属性

  |属性	|描述|
|-----|-----|
|border-radius|所有四个边角 border-*-*-radius 属性的缩写|
|border-top-left-radius|	定义了左上角的弧度|
|border-top-right-radius|	定义了右上角的弧度|
|border-bottom-right-radius|	定义了右下角的弧度|
|border-bottom-left-radius|	定义了左下角的弧度|

## 动画效果

>  *@keyframes* 规则是创建动画，在其规则内指定一个CSS样式和动画将逐步从目前的样式更改为新的样式

* 5秒内背景色从红变绿


  > *from* 相当于是*0%*， *to* 相当于是*100%*

```css
div
{
    animation: myfirst 5s;
    -webkit-animation: myfirst 5s; /* Safari 与 Chrome */
}
keyframes myfirst{
	from{background:red;}
	to{background:green;}
}
```

* 当动画为 25% 及 50% 时改变背景色，然后当动画 100% 完成时再次改变，每次加上位置上的变化

  ```css
div
{
    animation: myfirst 5s;
    -webkit-animation: myfirst 5s; /* Safari 与 Chrome */
}
keyframes myfirst
{
     0%   {background: red; left:0px; top:0px;}
    25%  {background: yellow; left:200px; top:0px;}
    50%  {background: blue; left:200px; top:200px;}
    75%  {background: green; left:0px; top:200px;}
    100% {background: red; left:0px; top:0px;}
}
  ```

* *@keyframes* 规则和所有动画属性


  |属性|描述|
|-----|-----|
|@keyframes	|规定动画。|
|animation|	所有动画属性的简写属性，除了 animation-play-state 属性。|
|animation-name|规定 @keyframes 动画的名称。|
|animation-duration|规定动画完成一个周期所花费的秒或毫秒。默认是 0。|
|animation-timing-function|规定动画的速度曲线。默认是 "ease"。|
|animation-fill-mode|规定当动画不播放时（当动画完成时，或当动画有一个延迟未开始播放时），要应用到元素的样式，*forwards*是定在最后的样式，默认是回到初始样式。|
|animation-delay|规定动画何时开始。默认是 0。|
|animation-iteration-count|规定动画被播放的次数。默认是 1。|
|animation-direction|规定动画是否在下一周期逆向地播放。默认是 "normal"。|
|animation-play-state|规定动画是否正在运行或暂停。默认是 "running"。|

##  过渡
> CSS3 过渡是元素从一种样式逐渐改变为另一种的效果。要实现这一点，必须规定两项内容：**指定要添加效果的CSS属性**并且**指定效果的持续时间**。

  ```
#div{
       width: 50px;
       height:50px;
       background-color: green;
       transition: width,height,background-color,2s;
        }
#div:hover{
       width: 100px;
       height: 100px;
       background-color: blue;
}
  ```

* 过渡属性

  |属性|描述|
|----|----|
|transition|简写属性，用于在一个属性中设置四个过渡属性。|
|transition-property|	规定应用过渡的 CSS 属性的名称。|
|transition-duration|	定义过渡效果花费的时间。默认是 0。|
|transition-timing-function|规定过渡效果的时间曲线。默认是 "ease"。|
|transition-delay|规定过渡效果何时开始。默认是 0。|

 
