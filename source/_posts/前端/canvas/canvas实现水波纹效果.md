---
title: canvas实现水波纹效果
tags:
  - canvas
  - JavaScript
url: 643.html
id: 643
categories:
  - 前端
  - canvas
date: 2018-03-11 21:24:16
mathjax: true
---

本文将会从水波的基本原理开始，详细讲解在canvas中模拟水波扩散，分析并计算水波的能量分布，并通过振幅模拟水波对图像的折射效果，最后实现水波特效。
## 水波基本原理
首先复习一波高中物理知识。

波是指振动的传播。波的传播方向与质点振动方向垂直的为横波，相同则为纵波，水波是横波和纵波的叠加。

对于水波这种波，我们在实现这个特效的时候，需要考虑到下面的特性：

- 圆形波：当你投一块石头到水池中时，你会看到一个以石头入水点为圆心所形成的一圈圈的水波
- 反射：水波碰到墙壁后会反射
- 衰减：因为水是有阻尼的，所以你会看到水波越往外扩散，越弱，最后消失，水面回复平静
- 水波使得图像发生折射，由于水波，使得水面凹凸不平，会折射和反射水池中的图像
- 衍射，波在传播中遇到有很大障碍物或遇到大障碍物中的孔隙时，会绕过障碍物的边缘或孔隙的边缘，呈现路径弯曲，在障碍物或孔隙边缘的背后展衍。

水波纹效果反映到图像上，其本质就是像素的偏移，相当于很多缩放的结合。因此对图像的处理就转化为如何移动图像上的像素点，从而模拟和表现出水波纹的效果。下面是本文将会实现的水波纹特效：

<iframe width="800px" height="500px" src="http://asset.uusama.com/example/water_ripple.html"></iframe>

## 波幅计算
### 波幅表示方法
波的本质是振动，然后传递能量，波的表现形式就是能量的分布情况，我们使用波幅（振动幅度）来描述每一点携带的能量。

假设一开始水面是平静的，整个水面的能量均匀分布。我们知道在canvas中，我们可以使用`ctx.getImageData(0, 0, width, height)`方法将一幅宽为`width`，高为`height`的图像像素信息存入一个数组中，这个数组大小为 `width` × `height` × 4 bytes（RGBA信息）。

我们可以建立两个和图像一样大小 `width` × `height`的数组，用来保存水面上每一个点的前一时刻和后一时刻波幅数据。或者直接使用一个 2 × `width` × `height`的数组，分为前半部分和后半部分来保存前后时刻的波幅数据。

水面在初始状态时是平静的平面，各点的波幅都为0，所以，数组的所有初始值都等于0。
```javascript
var width = settings.width,  // canvas宽度
      height = settings.height, // canvas高度
	  amplitude_size = width * (height + 2) * 2, // 振幅数组大小
	  ripple_map = [],  // 产生水波下一时刻振幅
      last_map = [];  // 初始时刻振幅
// 波幅数组初始化为0
for (var i = 0; i < amplitude_size; i++) {
	ripple_map[i] = last_map[i] = 0;
}
```

### 忽略阻尼计算振幅
由上面一小节，我们可以用$$X_i$$来表示图像中的任意一个像素点，其中$$i$$的值在0到 `width` × `height`之间，我们把宽度`width`简记为$$W$$，将高度`height`简记为$$H$$，则可以用下面的集合表示图像上的像素点集合

$$
\{X_i|0\le i \le WH\}
$$

- 其中坐标为(x,y)的点为$$X_{yW+x}$$

由于波的传播特性，某一点下一时刻的振动情况，受到周围质点的振动以及自身振动情况的联合影响。为了使问题简化，我们假设$$X_i$$点的振幅$$A_i$$除了受到自身的影响外，还受到来自它周围前、后、左、右四个点$$(X_{i-W},X_{i+W},X_{i-1},X_{i+1})$$的影响，并且假设这四个点对$$X_i$$点的影响力机会均等并且线性叠加的。那么可以得到$$X_i$$点的振幅公式：

$$
A_i^{\prime} = a(A_{i-W}+A_{i+W}+A_{i-1}+A_{i+1})+bA_i
$$

- $$A_i$$分别为点$$X_i$$当前时刻的振幅
- a、b为待定系数，$$A_0^{\prime}$$为$$X_0$$点下一时刻的振幅
- 对于图像边界上的点，需要进行特殊处理，可以适当增大振幅数组：(W+2)x(H+2)

假设水的阻尼为0。在这种理想条件下，水的总势能将保持不变。也就是说在任何时刻，所有点的振幅的和保持不变。那么可以得到下面能量守恒公式：

$$
\sum_{i=0}^n{A_i^{\prime}}=\sum_{i=0}^n{A_i}
$$

将上面的$$X_i$$点的振幅公式带入可得：

$$
\sum_{i=0}^n{[a(A_{i-W}+A_{i+W}+A_{i-1}+A_{i+1})+bA_i]}=\sum_{i=0}^n{A_i}
$$

拆开可得：

$$
a \sum_{i=0}^n{A_{i-W}}+a \sum_{i=0}^n{A_{i+W}}+a \sum_{i=0}^n{A_{i-1}}+a \sum_{i=0}^n{A_{i+1}}+b \sum_{i=0}^n{A_i}=\sum_{i=0}^n{A_i}
$$

其中可以近似的认为：

$$
\sum_{i=0}^n{A_{i-W}}= \sum_{i=0}^n{A_{i+W}}= \sum_{i=0}^n{A_{i-1}}= \sum_{i=0}^n{A_{i+1}}= \sum_{i=0}^n{A_i}
$$

等式两边消去可得：

$$
4a+b=1
$$

找出一个最简解：$$a = \frac{1}{2}, b = -1$$

因为$$\frac{1}{2}$$可以用移位运算符“>>”来进行，不用进行乘除法，所以，这组解是最适用的而且是最快的。那么最后得到的下一时刻的振幅公式就是：

$$
A_i^{\prime} =\frac{1}{2}(A_{i-W}+A_{i+W}+A_{i-1}+A_{i+1})-A_i
$$

得到上面这个近似公式后，如果已知某一时刻水面上任意一点的波幅，就可以求出下一时刻水面上任意一点的波幅。

### 考虑阻尼
然而，在实际中是存在阻尼的，否则，用上面这个公式，一旦你在水中增加一个波源，水面将永不停止的震荡下去。

所以，还需要对波幅数据进行衰减处理，让每一个点在经过一次计算后，波幅都比理想值按一定的比例降低。这个衰减率经过测试，用$$\frac{1}{32}$$比较合适，也就是$$\frac{1}{2^5}$$，可以通过移位运算很快的获得。

最后的振幅计算算法如下：
```javascript
// 计算下一时刻波幅，index为像素点位置，old_amplitude为上一时刻该点波幅
function calculAmplitude(index, old_amplitude) {
    var x_boundary = 0, judge = map_index % width;
	// 由于波幅数据顺序存储，加上左右边界检查，避免左边水波传递到右边
    if (judge == 0) {
        x_boundary = 1; // 左边边界
    }else if (judge == width - 1) {
        x_boundary = 2; // 右边边界
    }
    var top = ripple_map[index - width],// 上边的相邻点
        bottom = ripple_map[index + width],// 下边的相邻点
        left = x_boundary != 1 ? ripple_map[index - 1] : 0,// 左边的相邻点
        right = x_boundary != 2 ? ripple_map[index + 1] : 0;// 右边的相邻点
    // 计算当前像素点下一时刻的振幅
    var amplitude = top + bottom + left + right;
    amplitude >>= 1;
    amplitude -= old_amplitude;
    amplitude -= amplitude >> 5;  // 计算衰减
    return amplitude;
}
```

## 页面渲染
因为水的折射，当水面不与我们的视线相垂直的时候，我们所看到的水下的景物并不是在观察点的正下方，而存在一定的偏移。

偏移的程度与水波的斜率，水的折射率和水的深度都有关系，如果要进行精确的计算的话，显然是很不现实的。同样，我们只需要做线形的近似处理就行了。

因为水面越倾斜，所看到的水下景物偏移量就越大，最简单的做法可以近似的用水面上某点的前后、左右两点的波幅之差来代表所看到水底景物的偏移量。

这里我们选用画面的中点作为参考点来计算视觉的偏移。

我们将原始图像的像素信息保存在两个数组中，一个用于保存原始图像数据，一个用于实时保存实时渲染数据。这里需要注意更新图像的时候，图像的恢复问题，这里我们用一个反相器来进行恢复，一个点偏移了，我们给它一个反方向的偏移来抵消就可以恢复。

根据偏移量将原始图象上的每一个象素复制到渲染页面上，将渲染数据绘制到canvas中即可。
```javascript
// 渲染下一帧
function renderRipple() {
    var i = old_index,
        deviation_x,  // x水平方向偏移
        deviation_y,  // y竖直方向偏移
        pixel_deviation, // 偏移后的ImageData对象像素索引
        pixel_source;  // 原始ImageData对象像素索引

    // 交互索引 old_index, new_index
    old_index = new_index;
    new_index = i;

    // 设置像素索引和振幅索引
    i = 0;
    map_index = old_index;

    // 渲染所有像素点
    for (var y = 0; y < height; y++) {
        for (var x = 0; x < width; x++) {
            // 计算当前像素点下一时刻的振幅
            var amplitude = calculAmplitude(map_index, ripple_map[new_index + i]);

            // 更新振幅数组
            ripple_map[new_index + i] = amplitude;

            amplitude = 1024 - amplitude;
            var old_amplitude = last_map[i];
            last_map[i] = amplitude;

            if (old_amplitude != amplitude) {
			     // 计算偏移
                deviation_x = (((x - half_width) * amplitude / 1024) << 0) + half_width;
                deviation_y = (((y - half_height) * amplitude / 1024) << 0) + half_height;

                // 检查边界
                if (deviation_x > width) {
                    deviation_x = width - 1;
                }
                if (deviation_x < 0) {
                    deviation_x = 0;
                }
                if (deviation_y > height) {
                    deviation_y = height - 1;
                }
                if (deviation_y < 0) {
                    deviation_y = 0;
                }
				
				// 计算imageData中对应的像素RGBA偏移位置
                pixel_source = i * 4;
                pixel_deviation = (deviation_x + (deviation_y * width)) * 4;

                // 移动像素的RGBA信息，ripple和texture为背景图的ImageData对象
                ripple.data[pixel_source] = texture.data[pixel_deviation];
                ripple.data[pixel_source + 1] = texture.data[pixel_deviation + 1];
                ripple.data[pixel_source + 2] = texture.data[pixel_deviation + 2];
            }
            ++i;
            ++map_index;
        }
    }
    // 渲染处理之后的图像
    ctx.putImageData(ripple, 0, 0);
}
```

## 波源
为了形成波，我们必须在平静的水面上加入波源，就像向水池中投入一个石头一样，形成的波源的大小和能量与石头的半径和你扔石头的力量都有关系。

为了模拟波源，我们只需要修改一开始初始化的波幅分布数组即可。需要注意投入石头的地方的波幅不易过小和过大。

另外，这个波源的半径也很好控制，只要以波源为圆心，画一个圆，让这个圆内的所有点都来一个脉冲即可。

波源生成方法如下：
```javascript
// 在指定地点产生波源
function disturb(circleX, circleY) {
    // 下面的移位运算可以将值向下取整
    circleX <<= 0;
    circleY <<= 0;
    var maxDistanceX = circleX + dropRadius,
        maxDistanceY = circleY + dropRadius;
    for (var y = circleY - dropRadius; y < maxDistanceY; y++) {
        for (var x = circleX - dropRadius; x < maxDistanceX; x++) {
            ripple_map[old_index + y * width + x] += 512;
        }
    }
}
```

## 待处理事宜
还有很多要完善的地方，以后会更新到[github](https://github.com/youyouzh/water_ripple)，本文所有的效果代码也可以在Git上面找到，欢迎大家star。简单列一下需要优化的点：

- 添加衍射
- 兼容跨域图片
- 图片自动缩放处理
- JQuery插件化封装
- 适配优化，速度优化，效果优化
- 普通HTML元素支持，局部特效

### 衍射
在水波扩散的过程中，如果遇到障碍物，水波会绕过障碍物的边缘或孔隙的边缘，呈现路径弯曲，在障碍物或孔隙边缘的背后展衍。

其实实现起来很简单，我们只要始终保持障碍物的振幅一直为0即可。
