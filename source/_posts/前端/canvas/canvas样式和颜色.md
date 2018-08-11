---
title: canvas样式和颜色
tags:
  - canvas
  - JavaScript
url: 601.html
id: 601
categories:
  - 前端
  - canvas
date: 2018-03-01 16:46:38
---
在[上一篇文章](http://uusama.com/592.html)中，我们可以通过canvas绘制基本的图形，这些图形的颜色都是默认的，这篇文章将会介绍如何设置图形的颜色行样式，使得绘制的图形更加炫酷！
## 色彩 Colors
通过设置canvas上下文的两个颜色属性，可以给绘制的图形上色：

- `fillStyle = color`  设置图形的填充颜色
- `strokeStyle = color`  设置图形轮廓的颜色

`color` 可以是表示 CSS 颜色值的字符串，渐变对象或者图案对象

一旦设置了`strokeStyle`或者`fillStyle`的值，那么这个新值就会成为新绘制的图形的默认值。如果你要给每个图形上不同的颜色，需要重新设置`fillStyle`或`strokeStyle`的值。

```javascript
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');
var colors = [
        'blue',  // 英文单词描述的颜色
        '#FFA500', // 十六进制表示的颜色
        'rgb(255,0,0)', // rgb比例表示的颜色
        'rgba(255,0,255,0.8)' // rgba表示的颜色
];
for (var i = 0; i < 4; i++) {
    // 绘制四个矩形
    ctx.fillStyle = colors[i];
    ctx.fillRect(20 + i * 30, 20, 20, 20);

    // 绘制四个圆
    ctx.beginPath();
    ctx.strokeStyle = colors[i];
    ctx.arc(30 + i * 30, 80, 10, 0, Math.PI * 2, true);
    ctx.stroke();
}
```
示意图如下：
[![](http://uusama.com/wp-content/uploads/2018/03/2018030108402979.png)](http://uusama.com/wp-content/uploads/2018/03/2018030108402979.png)

## 透明度 Transparency
在上面设置填充颜色的使用，可以使用`rgba()`的形式指定颜色的透明度。但是对于需要绘制大量拥有相同透明度的图形时，另外一个方法更加高效：

- `globalAlpha = transparencyValue` 这个属性影响到 canvas 里所有图形的透明度，有效的值范围是 0.0 （完全透明）到 1.0（完全不透明），默认是 1.0。

```javascript
var ctx = document.getElementById('canvas').getContext('2d');
// 画背景
ctx.fillStyle = '#FD0';
ctx.fillRect(0,0,75,75);
ctx.fillStyle = '#6C0';
ctx.fillRect(75,0,75,75);
ctx.fillStyle = '#09F';
ctx.fillRect(0,75,75,75);
ctx.fillStyle = '#F30';
ctx.fillRect(75,75,75,75);
ctx.fillStyle = '#FFF';

// 设置透明度值
ctx.globalAlpha = 0.2;

// 画半透明圆
for (var i=0;i<7;i++){
  ctx.beginPath();
  ctx.arc(75,75,10+10*i,0,Math.PI*2,true);
  ctx.fill();
}
```
示意图如下：
[![](http://uusama.com/wp-content/uploads/2018/03/2018030108411451.png)](http://uusama.com/wp-content/uploads/2018/03/2018030108411451.png)

## 线条样式 Line styles
画边框的时候使用轮廓线条样式，canvas也提供了很多属性来设置。

- `lineWidth = value`
	- 设置线条宽度（粗细）
	- 属性值`value`必须为正数。默认值是1.0
	- 因为像素网格与路径位置之间的关系，所有宽度为奇数的线可能不能精确呈现
- `lineCap = type` 
	- 设置线条末端样式
	- 可能值为：butt，round 和 square。默认是 butt
	- butt：默认。向线条的每个末端添加平直的边缘
	- round：向线条的每个末端添加圆形线帽
	- square：向线条的每个末端添加正方形线帽
- `lineJoin = type` 
	- 决定了图形中两线段连接处所显示的样子
	- 可能值为：round, bevel 和 miter。默认是 miter。
	- 当值是 miter 的时候，延伸效果受到`miterLimit`属性的制约
	- bevel：创建斜角
	- round：创建圆角
	- miter：默认。创建尖角
- `miterLimit = value`
	- 设定外延交点与连接点的最大距离

还可以设置虚线：

用`setLineDash()`方法和`lineDashOffset`属性来制定虚线样式.

`setLineDash()`方法接受一个数组，来指定线段与间隙的交替；`lineDashOffset`属性设置起始偏移量。

## 渐变 Gradients
就好像一般的绘图软件一样，可以用线性或者径向的渐变来填充或描边。

我们用下面的方法新建一个`canvasGradient`对象，并且赋给图形的`fillStyle`或`strokeStyle`属性。

- `createLinearGradient(x1, y1, x2, y2)`
	- 创建一个沿参数坐标指定的直线的渐变对象
	- `(x1,y1)`表示渐变直线的起点坐标
	- `(x2,y2)`表示渐变直线的终点坐标
- `createRadialGradient(x1, y1, r1, x2, y2, r2)`
	- 根据两个圆的坐标，创建放射性渐变对象
	- `(x1,y1)`表示开始圆的圆心坐标
	- `r1`表示开始圆的半径
	- `(x2,y2)`表示结束圆的圆心坐标
	- `r2`表示结束圆的半径
- `gradient.addColorStop(position, color)`
	- 添加一个由偏移值和颜色值指定的断点色标到渐变
	- `offset`表示渐变中颜色所在的相对位置，0到1之间的值，超出范围将抛出INDEX_SIZE_ERR错误
	- `color`指定颜色值，如果颜色值不能被解析为有效的CSS颜色值，将抛出SYNTAX_ERR错误
	- 可以调用该函数给某个渐变对象添加任意多个色标

需要注意：创建渐变对象的区域一定要和绘制图形的区域相互匹配，他们的坐标是独立的。

```
var ctx = document.getElementById('canvas').getContext('2d');
var Lingrad = ctx.createLinearGradient(150,0,200,50); // 线性渐变
Lingrad.addColorStop(0,"green"); // 添加渐变点
Lingrad.addColorStop(0.5,"cyan"); // 添加渐变点
Lingrad.addColorStop(1,"white"); // 添加渐变点
ctx.fillStyle = Lingrad;
ctx.fillRect(150,0,50,50);

var radgrad = ctx.createRadialGradient(45,45,10,52,50,30);
radgrad.addColorStop(0, '#A7D30C');
radgrad.addColorStop(0.9, '#019F62');
radgrad.addColorStop(1, 'rgba(1,159,98,0)');
ctx.fillStyle = radgrad;
ctx.fillRect(0,0,150,150);
```
示意图如下：
[![](http://uusama.com/wp-content/uploads/2018/03/2018030108421323.png)](http://uusama.com/wp-content/uploads/2018/03/2018030108421323.png)

## 图案样式 Patterns
图案样式的应用跟渐变很类似的，创建出一个 pattern 之后，赋给 fillStyle 或 strokeStyle 属性即可。

`createPattern(image, type)` 方法可以创建一个图案样式对象：

- `image` 可以是一个 Image 对象的引用，或者另一个 canvas 对象
- 使用 Image 对象之前，需要确认 Image 对象已经加载完毕
- type 指定重复方式，值为：repeat，repeat-x，repeat-y 和 no-repeat

```javascript
var ctx = document.getElementById('canvas').getContext('2d');

// 创建新 image 对象，用作图案
var img = new Image();
// 根据的url创建Image对象
img.src = 'images/wallpaper.png';

img.onload = function(){ // 需要等到Image对象加载完毕
	// 创建图案
	var ptrn = ctx.createPattern(img,'repeat');
	ctx.fillStyle = ptrn;
	ctx.fillRect(0,0,150,150);
}
```

## 阴影 Shadows
可以为图形或者位置添加阴影效果，就像CSS中的属性一样，使用下面的属性指定阴影效果：

- `shadowOffsetX = float`
- `shadowOffsetY = float`
	- `shadowOffsetX`和`shadowOffsetY`用来设定阴影在 X 和 Y 轴的延伸距离
	- 它们是不受变换矩阵所影响的。
	- 负值表示阴影会往上或左延伸，正值则表示会往下或右延伸，它们默认都为 0
- `shadowBlur = float`
	- `shadowBlur`用于设定阴影的模糊程度
	- 其数值并不跟像素数量挂钩，也不受变换矩阵的影响，默认为 0
- `shadowColor = color`
	- shadowColor 是标准的 CSS 颜色值，用于设定阴影颜色效果，默认是全透明的黑色

文字阴影效果
```javascript
var ctx = document.getElementById('canvas').getContext('2d');

ctx.shadowOffsetX = 2;
ctx.shadowOffsetY = 2;
ctx.shadowBlur = 2;
ctx.shadowColor = "rgba(0, 0, 0, 0.5)";

ctx.font = "40px Times New Roman";
ctx.fillStyle = "Black";
ctx.fillText("Sample String", 5, 30);
```
示意图如下：
[![](http://uusama.com/wp-content/uploads/2018/03/2018030108425355.png)](http://uusama.com/wp-content/uploads/2018/03/2018030108425355.png)

关于canvas色彩和样式的更多内容可以[参考官方文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Applying_styles_and_colors)