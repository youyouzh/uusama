---
title: canvas API总结
tags:
  - canvas
  - JavaScript
url: 632.html
id: 632
categories:
  - 前端
  - canvas
date: 2018-03-07 15:19:47
comments: true
keywords: canvas, JavaScript, canvas api
---

## 说明
从简单的基本图形，到复杂炫酷的动画，通过canvas元素获取的2D图形渲染上下文`CanvasRenderingContext2D`，能够使用丰富的API来进行图形绘制。这篇文章将会总结在之前的canvas教程中用到的所有渲染上下文的API。

可以参考之前的教程：

- 如何绘制基本图形可以参考：[canvas基本图形绘制](http://uusama.com/592.html)
- 如何对基本图形移动旋转缩放可以参考：[canvas图形变换](http://uusama.com/613.html)
- 如何设置基本图形颜色和样式可以参考：[canvas样式和颜色](http://uusama.com/601.html)
- 如何使用外部图片以及图形组合可以参考：[canvas使用图片，图形组合以及裁剪](http://uusama.com/619.html)
- canvas如何保存和加载图像可以参考：[canvas图像保存](http://uusama.com/624.html)
- canvas动画教程和实例：[canvas动画](http://uusama.com/627.html)
- canvas系列教程可以参考：[canvas](http://uusama.com/tag/canvas)

以下所介绍的方法和属性，都是`CanvasRenderingContext2D`对象的方法和属性。调用方式如下：
```javascript
var canvas = document.getElementById("canvas");
var ctx = canvas.getContext("2d");
// 使用ctx即可调用
ctx.fillStyle = "rgba(0, 0, 200, 0.5)";
ctx.fillRect (30, 30, 55, 50);
```

更加详细的接口API文档可以参考[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D)。

## 基本图形绘制
用于绘制基本图形。
### 矩形绘制（不需要路径）

- `void ctx.fillRect(x, y, width, height);`
	- 绘制填充矩形，矩形的起点在 (x, y) 位置，矩形的尺寸是 width 和 height 
- `void ctx.strokeRect(x, y, width, height);`
	- 在 canvas 中，使用当前的绘画样式，描绘一个起点在 (x, y) 、宽度为 w 、高度为 h 的矩形
- `void ctx.clearRect(x, y, width, height);`
	- 设置指定矩形区域内（以点 (x, y) 为起点，范围是(width, height) ）所有像素变成透明，并擦除之前绘制的所有内容

以上API参数说明如下：

- `x`矩形起始点（左上角定点）的 x 轴坐标
- `y`矩形起始点的 y 轴坐标
- `width`矩形的宽度
- `height`矩形的高度

### 绘制文本

- `void ctx.fillText(text, x, y [, maxWidth]);`
	- 在(x,y)位置绘制（填充）文本
- `void ctx.strokeText(text, x, y [, maxWidth]);`
	- 在(x,y)位置绘制（描边）文本
- `ctx.measureText(text);`
	- 返回一个 TextMetrics 对象，包含关于文本尺寸的信息（例如文本的宽度）

以上API参数说明如下：

- `x`文本起点的 x 轴坐标
- `y`文本起点的 y 轴坐标
- `text`需要绘制的文本
- `maxWidth`可选，绘制的最大宽度

### 路径图形

- `void ctx.beginPath();`
	- 通过清空子路径列表开始一个新路径
- `void ctx.closePath();`
	- 使笔点返回到当前子路径的起始点。它尝试从当前点到起始点绘制一条直线。如果图形已经是封闭的或者只有一个点，那么此方法不会做任何操作
- `void ctx.moveTo(x, y);`
	- 将一个新的子路径的起始点移动到(x，y)坐标
- `void ctx.lineTo(x, y);`
	- 使用直线连接子路径的最后的点到x,y坐标
- `void ctx.quadraticCurveTo(cpx, cpy, x, y);`
	- 添加一个2次贝赛尔曲线路径
	- `cpx, cpy`第一个控制点(x,y)坐标
	- `x,y`为结束点坐标
- `void ctx.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y);`
	- 添加一个3次贝赛尔曲线路径。该方法需要三个点。 第一、第二个点是控制点，第三个点是结束点。起始点是当前路径的最后一个点，绘制贝赛尔曲线前，可以通过调用 moveTo() 进行修改。
	- `cp1x, cp1y`第一个控制点(x,y)坐标
	- `cp2x, cp2y`第二个控制点(x,y)坐标
	- `x,y`结束点坐标
- `void ctx.arc(x, y, radius, startAngle, endAngle, anticlockwise);`
	- 绘制一段圆弧路径， 圆弧路径的圆心在 (x, y) 位置，半径为 r ，根据anticlockwise （默认为顺时针）指定的方向从 startAngle 开始绘制，到 endAngle 结束。
	- `x,y`为绘制圆弧所在圆上的圆心坐标
	- `radius`为半径
	- `startAngle`以及`endAngle`参数用以x轴为基准弧度定义了开始以及结束的弧度
	- `anticlockwise`为一个布尔值，指定绘制反向，默认为false表示顺时针方向。true表示逆时针方向。
- `void ctx.arcTo(x1, y1, x2, y2, radius);`
	- 根据控制点和半径绘制圆弧路径，使用当前的描点(前一个moveTo或lineTo等函数的止点)。根据当前描点与给定的控制点1连接的直线，和控制点1与控制点2连接的直线，作为使用指定半径的圆的切线，画出两条切线之间的弧线路径
	- `x1,y1`指定第一个控制点的坐标
	- `x2,y2`指定第二个控制点的坐标
	- `radius`指定圆弧的半径
- `void ctx.ellipse(x, y, radiusX, radiusY, rotation, startAngle, endAngle, anticlockwise);`
	- 添加一个椭圆路径，椭圆的圆心在（x,y）位置，半径分别是radiusX 和 radiusY ，按照anticlockwise （默认顺时针）指定的方向，从 startAngle  开始绘制，到 endAngle 结束。
	- `x,y`椭圆圆心的(x,y)坐标
	- `radiusX`椭圆长轴的半径
	- `radiusY`椭圆短轴的半径
	- `rotation`椭圆的旋转角度，以弧度表示(非角度度数)
	- `startAngle`将要绘制的起始点角度，从 x 轴测量，以弧度表示(非角度度数)
	- `endAngle`椭圆将要绘制的结束点角度，以弧度表示(非角度度数)
	- `anticlockwise`为一个布尔值，指定绘制反向，默认为false表示顺时针方向。true表示逆时针方向。
- `void ctx.rect(x, y, width, height);`
	- 创建一个矩形路径，矩形的起点位置是 (x, y) ，尺寸为 width 和 height

### 路径

- `void ctx.fill();` `void ctx.fill(fillRule);` `void ctx.fill(path, fillRule);`
	- 使用当前的样式填充子路径
	- `path`需要填充的`Path2D`路径
	- `fillRule`一种算法，决定点是在路径内还是在路径外:"nonzero","evenodd"
- `void ctx.stroke();` `void ctx.stroke(path);`
	- 使用当前的样式描边子路径
- `void ctx.clip();` `void ctx.clip(fillRule);` `void ctx.clip(path, fillRule);`
	- 从当前路径创建一个剪切路径。在`clip()`调用之后，绘制的所有信息只会出现在剪切路径内部
- `boolean ctx.isPointInPath(x, y);` `boolean ctx.isPointInPath(x, y, fillRule);` `boolean ctx.isPointInPath(path, x, y);` `boolean ctx.isPointInPath(path, x, y, fillRule);`
	- 判断当前路径是否包含检测点
- `boolean ctx.isPointInStroke(x, y);` `boolean ctx.isPointInStroke(path, x, y);` 
	- 判断检测点是否在路径的描边线上
	
## 颜色和样式
控制绘制图形的颜色以及样式。
### 线型

- `ctx.lineWidth = value;`
	- 线的宽度。默认 1.0
- `ctx.lineCap = "butt" || "round" || "square";`
	- 线末端的类型。 允许的值： butt (默认), round, square
- `ctx.lineJoin = "miter" || "round" || "bevel";`
	- 定义两线相交拐点的类型。允许的值：round, bevel, miter(默认)
- `ctx.miterLimit = value;`
	- 斜接面限制比例。默认 10
- `Array ctx.getLineDash();`
	- 返回一组描述交替绘制线段和间距（坐标空间单位）长度的数字。如果数组元素的数量是奇数，数组元素会被复制并重复。 例如， 设置线段为 [5, 15, 25] 将会得到以下返回值 [5, 15, 25, 5, 15, 25]。
- `void ctx.setLineDash(segments);`
	- 设置当前的线段样式
- `ctx.lineDashOffset = value;`
	- 设置虚线偏移量的属性，偏移量是float精度的数字。 初始值为 0.0。

### 文本样式

- `ctx.font = value;`
	- 设置绘制文字时，当前字体样式的属性
	- `value`符合CSS font 语法的DOMString 字符串。默认字体是 10px sans-serif.
- `ctx.textAlign = "left" || "right" || "center" || "start" || "end";`
	- 文本对齐设置
- `ctx.textBaseline = "top" || "hanging" || "middle" || "alphabetic" || "ideographic" || "bottom";`
	- 基线对齐设置
- `ctx.direction = "ltr" || "rtl" || "inherit";`
	- 文本的方向

### 填充和描边样式

- `ctx.fillStyle = color || gradient || pattern`
	- 图形内部的颜色和样式。 默认 #000 (黑色).
	- `color`DOMString 字符串，被转换成 CSS color 颜色值
	- `gradient`CanvasGradient 对象 （线性渐变或者放射性渐变）
	- `pattern` CanvasPattern 对象 （可重复图像）
- `ctx.strokeStyle = color || gradient || pattern`
	- 图形边线的颜色和样式。 默认 #000 (黑色)

### 渐变和图案

- `CanvasGradient ctx.createLinearGradient(x0, y0, x1, y1);`
	- 创建一个沿参数坐标指定的直线的渐变
- `CanvasGradient ctx.createRadialGradient(x0, y0, r0, x1, y1, r1);`
	- 创建一个沿着参数坐标指定的线的放射性性渐变
- `CanvasPattern ctx.createPattern(image, repetition);`
	- 使用指定的图片 (a CanvasImageSource)创建图案
	- `image`作为重复图像源的`CanvasImageSource`对象
	- `repetition` DOMString，指定如何重复图像。允许的值有："repeat" (both directions),"repeat-x" (horizontal only),"repeat-y" (vertical only), "no-repeat" (neither).

### 阴影

- `ctx.shadowBlur = level;`
	- 描述模糊效果。 默认 0
- `ctx.shadowColor = color;`
	- 阴影的颜色。 默认fully-transparent black
- `ctx.shadowOffsetX = offset;`
	- 阴影水平方向的偏移量。 默认 0
- `ctx.shadowOffsetY = offset;`
	- 阴影垂直方向的偏移量。 默认 0

## 变换
在`CanvasRenderingContext2D`渲染背景中的对象会有一个当前的变换矩阵，一些方法可以对其进行控制。当创建当前的默认路径，绘制文本、图形和Path2D对象的时候，会应用此变换矩阵。

- `void ctx.rotate(angle);`
	- 在变换矩阵中增加旋转，角度变量表示一个顺时针旋转角度并且用弧度表示
	- `angle` 顺时针旋转的弧度。如果你想通过角度值计算，可以使用公式： degree * Math.PI / 180
- `void ctx.scale(x, y);`
	- 根据 x 水平方向和 y 垂直方向，为canvas 单位添加缩放变换
- `void ctx.translate(x, y);`
	- 通过在网格中移动 canvas 和 canvas 原点 x 水平方向、原点 y 垂直方向，添加平移变换
- `void ctx.transform(m11, m12, m21, m22, dx, dy);`
	- 使用方法参数描述的矩阵多次叠加当前的变换矩阵。
	- `m11` 水平方向的缩放
	- `m12` 水平方向的倾斜
	- `m21` 竖直方向的倾斜
	- `m22` 竖直方向的缩放
	- `dx` 水平方向的移动
	- `dy` 竖直方向的移动
- `void ctx.setTtransform(m11, m12, m21, m22, dx, dy);`
	- 重新设置当前的变换为单位矩阵，并使用同样的变量调用`transform()`方法
- `void ctx.resetTransform();`
	- 使用单位矩阵重新设置当前的变换

## 合成
规定两个前后绘制的两个图形之间的组合方式。

- `ctx.globalAlpha = value;`
	- 在合成到 canvas 之前，设置图形和图像透明度的值。默认 1.0 (不透明)。
- `ctx.globalCompositeOperation = type;`
	- 通过`globalAlpha`应用，设置如何在已经存在的位图上绘制图形和图像

## 图像相关
设置图片和canvas的交互方式
### 绘制图像
`drawImage`方法用于在canvas上绘制图像，有下面三种形式：

- `void ctx.drawImage(image, dx, dy);`
- `void ctx.drawImage(image, dx, dy, dWidth, dHeight);`
- `void ctx.drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight);`

以上方法的参数含义如下：

- `image`绘制到上下文的元素。允许任何的 canvas 图像源(CanvasImageSource)，例如：HTMLImageElement，HTMLVideoElement，或者 HTMLCanvasElement
- `dx`目标画布的左上角在目标canvas上 X 轴的位置
- `dy`目标画布的左上角在目标canvas上 Y 轴的位置
- `dWidth`在目标画布上绘制图像的宽度。 允许对绘制的图像进行缩放。 如果不说明， 在绘制时图片宽度不会缩放
- `dHeight`在目标画布上绘制图像的高度。 允许对绘制的图像进行缩放。 如果不说明， 在绘制时图片高度不会缩放
- `sx`需要绘制到目标上下文中的，源图像的矩形选择框的左上角 X 坐标
- `sy`需要绘制到目标上下文中的，源图像的矩形选择框的左上角 Y 坐标
- `sWidth`需要绘制到目标上下文中的，源图像的矩形选择框的宽度。如果不说明，整个矩形从坐标的sx和sy开始，到图像的右下角结束
- `sHeight`需要绘制到目标上下文中的，源图像的矩形选择框的高度

### 像素控制

- `ImageData ctx.createImageData(width, height);` `ImageData ctx.createImageData(imagedata);`
	- 创建一个 新的、空白的、指定大小的 ImageData 对象。 所有的像素在新对象中都是透明的。
	- `width` `ImageData`新对象的宽度
	- `height` `ImageData`新对象的高度
	- `imagedata` 从现有的 ImageData 对象中，复制一个和其宽度和高度相同的对象。图像自身不允许被复制。
- `ImageData ctx.getImageData(sx, sy, sw, sh);`
	- 返回一个 ImageData 对象，用来描述canvas区域隐含的像素数据，这个区域通过矩形表示，起始点为(sx, sy)、宽为sw、高为sh
- `void ctx.putImageData(imagedata, dx, dy, [ dirtyX, dirtyY, dirtyWidth, dirtyHeight ]);`
	- 将数据从已有的 ImageData 绘制到位图上。 如果提供了脏矩形，只能绘制矩形的像素
	- `dx` 源图像数据在目标画布中的位置偏移量（x 轴方向的偏移量）
	- `dy` 源图像数据在目标画布中的位置偏移量（y 轴方向的偏移量）
	- `dirtyX` 可选，在源图像数据中，矩形区域左上角的位置。默认是整个图像数据的左上角（x 坐标）
	- `dirtyY` 可选，在源图像数据中，矩形区域左上角的位置。默认是整个图像数据的左上角（y 坐标）
	- `dirtyWidth` 可选，在源图像数据中，矩形区域的宽度。默认是图像数据的宽度
	- `dirtyHeight` 可选，在源图像数据中，矩形区域的高度。默认是图像数据的高度

## canvas 状态
CanvasRenderingContext2D渲染环境包含了多种绘图的样式状态（属性有线的样式、填充样式、阴影样式、文本样式）。下面的方法会帮助你使用这些状态：

- `void ctx.save();`
	- 使用栈保存当前的绘画样式状态，你可以使用`restore()`恢复任何改变
- `void ctx.restore();`
	- 恢复到最近的绘制样式状态，此状态是通过`save()`保存到”状态栈“中最新的元素
- `ctx.canvas;`
	- 对`HTMLCanvasElement`只读的反向引用。如果和`canvas`元素没有联系，可能为`null`。

以上保存和恢复的canvas状态包括：

- 当前的变换矩阵
- 当前的剪切区域
- 当前的虚线列表
- 以下属性当前的值：
	- `strokeStyle`线框填充样式
	- `fillStyle`图形填充样式
	- `globalAlpha`全局透明度
	- `lineWidth`线宽
	- `lineCap`线头（线帽）
	- `lineJoin`线交方式
	- `miterLimit`斜接面限制比例
	- `lineDashOffset`虚线偏移
	- `shadowOffsetX`阴影X偏移
	- `shadowOffsetY`阴影Y偏移
	- `shadowBlur`阴影模糊程度
	- `shadowColor`阴影颜色
	- `globalCompositeOperation`图形组合方式
	- `font`字体样式
	- `textAlign`文本的对齐方式
	- `textBaseline`文本基线
	- `direction`文本方向
	- `imageSmoothingEnabled`图形缩放是否平滑
