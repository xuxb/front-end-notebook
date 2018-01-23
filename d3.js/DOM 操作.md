
此时 SVG 里没有 rect 元素，即元素数量为 0。
有一数组 dataset，将数组与元素数量为 0 的选择集绑定后，选择其 Enter 部分（请仔细看上图），
然后添加（append）元素，也就是添加足够的元素，使得每一个数据都有元素与之对应。

任何数据集中的值都不太可能完全对应于可视化中使用的像素测量值。比例提供了一种方便的方法，将这些数据值映射为可用于可视化目的的新值。

d3.select()
d3.selectAll()

node.datum()
node.data()

node.text()
node.attr()

currentNode.append(nodeName)
parentNode.insert(nodeName, node)
currentNode.remove()

d3.scale.linear()
    .domain([min, max])
    .range([min, max])

update()
enter()
exit()