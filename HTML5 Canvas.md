# HTML5 Canvas

[Canvas - Web API 接口参考 | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API)

[Canvas 教程 - Web API 接口参考 | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial)

### canvas元素

- 只有两个属性`width`, `height`

  ```html
  <canvas width="150px" height="150px">替换内容</canvas>
  ```

  

- 当没有设置宽度和高度的时候，canvas 会初始化宽度为 300 像素和高度为 150 像素。

- `canvas`元素创造了一个画布，它公开了一个或多个**rendering context(渲染上下文)**，用来绘制和处理要展示的内容。

  1. 首先脚本需要找到渲染上下文

  ```javascript
  var canvas = document.getElementById('vanvas');
  var context = canvas.getContext('2d'); //获得渲染上下文和它的绘画功能
  ```

  2. 在它的上面绘制内容
  
  ```javascript
  // Blue rectangle
  context.fillStyle = "blue";
  context.fillRect(10,40,30,70);
  ```

### 绘制图形

- **线段**  

  - 绘制线段的基本步骤

  ```javascript
  // 1. 新建一个路径
  context.beginPath();
  // 2. 指定路径的起始坐标
  context.moveTo(x, y);
  // 3. 指定路径的终点坐标
  context.lineTo(x, y);
  // 4. 绘制直线
  //context.strokeStyle = "red"; //color
  //context.lineWidth = 5; //thickness
  context.stroke();
  ```

  - `lineCap`属性

    - 指定如何绘制每一条线段末端的属性

    - 有 3 个可能的值，分别是：`butt`, `round` and `square`。默认值是 `butt`。

      ```javascript
      ctx.lineCap = "butt";
      ctx.lineCap = "round";
      ctx.lineCap = "square";
      ```

  - `lineJoin`属性
    - 定义两条线段相连时，相连处的连接方式
    - 有 3 个可能的值，分别是：`round`, `bevel` and `miter`。默认值是 `miter`。
    - 如果 2 个相连部分在同一方向，那么 lineJoin 不会产生任何效果

