# SVG 基础

## 通用样式
* fill:  填充色
* fill-opacity: 填充色透明度
* stroke: 定义图形、文本等的边线颜色
* stroke-width: 定义图形、文字边线的宽度 
* stroke-opacity: 定义图形、文字边线的透明度
* stroke-linecap：用来定义开放式路径的端点的样式（`butt`、`round`、`square`）
* stroke-dasharray：用来创建虚线（如 `stroke-dasharray="5,5"`）
* fill-rule

## 形状

### 矩形（`<rect>`）
* x: 元素距离左边的距离
* y: 元素距离上边的距离
* width: 元素的宽度
* height: 元素的高度
* rx: 矩形 X 轴方向的半径
* rx: 矩形 Y 轴方向的半径

### 圆形 （`<circle>`）
* cx: 圆心距离 Y 轴的距离
* cy: 圆心距离 X 轴的距离
* r: 圆的半径

### 椭圆（`<ellipse>`）
* cx: 圆心距离 Y 轴的距离
* cy: 圆心距离 X 轴的距离
* rx: 圆的 X 轴的半径
* ry: 圆的 Y 轴的半径

### 直线（`<line>`）
* x1: X 轴方向的起点
* y1: Y 轴方向的起点
* x2: X 轴方向的终点
* y2: Y 轴方向的终点

### 折线 （`<polyline>`）
* points 属性里定义了各个点的坐标，x 和 y 坐标之间用逗号分别，多个坐标之间用空格分割
    * 例如 `<polyline points="3,3 10,40 50,100 20,150"/>`

### 多边形 （`<polygen>`）
* points 属性里定义了各个点的坐标，x 和 y 坐标之间用逗号分别，多个坐标之间用空格分割，并组成闭合的环形

### 路径 （`<path>`）
* d 用于定义一系列指令，例如 `<path d="M150 0 L75 200 L225 200 Z" />`

|指令	| 参数 | 名称 | 描述 |
| --- | --- | --- | --- |
|M	|x,y	|moveto |移动到	移动虚拟画笔到指定的（x,y）坐标，仅移动不绘制|
|m	|x,y	|moveto	|同M，但使用相对坐标|
|L	|x,y	|lineto |连直线到	从当前画笔所在位置绘制一条直线到指定的（x,y）坐标|
|l	|x,y	|lineto	|同L，但使用相对坐标|
|H	|x	|horizontal lineto |水平连线到	绘制一条水平直线到参数指定的x坐标点，y坐标为画笔的y坐标|
|h	|x	|horizontal lineto	|同H，但使用相对坐标|
|V	|y	|vertical lineto |垂直连线到	从当前位置绘制一条垂直直线到参数指定的y坐标|
|v	|y	|vertical lineto	|同V，但使用相对坐标|
|C	|x1,y1 x2,y2 x,y	|curveto 三次方贝塞尔曲线	|从当前画笔位置绘制一条三次贝兹曲线到参数（x,y）指定的坐标。x1，y1和x2,y2是曲线的开始和结束控制点，用于控制曲线的弧度|
|c	|x1,y1 x2,y2 x,y	|curveto	|同C，但使用相对坐标|
|S	|x2,y2 x,y	|shorthand |平滑三次方贝塞尔曲线	从当前画笔位置绘制一条三次贝塞尔曲线到参数（x,y）指定的坐标。x2,y2是结束控制点。开始控制点和前一条曲线的结束控制点相同|
|s	|x2,y2 x,y	|shorthand |平滑三次方贝塞尔曲线	同S，但使用相对坐标|
|Q	|x1,y1 x,y	|二次方贝塞尔曲线	|从当前画笔位置绘制一条二次方贝塞尔曲线到参数（x,y）指定的坐标。x1,y1是控制点，用于控制曲线的弧度|
|q	|x1,y1 x,y	|二次方贝塞尔曲线	|同Q，但使用相对坐标|
|T	|x,y	|平滑的二次贝塞尔曲线	|从当前画笔位置绘制一条二次贝塞尔曲线到参数（x,y）指定的坐标。控制点被假定为最后一次使用的控制点|
|t	|x,y	|平滑的二次贝塞尔曲线	|同T，但使用相对坐标|
|A	|rx,ry x-axis-rotation large-arc-flag,sweepflag x,y	|椭圆弧线	|从当前画笔位置开始绘制一条椭圆弧线到（x,y）指定的坐标。rx和ry分别为椭圆弧线水平和垂直方向上的半径。x-axis-rotation指定弧线绕x轴旋转的度数。它只在rx和ry的值不相同是有效果。large-arc-flag是大弧标志位，取值0或1，用于决定绘制大弧还是小弧。sweep-flag用于决定弧线绘制的方向|
|a	|rx,ry x-axis-rotation large-arc-flag,sweepflag x,y	|椭圆弧线	|同A，但使用相对坐标|
|Z	|无	|闭合路径	|从结束点绘制一条直线到开始点，闭合路径|
|z	|无	|闭合路径	|同Z|


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>SVG 学习</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
    </style>
</head>
<body>
    <svg width="400" height="400" style="border: 1px solid #999;">
        <rect x="20" y="40" width="200" height="200" rx="20" ry="30" style="fill: #009;stroke: #f00; stroke-width: 2; fill-opacity: 0.5; stroke-opacity: 5.2"/>
        <circle cx="290" cy="60" r="40" style="fill: #0f0; fill-opacity: .4;"/>
        <ellipse cx="300" cy="150" rx="60" ry="40" style="fill: #900; stroke: #999; stroke-width: 3; stroke-opacity: .5;"/>
        <line x1="240" y1="220" x2="395" y2="220" style="stroke: #333; stroke-width: 2;"/>
        <polyline points="3,3 10,40 50,100 20,150 20,180 50,180 50,210 80,210 110,210" style="fill:none; stroke:#099; stroke-width: 1"/>
        <polygon points="110, 20 30, 40 80, 50" style="fill: #934;"/>
        <polygon points="100,10 40,198 190,78 10,78 160,198" style="fill:lime;stroke:purple;stroke-width:5;fill-rule:evenodd;" />
        <path d="M 175 200 l 150 0" stroke="green" stroke-width="3" fill="none" />
        <path d="M 100 350 q 150 -300 300 0" stroke="blue" stroke-width="5" fill="none" />
    </svg>
</body>
</html>
```
