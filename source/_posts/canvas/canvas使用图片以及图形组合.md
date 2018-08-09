---
title: canvas使用图片，图形组合以及裁剪
tags:
  - canvas
  - JavaScript
url: 619.html
id: 619
categories:
  - canvas
  - 前端
date: 2018-03-02 22:27:04
---
这篇文章将会介绍如何在canvas中对绘制的基本图形进行移动，旋转，缩放等变形方法，还会介绍如何在canvas中加载图像，图形之间的组合方式以及裁剪。

- 如何绘制基本图形可以参考：[canvas基本图形绘制](http://uusama.com/592.html)
- 如何对基本图形移动旋转缩放可以参考：[canvas图形变换](http://uusama.com/613.html)
- 如何设置基本图形颜色和样式可以参考：[canvas样式和颜色](http://uusama.com/601.html)
- canvas系列教程可以参考：[canvas](http://uusama.com/tag/canvas)

## 获取图片源
### 概述
canvas可以用于动态的图像合成或者作为图形的背景，以及游戏界面（Sprites）等等。浏览器支持的任意格式的外部图片都可以使用，比如PNG、GIF或者JPEG。 你甚至可以将同一个页面中其他canvas元素生成的图片作为图片源。

引入图像到canvas里需要以下两步基本操作：

- 获得一个指向HTMLImageElement的对象或者另一个canvas元素的引用作为源，也可以通过提供一个URL的方式来使用图片（参见例子）
- 使用drawImage()函数将图片绘制到画布上

### canvas可用的图片源
canvas的API可以使用下面这些类型中的一种作为图片的源：

- `HTMLImageElement` 由Image()函数构造出来的，或者任何的`img`元素
- `HTMLVideoElement` HTML中的video元素，还可以从视频中抓取当前帧作为一个图像
- `HTMLCanvasElement` 可以使用另一个 canvas 元素作为图片源
- `ImageBitmap` 高性能的位图，可以低延迟地绘制，它可以从上述的所有源以及其它几种源中生成

这些源统一由 CanvasImageSource类型来引用。下面将会介绍这些源的获取方式。

### 使用相同页面内的图片
对于和当前canvas相同页面内的图片，可以通过下面的方式获取`HTMLImageElement`源：

- `document.images`集合中获取
- `document.getElementsByTagName()`, `document.getElementsById()`等获取图片源标签

### 使用其它 canvas 元素
和引用页面内的图片类似，用`document.getElementsByTagName`或`document.getElementById`方法来获取其它`canvas`元素。

但你引入的应该是已经准备好的`canvas`。

### 在脚本中创建图像
我们可以用脚本创建一个新的`HTMLImageElement`对象。常用有以下两种方法：

- `document.createElement('img')` 调用`document`的API
- `new Image()`

创建了`HTMLImageElement`对象之后，需要设置图片地址以及其他相关属性。这种方法可以用于：

- 加载远程服务端图片资源
	- 对于跨域资源，需在源上使用`crossorigin`属性并且图片的服务器允许跨域访问这个图片，否则，使用这个图片将会污染canvas
	- 需要等到该图片加载完成后才可以使用
	- 同时使用多个远程图片时，需要预加载
- 加载`data:url` base64图片编码
	- 图片内容即时可用，无须再到服务器兜一圈
	- 图像没法缓存，图片大的话内嵌的 url 数据会相当的长

当脚本执行后，图片开始装载。

下面是一个示例：
```javascript
var img = document.createElement('img'); // 使用 DOM HTMLImageElement
img.src = 'http://asset.uusama.com/loli.jpg'; // 指定图片地址，域名不同
img.alt = 'loli';   // 替换文本，可不用
img.crossorigin = 'anonymous';  // 指定跨域请求不需要身份标识
// 图片加载完毕的回调
img.onload = function() {
	// 图片加载完毕可以使用
}

var img2 = new Image();  // 使用 Image 对象的构造函数穿件HTMLImageElement
// 使用指定图片编码的方式加载图片
img2.src = 'data:image/gif;base64,R0lGODlhCwALAIAAAAAA3pn/ZiH5BAEAAAEALAAAAAALAAsAAAIUhA+hkcuO4lmNVindo7qyrIXiGBYAOw==';
```

### 使用视频帧
你还可以使用video元素中的视频帧（即便视频是不可见的）。例如，如果你有一个ID为“myvideo”的video元素，你可以这样做：
```javascript
function getMyVideo() {
  var canvas = document.getElementById('canvas');
  if (canvas.getContext) {
    var ctx = canvas.getContext('2d');

    return document.getElementById('myvideo');
  }
}
```
它将为这个视频返回HTMLVideoElement对象，正如我们前面提到的，它可以作为我们的Canvas图片源。

## 使用图片源
上面介绍了如何获取图片源，下面介绍如何使用这些图片源，将它们绘制到canvas中。

绘制图片主要用到`drawImage()`方法，这个方法有下面三种形式：

- `drawImage(image, dx, dy)`按原始比例绘制
- `drawImage(image, dx, dy, dWidth, dHeight)`按一定比例缩放绘制
- `drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)`按剪切缩放绘制

各个参数的含义如下：

- `image`绘制到上下文的元素。允许任何的 canvas 图像源(CanvasImageSource)，例如：HTMLImageElement，HTMLVideoElement，或者 HTMLCanvasElement
- `dx`目标画布的左上角在目标canvas上 X 轴的位置
- `dy`目标画布的左上角在目标canvas上 Y 轴的位置
- `dWidth`在目标画布上绘制图像的宽度。 允许对绘制的图像进行缩放。 如果不说明， 在绘制时图片宽度不会缩放
- `dHeight`在目标画布上绘制图像的高度。 允许对绘制的图像进行缩放。 如果不说明， 在绘制时图片高度不会缩放
- `sx`需要绘制到目标上下文中的，源图像的矩形选择框的左上角 X 坐标
- `sy`需要绘制到目标上下文中的，源图像的矩形选择框的左上角 Y 坐标
- `sWidth`需要绘制到目标上下文中的，源图像的矩形选择框的宽度。如果不说明，整个矩形从坐标的sx和sy开始，到图像的右下角结束
- `sHeight`需要绘制到目标上下文中的，源图像的矩形选择框的高度

可以参看下面的示意图：

## 图形组合
在之前的例子中，我们都有意无意的避免把一个图形放在另外一个图形上面，那样会导致后面的图形把前面的图形遮住了。

canvas中的`globalCompositeOperation`的属性提供设置如何组合两个以上图形的方法。

`globalCompositeOperation = type` type取值如下：

- `source-over`默认设置，在现有画布内容的顶部绘制新形状
- `source-in`只在新的形状和现有画布内容**重叠**的地方绘制新的形状，其他地方是透明的
- `source-out`只在新的形状和现有画布内容**不重叠**的地方绘制新的形状，其他地方是透明的
- `source-atop`新的形状只在与现有画布内容重叠的地方绘制
- `destination-over`在现有画布内容后面绘制新的图形
- `destination-in`只在新的形状和现有画布内容**重叠**的地方保留现有画布内容，其他地方是透明的
- `destination-out`只在新的形状和现有画布内容**不重叠**的地方保留现有画布内容，其他地方是透明的
- `destination-atop`只在新的形状和现有画布内容**重叠**的地方保留现有画布内容并且在后面绘制新的形状，其他地方是透明的
- `lighter`在两个形状重叠的地方，颜色值为两者颜色值之和
- `copy`只显示新的形状
- `xor`新的形状和现有画布内容**重叠**的地方变透明
- `multiply`顶层的像素乘以底层的对应像素。结果是一个更黑暗的画面
- `screen`像素被反转、相乘、再反转。结果是较轻的图片（与乘法相反）
- `overlay``multiply`和`screen`的结合。基础层的深色部分变暗，浅色部分变亮
- `darken`保留两层中最暗的像素
- `lighten`保留两层中最亮的像素
- `color-dodge`用倒置的顶层来划分底层
- `color-burn`用倒置的顶层来划分底层，然后将结果倒置
- `hard-light``multiply`和`screen`的结合，但顶部和底部层交换
- `soft-light`柔和的`hard-light`。纯黑色或白色不会导致纯黑色或白色
- `difference`从顶层减去底层或者其他值，总是得到正值
- `exclusion`类似于`difference`，但对比度较低
- `hue`保持底层的亮度和色度，而采用顶层的色调
- `saturation`保持底层的亮度和色调，而采用顶层的色度
- `color`保留底层的亮度，同时采用顶层的色调和色度
- `luminosity`保持底层的色调和色度，而采用顶层的亮度

关于上面的说明中，补充说明如下：

- 不同透明度的两个图形的重叠程度使用相减来衡量
- 现有画布内容和底层表示原有内容
- 新的形状和顶层表示新绘制的图形
- 色调对应HLS中的H(Hue),色度对应S(Saturation),亮度对应L(lightness)
- 亮度的比较需要将RGB颜色空间转到HLS颜色空间

下面是每一种组合方式的效果：
<iframe id="gco" src="http://asset.uusama.com/example/gco.html" width="90%" height="1000" >
</iframe>

## 图形剪切
`clip()`

使用 clip() 方法可以创建一个新的裁切路径，然后对现有内容进行裁剪，去掉不需要的部分。有下面三种形式：

- `clip()`
- `clip(fillRule)`
- `clip(path, fillRule)`

参数含义如下：

- `fillRule`这个算法判断一个点是在路径内还是在路径外。允许的值："nonzero": 非零环绕原则，默认的原则。"evenodd": 奇偶环绕原则
- `path`需要剪切的 Path2D 路径

如果和上面介绍的`globalCompositeOperation`属性作一比较，它可以实现与`source-in`和`source-atop`差不多的效果。最重要的区别是裁切路径不会在 canvas 上绘制东西，而且它永远不受新图形的影响。这些特性使得它在特定区域里绘制图形时相当好用。裁剪效果如下图：
[![canvas裁剪路径示意图](http://uusama.com/wp-content/uploads/2018/03/2018030214245559.png)](http://uusama.com/wp-content/uploads/2018/03/2018030214245559.png)

默认情况下，canvas 有一个与它自身一样大的裁切路径（也就是没有裁切效果）。

看下面的简单例子：
```javascript
var canvas = document.getElementById("canvas");
var ctx = canvas.getContext("2d");

// 绘制裁剪路径
ctx.arc(100, 100, 75, 0, Math.PI*2, false);
ctx.clip();

ctx.fillRect(0, 0, 100,100);
```
显示效果如下：
[![canvas裁剪示例](http://uusama.com/wp-content/uploads/2018/03/2018030214252029.png)](http://uusama.com/wp-content/uploads/2018/03/2018030214252029.png)