---
title: EventEmitter 实现原理
categories: JavaScript 编程题
tags: [编程题]
date: 2021-03-12 15:32:28
---

# EventEmitter 实现原理

```js
class EventEmitter {
  constructor() {
    this.events = Object.create(null)
  }

  // 监听事件
  on(eventName, fn) {
    if (!this.events[eventName]) {
      this.events[eventName] = []
    }

    this.events[eventName].push(fn)
  }

  // 触发事件
  emit(eventName, ...args) {
    if (!this.events[eventName] || !Array.isArray(this.events[eventName])) return

    this.events[eventName].map(fn => {
      fn.apply(this, args)
    })
  }

  // 注销事件
  off(eventName, fn) {
    if (fn === void 0) {
      this.events[eventName] = null
      return
    }

    if (this.events[eventName].length) {
      const index = this.events[eventName].findIndex(item => item === fn)

      if (index > -1) {
        this.events[eventName].splice(index, 1)
      }
    }
  }

  // 注册单次执行的事件
  once(eventName, fn) {
    const only = (...args) => {
      fn.apply(this, args)
      this.off(eventName)
    }

    this.on(eventName, only)
  }
}


// test on/emit function
const eventBus = new EventEmitter()
eventBus.on('click', () => console.log(1))
eventBus.emit('click')
// --- output ---
// 1

// test on/emit function
const cb = function (...args) {
  console.log('2: ', args)
}
eventBus.on('input', () => console.log(1))
eventBus.on('input', cb)
eventBus.emit('input', {name: 'jack'}, {age: 18})
// --- output ---
// 1 ​​​​​
// 2:  [ { name: 'jack' }, { age: 18 } ] 

// test on/off/emit function
eventBus.off('input', cb)
eventBus.emit('input', {name: 'jack'}, {age: 18})
// --- output ---
// 1 ​​​​​

// test on/off/emit function
eventBus.off('input')
eventBus.emit('input', {name: 'jack'}, {age: 18})
// --- output ---

// test once/emit function
eventBus.once('change', cb)
eventBus.emit('change', {name: 'jack'})
eventBus.emit('change', {name: 'jack'})
// --- output ---
// [ { name: 'jack' } ] 
```