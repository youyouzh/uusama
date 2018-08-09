---
title: canvas图形变换
tags:
  - canvas
  - JavaScript
url: 613.html
id: 613
categories:
  - canvas
  - 前端
date: 2018-03-02 14:33:58
---

这篇文章将会介绍如何在canvas中对绘制的基本图形进行移动，旋转，缩放等变形方法，还会介绍如何在canvas中加载图像。
## 状态的保存和恢复
为了在变形之后，能够将图形恢复原样，需要保存图形的原有状态：

- `save()` `restore`保存和恢复canvas状态，都没有参数。
- 可以调用任意多次`save`方法
- 每一次调用`restore`方法，上一个保存的状态就从栈中弹出，所有设定都恢复

Canvas状态存储在栈中，每当save()方法被调用后，当前的状态就被推送到栈中保存。一个绘画状态包括：

- 当前应用的变形（即移动，旋转和缩放等）
- `strokeStyle`, `fillStyle`, `globalAlpha`, `lineWidth`, `lineCap`, `lineJoin`, `miterLimit`, `shadowOffsetX`, `shadowOffsetY`, `shadowBlur`, `shadowColor`, `globalCompositeOperation`的值
- 当前的裁切路径（clipping path）

一个简单的示例如下：
```javascript
var ctx = document.getElementById('canvas').getContext('2d');

ctx.fillRect(0,0,150,150);   // 使用默认设置绘制一个矩形
ctx.save();                  // 保存默认状态

ctx.fillStyle = '#09F'       // 在原有配置基础上对颜色做改变
ctx.fillRect(15,15,120,120); // 使用新的设置绘制一个矩形

ctx.save();                  // 保存当前状态
ctx.fillStyle = '#FFF'       // 再次改变颜色配置
ctx.globalAlpha = 0.5;    
ctx.fillRect(30,30,90,90);   // 使用新的配置绘制一个矩形

ctx.restore();               // 重新加载之前的颜色状态
ctx.fillRect(45,45,60,60);   // 使用上一次的配置绘制一个矩形

ctx.restore();               // 加载默认颜色配置
ctx.fillRect(60,60,30,30);   // 使用加载的配置绘制一个矩形
```
效果如下所示：
[![canvas保存和恢复示例](http://uusama.com/wp-content/uploads/2018/03/2018030206280242.png)](http://uusama.com/wp-content/uploads/2018/03/2018030206280242.png)

## 变形
图形的变形包括移动，旋转和缩放等，所有这些变形是针对canvas坐标空间的变形，所以在变形之前最好先保存状态，这样便于使用`restore`恢复现场。

### 移动 Translating

`translate(x, y)`

该方法用来移动 canvas 和它的原点到一个不同的位置，接受两个参数。x 是左右偏移量，y 是上下偏移量，如下图所示。
[![canvas移动栅格示意图](http://uusama.com/wp-content/uploads/2018/03/201803020631577.png)](http://uusama.com/wp-content/uploads/2018/03/201803020631577.png)
### 旋转 Rotating
`rotate(angle)`

该方法用于以原点为中心旋转 canvas，接受一个参数：旋转的角度(angle)，它是顺时针方向的，以弧度为单位的值。
[![canvas旋转栅格示意图](http://uusama.com/wp-content/uploads/2018/03/2018030206324130.png)](http://uusama.com/wp-content/uploads/2018/03/2018030206324130.png)

### 缩放 Scaling
`scale(x, y)`

该方法用来对形状，位图进行缩小或者放大，接受两个参数。x,y 分别是横轴和纵轴的缩放因子，它们都必须是正值。值比 1.0 小表示缩小，比 1.0 大则表示放大，值为 1.0 时什么效果都没有。

### 变形 Transforms
`transform(m11, m12, m21, m22, dx, dy)`

该方法允许对变形矩阵直接修改，将当前的变形矩阵乘上一个基于自身参数的矩阵，在这里我们用下面的矩阵表示：

$$\\begin{bmatrix}m11&m21&dx\\\\m12&m22&dy\\\\0&0&1\\end{bmatrix}$$

各参数含义如下：

- `m11` 水平方向的缩放
- `m12` 水平方向的倾斜
- `m21` 竖直方向的倾斜
- `m22` 竖直方向的缩放
- `dx` 水平方向的移动
- `dy` 竖直方向的移动

另外还有两个变形方法：

- `resetTransform()` 重置当前变形为单位矩阵
- `setTransform(m11, m12, m21, m22, dx, dy)` 将当前的变形矩阵重置为单位矩阵，然后用相同的参数调用 transform 方法

下面是变形的例子：
```javascript
var ctx = document.getElementById('canvas').getContext('2d');

    // 绘制矩形
    function drawRect() {
        // 绘制矩形
        ctx.fillRect(0,0,50,50);
    }

    ctx.fillStyle = 'blue';
    drawRect();

    ctx.save();
    ctx.fillStyle = 'cyan';
    ctx.translate(60, 0);  // 变换坐标空间x轴向右平移60
    drawRect();

    ctx.restore();
    ctx.save();
    ctx.fillStyle = 'pink';
    ctx.translate(0, 60); // 变换坐标空间Y轴向下平移60
    drawRect();

    ctx.restore();
    ctx.translate(200, 0);  // X轴向右平移100，避免后续图形遮罩
    ctx.save();
    ctx.fillStyle = 'black';
    ctx.rotate(45 / 180 * Math.PI); // 变换坐标空间旋转45度
    drawRect();

    ctx.restore();
    ctx.translate(100, 0);  // X轴向右平移100，避免后续图形遮罩
    ctx.save();
    ctx.fillStyle = 'red';
    ctx.scale(2, 0.5);  // 变换坐标空间X轴增大2倍，Y轴缩小2倍
    drawRect();

    ctx.restore();
    ctx.save();
    // 重置变形矩阵为，X轴放大2倍，旋转45度，Y轴旋转45度，缩小2倍，X轴向右移动100，Y轴向下移动100
    ctx.setTransform(2, Math.PI/4, Math.PI/4, 0.5, 100, 100);
    drawRect();
```

效果如下所示：
[![canvas变形示例](http://uusama.com/wp-content/uploads/2018/03/2018030206285214.png)](http://uusama.com/wp-content/uploads/2018/03/2018030206285214.png)