---
title: flex布局
date: 2018-02-21 16:42:30
tags: CSS
categories: CSS
---

# flex布局

> flex布局是flexible box的缩写，意为“弹性布局”，用来为盒模型提供最大的灵活性

* 如何设置flex布局

```
.box{
	display:flex/inline-flex;
	display:-webkit-flex/-webkit-inline-flex;
}

```

* flex的基本概念
	* flex-direction
	* flex-wrap
	* flex-flow
	* justify-content
	* align-items
	* align-content

  1. 水平主轴：main axis，起点——main start，终点——main end；
  2. 垂直交叉轴：cross axis，起点——cross start，终点——cross end；

* flex容器属性

1. 主轴方向**flex-direction**
	
	|属性|说明|
	|---|---|
	|row（默认值）|主轴为水平方向，起点在左端|
	|row-reverse|主轴为水平方向，起点在右端。
	|column|主轴为垂直方向|起点在上沿。
	|column-reverse|主轴为垂直方向，起点在下沿|

	```
.box{
	flex-direction:row/row-reverse/column/column-revwese;
}
	```

2. 是否换行**flex-wrap**

	|属性|说明|
	|---|---|
	|nowrap（默认）|不换行|
	|wrap|换行，第一行在上方|
	|wrap-reverse|换行，第一行在下方|
	
 
	```
.box{
	flex-wrap:nowrap/wrap/wrap-reverse;
}
	```

3. 主轴方向和换行**flew-flow**

	```
.box{
	flex-flow:<flex-direction>||<flex-wrap>;
}
	```
	
4. 	主轴对齐方式**justify-content**

	|属性|说明|
	|---|---|
	|flex-start（默认值）|左对齐|
	|flex-end|右对齐|
	|center|居中|
	|space-between|两端对齐，项目之间的间隔都相等|
	|space-around|每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍|



	```
.box{
	justify-content:flex-start|flex-end|center|space-between|space-around;
}
	```
	
5. 垂直交叉轴对齐方式**align-items**

  |属性|说明|
  |---|---|
  |flex-start|交叉轴的起点对齐|
  |flex-end|交叉轴的终点对齐|
  |center|交叉轴的中点对齐|
  |baseline:|项目的第一行文字的基线对齐|
  |stretch（默认值）|如果项目未设置高度或设为auto，将占满整个容器的高度|


	```
		.box{
			align-items:flex-start/flex-end/center/baseline/stretch;
		}
	```
	
6. 多根轴线对齐方式**alige-content**
	
  |属性|说明|
  |---|---|
  |flex-start|与交叉轴的起点对齐|
  |flex-end|与交叉轴的终点对齐|
  |center|与交叉轴的中点对齐|
  |space-between|与交叉轴两端对齐，轴线之间的间隔平均分布|
  |space-around|每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍|
  |stretch（默认值）|轴线占满整个交叉轴|

	```
		.box{
			align-content:flex-start/flex-end/center/space-between/space-around/stretch;
		}

	```
	
* flex项目属性

	* order
	* flex-grow
	* flex-shrink
	* flex-basis
	* flex
	* align-self

1. order属性

 	> order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
	
	**注意**
	  * 该属性不会对**item**外观显示造成任何影响；
	  * 可以依靠此属性更改**item**的排列顺序。
 	
 	```
 	.item{
 		order:<integer>;
 	}
 	```
 	
2. flew-grow属性

	> flex-grow属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大.
	
	```
		.item{
			flex-grow:<number>; /* default 0 */
		}
	```
	
	如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

3. flex-shrink属性

 > flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

	```
		.item {
			flex-shrink: <number>; /* default 1 */
	}
	```
	
  如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。

4. flex-basis属性

  > flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
  
  **注意**
   * *flex-basis*和*width*的区别在于：当**item**同时设置flex-basis和width时，**item**的宽度是flex-basis；当只设置**width**的值时，**item**的宽度为width；当二者都没有设置时，宽度为父级元素的宽度。
   * *flex-basis*的值受到*min-width*和*max-width*的节制。
   * 当所有同行的**item**的*flex-basis*之和小于**box**的width时，如果**item**的*flex-grow*为0，则**item**不会放大；如果**item**的*flex-grow*不为0，则按照*flex-grow*属性的规则放大并占满整行。
   * 当所有同行的**item**的*flex-basis*之和大于**box**的width时，如果**item**的*flex-shrink*为1，则**item**不会按比例缩小；如果**item**的*flex-shrink*不为0，则按照*flex-shrink*属性的规则缩小并占满整行。

  ```
  .item {
  		flex-basis: <length> | auto; /* default auto */
}
  ```
  
  它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。
  
5. flex属性

	> flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。

	```
	.item {
  flex: none | [ <'flex-grow'><'flex-	shrink'>? || <'flex-basis'> ]
	}
	```
	
	该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
	
  建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。
  
6. align-self属性

  > align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。

  ```
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
  ```
  
  该属性可能取6个值，除了auto，其他都与align-items属性完全一致。




