---
title: canvas动画
tags:
  - canvas
  - JavaScript
url: 627.html
id: 627
categories:
  - canvas
  - JavaScript
  - 前端
date: 2018-03-05 18:37:50
---
经过前面的文章，我们已经能够在canvas画布上画出各种炫酷的图形和画面，但是这些画面都是禁止的，怎么样才能让他们动起来呢？

- 如何绘制基本图形可以参考：[canvas基本图形绘制](http://uusama.com/592.html)
- 如何对基本图形移动旋转缩放可以参考：[canvas图形变换](http://uusama.com/613.html)
- 如何设置基本图形颜色和样式可以参考：[canvas样式和颜色](http://uusama.com/601.html)
- 如何使用外部图片以及图形组合可以参考：[canvas使用图片，图形组合以及裁剪](http://uusama.com/619.html)
- canvas如何保存和加载图像可以参考：[canvas图像保存](http://uusama.com/624.html)
- canvas系列教程可以参考：[canvas](http://uusama.com/tag/canvas)

## 动画的基本步骤
我们知道，动画是一帧一帧的画面不断反映实现的，人的眼睛看到一幅画或一个物体后，在0.34秒内不会消失。利用这一原理，在一幅画还没有消失前播放下一幅画，就会给人造成一种流畅的视觉变化效果。在canvas中，就是在绘制完当前画面之后，快速的绘制下一个画面。步骤如下：

- 清空canvas。
	- 除非接下来要画的内容会完全充满 canvas （例如背景图），否则你需要清空所有画布上的内容。最简单的做法就是用`clearRect`方法。
- 保存canvas状态。
	- 如果你要改变一些会改变 canvas 状态的设置（样式，变形之类的），又要在每画一帧之时都是原始状态的话，你需要先保存一下。
- 绘制动画图形（animated shapes）。
	- 这一步才是重绘动画帧。
- 恢复 canvas 状态。
	- 如果已经保存了 canvas 的状态，可以先恢复它，然后重绘下一帧。

## 操纵动画
在 canvas 上绘制内容是用 canvas 提供的或者自定义的方法，而通常，我们仅仅在脚本执行结束后才能看见结果，比如说，在 for 循环里面做完成动画是不太可能的。

因此，为了实现动画，我们需要一些可以定时执行重绘的方法。window对象提供了下面的方法实现定时动画：

- `setInterval(function, delay)`当设定好间隔时间后，function会定期执行
- `setTimeout(function, delay)`在设定好的时间之后执行函数
- `requestAnimationFrame(callback)`告诉浏览器你希望执行一个动画，并在重绘之前，请求浏览器执行一个特定的函数来更新动画。

如果你并不需要与用户互动，你可以使用setInterval()方法，它就可以定期执行指定代码。如果我们需要做一个游戏，我们可以使用键盘或者鼠标事件配合上setTimeout()方法来实现。通过设置事件监听，我们可以捕捉用户的交互，并执行相应的动作。

`window.requestAnimationFrame()`这个方法提供了更加平缓并更加有效率的方式来执行动画，当系统准备好了重绘条件的时候，才调用绘制动画帧。一般每秒钟回调函数执行60次，也有可能会被降低。

在使用`window.requestAnimationFrame()`方法的过程中，我推荐使用下面的兼容性方法来代替：
```javascript
window.requestAnimationFrame = (function(){
    return  window.requestAnimationFrame       ||
            window.webkitRequestAnimationFrame ||
            window.mozRequestAnimationFrame    ||
            window.oRequestAnimationFrame      ||
            window.msRequestAnimationFrame     ||
            function (callback) {
                window.setTimeout(callback, 1000 / 60);
            };
})();
```

## canvas动画实例-模拟小球自由落体运动
上面介绍了canvas动画的基本概念，接下来我们将会在canvas中实现小球下落的动画。小球的完整代码再本文结尾。[点击可跳转到结尾](#source)。
### 绘制小球
首先需要在canvas上绘制一个小球。
```javascript
var ctx = document.getElementById('canvas').getContext('2d');
if (!ctx) {
    console.log('您的浏览器不支持canvas');
    // 可以抛出异常强制结束JS执行
    throw new Error("Do not support canvas");
}

var ball = {
    x: 100,  // 小球的x坐标
    y: 100,  // 小球的y坐标
    radius: 25,  // 小球半径
    color: 'cyan', // 小球颜色
    draw: function() {  // 绘制小球的函数
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
        ctx.closePath();
        ctx.fillStyle = this.color;
        ctx.fill();
    },
    clear: function() {  // 清除小球区域的函数
        ctx.clearRect(this.x - this.radius,
                this.y - this.radius,
                this.radius * 2,
                this.radius * 2);
    }
}
ball.draw(); // 绘制小球
```

### 添加运动描述
绘制了小球之后，要添加动画，还需要为小球添加速率矢量进行移动。另外，速度也是变化的量，对于只有落体运动，还有竖直方向的重力加速度，所以还需要为小球加上加速度。
```javascript
var ball = {
        x: 100,  // 小球的x坐标
        y: 100,  // 小球的y坐标
        vx: 0,   // 小球水平方向速度
        vy: 0,   // 小球竖直方向速度
        ax: 0,   // 小球水平方向加速度
        ay: 0,   // 小球竖直方向加速度
        dt: 1,   // 两帧之间的时间为1个单位时间
        radius: 25,  // 小球半径
        color: 'cyan', // 小球颜色
        s: function(v, a, t) {
            // 匀加速直线运动的位移公式：s=vt+1/2at^2
            return v * t + (1 / 2.0) * a * t * t;
        },
        dx: function() {
            // 计算水平方向的位移
            return this.s(this.vx, this.ax, this.dt);
        },
        dy: function() {
            // 计算竖直方向的位置
            return this.s(this.vy, this.ay, this.dt);
        },
        next: function() {
            // 计算小球下一时刻的位移
            this.x += this.dx();
            this.y += this.dy();

            // 计算小球下一时刻的速度：v_t = v_0 + a*t
            this.vx = this.vx + this.ax * this.dt;
            this.vy = this.vy + this.ay * this.dt;

            this.boundary(0, canvas.width, canvas.height, 0);
        },
    };

```

假设每一帧之间的时间是单位时间，那么根据当前小球的位置速度和加速度，我们就可以计算下一帧的小球的位置和速度，此时清空上一帧的canvas，再绘制下一帧，即可实现动画效果。
```javascript
var animate; // 记录动画
ball.draw();

// 绘制一帧
function draw() {
    // 1：清空画布
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    // 2：绘制小球
    ball.draw();
    // 3：计算小球的下一个状态
    ball.next();
    // 4：进入下一帧
    animate = window.requestAnimationFrame(draw);
}
```

### 边界处理
若没有任何的碰撞检测，我们的小球很快就会超出画布。我们需要检查小球的 x 和 y 位置是否已经超出画布的尺寸以及是否需要将速度矢量反转。
```javascript
boundary: function(top, right, bottom, left) {
    // 检测小球下一帧是否出界，出界则补正
    if (this.y > bottom) {  // 下边界越界
        this.vy = -this.vy;  // 速度反向
    } else if (this.y < top) {
        this.vy = -this.vy;
    } else if (ball.x > right) {
        this.vx = -this.vx;  // 速度反向
    } else if (ball.x < left) {
        this.vx = -this.vx;
    }

}
```

### 添加拖尾效果
为了使得小球运动更加逼真，可以添加拖尾效果。使用`clearRect`函数清除前一帧动画时，若用一个半透明的`fillRect`函数取代之，就可轻松制作长尾效果。
```javascript
ctx.fillStyle = 'rgba(255,255,255,0.3)';
ctx.fillRect(0,0,canvas.width,canvas.height);
```
**移动鼠标到canvas内可让小球动起来！**
<canvas id="canvas3" width="500px" height="400px" style="border:1px solid;"></canvas>
<script type="text/javascript">function draw(){ctx.fillStyle="rgba(255,255,255,0.3)",ctx.fillRect(0,0,canvas.width,canvas.height),ball.draw(),ball.next(),animate=window.requestAnimationFrame(draw)}var ball,animate,canvas=document.getElementById("canvas3");if(ctx=canvas.getContext("2d"),!ctx)throw console.log("您的浏览器不支持canvas"),new Error("Do not support canvas");ball={x:100,y:100,vx:0,vy:0,ax:0,ay:0,dt:1,radius:25,color:"cyan",draw:function(){ctx.beginPath(),ctx.arc(this.x,this.y,this.radius,0,2*Math.PI,!0),ctx.closePath(),ctx.fillStyle=this.color,ctx.fill()},clear:function(){ctx.clearRect(this.x-this.radius,this.y-this.radius,2*this.radius,2*this.radius)},next:function(){this.x+=this.dx(),this.y+=this.dy(),this.vx=this.vx+this.ax*this.dt,this.vy=this.vy+this.ay*this.dt,this.boundary(0,canvas.width,canvas.height,0)},init:function(){this.vx=5,this.vy=0,this.ax=0,this.ay=.98},boundary:function(a,b,c,d){this.y>c?(this.vy=-this.vy,console.log(this.vy)):this.y<a?this.vy=-this.vy:ball.x>b?this.vx=-this.vx:ball.x<d&&(this.vx=-this.vx)},s:function(a,b,c){return a*c+.5*b*c*c},dx:function(){return this.s(this.vx,this.ax,this.dt)},dy:function(){return this.s(this.vy,this.ay,this.dt)}},window.requestAnimationFrame=function(){return window.requestAnimationFrame||window.webkitRequestAnimationFrame||window.mozRequestAnimationFrame||window.oRequestAnimationFrame||window.msRequestAnimationFrame||function(a){window.setTimeout(a,1e3/60)}}(),ball.init(),ball.draw(),canvas.addEventListener("mouseover",function(){animate=window.requestAnimationFrame(draw)}),canvas.addEventListener("mouseout",function(){window.cancelAnimationFrame(animate)});</script>

### 遗留问题和优化
在实际的生活中，小球碰撞到地面反弹的时候，反弹的高度会越来越低，因为碰撞地面损失了一部分速度。
```javascript
boundary: function(top, right, bottom, left) {
    // 检测小球下一帧是否出界，出界则补正
    if (this.y > bottom) {  // 下边界越界
        this.vy = -this.vy;  // 速度反向
        this.vy = 0.9 * this.vy; // 速度损失
    } else if (this.y < top) {
        this.vy = -this.vy;
    } else if (ball.x > right) {
        this.vx = -this.vx;  // 速度反向
    } else if (ball.x < left) {
        this.vx = -this.vx;
    }
}
```
上面这种方式会偶尔使得小球无法反弹。

在碰撞地面的时候，小球的反弹之后的速度和位移，准确值需要根据严格的匀加速公式以及损失之后的速度来计算。

边界检查时上述方法是检查圆心和边界的位置，更好的方式是检查圆周和边界的距离。

源码可以以及效果可以参考这儿：[本文实例](http://asset.uusama.com/example/ball_base.html), [优化之后的版本](http://asset.uusama.com/example/ball_update.html)

上述所有方式的源代码如下：
<p id="source"></p>
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ball animate</title>
    <style>
        body {
            margin:0;
            padding:0;
            height: 100%;
            /*background: #000;*/
            overflow: hidden;
        }
        canvas {
            padding: 0;
            background: #000;
            border: 1px solid;
        }
    </style>
</head>
<body>
<canvas id="canvas"></canvas>
<script>
    var canvas = document.getElementById('canvas');
    var SCREEN_WIDTH = window.innerWidth,
            SCREEN_HEIGHT = window.innerHeight,
            ACCURACY = 1, ACCURACY_COMPARE = 1e-5;
    canvas.width = 500;
    canvas.height = 500;
    var context = canvas.getContext('2d'),
            animation, running = false;

    var Ball = function(){
        // x 坐标
        this.x = 100;
        // y 坐标
        this.y = 100;

        // x 方向初始速度
        this.vx = 10;
        // y 方向初始速度
        this.vy = 25;

        // x 方向位移
        this.dx = function() {
            return this.s(this.vx, this.ax, this.dt);
        };
        // y 方向位移
        this.dy = function() {
            return this.s(this.vy, this.ay, this.dt);
        };
        // 计算位移
        this.s = function(v, a, t){
            return v * t + (1 / 2.0) * a * t * t;
        };

        // x 方向碰撞速度损失
        this.loss_x = 0.8;
        // y 方向碰撞速度损失
        this.loss_y = 0.8;

        // 水平方向加速度
        this.ax = 0;
        // 竖直方向加速度
        this.ay = 1;

        // 两帧之间的时间间隔
        this.dt = 1;
        // 小球半径
        this.radius = 20;
        // 小球质量
        this.m = 1;
        // 小球颜色
        this.color = 'cyan';
    };
    // 小球绘制函数
    Ball.prototype.draw = function() {
        context.beginPath();
        context.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
        context.closePath();
        context.fillStyle = this.color;
        context.fill();
    };
    // 清除小球
    Ball.prototype.clear = function() {
        context.clearRect(this.x - this.radius,
                this.y - this.radius,
                this.radius * 2,
                this.radius * 2);
    };
    // 获取小球下一个状态
    Ball.prototype.next = function() {
        // 记录当前状态值
        var current_x = this.x, current_vx = this.vx, t1, self = this,
                current_y = this.y, current_vy = this.vy;

        // 下一状态理想坐标
        this.x += this.dx();
        this.y += this.dy();

        // 下一状态理想速度
        this.vx = this.vx + this.ax * this.dt;
        this.vy = this.vy + this.ay * this.dt;

        function boundary(top, right, bottom, left) {
            // 边界判断，预判下一状态是否碰撞下方墙壁
            if (self.y > bottom) {
                // 越界需要重新计算速度和坐标
                // 计算刚好碰到边界之前的速度： vt = sqrt(2aS + v0^2)
                self.vy = Math.sqrt(Math.abs(2 * self.ay * (bottom - current_y) + current_vy * current_vy));
                self.vy = current_vy > 0 ? self.vy : -self.vy;
                if (isNaN(self.vy)) {
                    self.vy = 0;
                    self.ay = 0;
                    self.y = bottom;
                    return false;
                }
                // 计算从当前位置到碰撞位置的时间
                if (self.ay == 0) {
                    if (self.vy == 0) {
                        t1 = 0;
                    } else {
                        t1 = (bottom - current_y) / self.vy;
                    }
                } else {
                    t1 = Math.abs((self.vy - current_vy) / self.ay);
                }
                // 碰撞后速度衰减并反向
                self.vy = -self.vy * self.loss_y;
                // 碰撞后反弹的位移位置
                self.y = bottom + self.s(self.vy, self.ay, self.dt - t1);
                // 位移小于阈值，则速度归零
                if (bottom - self.y < ACCURACY) {
                    self.vy = 0;
                    self.y = bottom;
                    self.ay = 0;
                }
            }
            else if (self.x > right) {
                // 越界需要重新计算速度和坐标
                // 计算刚好碰到边界之前的速度： vt = sqrt(2aS + v0^2)
                self.vx = Math.sqrt(Math.abs(2 * self.ax * (right - current_x) + current_vx * current_vx));
                self.vx = current_vx > 0 ? self.vx : -self.vx;
                if (isNaN(self.vx)) {
                    self.vx = 0;
                    self.ax = 0;
                    self.x = right;
                    return false;
                }
                // 计算从当前位置到碰撞位置的时间
                if (self.ax == 0) {
                    if (self.vx == 0) {
                        t1 = 0;
                    } else {
                        t1 = (right - current_x) / self.vx;
                    }
                } else {
                    t1 = Math.abs((self.vx - current_vx) / self.ax);
                }
                // 碰撞后速度衰减并反向
                self.vx = -self.vx * self.loss_x;
                // 碰撞后反弹的位移位置
                self.x = right + self.s(self.vx, self.ax, self.dt - t1);
                // 位移小于阈值，则速度归零
                if (right - self.x < ACCURACY) {
                    self.vx = 0;
                    self.ax = 0;
                    self.x = right;
                }
            }
            else if (self.x < left) {
                // 越界需要重新计算速度和坐标
                // 计算刚好碰到边界之前的速度： vt = sqrt(2aS + v0^2)
                self.vx = Math.sqrt(Math.abs(2 * self.ax * (current_x - left) + current_vx * current_vx));
                self.vx = current_vx > 0 ? self.vx : -self.vx;
                if (isNaN(self.vx)) {
                    self.vx = 0;
                    self.ax = 0;
                    self.x = left;
                    return false;
                }
                // 计算从当前位置到碰撞位置的时间
                if (self.ax == 0) {
                    if (self.vx == 0) {
                        t1 = 0;
                    } else {
                        t1 = (current_x - left) / self.vx;
                    }
                } else {
                    t1 = Math.abs((self.vx - current_vx) / self.ax);
                }
                // 碰撞后速度衰减并反向
                self.vx = -self.vx * self.loss_x;
                // 碰撞后反弹的位移位置
                self.x = self.s(self.vx, self.ax, self.dt - t1);
                // 位移小于阈值，则速度归零
                if (self.x - left < ACCURACY) {
                    self.vx = 0;
                    self.ax = 0;
                    self.x = left;
                }
            }
            else if (self.y < top) {
                // 越界需要重新计算速度和坐标
                // 计算刚好碰到边界之前的速度： vt = sqrt(2aS + v0^2)
                self.vy = Math.sqrt(Math.abs(2 * self.ay * (current_y - top) + current_vy * current_vy));
                self.vy = current_vy > 0 ? self.vy : -self.vy;
                if (isNaN(self.vy)) {
                    self.vy = 0;
                    self.ay = 0;
                    self.y = top;
                    return false;
                }
                // 计算从当前位置到碰撞位置的时间
                if (self.ay == 0) {
                    if (self.vy == 0) {
                        t1 = 0;
                    } else {
                        t1 = (top - current_y) / self.vy;
                    }
                } else {
                    t1 = Math.abs((self.vy - current_vy) / self.ay);
                }
                // 碰撞后速度衰减并反向
                self.vy = -self.vy * self.loss_y;
                // 碰撞后反弹的位移位置
                self.y = self.s(self.vy, self.ay, self.dt - t1);
                // 位移小于阈值，则速度归零
                if (self.y - top < ACCURACY) {
                    self.vy = 0;
                    self.ay = 0;
                    self.y = top;
                }
            }
            else {}
        }

        boundary(0, canvas.width - this.radius, canvas.height - this.radius, 0);

    };

    function clear() {
        context.fillStyle = 'rgba(0,0,0,0.3)';
        context.fillRect(0,0,canvas.width,canvas.height);
    }

    var ball = new Ball(), animate;
    function draw() {
        clear();
        ball.draw();
        ball.next();
    }

    canvas.addEventListener('click',function(e){
        if (!running) {
            ball.x = e.clientX;
            ball.y = e.clientY;
            animate = setInterval(function() {
                draw();
            }, 20);
            running = true;
        } else {
            running = false;
            clearInterval(animate);
        }
    });

    ball.draw();
</script>
</body>
</html>
```