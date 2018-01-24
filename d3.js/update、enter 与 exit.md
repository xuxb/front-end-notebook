# update、enter 与 exit

作用：处理选择集和数据集关系不确定的情况

> 当有通过数据更新视图时，正确的顺序应该是 `update()` 、`enter()`、`exit()`

`selection.data()` 操作返回的是三个 Virtual selection，这三个 Virtual selection 是 enter、 update 以及 exit。

* `update` 表示在 DOM 中即将被更新的选择集，update 部分的处理办法一般是：**更新属性值**，如 `let updateSelection = svg.selectAll('circle').data(dataSet)`
* `enter` 在 DOM 中即将被添加的选择集，enter 部分的处理办法一般是：**添加元素后，赋予属性值**，如 `let enterSelection = svg.selectAll('circle').data(dataSet).enter()`
* `exit` 在 DOM 中即将被删除的选择集，exit 部分的处理办法一般是：**修改属性值，删除元素**，如 `let exitSelection = svg.selectAll('circle').data(dataSet).exit()`

对于数组中的数据元素，如果缺少与之对应的 DOM 元素，那么就会有一个占位符来顶替，而 `enter()` 方法返回的就是这些占位符集合的引用。
这个引用后只能链接 `append()`，`insert()` 以及 `select()` 操作符，通过他们来操作该引用所指向的集合。


### `data()` 数据绑定（`selection.selectAll().data().enter().append()`）
* `let selectoin = svg.selectAll('circle')` 因为 SVG 容器是空的，返回一个新的空选择 `selection`。 
* `enterSelectoin = selection.data(dataSet)` 将这个选择连接到一组数据，产生三个新的选择，代表三种可能的状态：`enter`、`update` 和 `exit`。由于选择是空的，所以`update` 和 `exit` 的 `selection` 是空的，而 `enter` 的 `selection` 包含每个新基准的占位符。
* `selection.data(dataSet)` 返回更新选择，`selection.enter()` 返回 `enter` 选择，而进入和退出选择被挂起 。
* `enterSelectoin.append()` 将缺失的元素添加到 SVG 容器中，这会为每个数据附加一个新的 `circle` 到 SVG 容器。

> 当把数据分配到一个元素上时，这个数据被存储到该元素的 `_data_` 属性中, 数据可以通过 `_data_` 属性被再选。这也就是我们所说的数据绑定到 DOM 元素

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>D3.js - enter、update、exit</title>
    <style>
        .container text {
            font-size: 30px;
            fill: #090;
        }
    </style>
</head>

<body>
    <script src="http://d3js.org/d3.v3.js" charset="utf-8"></script>
    <script>
        let w = 500;
        let h = 400;
        let padding = {
            top: 20,
            right: 20,
            bottom: 30,
            left: 40
        };
        let dataSet = 'abcdefghijklmn'.split('');

        let svg = d3.select('body')
            .append('svg')
            .attr('width', w)
            .attr('height', h);

        let container = svg.append('g')
            .attr('class', 'container')
            .attr('transform', `translate(${padding.left}, ${padding.top})`)
            .attr('width', w - padding.left - padding.right)
            .attr('height', h - padding.top - padding.bottom);

        function update(dataSet) {
            let texts = container.selectAll('text').data(dataSet);

            console.log(dataSet);

            // update 部分
            texts.attr('x', (data, i) => i * 30)
                .attr('y', 120)
                .style('fill', '#f00')
                .text((data) => { console.log('update ----> ' + data); return data; });

            // enter 部分
            texts.enter()
                .append('text')
                .attr('x', (data, i) => i * 30)
                .attr('y', 120)
                .style('fill', '#0f0')
                .text((data) => { console.log('enter ----> ' + data); return data; });

            // exit 部分
            texts.exit()
                .transition()
                .duration(200)
                .style('opacity', (data) => { console.log('exit ----> ' + data); return 0; })
                .style('transform', 'scale(0)')
                .remove();
        }

        window.setInterval(() => {
            let data = ['A', 'B', ...d3.shuffle(dataSet).slice(0, Math.ceil(Math.random() * 10))];
            update(data);
        }, 1000);
    </script>
</body>
</html>
```