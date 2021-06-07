---
title: React 中 ref 的使用
categories: React
tags: [React]
date: 2021-05-26 16:32:38
---

# React 中 ref 的使用

**`ref` 与 `Refs` 是两个不同的概念**
* `ref` 是一个特殊的属性，可作用于 `DOM 元素`或 `React 元素`上，用于接收 HTML 元素或组件实例，作为其 current 属性
* `Refs` 是 React 提供的可用特定 API 创建的一个对象，可以用 `React.creatRef()` 、`React.useRef()` 创建，生成的对象类似于 `{ current: null }`，用于赋值给 `DOM 元素` 或 `React 元素` 上

**适合使用 refs 的几种情况**
* 管理焦点，文本选择或媒体播放
* 触发强制动画
* 集成第三方 DOM 库

> React 为提供了直接访问 *DOM元素* 和 *React 元素*的方式，就是通过 Refs。使用的方式很简单，就是为想要访问的元素上添加 `ref` 属性，将 `Refs` 对象附加到 `ref` 属性上，那么此时 `Refs` 对象的 `current` 属性就不再是空，而是对应的 *DOM元素* 或 *React 元素实例*

**React.createRef()**
使用 `React.createRef()` 的方式可以创建一个 *Ref 对象*，可通过附加到 ref 属性上访问一个 *原生DOM元素* 或者 *class 组件*。这种方式可以在*函数组件*和*class 组件*中使用。

**React.useRef(initialValue)**
`React.useRef()` 返回一个可变的 *Ref 对象*，其 `.current` 属性被初始化为传入的参数 `initialValue`。返回的 `Refs` 对象在组件的整个生命周期内保持不变。这种方式只能在**函数组件**中使用。

> ref 属性只能被添加到原生 DOM元素 或者 React的 class 组件上。不能在直接函数组件 上使用 ref 属性，因为函数组件没有实例。若想在函数组件上使用 ref 属性，可以通过 React.forwardRef 将 Ref 转发到函数组件内部

当 ref 属性接收 Ref 对象时，会将其对应的 DOM元素 或者 class组件实例 直接赋值给 Ref 对象中的 current 属性上。而当 ref 属性接收一个回调函数时，会将其对应的 DOM元素 或 class组件实例作为回调函数的参数调用回调函数。

### 1. React.createRef()

`React.createRef` 主要用在 class 组件中，用于创建 Refs 对象，用来保存和引用一些值，并且修改这个属性不会触发组件更新。

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  componentDidMount() {
    this.myRef.current.focus();
  }
  render() {
    return <input ref={this.myRef} />;
  }
}
```

### 2. React.useRef()

`React.useRef()` 作为 react hooks 的一种，主要用在函数组件中。

`React.useRef()` 返回一个可变的 *Ref 对象*，其 `.current` 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的**整个生命周期内保持不变**，所以可用于保存 **React 组件**、**DOM 元素**或**普通 js 对象**的值

默认情况下，不能在函数组件上使用 ref 属性，因为函数组件没有示例，所以以下代码不能正常运行

> 函数组件中 state 变化时，会重新创建、初始化所有的变量和表达式，故不能使用 `React.createRef()`

```js
// 不能正常运行
function MyFunctionComponent() {
  return <input />;
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  render() {
    return (
      <MyFunctionComponent ref={this.textInput} />
    );
  }
}
```

如果要在函数组件中使用 ref，可以使用 React.forwardRef（可与 useImperativeHandle 结合使用）

```js
// 可以正常运行
function MyFunctionComponent(props, ref) {
  React.useImperativeHandle(ref, () => ({
    onCheck,
  }));

  const onCheck = () => {};

  return <input />;
}

const NewMyFunctionComponent = React.forwardRef(MyFunctionComponent)

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  render() {
    return (
      <MyFunctionComponent ref={this.textInput} />
    );
  }
}
```

总之，在函数组件内部使用 ref 属性，只能指向一个 DOM 元素或 class 组件

```js
function CustomTextInput(props) {
  // 这里必须声明 textInput，这样 ref 才可以引用它
  const textInput = useRef(null);

  function handleClick() {
    textInput.current.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}
```



### 3. React.forwardRef()

`React.forwardRef` 主要用于穿过父元素直接获取子元素的 ref。HOC（higher-order component）在 ref 使用上会存在问题，HOC 的 ref 是无法通过 props 进行传递的，因此无法直接获取被包裹组件（WrappedComponent），所以需要进行中转。

```js
function HOCProps(WrappedComponent) {
  class HOCComponent extends React.Component {
    constructor(props) {
      super(props);
      this.setWrappedInstance = this.setWrappedInstance.bind(this);
    }
    
    getWrappedInstance() {
      return this.wrappedInstance;
    }

    // 实现 ref 的访问
    setWrappedInstance(ref) {
      this.wrappedInstance = ref;
    }
    
    render() {
      return <WrappedComponent ref={this.setWrappedInstance} {...this.props} />;
    }
  }

  return HOCComponent;
}

const App = HOCProps(Wrap);

<App ref={(dom) => {
  console.log(dom); // 只能获取到 HOCComponent
  console.log(dom.getWrappedInstance()); // 通过中转后可以获取到 WrappedComponent
}} />
```

利用 forwardRef 能直接穿透 HOCComponent 获取到 WrappedComponent

> React.forwardRef 的原理其实非常简单，forwardRef 会生成 react 内部一种较为特殊的 Component。当进行创建更新操作时，会将 forwardRef 组件上的 props 与 ref 直接传递给提前注入的 render 函数，来生成 children。

```js
function HOCProps(WrappedComponent) {
  class HOCComponent extends React.Component {
    render() {
      const { forwardedRef, ...rest } = this.props;
      return <WrappedComponent ref={forwardedRef} {...rest} />;
    }
  }

  return React.forwardRef((props, ref) => {
    return <HOCComponent forwardedRef={ref} {...props}  />;
  });
}

const App = HOCProps(Wrap);

<App ref={(dom) => {
  // 可以直接获取 WrappedComponent
  console.log(dom);
}} />

```

### 4. React.useImperativeHandle()

`useImperativeHandle(ref, createHandle, [deps])`

`useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。`useImperativeHandle` 应当与 `forwardRef` 一起使用.

```js
function FancyInput(props, ref) {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));

  return <input ref={inputRef} />;
}
FancyInput = forwardRef(FancyInput);
```

在本例中，渲染 `<FancyInput ref={inputRef} />` 的父组件可以调用 `inputRef.current.focus()`。


### 5. callback ref

当 ref 属性接收一个回调函数时，会将其对应的 DOM元素 或 class 组件实例作为回调函数的参数调用回调函数。因此我们可以通过回调 Refs 的方式不依靠 Ref 对象，更灵活地控制要访问的元素或实例

> callback ref 采用了组件 render 过程中在闭包函数中分配 ref 的模式，通常会使用内联函数的形式，那么每次渲染都会重新创建，由于 react 会清理旧的 ref 然后设置新的，因此更新期间会调用两次，第一次为 null，如果在 callback 中带有业务逻辑的话，可能会出错，当然可以通过将 callback 定义成类成员函数并进行绑定的方式避免。


```js
class MyComponent extends React.Component {
  componentDidMount() {
    this.myRef.focus();
  }
  render() {
    return <input ref={(ele) => {
      this.myRef = ele;
    }} />;
  }
}
```

### 6. string ref (不再建议使用)

React 官方文档直接提出 `string ref` 将会在未来版本被移出，建议用户使用 `callback ref` 来代替

```js
// string ref
class MyComponent extends React.Component {
  componentDidMount() {
    this.refs.myRef.focus();
  }
  render() {
    return <input ref="myRef" />;
  }
}
```

`string ref` 存在以下问题：
1. 存在性能问题：当 ref 定义为 string 时，需要 React 追踪当前正在渲染的组件，在 reconciliation 阶段，React Element 创建和更新的过程中，ref 会被封装为一个闭包函数，等待 commit 阶段被执行，这会对 React 的性能产生一些影响。
2. 使用 string ref 会造成 ref 挂载位置产生歧义
3. string ref 无法被组合，例如一个第三方库的父组件已经给子组件传递了 ref，那么我们就无法再在子组件上添加 ref 了，而 callback ref 可完美解决此问题
4. 在根组件上使用无法生效
5. 对于静态类型较不友好，当使用 string ref 时，必须显式声明 refs 的类型，无法完成自动推导。
6. 编译器无法将 string ref 与其 refs 上对应的属性进行混淆，而使用 callback ref，可被混淆。


### 7. 总结

在 class 内部通过 `React.createRef()` 创建 Refs 对象，在函数组件内部通过 `React.useRef()` 创建 Refs 对象，如果被引用的组件为函数组件，则需要通过 `React.forwardRef()` 和 `React.useImperativeHandler()` 来作处理。

* 父组件为 class 组件，子组件为 class 组件 ---- 父组件中直接使用 `React.createRef()` 引用子组件实例
* 父组件为 class 组件，子组件为 函数组件 ---- 父组件中 `React.createRef()`，子组件使用 `React.forwardRef()` 和 `React.useImperativeHandler()`
* 父组件为 函数组件，子组件为 class 组件 ---- 父组件中直接使用 `React.useRef()` 引用子组件实例
* 父组件为 函数组件，子组件为 函数组件 ---- 父组件中 `React.useRef()`，子组件使用 `React.forwardRef()` 和 `React.useImperativeHandler()`
