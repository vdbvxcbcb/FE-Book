

## 基本用法

**`<canvas>` 是一个可以使用 js 来绘制图形的 HTML 元素。**

**canvas 的默认大小为 300 像素 × 150 像素（宽 × 高，像素的单位是 px）。**

```html
<canvas id="tutorial" width="150" height="150"></canvas>
```

`<canvas>` 看起来和 `<img>` 元素很相像，

**但 html 中，`<canvas>` 标签只有两个属性—— width 和 height ，都是可选的，也可以利用 DOM  来设置。**

CSS 的尺寸与初始画布的比例不一致， `<canvas>`会出现扭曲。

尝试用 width 和 height 属性为 `<canvas>` 明确规定宽高，而不是使用 CSS。

**id 属性并不是 `<canvas>`元素所特有的，而是每一个 HTML 元素都默认具有的属性（比如 class 属性）。**

给每个标签都加上一个 id 属性是个好主意，因为这样就能在我们的脚本中很容易找到它。

**css 中，`<canvas>` 元素可以像任何一个普通的图像一样（有 margin，border，background ）**

**基本准备工作只有三步：**

1. **布置画布：通过添加**`<canvas>`**标签，添加 canvas 元素**

1. **获取画布：通过**`<canvas>`**标签的 id，获得 canvas 对象**

1. **获得画笔：通过 canvas 对象的**`getContext("2d")`**方法，获得 2D 渲染上下文**

```html
<!DOCTYPE html>
<html lang="zh-hans">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Canvas</title>
</head>
<body>
  <!-- 添加 canvas 元素 -->
  <canvas id="canvas" width="150" height="150"></canvas>
  <script type="text/javascript">
    function draw() {
      // 获得 canvas 对象
      var canvas = document.getElementById("canvas");
      if (canvas.getContext) {
        // 获得 2D 渲染上下文
        var ctx = canvas.getContext("2d");

        ctx.fillStyle = "rgb(200, 0, 0)";
        ctx.fillRect (10, 10, 55, 50);

        ctx.fillStyle = "rgba(0, 0, 200, 0.5)";
        ctx.fillRect (30, 30, 55, 50);
      }
    }
    draw()
  </script>
</body>
</html>
```

![mooo.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1660661630595-d6d2d881-a8b9-4fd7-a5fc-51284da4bcbc.png)

 **`<canvas>` 元素的内容为替换内容**

不支持 `<canvas>` 的浏览器将会忽略容器并在其中渲染替换内容。

而支持 `<canvas>` 的浏览器将会忽略在容器中包含的内容，并且只是正常渲染 canvas。

```html
<canvas id="stockGraph" width="150" height="150">
  current stock price: $3.15 +0.15
</canvas>

<canvas id="clock" width="150" height="150">
  <img src="images/clock.png" width="150" height="150" alt=""/>
</canvas>

<canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;" width="800" height="600">
  你的浏览器居然不支持 Canvas？！赶快换一个吧！！
</canvas>
```

### canvas 基础案例

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>canvas-基础案例</title>
  </head>
  <body>
    <!-- step1. 编写 canvas HTML 标签-->
    <canvas id="canvas" height="800" width="800"></canvas>
    <script>
      // step2. 获取 canvas DOM 对象
      const canvas = document.getElementById("canvas");
      // step3. 创建 canvas 对象
      const ctx = canvas.getContext("2d");
      // step4. 设置绘图属性并调用绘图 API
      /* 正方形绘制 */
      ctx.fillStyle = "red";
      ctx.fillRect(0, 0, 50, 50);
      /* 路径绘制 */
      ctx.beginPath();
      ctx.lineWidth = 1;
      ctx.strokeStyle = "blue";
      ctx.moveTo(100, 100); // 起点
      ctx.lineTo(250, 75); // 可添加多个中间点
      ctx.lineTo(300, 100); // 最后一个即为终点
      ctx.stroke();
      /* 圆形绘制 */
      ctx.beginPath(); // 结束上一段的绘制
      ctx.lineWidth = 10;
      ctx.strokeStyle = "green";
      ctx.fillStyle = "yellow";
      ctx.arc(200, 200, 50, 0, 2 * Math.PI);
      ctx.stroke();
      ctx.fill();
      /* 点绘制 */
      ctx.beginPath(); // 结束上一段的绘制
      ctx.lineWidth = 1;
      ctx.strokeStyle = "red";
      ctx.moveTo(150, 150);
      ctx.lineTo(151, 151); // 也就是一个像素的路径
      ctx.stroke();
    </script>
  </body>
</html>
```

## 绘制图形

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1660661630589-7eed60c1-421f-4872-9ffb-8876c3d612d4.png)

画布栅格（canvas grid）以及坐标空间

canvas 元素默认被网格所覆盖。通常来说网格中的一个单元相当于 canvas 元素中的一像素。

网格的起点为左上角（坐标为（0,0））。所有元素的位置都相对于原点定位。

`<canvas>` 只支持两种形式的图形绘制：矩形和路径（由一系列点连成的线段）。

所有其它类型的图形都是通过一条或者多条路径组合而成的。

### 矩形

api

[`fillRect(x, y, width, height)`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/fillRect)

绘制一个填充的矩形

[`strokeRect(x, y, width, height)`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/strokeRect)

绘制一个矩形的边框

[`clearRect(x, y, width, height)`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/clearRect)

清除指定矩形区域，让清除部分完全透明。（类似橡皮擦）

```html
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Canvas</title>
</head>
<body>
  <canvas id="canvas" width="150" height="150"></canvas>
  <script type="text/javascript">
    function draw() {
      let canvas = document.getElementById('canvas');
      if (canvas.getContext) {
        let ctx = canvas.getContext('2d');
        // fillRect() 函数绘制了一个边长为 100px 的黑色正方形
        ctx.fillRect(25, 25, 100, 100);
        // clearRect() 函数从正方形的中心开始擦除了一个 60 * 60px 的正方形
        ctx.clearRect(45, 45, 60, 60);
        // strokeRect() 在清除区域内生成一个 50 * 50 的正方形边框
        ctx.strokeRect(50, 50, 50, 50);
      }
    } 
    draw()
  </script>
</body>
</html>
```

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1660661630588-0f5b91f6-1d4b-4cc4-ae96-271f04caa3d1.png)

以上的三个函数绘制之后会马上显现在 canvas 上，即时生效。

`rect(x, y, width, height)`

绘制一个左上角坐标为（x,y），宽高为 width 以及 height 的矩形。

该方法执行时，moveTo() 方法自动设置坐标参数（0,0）。也就是说，当前笔触自动重置回默认坐标。

```js
var canvas = document.getElementById("canvas");
var ctx = canvas.getContext("2d");
ctx.rect(10, 10, 100, 100);
ctx.fill();
```

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1660751064346-e8245772-add5-437d-ade4-e712c503f030.png)

### 路径

图形的基本元素是路径。（两点成线，可以直线曲线，可以多条线）

**路径是通过不同颜色和宽度的线相连形成的不同形状的点的集合。**

**一个路径，甚至一个子路径，都是闭合的。**

`beginPath()`      新建一条路径

**新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。**

**本质上，路径是由很多子路径构成，这些子路径都是在一个列表中，所有的子路径（线、弧形、等等）构成图形。**

**每次这个方法调用之后，绘制状态清空重置，然后我们就可以重新绘制新的图形。**

**之后代码设置的绘制结束于绘制方法 stroke()、fill() 或者 closePath()**

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1693972938455-a69aee81-f9e4-495e-85fa-7bdae414ed03.png" alt="Snipaste_2023-09-06_11-51-49" style="zoom: 67%;" />

`closePath()`      闭合路径

闭合路径之后图形绘制命令又重新指向到上下文中。 

`moveTo(x, y)`     移动画笔

移动画笔到（x,y）作为开始点，单位是 px

当 canvas 初始化或者 beginPath() 调用后，通常会使用 moveTo() 函数设置起点。

moveTo() 可以想象为在纸上作业，一支钢笔或者铅笔的笔尖从一个点到另一个点的移动过程。  

使用 moveTo() 函数能够绘制一些不连续的路径

`lineTo(x1, y1)` 绘制直线

绘制一条从当前位置到指定 x 以及 y 位置的直线。

上一笔的结束点（x1，y1），绘制一条从当前位置到指定 x1 以及 y1 位置的直线。

`lineWidth`  `strokeStyle` 选择画笔粗细与颜色

这里我们暂且只设置一下画笔的粗细与颜色。

context.lineWidth = 5，这句话的意思是设置画笔(线条)的粗细为 5 px。

context.strokeStyle = "#AA394C"，这句话的意思是设置画笔(线条)的颜色为玫红色。

因为 Canvas 是基于状态的绘制，所以我们在选择画笔粗细和颜色的同时，其实也是选择了线条的粗细和颜色。

`stroke()`  描边

通过线条来绘制图形轮廓。

每次使用 stroke() 时，它都会把之前设置的绘制状态再绘制一遍

`fillStyle` 选择油漆桶的颜色

`fill()`  填充


通过填充路径的内容区域生成实心的图形。

调用 fill() 函数时，所有没有闭合的形状都会自动闭合，所以不需要调用 closePath() 函数。

但是调用 stroke() 时不会自动闭合。

![Snipaste_2023-09-06_12-01-52](https://cdn.nlark.com/yuque/0/2023/png/1614731/1693972929890-38146ff3-ba76-41db-99a1-82a8d19cc7b2.png)

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>绘制折线</title>
</head>
<body>
<div id="canvas-warp">
    <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
        你的浏览器居然不支持Canvas？！赶快换一个吧！！
    </canvas>
</div>

<script>
    window.onload = function(){
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");

        context.moveTo(100,100);
        context.lineTo(300,300);
        context.lineTo(100,500);
        context.lineWidth = 5;
        context.strokeStyle = "red";
        context.stroke();

        context.moveTo(300,100);
        context.lineTo(500,300);
        context.lineTo(300,500);
        context.lineWidth = 5;
        context.strokeStyle = "blue";
        context.stroke();

        context.moveTo(500,100);
        context.lineTo(700,300);
        context.lineTo(500,500);
        context.lineWidth = 5;
        context.strokeStyle = "black";
        context.stroke();
    }
</script>
</body>
</html>
```

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1660664093220-1f15897d-95d0-488e-adb9-1b848a8a7090.jpeg" alt="img" style="zoom:50%;" />

是不是很奇怪？说好的先红色，再蓝色，再黑色呢？怎么全是黑色了？

其实，这里的原因是之前一直强调的一点—— Canvas 是基于状态的绘制。

第一次 stroke() 时，绘制一条红色的折线；

第二次 stroke() 时，会再重新绘制之前的那条红色的折线，但是这个时候的画笔被更换成蓝色的了，所以画出的折线全是蓝色的。

同理，第三次绘制的时候，画笔颜色是最后的黑色，所以会重新绘制三条黑色的折线。

换言之，strokeStyle 属性被覆盖了。

#### 使用 beginPath() 开始绘制

为了让绘制方法不重复绘制，我们可以在每次绘制之前加上 beginPath()，代表下次绘制的起始之处为 beginPath() 之后的代码。

我们在三次绘制之前分别加上 context.beginPath() 

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>绘制折线</title>
</head>
<body>
<div id="canvas-warp">
    <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
        你的浏览器居然不支持Canvas？！赶快换一个吧！！
    </canvas>
</div>

<script>
    window.onload = function(){
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");

        context.beginPath();
        context.moveTo(100,100);
        context.lineTo(300,300);
        context.lineTo(100,500);
        context.lineWidth = 5;
        context.strokeStyle = "red";
        context.stroke();

        context.beginPath();
        context.moveTo(300,100);
        context.lineTo(500,300);
        context.lineTo(300,500);
        context.lineWidth = 5;
        context.strokeStyle = "blue";
        context.stroke();

        context.beginPath();
        context.moveTo(500,100);
        context.lineTo(700,300);
        context.lineTo(500,500);
        context.lineWidth = 5;
        context.strokeStyle = "black";
        context.stroke();
    }
</script>
</body>
</html>
```

<img src="https://cdn.nlark.com/yuque/0/2022/jpeg/1614731/1660664093202-228ea524-ae21-424d-96cb-28c99d521437.jpeg" alt="img" style="zoom:50%;" />

`closePath()`

**闭合路径之后图形绘制命令又重新指向到上下文中。**

**闭合路径 closePath()，不是必需的。**

这个方法会通过**绘制一条从当前点到开始点的直线来闭合图形。**

**如果图形是已经闭合了的，即当前点为开始点，该函数什么也不做。**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>绘制矩形</title>
</head>
<body>
<div id="canvas-warp">
    <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
        你的浏览器居然不支持Canvas？！赶快换一个吧！！
    </canvas>
</div>

<script>
    window.onload = function(){
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");

        context.beginPath();
        context.moveTo(150, 50);
        context.lineTo(650, 50);  // ➡
        context.lineTo(650, 550); // ⬇
        context.lineTo(150, 550); // ⬅
        context.lineTo(150, 50);  // ⬆  绘制最后一笔使图像闭合
        context.lineWidth = 5;
        context.strokeStyle = "black";
        context.stroke();
    }
</script>
</body>
</html>
</body>
</html>
```

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1660751419742-be344b0a-c244-426a-bfb2-1881f5de71be.png" alt="img" style="zoom: 50%;" />

最后一笔闭合的时候有问题，导致左上角有一个缺口。

这种情况是设置了`lineWidth`导致的。如果默认 1 笔触的话，是没有问题的。但是笔触越大，线条越宽，这种缺口就越明显。

#### 使用 closePath() 闭合图形

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>绘制矩形</title>
</head>
<body>
<div id="canvas-warp">
    <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
        你的浏览器居然不支持Canvas？！赶快换一个吧！！
    </canvas>
</div>

<script>
    window.onload = function(){
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");

        context.beginPath();
        context.moveTo(150, 50);
        context.lineTo(650, 50);
        context.lineTo(650, 550);
        context.lineTo(150, 550);
        context.lineTo(150, 50);  // 最后一笔可以不画
        context.closePath();      // 使用 closePath() 闭合图形

        context.lineWidth = 5;
        context.strokeStyle = "black";
        context.stroke();

    }
</script>
</body>
</html>
</body>
</html>
```

<img src="https://cdn.nlark.com/yuque/0/2022/png/1614731/1660751419735-187d8396-a795-4acc-aa98-6be7ff0b7958.png" alt="img" style="zoom: 50%;" />

> **每次开始绘制前都务必要使用`beginPath()`，为了代码的完整性，建议大家在每次绘制结束后使用`closePath()`。**
>
> 大多数情况下不使用是没有什么关系的，但是使用的话可以增强代码的可读性以及意想不到的效果。

#### 三角形

```html
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Canvas</title>
</head>
<body>
  <canvas id="canvas" width="150" height="150"></canvas>
  <script type="text/javascript">
    function draw() {
      var canvas = document.getElementById('canvas');
      if (canvas.getContext) {
        var ctx = canvas.getContext('2d');
        ctx.beginPath();
        ctx.moveTo(75, 50);
        ctx.lineTo(100, 75);
        ctx.lineTo(100, 25);
        ctx.fill();
     }
    }
    draw()
  </script>
</body>
</html>
```

![img](https://cdn.nlark.com/yuque/0/2022/png/1614731/1660662539665-85770197-1387-4921-a1fe-67ab7815f9d1.png)



```html
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Canvas</title>
</head>
<body>
  <canvas id="canvas" width="150" height="150"></canvas>
  <script type="text/javascript">
    function draw() {
      var canvas = document.getElementById('canvas');
      if (canvas.getContext){
      var ctx = canvas.getContext('2d');
    
      // 填充三角形
      ctx.beginPath();
      ctx.moveTo(25, 25);
      ctx.lineTo(105, 25);
      ctx.lineTo(25, 105);
      ctx.fill();
    
      // 描边三角形
      ctx.beginPath();
      ctx.moveTo(125, 125);
      ctx.lineTo(125, 45);
      ctx.lineTo(45, 125);
      ctx.closePath();
      ctx.stroke();
      }
    }
    draw()
  </script>
</body>
</html>
```

![hyjyu.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1660749962008-c401b2e6-a40b-4940-a297-e108abcc5341.png)



#### 笑脸

arc(x, y, radius, startAngle, endAngle, anticlockwise)

画一个以（x,y）为圆心的以 radius 为半径的圆弧（圆），

从 startAngle 到 endAngle ，开始的弧度到结束的弧度。

按照 anticlockwise 给定的方向（默认 false，为顺时针）来生成。为 true 时，是逆时针方向。

arcTo(x1, y1, x2, y2, radius)

根据给定的控制点和半径画一段圆弧，再以直线连接两个控制点。

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1693975566148-ffdf06c1-9d35-486a-ac71-605aaef6514e.png" alt="Snipaste_2023-09-06_12-45-53" style="zoom:67%;" />

```html
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Canvas</title>
</head>
<body>
  <canvas id="canvas" width="150" height="150"></canvas>
  <script type="text/javascript">
    function draw() {
      var canvas = document.getElementById('canvas');
      if (canvas.getContext){
        var ctx = canvas.getContext('2d');
        ctx.beginPath();
        ctx.arc(75, 75, 50, 0, Math.PI * 2, true); // 绘制圆脸
        ctx.moveTo(110, 75);
        ctx.arc(75, 75, 35, 0, Math.PI, false);    // 口 (顺时针)
        ctx.moveTo(65, 65);
        ctx.arc(60, 65, 5, 0, Math.PI * 2, true);  // 左眼
        ctx.moveTo(95, 65);
        ctx.arc(90, 65, 5, 0, Math.PI * 2, true);  // 右眼
        ctx.stroke();
      }
    }
    draw()
  </script>
</body>
</html>
```

![gtrrt.png](https://cdn.nlark.com/yuque/0/2022/png/1614731/1660749961987-402e09b2-ec8f-437c-964e-d5d876eb5bf8.png)



### 使用样式和颜色

**画笔的颜色和粗细**

**先用 `fillStyle` 或 `strokeStyle` 属性选择要填充或描边的颜色，再使用 `fill()` 或 `stroke()`**

**Canvas 是基于状态的绘制，只有调用了 stroke() 和 fill() 才确定绘制。**

**所以我们要把这两行代码放在最后，其余的状态设置的代码放在它们之前，将状态设置与确定绘制的代码分隔开。**

如果我们想要给图形上色，有两个重要的属性可以做到：`fillStyle` 和 `strokeStyle。`

- [`fillStyle = color`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/fillStyle)

  设置图形的填充颜色。

 -  [`strokeStyle = color`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/strokeStyle)

​       设置图形轮廓的颜色。

`color` 可以是表示 CSS 颜色值的字符串，渐变对象或者图案对象。

**一旦设置了 `strokeStyle` 或者 `fillStyle` 的值，那么这个新值就会成为新绘制的图形的默认值。**

**要给每个图形上不同的颜色，需要重新设置 `fillStyle` 或 `strokeStyle` 的值。**

context.lineWidth = 5，这句话的意思是设置画笔(线条)的粗细为 5 px。

context.strokeStyle = "#AA394C"，这句话的意思是设置画笔(线条)的颜色为玫红色。

#### 给矩形上色

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>绘制矩形</title>
</head>
<body>
<div id="canvas-warp">
    <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
        你的浏览器居然不支持Canvas？！赶快换一个吧！！
    </canvas>
</div>

<script>
    window.onload = function(){
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");

        context.beginPath();
        context.moveTo(150,50);
        context.lineTo(650,50);
        context.lineTo(650,550);
        context.lineTo(150,550);
        context.lineTo(150,50);     // 最后一笔可以不画
        context.closePath();        // 使用closePath()闭合图形

        context.fillStyle = "yellow";   // 选择油漆桶的颜色
        context.lineWidth = 5;
        context.strokeStyle = "black";

        context.fill();                 // 确定填充
        context.stroke();

    }
</script>
</body>
</html>
</body>
</html>
```



<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1678025901609-2e7df713-b0a6-4821-a570-f4a4f71383ba.png" alt="Snipaste_2023-03-05_22-17-58.png" style="zoom: 25%;" />

﻿绘制矩形其实都是这样的四笔，我们每次用这种笨方法画矩形都要画这四笔有多麻烦，如果我们要花10个矩形？100个？1000个？

都这样写，代码会臃肿，复用性很低。这里，我们可以使用 JavaScript 封装这些方法。

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>封装绘制矩形方法</title>
</head>
<body>
<div id="canvas-warp">
    <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
        你的浏览器居然不支持Canvas？！赶快换一个吧！！
    </canvas>
</div>

<script>
    window.onload = function(){
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");

        drawRect(context, 150, 50, 50, 50, "red", 5, "blue");
        drawRect(context, 250, 50, 50, 50, "green", 5, "red");
        drawRect(context, 350, 50, 50, 50, "yellow", 5, "black");
    }

    function drawRect(cxt, x, y, width, height, fillColor, borderWidth, borderColor){
        cxt.beginPath();
        cxt.moveTo(x, y);
        cxt.lineTo(x + width, y);
        cxt.lineTo(x + width, y + height);
        cxt.lineTo(x, y + height);
        cxt.lineTo(x, y);
        cxt.closePath();

        cxt.lineWidth = borderWidth;
        cxt.strokeStyle = borderColor;
        cxt.fillStyle = fillColor;

        cxt.fill();
        cxt.stroke();
    }
</script>
</body>
</html>
</body>
</html>
```

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1678026123209-ba11536d-fe54-4126-9d07-85b321ad28d1.png" alt="Snipaste_2023-03-05_22-21-44.png" style="zoom: 33%;" />

在 Canvas API 中已经帮我们封装好了一个绘制矩形的方法—— rect() 。

基于此，我们可以把刚才封装的方法优化一下。

```js
function drawRect(cxt, x, y, width, height, fillColor, borderWidth, borderColor) {
    cxt.beginPath();
    cxt.rect(x, y, width, height);
    // cxt.closePath(); 可以不用 closePath()

    cxt.lineWidth = borderWidth;
    cxt.strokeStyle = borderColor;
    cxt.fillStyle = fillColor;

    cxt.fill();
    cxt.stroke();
}
```



```html
<!DOCTYPE html>
<html lang="zh">
    <head>
        <meta charset="UTF-8">
        <title>绘制魔性图形</title>
    </head>
    <body>
        <div id="canvas-warp">
            <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
                你的浏览器居然不支持Canvas？！赶快换一个吧！！
            </canvas>
        </div>

        <script>
            window.onload = function(){
                var canvas = document.getElementById("canvas");
                canvas.width = 800;
                canvas.height = 600;
                var context = canvas.getContext("2d");

                context.beginPath();
                context.rect(0, 0, 800, 600);
                context.fillStyle = "#AA9033";

                context.fill();

                context.beginPath();
                for(var i = 0; i < 20; i++){
                    drawWhiteRect(context, 200 + 10 * i, 100 + 10 * i, 400 - 20 * i, 400 - 20 * i);
                    drawBlackRect(context, 205 + 10 * i, 105 + 10 * i, 390 - 20 * i, 390 - 20 * i);
                }

                context.beginPath();
                context.rect(395, 295, 5, 5);
                context.fillStyle = "black";
                context.lineWidth = 5;

                context.fill();
                context.stroke();
            }

            function drawBlackRect(cxt, x, y, width, height){
                cxt.beginPath();
                cxt.rect(x, y, width, height);

                cxt.lineWidth = 5;
                cxt.strokeStyle = "black";

                cxt.stroke();
            }

            function drawWhiteRect(cxt, x, y, width, height){
                cxt.beginPath();
                cxt.rect(x, y, width, height);

                cxt.lineWidth = 5;
                cxt.strokeStyle = "white";

                cxt.stroke();
            }
        </script>
    </body>
</html>
</body>
</html>
```

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1678026559837-a7f0d966-b961-4e03-9176-f76a9bed4ddb.png" alt="Snipaste_2023-03-05_22-29-00.png" style="zoom:33%;" />

### ﻿线条的属性

![Snipaste_2023-09-09_12-12-02](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694232790955-9cf6d190-3615-4bb8-815f-826ddc9ccf7d.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      /* 编写样式 */
      body {
        padding: 0;
        margin: 0;
        background-image: url(../images/grid.png);
      }
      canvas {
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <!-- onload="draw()" -->
  <body>
    <!-- 编写canvas -->
    <canvas id="tutorial" width="150" height="150">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      draw();
    };

    function draw() {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        ctx.beginPath();

        // 真正的1像素
        ctx.moveTo(10.5, 10);
        ctx.lineTo(10.5, 100);
        ctx.lineWidth = 1;

        // 真正的2像素
        // ctx.moveTo(10, 10);
        // ctx.lineTo(10, 100);
        // ctx.lineWidth = 2;

        ctx.stroke();
      }
    }
  </script>
</html>
```

![Snipaste_2023-09-09_12-12-28](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694232790640-3f8d8ef9-d7b7-4ecd-8d8d-61ecf6cdc18c.png)

lineCap 属性

lineCap 定义上下文 线的端点，可以有以下 3 个值。

● butt：默认值，端点是垂直于线段边缘的平直边缘。
● round：端点是在线段边缘处以线宽为直径的半圆。
● square：端点是在选段边缘处以线宽为长、以一半线宽为宽的矩形。

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>线条的帽子</title>
</head>
<body>
<div id="canvas-warp">
    <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
        你的浏览器居然不支持Canvas？！赶快换一个吧！！
    </canvas>
</div>

<script>
    window.onload = function(){
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");

        context.lineWidth = 50;
        context.strokeStyle = "#1BAAAA";

        context.beginPath();
        context.moveTo(100,100);
        context.lineTo(700,100);
        context.lineCap = "butt";
        context.stroke();

        context.beginPath();
        context.moveTo(100,300);
        context.lineTo(700,300);
        context.lineCap = "round";
        context.stroke();

        context.beginPath();
        context.moveTo(100,500);
        context.lineTo(700,500);
        context.lineCap = "square";
        context.stroke();

        //下面画两个基准线方便观察
        context.lineWidth = 3;
        context.strokeStyle = "black";

        context.beginPath();
        context.moveTo(100,0);
        context.lineTo(100,600);
        context.moveTo(700,0);
        context.lineTo(700,600);
        context.stroke();
    }
</script>
</body>
</html>
```

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1687093179892-f54f09c4-1448-436e-896a-084a2baa4306.png" alt="Snipaste_2023-06-18_20-59-33" style="zoom:50%;" />

做了两条平行线做一下参考，这样一眼就能看清 lineCap 三个值的特点。

但要注意，这个帽子只在线条的端点处起作用，哪怕是折点很多的折线，也仅仅是在开始和终止的两个端点带帽子。

如果想改变线条折点(两个线段的连接处)的样式，那就要用到下面的lineJoin属性。

lineJoin 属性

lineJoin 定义两条线相交产生的拐角，可将其称为连接。在连接处创建一个填充三角形，可以使用 lineJoin 设置它的基本属性。

● miter：默认值，在连接处边缘延长相接。miterLimit 是角长和线宽所允许的最大比例(默认是 10)。
● bevel：连接处是一个对角线斜角。
● round：连接处是一个圆。

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>线条的连接</title>
</head>
<body>
<div id="canvas-warp">
    <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
        你的浏览器居然不支持Canvas？！赶快换一个吧！！
    </canvas>
</div>

<script>
    window.onload = function(){
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");

        context.beginPath();
        context.moveTo(100,100);
        context.lineTo(300,300);
        context.lineTo(100,500);
        context.lineJoin = "miter";
        context.lineWidth = 20;
        context.strokeStyle = "red";
        context.stroke();

        context.beginPath();
        context.moveTo(300,100);
        context.lineTo(500,300);
        context.lineTo(300,500);
        context.lineJoin = "bevel";
        context.lineWidth = 20;
        context.strokeStyle = "blue";
        context.stroke();

        context.beginPath();
        context.moveTo(500,100);
        context.lineTo(700,300);
        context.lineTo(500,500);
        context.lineJoin = "round";
        context.lineWidth = 20;
        context.strokeStyle = "black";
        context.stroke();
    }
</script>
</body>
</html>
```

![Snipaste_2023-06-18_21-00-21](https://cdn.nlark.com/yuque/0/2023/png/1614731/1687093226103-930b1134-047d-4746-aa70-e154855ae28d.png)

高级线段绘制举例

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>miterLimit</title>
</head>
<body>
<div id="canvas-warp">
    <canvas id="canvas" style="border: 1px solid #aaaaaa; display: block; margin: 50px auto;">
        你的浏览器居然不支持Canvas？！赶快换一个吧！！
    </canvas>
</div>

<script>
    window.onload = function(){
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 600;
        var context = canvas.getContext("2d");

        context.beginPath();
        context.moveTo(100,100);
        context.lineTo(300,300);
        context.lineTo(100,500);
        context.lineJoin = "miter";
        context.miterLimit = 10;
        context.lineWidth = 5;
        context.strokeStyle = "red";
        context.stroke();

        context.beginPath();
        context.moveTo(300,200);
        context.lineTo(500,300);
        context.lineTo(300,400);
        context.lineJoin = "miter";
        context.miterLimit = 10;
        context.lineWidth = 5;
        context.strokeStyle = "blue";
        context.stroke();

        context.beginPath();
        context.moveTo(500,290);
        context.lineTo(700,300);
        context.lineTo(500,310);
        context.lineJoin = "miter";
        context.miterLimit = 10;
        context.lineWidth = 5;
        context.strokeStyle = "black";
        context.stroke();
    }
</script>
</body>
</html>
```

![Snipaste_2023-06-18_21-15-58](https://cdn.nlark.com/yuque/0/2023/png/1614731/1687094185491-37d9f2fc-02ba-41bf-a010-6f1dd35f6344.png)

这 3 个线段和连接的实例有助于说明在画布上绘制线段时不同属性的组合。

实例 1 尝试从画布左上角开始绘制，结果发生了一个奇怪的现象。

Canvas 路径在 x 轴和 y 轴方向上都绘制到了起点的外侧。由于这个原因实例 1 上面的线看起来要细些。

另外，左上角水平部分中圆形端点也无法看到，它们都被绘制到了屏幕之外的负值坐标区域。此外，`lineJoin` 定义的对角线斜角也没有绘出。

实例 2 调整了例子 1 中出现的问题,将起始点离开左上角。这样就绘制出了完整的水平线,并且圆形 `lineCap` 和斜角 `lineJoin` 都被绘制出来了。

实例 3 显示了去掉 `lineCap` 设置后的默认端点效果,并且将 `lineJoin` 调整为圆角。

## 绘制文本

![Snipaste_2023-09-09_12-14-45](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694232945876-12631576-98e4-4177-b6b0-ceee7f5eaea9.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      /* 编写样式 */
      body {
        padding: 0;
        margin: 0;
        background-image: url(../images/grid.png);
      }
      canvas {
        /* color: purple; */
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <!-- onload="draw()" -->
  <body>
    <!-- 编写canvas -->
    <canvas id="tutorial" width="150" height="150">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      draw();
    };

    function draw() {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        ctx.beginPath();
        ctx.moveTo(0, 100);
        ctx.lineTo(150, 100);
        ctx.strokeStyle = "purple";
        ctx.stroke();

        ctx.font = "40px serif";
        ctx.textAlign = "center";
        ctx.textBaseline = "middle";
        ctx.fillText("Ay", 100, 100);
      }
    }
  </script>
</html>
```

![Snipaste_2023-09-09_12-16-05](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694232985759-65e7e157-769e-48c3-977d-83ea9361be1a.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      /* 编写样式 */
      body {
        padding: 0;
        margin: 0;
        background-image: url(../images/grid.png);
      }
      canvas {
        /* color: purple; */
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <!-- onload="draw()" -->
  <body>
    <!-- 编写canvas -->
    <canvas id="tutorial" width="150" height="150">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      draw();
    };

    function draw() {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        ctx.beginPath();
        ctx.moveTo(0, 100);
        ctx.lineTo(150, 100);
        ctx.strokeStyle = "purple";
        ctx.stroke();

        ctx.font = "40px serif";
        ctx.textAlign = "center";
        ctx.textBaseline = "middle";
        ctx.strokeText("Ay", 100, 100);
      }
    }
  </script>
</html>
```

![Snipaste_2023-09-09_12-16-21](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694232991981-acaa3b1a-46ae-48c8-a321-beb1ae359366.png)

## 绘制图片

![Snipaste_2023-09-09_12-15-00](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694232948794-8496979a-6e73-4d0f-a15e-141c3e82be7e.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      /* 编写样式 */
      body {
        padding: 0;
        margin: 0;
        background-image: url(../images/grid.png);
      }
      canvas {
        /* color: purple; */
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <!-- onload="draw()" -->
  <body>
    <!-- 编写canvas -->
    <canvas id="tutorial" width="150" height="150">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      draw();
    };

    function draw() {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        var image = new Image();
        // 只有一张图片，如果有多种图片怎么办？使用定时器：requestAnimationFrame
        image.onload = function () {
          // 1.绘制背景图片（放在最底层）
          ctx.drawImage(image, 0, 0, 150, 150);

          // 2.绘制路径（这个在最顶层）
          ctx.beginPath();
          ctx.moveTo(40, 100);
          ctx.lineTo(60, 60);
          ctx.lineTo(100, 50);
          ctx.lineTo(130, 20);
          ctx.stroke();
        };
        image.src = "../images/backdrop.png";
      }
    }
  </script>
</html>
```

![Snipaste_2023-09-09_12-17-36](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694233106048-b582ba8f-5191-497b-ac58-5890192c4131.png)

## 绘制状态

![Snipaste_2023-09-09_12-24-33](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694233482019-9bf34ee5-649b-4e18-8966-4ef2c70bf3db.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      /* 编写样式 */
      body {
        padding: 0;
        margin: 0;
        background-image: url(../images/grid.png);
      }
      canvas {
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <!-- onload="draw()" -->
  <body>
    <!-- 编写canvas -->
    <canvas id="tutorial" width="150" height="150">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      draw();
    };

    function draw() {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        ctx.save();

        ctx.fillStyle = "red";
        // 这还有其它绘图状态 .....
        ctx.fillRect(10, 10, 30, 15);
        ctx.save(); // red

        ctx.fillStyle = "green";
        ctx.fillRect(50, 10, 30, 15);
        ctx.save(); // green

        ctx.fillStyle = "blue";
        ctx.fillRect(90, 10, 30, 15);
        ctx.save(); // blue

        ctx.restore(); // blue
        ctx.fillRect(90, 40, 30, 80);

        ctx.restore(); // green
        ctx.fillRect(50, 40, 30, 80);

        ctx.restore(); // red

        ctx.fillRect(10, 40, 30, 80);
      }
    }
  </script>
```

![Snipaste_2023-09-09_12-25-18](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694233523661-1322f791-6561-4eda-8aac-c79c1a96a216.png)

## 变形

![Snipaste_2023-09-09_12-35-40](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694234145569-07c04c33-f222-4239-8417-60b356aa4b28.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        padding: 0;
        margin: 0;
        background-image: url(../images/grid.png);
      }
      canvas {
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <body>
    <canvas id="tutorial" width="150" height="150">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      draw();
    };

    function draw() {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        // 1.都是在坐标原点开始绘制
        // ctx.fillStyle = "green";
        // ctx.fillRect(0, 0, 40, 40);

        // ctx.fillStyle = "blue";
        // ctx.fillRect(0, 0, 40, 40);

        // 2.移动坐标原点
        ctx.save();
        ctx.translate(10, 10);
        ctx.fillStyle = "green";
        ctx.fillRect(0, 0, 40, 40);

        ctx.restore();
        ctx.translate(10, 10);
        ctx.fillStyle = "blue";
        ctx.fillRect(0, 0, 25, 25);
      }
    }
  </script>
</html>
```

![Snipaste_2023-09-09_12-38-02](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694234291225-76eba5f0-07d6-43ae-b45e-d6e0d6e60f62.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        padding: 0;
        margin: 0;
        background-image: url(../images/grid.png);
      }
      canvas {
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <body>
    <canvas id="tutorial" width="150" height="150">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      draw();
    };

    function draw() {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        // 1.旋转 （如何修改形变原点？）
        ctx.save();
        ctx.translate(100, 0); // 改变坐标点
        ctx.rotate((Math.PI / 180) * 45); // 改变坐标
        ctx.fillStyle = "green";
        ctx.fillRect(0, 0, 40, 40);

        // 2.旋转（修改形变原点）
        ctx.restore(); // 恢复状态
        ctx.translate(100, 100); // 改变坐标点
        ctx.rotate((Math.PI / 180) * 45);
        ctx.fillStyle = "red";
        ctx.fillRect(-20, -20, 40, 40);
      }
    }
  </script>
</html>
```

![Snipaste_2023-09-09_12-42-59](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694234598161-7b0c4974-0548-4e0d-970c-dc4b9524a3fd.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        padding: 0;
        margin: 0;
        background-image: url(../images/grid.png);
      }
      canvas {
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <body>
    <canvas id="tutorial" width="150" height="150">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      draw();
    };

    function draw() {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        // 1.缩放 （如何修改形变原点？）
        ctx.save();
        ctx.translate(20, 0);
        ctx.scale(2, 1);

        ctx.fillStyle = "green";
        ctx.fillRect(0, 0, 40, 40);
        ctx.restore();

        // 2.缩放修改形变原点
        ctx.save();
        ctx.translate(100, 100);
        ctx.fillStyle = "green";
        ctx.scale(0.5, 0.5);
        ctx.fillRect(-20, -20, 40, 40);
      }
    }
  </script>
</html>
```

![Snipaste_2023-09-09_12-43-10](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694234607189-6fb703e4-2ba9-43cd-adc5-c791782aaef6.png)

## 动画

模板

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        padding: 0;
        margin: 0;
        background-image: url(../../images/grid.png);
      }
      canvas {
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <body>
    <canvas id="tutorial" width="300" height="300">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
    <script>
      window.onload = function () {
        var canvas = document.getElementById("tutorial");
        if (canvas.getContext) {
          var ctx = canvas.getContext("2d");
        }
        requestAnimationFrame(draw);

        function draw() {
          ctx.clearRect(0, 0, 300, 300);
          ctx.save();

          ctx.restore();
          requestAnimationFrame(draw);
        }
      };
    </script>
  </body>
</html>
```

秒针动画

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1694238757152-c5003cb2-e56e-40e3-a12a-b325b022ecef.png" alt="Snipaste_2023-09-09_13-25-42" style="zoom:67%;" />

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        padding: 0;
        margin: 0;
        background-image: url(../images/grid.png);
      }
      canvas {
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <body>
    <canvas id="tutorial" width="200" height="200">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");
      }
      // 1.在浏览器下次重绘之前会回调给函数（一般1s会回调60次）
      requestAnimationFrame(draw);

      // 这方法一秒会执行 60 次左右（在浏览器下一次重绘之前会回调）
      function draw() {
        let second = new Date().getSeconds();
        // 第一步：用 clearRect(x,y, w,h)方法，清空 canvas 。
        ctx.clearRect(0, 0, 200, 200);
        // 第二步：保存 canvas 状态 。
        ctx.save();
        // 第三步：修改 canvas 状态 （样式、移动坐标、旋转等）。
        ctx.translate(100, 100);
        ctx.rotate(((Math.PI * 2) / 60) * second);
        ctx.strokeStyle = "red";
        ctx.lineWidth = 8;
        ctx.lineCap = "round";

        // 第四步：绘制秒针图形（即绘制动画中的一帧）。
        ctx.beginPath();
        ctx.moveTo(0, 0);
        ctx.lineTo(0, -80);
        ctx.stroke();
        // 第五步：恢复 canvas 状态 ，准备重绘下一帧。
        ctx.restore();

        // 2.在回调函数中再次调用 requestAnimationFrame
        requestAnimationFrame(draw);
      }
    };
  </script>
</html>
```

![screen1](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1694237520841-99db8672-471e-4f67-9804-0f8b64e44142.gif)

太阳系旋转动画

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1694238746406-04b3d081-04f6-4ff6-8295-5cfd6271224f.png" alt="Snipaste_2023-09-09_13-26-13" style="zoom: 67%;" />

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        padding: 0;
        margin: 0;
        background-image: url(../../images/grid.png);
      }
      canvas {
        background-color: rgba(255, 0, 0, 0.1);
      }
    </style>
  </head>
  <body>
    <canvas id="tutorial" width="300" height="300">
      该浏览器不支持Canvas，请更新你的浏览器
    </canvas>
  </body>

  <script>
    window.onload = function () {
      var canvas = document.getElementById("tutorial");
      if (canvas.getContext) {
        var ctx = canvas.getContext("2d");
      }
      var sum = new Image();
      var moon = new Image();
      var earth = new Image();
      sum.src = "../../images/canvas_sun.png";
      moon.src = "../../images/canvas_moon.png";
      earth.src = "../../images/canvas_earth.png";

      requestAnimationFrame(draw);
      function draw() {
        // 1.清除画布
        ctx.clearRect(0, 0, 300, 300);
        // 2.保存状态
        // ctx.save();
        // 3.绘制背景
        drawBg();
        // 4.绘制地球
        drawEarth();
        // 5.恢复状态
        // ctx.restore();
        requestAnimationFrame(draw);
      }

      function drawEarth() {
        ctx.save();
        let time = new Date();
        let second = time.getSeconds();
        let millSecond = time.getMilliseconds();
        ctx.translate(150, 150); // 1.移动坐标
        ctx.rotate(
          ((Math.PI * 2) / 60) * second +
            ((Math.PI * 2) / 60 / 1000) * millSecond
        );
        ctx.translate(105, 0); // 2.移动到 圆 的开始点
        ctx.drawImage(earth, -12, -12); // 24 * 24

        // 绘制月球
        drawMoon(second, millSecond);
        // 绘制蒙版
        drawMask();
        ctx.restore();
      }
      function drawBg() {
        // 4.开始绘制太阳背景
        ctx.save();
        ctx.strokeStyle = "rgba(0, 153, 255, 0.4)";
        ctx.drawImage(sum, 0, 0, 300, 300);
        ctx.beginPath();
        ctx.arc(150, 150, 105, 0, Math.PI * 2, false);
        ctx.stroke();
        ctx.restore();
      }
      function drawMoon(second, millSecond) {
        // 6.绘制月球（月球绕地球旋转一圈 27.3 天）
        ctx.save();
        // (10秒转圈)
        ctx.rotate(
          ((Math.PI * 2) / 10) * second +
            ((Math.PI * 2) / 10 / 1000) * millSecond
        );
        ctx.translate(0, 28);
        ctx.drawImage(moon, -3.5, -3.5); // 24 * 24
        ctx.restore();
      }
      function drawMask() {
        // 遮住半个地球(这个会绘制在顶层)
        ctx.fillStyle = "rgba(0,0,0, 0.4)";
        ctx.fillRect(0, -12, 40, 24); // 地球正上方
      }
    };
  </script>
</html>
```

![screen1](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1694237430656-d0e81905-5cc5-4e2c-9505-03688430de63.gif)

时钟动画

![Snipaste_2023-09-09_13-26-25](https://cdn.nlark.com/yuque/0/2023/png/1614731/1694238705110-4bd52b7a-773e-4046-8b1e-49731513f7b8.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        padding: 0;
        margin: 0;
        background-image: url(../../images/grid.png);
      }
      canvas {
        /* background-color: rgba(255, 0, 0, 0.1); */
      }
      .clock {
        width: 300px;
        height: 300px;
        margin: 10px;
        border-radius: 50px;
        background-color: black;
      }
    </style>
  </head>
  <body>
    <div class="clock">
      <canvas id="tutorial" width="300" height="300">
        该浏览器不支持Canvas，请更新你的浏览器
      </canvas>
    </div>
    <script>
      window.onload = function () {
        var canvas = document.getElementById("tutorial");
        if (canvas.getContext) {
          var ctx = canvas.getContext("2d");
        }
        requestAnimationFrame(draw);

        function draw() {
          ctx.clearRect(0, 0, 300, 300);

          ctx.save();
          ctx.translate(150, 150); // 移动坐标点（抽取）
          // 拿到时间
          var time = new Date();
          var hours = time.getHours();
          var min = time.getMinutes();
          var second = time.getSeconds();

          drawBg();
          drawText();
          drawHours(hours, min, second);
          drawMinute(hours, min, second);
          drawSecond(hours, min, second);
          drawCircle();
          drawHoursRule();
          drawMinuteRule();
          ctx.restore();

          requestAnimationFrame(draw);
        }

        function drawBg() {
          ctx.save();
          // ctx.translate(150, 150); // 移动坐标点（抽取）
          ctx.fillStyle = "white";
          ctx.beginPath();
          ctx.arc(0, 0, 130, 0, Math.PI * 2);
          ctx.fill();
          ctx.restore();
        }

        function drawText() {
          ctx.save();
          // ctx.translate(150, 150); //（抽取）
          ctx.font = "30px fangsong";
          ctx.textBaseline = "middle";
          ctx.textAlign = "center";
          //  x = Math.cos(  Math.PI * 2 / 12  * i  )  *  R
          //  y = Math.sin(  Math.PI * 2 / 12  * i  )  * R
          var timeNumbers = [3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 1, 2];
          for (var i = 0; i < 12; i++) {
            var x = Math.cos(((Math.PI * 2) / 12) * i) * 100;
            var y = Math.sin(((Math.PI * 2) / 12) * i) * 100;
            ctx.fillText(timeNumbers[i], x, y);
          }
          ctx.restore();
        }

        function drawHours(hours, min, second) {
          // 3.绘制时针
          ctx.save();
          ctx.lineWidth = 5;
          ctx.lineCap = "round";
          // ctx.translate(150, 150); //（抽取）
          // 1小时 1分 1 秒 的弧度
          // ((Math.PI * 2) / 12) * 1h => 分成 12 份
          // ((Math.PI * 2) / 12 / 60) * 1m =>分成 60份
          // ((Math.PI * 2) / 12 / 60 / 60) * 1s => 分成 60份
          ctx.rotate(
            ((Math.PI * 2) / 12) * hours +
              ((Math.PI * 2) / 12 / 60) * min +
              ((Math.PI * 2) / 12 / 60 / 60) * second
          );
          ctx.beginPath();
          ctx.moveTo(0, 0);
          ctx.lineTo(0, -50);
          ctx.stroke();
          ctx.restore();
        }

        function drawMinute(hours, min, second) {
          // 4.绘制分针
          ctx.save();
          ctx.lineWidth = 3;
          ctx.lineCap = "round";
          // ctx.translate(150, 150); //（抽取）
          // 1分 1 秒 的弧度

          // ((Math.PI * 2) / 60) * 1m =>分成60份
          // ((Math.PI * 2) / 60 / 60) * 1s => 分成 60份

          ctx.rotate(
            ((Math.PI * 2) / 60) * min + ((Math.PI * 2) / 60 / 60) * second
          );
          ctx.beginPath();
          ctx.moveTo(0, 0);
          ctx.lineTo(0, -70);
          ctx.stroke();
          ctx.restore();
        }

        function drawSecond(hours, min, second) {
          // 5.绘制秒针
          ctx.save();
          ctx.lineWidth = 2;
          ctx.lineCap = "round";
          ctx.strokeStyle = "red";
          // ctx.translate(150, 150); //（抽取）
          // 1 秒 的弧度
          // ((Math.PI * 2)  / 60) * 1s => 分成 60份
          ctx.rotate(((Math.PI * 2) / 60) * second);
          ctx.beginPath();
          ctx.moveTo(0, 0);
          ctx.lineTo(0, -90);
          ctx.stroke();
          ctx.restore();
        }

        function drawCircle() {
          // 6.绘制圆环
          ctx.save();
          // ctx.translate(150, 150);

          ctx.beginPath();
          ctx.arc(0, 0, 8, 0, Math.PI * 2);
          ctx.fill();

          ctx.beginPath();

          ctx.fillStyle = "gray";
          ctx.arc(0, 0, 5, 0, 2 * Math.PI);
          ctx.fill();
          ctx.restore();
        }

        function drawHoursRule() {
          // 7.绘制时刻度
          ctx.save();
          ctx.lineWidth = 3;
          // ctx.translate(150, 150);
          // 时的刻度
          for (var j = 0; j < 12; j++) {
            ctx.rotate(((Math.PI * 2) / 12) * 1);
            ctx.beginPath();
            ctx.moveTo(0, -130);
            ctx.lineTo(0, -122);
            ctx.stroke();
          }
          ctx.restore();
        }

        function drawMinuteRule() {
          // 8.绘制分刻度
          ctx.save();
          ctx.lineWidth = 1;
          // ctx.translate(150, 150);
          // 时的刻度
          for (var j = 0; j < 60; j++) {
            ctx.rotate(((Math.PI * 2) / 60) * 1);
            ctx.beginPath();
            ctx.moveTo(0, -130);
            ctx.lineTo(0, -125);
            ctx.stroke();
          }
          ctx.restore();
        }
      };
    </script>
  </body>
</html>
```

![screen1](https://cdn.nlark.com/yuque/0/2023/gif/1614731/1694238687452-9f015b53-b359-46be-bfd7-03c665887aae.gif)

## 图片压缩

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>canvas-图片压缩</title>
  </head>
  <body>
    <input type="file" id="upload" />
    <script>
      const ACCEPT = ["image/jpg", "image/png"];
      const MAXSIZE = 1024 * 1024;
      const upload = document.getElementById("upload");
      const uploadToServer = (compressImage) => {
        console.log("upload to server...", compressImage);
      };
      // 转换base64
      const convertImageToBase64 = (file, callback) => {
        let reader = new FileReader();
        reader.addEventListener("load", (e) => {
          const base64Image = e.target.result;
          callback && callback(base64Image);
          reader = null;
        });
        reader.readAsDataURL(file);
      };
      // 执行压缩
      const compress = (base64Image, callback) => {
        let maxW = 600;
        let maxH = 600;
        const image = new Image();
        image.addEventListener("load", () => {
          let ratio; // 图片压缩比
          let needCompress = false;
          if (maxW < image.naturalWidth) {
            needCompress = true;
            ratio = image.naturalWidth / maxW;
            maxH = image.naturalHeight / ratio;
          }
          if (maxH < image.naturalHeight) {
            needCompress = true;
            ratio = image.naturalHeight / maxH;
            maxW = image.naturalWidth / ratio;
          }
          if (!needCompress) {
            maxW = image.naturalWidth;
            maxH = image.naturalHeight;
          }

          const canvas = document.createElement("canvas");
          canvas.setAttribute("id", "__compress__");
          canvas.width = maxW;
          canvas.height = maxH;
          canvas.style.visibility = "hidden";
          document.body.appendChild(canvas);

          const ctx = canvas.getContext("2d");
          ctx.clearRect(0, 0, maxW, maxH);
          ctx.drawImage(image, 0, 0, maxW, maxH);
          const compressImage = canvas.toDataURL("image/png", 0.9);
          callback && callback(compressImage);
          const _image = new Image();
          _image.src = compressImage;
          document.body.appendChild(_image);
          canvas.remove();
          console.log("压缩比", image.src.length / _image.src.length);
        });
        image.src = base64Image;
        document.body.appendChild(image);
      };
      upload.addEventListener("change", (e) => {
        const [file] = e.target.files;
        if (!file) {
          return false;
        }
        const { type: fileType, size: fileSize } = file;
        // 图片类型检查
        if (ACCEPT.indexOf(fileType) < 0) {
          alert(`不支持 ${fileType} 这种类型！`);
          upload.value = "";
          return false;
        }
        // 图片容量检查
        if (fileSize > MAXSIZE) {
          alert(`文件超出1MB！`);
          upload.value = "";
          return false;
        }
        // 图片压缩
        convertImageToBase64(file, (base64Image) => {
          compress(base64Image, uploadToServer);
        });
      });
    </script>
  </body>
</html>
```



















































































































































































































































