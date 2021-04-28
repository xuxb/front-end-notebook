---
title: SVG 基础元素
categories: SVG
tags: [SVG]
date: 2021-03-24
---

# SVG 基础元素

## SVG 元素通用属性
* transform 对基础图形进行变换处理（与 CSS3 中的 transform 属性有点不一致）
    * 平移 `translate(50,30)` 
    * 缩放 `scale(2)` 
    * 旋转 `rotate(30)` 
    * 翻转 `skewX()`、`skewY(10)`
* fill：填充色
* stroke：定义图形、文本等的边线颜色

说明：支持 hover 伪类、id、class 等

## SVG 元素通用 CSS 样式
* display: 控制元素是否可见（`inline`、`none`，所有元素的初始值都为 `inline`）
* fill:  填充色
* fill-opacity: 填充色透明度
* stroke: 定义图形、文本等的边线颜色
* stroke-width: 定义图形、文字边线的宽度 
* stroke-opacity: 定义图形、文字边线的透明度
* stroke-linecap：用来定义开放式路径的端点的样式（`butt`、`round`、`square`）
* stroke-linejoin：用来控制两个线段之间的联合是如何绘制
* stroke-dasharray：用来创建虚线（如 `stroke-dasharray="5,5"`）
* fill-rule：判断路径的哪一侧在路径所构成的形状的内部，从而判断fill属性如何给这个形状上色

![stroke-linecap 属性](https://mdn.mozillademos.org/files/730/SVG_Stroke_Linecap_Example.png)
![stroke-linejoin 属性](https://mdn.mozillademos.org/files/731/SVG_Stroke_Linejoin_Example.png)

## 文本
### SVG 文本 （`<text>`）
说明：用来定义文字文本，例如 `<text>测试文字</text>`

支持的样式：`font-family`，`font-style`，`font-weight`，`font-variant`，`font-stretch`，`font-size`，`font-size-adjust`，`kerning`，`letter-spacing`，`word-spacing` 和 `text-decoration`

### 内联文本  （`<tspan>`）
说明：嵌套在 `<text>` 标签中，与 `<span>` 标签类似，用于定义一组文本的样式

### 文本路径  （`<textPath>`）
说明：嵌套在 `<text>` 标签中，使文字按照指定路径排列，放在 `<text>` 标记内部引用预定义的 `<path>`，引用时，使用 `xlink:href` 属性指明需要引用的路径的 ID

### 链接 （`<a>`）
说明：创建一个 SVG 元素链接

* xlink:show
* xlink:actuate
* xlink:href
* target


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
    <svg width="800" height="400" style="border: 1px solid #aaa;">
        <defs>
            <path id="MyPath" d="M 100 200 
                     C 200 100 300   0 400 100
                     C 500 200 600 300 700 200
                     C 800 100 900 100 900 100" />
        </defs>
        <a xlink:href="https://www.baidu.com" target="_blank">
            <text x="100" y="15" style="fill: #009; cursor: pointer;"> 打开百度</text>
        </a>
        <text x="0" y="15" style="fill: red;" transform="rotate(30 20, 30)">
             Just test SVG! <tspan style="fill: #0f0; font-size: 26px; font-style: italic;">中国人民银行</tspan> nothing nothing nothing
        </text>
        <text>
            <textPath xlink:href="#MyPath">
                SVG 基本功能测试基本功能测试基本功能测试基本功能测试基本功能测试
            </textPath>
        </text>
    </svg>
</body>
</html>
```


## 基本图形

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

## 结构元素
### 分组 （`<g>`）
说明：`<g>` 用于分组，它能把多个元素放在一组里，对 `<g>` 标记的样式和渲染会作用到这个分组内的所有元素上。组内的所有元素都会继承 `<g>` 标记上的所有属性。用 `<g>` 定义的分组还可以使用 `<use>` 进行复制使用。

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
    <svg width="400" height="300" style="border: 1px solid #999;">
        <g style="fill: red; stroke: #090; stroke-width: 2;">
            <circle r="40" cx="180" cy="140"></circle>
            <rect x="30" y="40" width="100" height="50"></rect>
            <rect x="230" y="40" width="100" height="50"></rect>
        </g>
    </svg>
</body>
</html>
```

### 复制 （`<use>`）
说明：`<use>` 能从 SVG 文档内部取出一个节点，克隆它，并把它输出到别处。子元素能继承来自 `<use>` 样式

```html
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
    <svg width="400" height="300" style="border: 1px solid #999;">
        <defs>
            <g id="shape" style="fill: inherit; stroke: #090; stroke-width: 2;">
                <circle r="40" cx="180" cy="140"></circle>
                <rect x="30" y="40" width="100" height="50"></rect>
                <rect x="230" y="40" width="100" height="50"></rect>
            </g>
        </defs>

        <text y="15">图形1</text>
        <use x="20" y="20" xlink:href="#shape" style="fill: #009;"></use>
    </svg>
</body>

</html>
```

### 模板（<symbol>）
`<symbol>` 的作用是定义一个图像模板，使用 `<use>` 标记实例化它，然后在 SVG 文档中反复使用。`<symbol>` 本身不会输出任何图像，只有使用 `<use>` 实例化后才会显示。

```html
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
    <svg width="400" height="300" style="border: 1px solid #999;">
        <!-- symbol definition  NEVER draw -->
        <symbol id="sym01" viewBox="0 0 150 110">
            <circle cx="50" cy="50" r="40" stroke-width="8" stroke="red" fill="red" />
            <circle cx="90" cy="60" r="40" stroke-width="8" stroke="green" fill="white" />
        </symbol>
    
        <!-- actual drawing by "use" element -->
        <use xlink:href="#sym01" x="0" y="0" width="100" height="50" />
        <use xlink:href="#sym01" x="0" y="50" width="75" height="38" />
        <use xlink:href="#sym01" x="0" y="100" width="50" height="25" />
    </svg>
</body>

</html>
```