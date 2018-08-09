---
title: canvas基本图形绘制
tags:
  - canvas
  - JavaScript
url: 592.html
id: 592
categories:
  - canvas
  - 前端
date: 2018-03-01 11:49:23
---

## 概述
我们可以通过HTML中的canvas画布画出丰富多彩的图形甚至动画，你只需要：

- 在HTML中放置一个canvas标签
- 在HTML的JavaScript代码中获取这个canvas标签元素
- 通过获取的canvas元素，获取一个渲染上下文
- 通过获取的渲染上下文可以在画布上任意挥洒图画

另外需要注意的是，并不是所有浏览器都支持 canvas 元素，所以需要注意。

- 在canvas标签中可以包含浏览器不支持canvas时的显示内容
- 在JavaScript中通过canvas元素获取上下文时，需要检测是否获取成功

下面是一个完整列子：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>canvas实例</title>
    <!-- 下面的声明用于适配移动端缩放 -->
    <meta name="viewport" content="width=640, minimum-scale=0.5, maximum-scale=2.0, user-scalable=yes"/>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow:hidden;  /* 在使用全屏画布时，隐藏滚动条 */
        }
    </style>
</head>
<body>
<!-- 需要指定canvas画布的宽度和高度 -->
<canvas id="canvas" width="300" height="300">
    <!-- 可以使用图片，文字提示等替换不支持canvas的浏览器的显示内容 -->
    <span>您的浏览器不支持canvas，请使用更高级的浏览器！</span>
    <img src="" width="300" height="300" alt="" />
</canvas>

<script type="application/javascript">
    var canvas = document.getElementById('canvas');    // 获取canvas标签元素
    var ctx = canvas.getContent('2d');  // 通过canvas元素获取2d画布上下文
    if (!ctx) {
        console.log('您的浏览器不支持canvas');
        // 可以抛出异常强制结束JS执行
        throw new Error("Do not support canvas");
    }
    // 绘制一个长方形
    ctx.fillStyle = "rgb(225,0,0)";
    ctx.fillRect (10, 10, 55, 50);
</script>
</body>
</html>
```
更多详细内容可以[参考官方文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Drawing_shapes)

## 坐标空间
一旦创建了canvas画布之后，我们可以在画布中任意绘制，在这个画布中我们通过坐标空间来描述位置和距离。

canvas元素默认被网格所覆盖，网格中的一个单元相当于canvas元素中的一个像素。起点为左上角（坐标为（0,0）），所有元素的位置都相对于原点定位。看下面的示意图：
[![](http://uusama.com/wp-content/uploads/2018/03/2018030103452169.png)](http://uusama.com/wp-content/uploads/2018/03/2018030103452169.png)
所以图中蓝色方形左上角的坐标为距离左边（X轴）x像素，距离上边（Y轴）y像素（坐标为（x,y））

## 绘制基本图形
### 绘制矩形
HTML中的元素canvas只有绘制矩形不需要生成路径，其他的所有图形绘制都至少需要生成一条路径。相关函数如下：

- `fillRect(x, y, width, height)` ：绘制一个填充的矩形区域
- `strokeRect(x, y, width, height)` ： 绘制一个矩形的边框
- `clearRect(x, y, width, height)` ：清除指定矩形区域，让清除部分完全透明

这三个函数的参数都相同，具体说明如下：

- `x`与`y`指定了在canvas画布上所绘制的矩形的左上角（相对于原点）的坐标
- `width`和`height`指定矩形的尺寸-宽度和长度

```javascript
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');

ctx.fillRect(25,25,100,100);
ctx.clearRect(45,45,60,60);
ctx.strokeRect(50,50,50,50);
```
显示效果如下：
[![](http://uusama.com/wp-content/uploads/2018/03/2018030106083865.png)](http://uusama.com/wp-content/uploads/2018/03/2018030106083865.png)
这三个函数一旦调用，就会立即上canvas上绘制并显示效果。

### 移动笔触
在canvas画布上画东西，就像用笔在纸上面画画一样，在画之前，首先需要把笔移动到纸上的某个位置才能开始画。移动的过程中并不会在纸上画下任何东西。
`moveTo(x, y)`函数，将笔触移动到指定的坐标x以及y上。

### 绘制路径
图形的基本元素是路径，路径是通过不同颜色和宽度的线段或曲线相连形成的不同形状的点集合。一个路径，甚至一个子路径，都是闭合的。绘制路径的步骤如下：

- 首先，你需要创建路径起始点
- 然后你使用画图命令去画出路径
- 之后你把路径封闭
- 一旦路径生成，你就能通过描边或填充路径区域来渲染图形

相关函数如下：

- `beginPath()` 新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径
- `closePath()` 闭合路径之后图形绘制命令又重新指向到上下文中
- `stroke()` 通过线条来绘制图形轮廓
- `fill()` 通过填充路径的内容区域生成实心的图形
	- `rule`可选，表示填充规则
	- 可以为：[非零绕组规则](http://en.wikipedia.org/wiki/Nonzero-rule)：nonzero和[奇偶绕组法则](http://en.wikipedia.org/wiki/Even%E2%80%93odd_rule)：evenodd，默认为nonzero

需要注意的是，如果路径使用 `stroke()` 填充，则可以不用显式调用关闭路径 `closePath()` ，路径会自动闭合。但是使用 `fill()` 描边时必须调用`closePath()`。

### 绘制直线
`lineTo(x, y)` 绘制一条从当前位置到指定坐标位置(x,y)的直线。

直线的起点是当前笔触的位置，可能是前一个路径的终点或者`moveTo()`函数移动的点

绘制一个三角形的列子：
```javascript
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');

ctx.beginPath();  // 新建路径
ctx.moveTo(75,50); // 移动画笔
ctx.lineTo(100,75); // 绘制三角形的边
ctx.lineTo(100,25); // 绘制三角形的边
// 使用填充可以不用调用 closePath() 路径自动闭合
ctx.fill();   // 填充三角形
```
显示效果如下：
[![](http://uusama.com/wp-content/uploads/2018/03/20180301034540100.png)](http://uusama.com/wp-content/uploads/2018/03/20180301034540100.png)
### 绘制圆弧
绘制圆弧有两个方法可以调用：

- `arc(x, y, radius, startAngle, endAngle, anticlockwise)` 
	- `x,y`为绘制圆弧所在圆上的圆心坐标
	- `radius`为半径
	- `startAngle`以及`endAngle`参数用以x轴为基准弧度定义了开始以及结束的弧度
	- 角度与弧度转换表达式：radians=(Math.PI/180)*degrees
	- `anticlockwise`为一个布尔值，指定绘制反向，默认为false表示顺时针方向。true表示逆时针方向。
- `arcTo(x1, y1, x2, y2, radius)`
	- 根据当前描点与给定的控制点1连接的直线，和控制点1与控制点2连接的直线，作为使用指定半径的圆的切线，画出两条切线之间的弧线路径
	- `x1,y1`指定第一个控制点的坐标
	- `x2,y2`指定第二个控制点的坐标
	- `radius`指定圆弧的半径

使用`arc`函数和`arcTo`函数的例子：
```javascript
var canvas = document.getElementById("canvas");
var ctx = canvas.getContext("2d");

// 使用实线画的 arcTo 圆弧
ctx.setLineDash([]);
ctx.beginPath();
ctx.moveTo(150, 20);
ctx.arcTo(150,100,50,20,30);
ctx.stroke();

ctx.fillStyle = 'blue';
// 当前描点
ctx.fillRect(150, 20, 5, 5);

ctx.fillStyle = 'red';
// 第一个控制点
ctx.fillRect(150, 100, 5, 5);
// 第二个控制点
ctx.fillRect(50, 20, 5, 5);

// 使用虚线画的 arc 圆弧
ctx.setLineDash([5,5]);
ctx.moveTo(150, 20);
ctx.lineTo(150,100);
ctx.lineTo(50, 20);
ctx.stroke();
ctx.beginPath();
ctx.arc(120,38,30,0,2*Math.PI);
ctx.stroke();
```
显示效果如下，其中红色为控制点，蓝色为描点（起点）：
[![](http://uusama.com/wp-content/uploads/2018/03/2018030103482629.png)](http://uusama.com/wp-content/uploads/2018/03/2018030103482629.png)

### 贝塞尔曲线
通过一些控制点绘制贝塞尔曲线，从而可以绘制绘制复杂有规律的图形。

- `quadraticCurveTo(cp1x, cp1y, x, y)`
	- 绘制二次贝塞尔曲线
	- `cp1x,cp1y`为一个控制点坐标
	- `x,y`为结束点坐标
- `bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)`
	- 绘制三次贝塞尔曲线
	- `cp1x,cp1y`为第一个控制点坐标
	- `cp2x,cp2y`为第二个控制点坐标
	- `x,y`为结束点坐标

[![](http://uusama.com/wp-content/uploads/2018/03/2018030103445159.png)](http://uusama.com/wp-content/uploads/2018/03/2018030103445159.png)

连续使用贝塞尔曲线绘制函数，可以绘制首尾相连的复杂曲线。看下面的例子：
```javascript
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');

// 二次贝塞尔曲线
ctx.beginPath();
ctx.moveTo(75,25);
ctx.quadraticCurveTo(25,25,25,62.5);
ctx.quadraticCurveTo(25,100,50,100);
ctx.quadraticCurveTo(50,120,30,125);
ctx.quadraticCurveTo(60,120,65,100);
ctx.quadraticCurveTo(125,100,125,62.5);
ctx.quadraticCurveTo(125,25,75,25);
ctx.stroke();
```
显示效果如下：
[![](http://uusama.com/wp-content/uploads/2018/03/2018030103461161.png)](http://uusama.com/wp-content/uploads/2018/03/2018030103461161.png)

## 绘制文本
canvas 提供了两种方法来渲染文本：

- `fillText(text, x, y [, maxWidth])`
	- 在指定的(x,y)位置填充指定的文本
	- 绘制的文本使用当前的`font`, `textAlign`, `textBaseline` 和 `direction`
	- `text` 文本内容
	- `x,y` 指定文本起点的坐标
	- `maxWidth` 可选，绘制的最大宽度，如果指定了值，并且经过计算字符串的值比最大宽度还要宽，字体为了适应会水平缩放（如果通过水平缩放当前字体，可以进行有效的或者合理可读的处理）或者使用小号的字体
- `strokeText(text, x, y [, maxWidth])`
	- 在指定的(x,y)位置绘制**文本边框**
	- 参数和`fillText()`函数相同

下面是一个简单的例子：
```javascript
var canvas = document.getElementById("canvas");
var ctx = canvas.getContext("2d");

ctx.font = "48px serif";// 指定字体大小
ctx.fillText("fillText", 50, 100, 100); // 指定宽度进行缩放
ctx.strokeText("strokeText", 50, 150);
```
[![](http://uusama.com/wp-content/uploads/2018/03/2018030106285223.png)](http://uusama.com/wp-content/uploads/2018/03/2018030106285223.png)
