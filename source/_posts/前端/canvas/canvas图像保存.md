---
title: canvas图像保存
tags:
  - canvas
  - JavaScript
url: 624.html
id: 624
categories:
  - 前端
  - canvas
date: 2018-03-02 23:39:59
---
通过前面的章节，我们能够在canvas画出各种炫酷多样的图形，但是这些画好的图像如何保存下一次使用呢？这篇文章将会探讨如何保存和加载canvas画布上的图像。

- 如何绘制基本图形可以参考：[canvas基本图形绘制](http://uusama.com/592.html)
- 如何对基本图形移动旋转缩放可以参考：[canvas图形变换](http://uusama.com/613.html)
- 如何设置基本图形颜色和样式可以参考：[canvas样式和颜色](http://uusama.com/601.html)
- 如何使用外部图片以及图形组合可以参考：[canvas使用图片，图形组合以及裁剪](http://uusama.com/619.html)
- canvas系列教程可以参考：[canvas](http://uusama.com/tag/canvas)

## canvas画布上的图像
到目前为止，我们尚未深入了解Canvas画布真实像素的原理，事实上，canvas上面无论多么复杂的画，都是由有限个像素构成的，每个像素可以通过RGBA颜色值来描述。

只要知道了canvas画布上的所有像素点所包含的RGBA值信息，我们就能保存和重复利用它了。

所有这些像素数据信息可以通过ImageData对象操纵，直接读取或将数据数组写入该对象中。

## ImageData 对象
### 说明
ImageData对象中存储着canvas对象真实的像素数据，它包含以下几个只读属性：

- `width`图片宽度，单位是像素
- `height`图片高度，单位是像素
- `data` `Uint8ClampedArray`类型的一维数组，包含着RGBA格式的整型数据，范围在0至255之间

`data`属性是一个`Uint8ClampedArray`类型的一维数组，包含 `width` × `height` × 4 bytes数据，索引值从0到(高度×宽度×4)-1。

它可以被使用作为查看初始像素数据。每个像素用4个1bytes值(按照红，绿，蓝和透明值的顺序; 这就是"RGBA"格式) 来代表。每个颜色值部分用0至255来代表，每个部分被分配到一个在数组内连续的索引，左上角像素的红色部份在数组的索引0位置。像素从左到右被处理，然后往下，遍历整个数组。

### `Uint8ClampedArray`类型
`Uint8ClampedArray`8位无符号整型固定数组） 类型化数组表示一个由值固定在0-255区间的8位无符号整型组成的数组；如果你指定一个在 [0,255] 区间外的值，它将被替换为0或255；如果你指定一个非整数，那么它将被设置为最接近它的整数。（数组）内容被初始化为0。一旦（数组）被创建，你可以使用对象的方法引用数组里的元素，或使用标准的数组索引语法（即使用方括号标记）。

可以使用`Uint8ClampedArray.length`属性来读取像素数组的大小（以bytes为单位）。

### 创建一个`ImageData`对象
可以使用`createImageData()`方法来创建`ImageData`对象，有下面两张形式：

- `createImageData(width, height)`
- `createImageData(imagedata)`

参数的含义如下：

- `width` `ImageData`新对象的宽度
- `height` `ImageData`新对象的高度
- `imagedata`从现有的`ImageData`对象中，复制一个和其宽度和高度相同的对象。图像自身不允许被复制。

使用上面两种方法创建的`ImageData`对象的所有像素都会被**预设为透明黑，并非复制了图片数据**。

## 从canvas获取`ImageData`对象
可以通过canvas获取到`ImageData`对象从而保存canvas画布的内容。方法如下：

`getImageData(left, top, width, height)`

这个方法会返回一个ImageData对象，它代表了画布区域的对象数据，此画布的四个角落分别表示为(left, top), (left + width, top), (left, top + height), 以及(left + width, top + height)四个点。这些坐标点被设定为画布坐标空间元素。

需要注意：任何在canvas画布以外的元素都会被返回成一个透明黑的ImageData对像。

一个颜色选择器的例子：
<canvas id="canvas" width="300px" height="160px"></canvas>
<p id="color" style="width:300px;height:50px;">把鼠标放在图片上提取颜色</p>
<script>
var img=new Image();img.src="http://uusama.com/wp-content/themes/Git-youyou/timthumb.php?src=http://uusama.com/wp-content/uploads/2017/07/2017072903202979.jpg&h=160&w=300&q=90&zc=1&ct=1";var canvas=document.getElementById("canvas");var ctx=canvas.getContext("2d");img.onload=function(){ctx.drawImage(img,0,0);img.style.display="none"};var color=document.getElementById("color");function pick(event){var x=event.layerX;var y=event.layerY;var pixel=ctx.getImageData(x,y,1,1);var data=pixel.data;var rgba="rgba("+data[0]+","+data[1]+","+data[2]+","+(data[3]/255)+")";color.style.background=rgba;color.textContent=rgba}canvas.addEventListener("mousemove",pick);
</script>
```javascript
// 远程加载一张图片
var img = new Image();
img.src = 'https://asset.uusama.com/loli.jpg';
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');
// 图片加载完成之后绘制在canvas上面
img.onload = function() {
  ctx.drawImage(img, 0, 0);
  img.style.display = 'none';
};

var color = document.getElementById('color');

// 提取颜色，并实施设置color的背景颜色
function pick(event) {
  // 获取光标当前坐标
  var x = event.layerX;
  var y = event.layerY;
  // 获取光标处的像素信息
  var pixel = ctx.getImageData(x, y, 1, 1);
  var data = pixel.data;
  var rgba = 'rgba(' + data[0] + ',' + data[1] +
             ',' + data[2] + ',' + (data[3] / 255) + ')';
  color.style.background =  rgba; // 设置color元素的背景色
  color.textContent = rgba; // 显示RGBA值
}
// 鼠标移动则提取颜色
canvas.addEventListener('mousemove', pick);
```
## 将`ImageData`对象信息写入canvas
你可以用putImageData()方法去对场景进行像素数据的写入：

- `putImageData(myImageData, dx, dy)`

dx和dy参数表示你希望在场景内左上角绘制的像素数据所得到的设备坐标。

例如，为了在场景内左上角绘制myImageData代表的图片，你可以写如下的代码：
```javascript
ctx.putImageData(myImageData, 0, 0);
```
## 保存图片
`HTMLCanvasElement`提供一个`toDataURL()`方法，此方法在保存图片的时候非常有用。它返回一个包含被类型参数规定的图像表现格式的数据链接。返回的图片分辨率是96dpi。

- `canvas.toDataURL(type, encoderOptions)`
    - `type`可选,图片格式，默认为 image/png
    - `encoderOptions`可选，在指定图片格式为 image/jpeg 或 image/webp的情况下，可以从 0 到 1 的区间内选择图片的质量。如果超出取值范围，将会使用默认值 0.92。其他参数会被忽略

当你从画布中生成了一个数据链接，例如，你可以将它用于任何<image>元素，或者将它放在一个有download属性的超链接里用于保存到本地。

你也可以从画布中创建一个Blob对像，使用`canvas.toBlob(callback, type, encoderOptions)`方法。
