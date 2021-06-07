---
title: requestAnimationFrame 与 requestIdleCallback
categories: HTML
tags: [HTML]
date: 2021-05-24 16:32:28
---

# requestAnimationFrame 与 requestIdleCallback

### 1、requestAnimationFrame

`requestAnimationFrame()` 方法告诉浏览器您希望执行动画，并请求浏览器调用指定的函数在下一次重绘之前更新动画。该方法使用一个回调函数作为参数，这个回调函数会在浏览器重绘之前调用。

> CSS 变换和动画的优势在于浏览器知道动画什么时候开始，因此会计算出正确的循环间隔，在恰当的时候刷新 UI。而对于 JavaScript 动画，浏览器无从知晓什么时候开始。

`requestAnimationFrame` 会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率
在隐藏或不可见的元素中，`requestAnimationFrame` 将不会进行重绘或回流，这当然就意味着更少的 CPU、GPU 和内存使用量。

> 使用 `setTimeout` 或 `setInterval` 定义动画会有一些问题：如果队列前面已经加入了其他任务，那动画代码就要等前面的任务完成后再执行。简言之，以毫秒表示的延迟时间并不代表到时候一定会执行动画代码，而仅代表到时候会把代码添加到任务队列中。如果 UI 线程繁忙，比如忙于处理用户操作，那么即使把代码加入队列也不会立即执行。

`requestAnimationFrame` 是由浏览器专门为动画提供的API，在运行时浏览器会自动优化方法的调用，并且如果页面不是激活状态下的话，动画会自动暂停，有效节省了 CPU 开销，采用系统时间间隔，保持最佳绘制效率，不会因为间隔时间过短，造成过度绘制，增加开销；也不会因为间隔时间太长，使用动画卡顿不流畅，让各种网页动画效果能够有一个统一的刷新机制，从而节省系统资源，提高系统性能，改善视觉效果


```js
function updateProgress(){
    var div = document.getElementById("status");
    div.style.width = (parseInt(div.style.width, 10) + 5) + "%";
    if (div.style.left != "100%"){
        requestAnimationFrame(updateProgress);
    }
}
requestAnimationFrame(updateProgress); 
```

### 2、requestIdleCallback

`window.requestIdleCallback(fn)` 会在浏览器空闲时期依次调用函数，这就可以让开发者在主事件循环中执行后台或低优先级的任务，而且不会对像动画和用户交互这样延迟触发而且关键的事件产生影响。函数一般会按先进先调用的顺序执行，除非函数在浏览器调用它之前就到了它的超时时间。