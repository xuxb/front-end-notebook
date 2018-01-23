# viewBox 与 preserveAspectRatio

### 视窗（viewport） 
视窗是指一块 SVG 可见的区域

如 `<svg width="500" height="300"></svg>` 定义了一个视区，宽 500 单位，高 300 单位（并没有指定是像素，也可以指定为 `em`、`rem` 等）

当 `width` 、 `height` 如果是纯数字，使用的就是 “像素” 作为单位的，SVG 中的其他子元素也是相同的原理

### 视图盒子（viewBox）
viewBox 属性相当于给 svg 图像设置了一个选区，然后将这个选区填充到整个 svg 画布中，最后的看到的结果就是选区中的图像缩放后的结果。

这个属性有四个值，分别为坐标系统中的x值，y值，宽度，高度，属性值之间用空格或者是逗号合开。

实际上这 4 个值唯一的确定了一个矩形和矩形的位置，x值和y值是矩形左上角顶点的坐标，宽和高分别是矩形的宽和高，单位为像素，这个矩形就是上述的选区。

```html
<svg width="400" height="300" viewBox="0,0,40,30" style="border:1px solid #cd0000;">
    <rect x="10" y="5" width="20" height="15" fill="#cd0000"/>
</svg>
```

### preserveAspectRatio
`preserveAspectRatio="xMidYMid meet"`

* xMidYMid：表示 viewBox 如何与 viewport 对齐
* meet：表示如何维持高宽比

| 值 | 含义 |
| --- | --- |
|xMin |	viewport 和 viewBox 左边对齐 |
|xMid |	viewport 和 viewBox  x 轴中心对齐 |
|xMax |	viewport 和 viewBox 右边对齐 |
|YMin |	viewport 和 viewBox 上边缘对齐 |
|YMid |	viewport 和 viewBox  y 轴中心点对齐 |
|YMax |	viewport 和 viewBox 下边缘对齐 |

| 值 | 含义 |
| --- | --- |
|meet | 保持纵横比缩放 viewBox 适应 viewport |
|slice | 保持纵横比同时比例小的方向放大填满 viewport |
|none |	 扭曲纵横比以充分适应 viewport |

	
	
	
