---
title: Sass 基础语法
categories: [CSS, Sass]
tags: [CSS, Sass]
date: 2021-03-24
---


# 1、 FIS3 中集成 Sass

## 1.1 插件安装

* sass 编译 `npm i fis3-parser-node-sass -g`
* 前缀添加 `npm i fis3-preprocessor-cssprefixer -g` 必须全局安装

```javascript
fis.match('*.scss', {
    parser: fis.plugin('node-sass'),
    rExt: 'css',
    release: '$0'
});
fis.match('_*.scss', {
    release: false
});
fis.match('*.scss', {
    preprocessor: fis.plugin('cssprefixer', {
        "browsers": ["Android >= 2.1", "iOS >= 4", "ie >= 8", "firefox >= 15"],
        "cascade": true
    })
});
```
## 1.2 目录规范

* base
    * _reset.scss
    * _typography.scss
* helpers
    * _function.scss
    * _mixin.scss
    * _variable.scss
* components
    * _header.scss
    * _footer.scss
    * _buttons.scss
    * _cards.scss
* layouts
    * _responsive.scss
* pages
    * _page1.scss
    * _page2.scss
* lib
    * bootstrap.scss
    * font-awesome.scss
* main.scss

## 1.3 编码规范

### 1.3.1 Sass 性能优化

* 用 class 分类代替子代选择器
* 建议用子选择器，而不是后代选择器，提高性能
* 特殊类别选择属性选择器

### 1.3.2 Sass 属性声明顺序

* z-index, display
* position, left, top, right, bottom
* overflow, float, clear
* margin, padding
* background, border
* font, text
* scss 代码块应有的顺序

### 1.3.2 Sass 使用规则

* 当前选择器的样式属性
* 父级选择器的伪类选择器 (`:first-letter`, `:hover`, `:active`)
* 伪类元素 (`:before`、`:after`)
* 父级选择器的声明样式 (`.selected`, `.active`, `.enlarged`)
* 用 Sass 的上下文媒体查询
* 子选择器作为最后的部分


## 2、 Sass 语法说明

### 2.1 后缀名

* Sass文件后缀名可为 `.scss`、 `.sass`
* 推荐使用 `.scss`，与 `css` 语法完全兼容
* 不推荐使用 `.sass` 要求更严格，不能使用 `{}` 和 `;`

### 2.2 文件导入

* 可在 `Sass` 文件中导入 `Sass` 文件或 `css` 文件 （对于 `Sass` 文件可不写后缀名）
* 基础文件以 _ 开头，如变量声明、混入等，（导入时可不写下划线）

> 示例：
> * `@import "page/page1"`
> * `@import "lib/bootstrap.css"`


### 2.3 变量

* 使用 `$` 声明并使用变量
* 变量可被覆盖（就近原则或在变量后面添加 `!default`）
* 变量可为全局或局部 （在选择器外部声明、选择器内部声明）
* 当变量为字符串时，可在类名、属性名中引用，如 `border-#{$variableName}`
* 变量为 list 结构 （在 `$size: 18px 30px 34px` 使用 `nth($size, 1)` 调用）
* 变量为 map 结构（使用 `@each $key, $value in $map` 调用）

```scss
$gray: #666 !default;
$directory: top;
$size: 18px 30px 34px;
$heading: (h1: #0aa, h2: #cc0, h3: #b0b);
body {
    color: #f00;
    a {
        font-size: nth($size, 1);
        color: $gray;
        border-#{$directory}: 1 px solid $gray;
        &:hover {
            $font-size: 40 px;
            font-size: $font-size;
        }
    }
    @each $head, $color in $heading {
        #{$head} {
            color: $color;
        }
    }
}
```

### 2.4 嵌套

* 可使用选择器嵌套、属性嵌套 （属性嵌套并没什么用）
* `@at-root` 可跳出所有的上级选择器 （可同时对过个选择器操作）
* `#{&}__modifer` 可实现 BEM 特性

```scss
// 编译前的 scss 文件
.footer {
    font-size: 16px;
    @at-root #{&}__modifer {
        color: #333
    }
    @at-root .nav & {
        color: #f0f;
    }
    @at-root {
        .child1 {
            color: #f00;
        }
        .child2 {
            color: #ff0;
        }
    }
}
```

```scss
// 编译后的 css 文件
.footer {
    font-size: 16px;
}

.footer__modifer {
    color: #333;
}

.nav .footer {
    color: #f0f;
}

.child1 {
    color: #f00;
}

.child2 {
    color: #ff0;
}
```

### 2.4 占位选择器

* 使用 `%` 声明占位选择器， 通过 `@extend` 调用占位选择器
* 当占位选择器没被调用时，不会被解析出来 （推荐使用）
* 占位选择器可以用其定义一些基础的样式文件

```scss
// 编译前的 scss 文件
% my-sty {
    color: #aaa;
    font-size: 10px;
}

.child-1 {
    @extend %my-sty;
}
```

```scss
// 编译后的 css 文件
.child1 {
    color: #aaa;
    font-size: 10px;
}
```

### 2.5 继承

说明：继承是建立在语义化的关系上。当一个元素拥有的类（如 `.seriousError` ）表明它属于另一个类（如 `.error`），此时适合用继承

* 使用 `%extend` 进行继承
* 可以继承任何定义给单个元素的选择器，比如 `.special.cool`、`a:hover`

```scss
// 编译前
a:hover {
    text-decoration: underline;
}
.hoverlink {
    @extend a: hover;
}
```

```scss
// 编译后
a:hover, .hoverlink {
  text-decoration: underline;
}
```

```scss
// 编译前
.error {
    border: 1px #f00;
    background-color: #fdd;
}
.seriousError {
    @extend .error;
    border-width: 3px;
}
```

```scss
// 编译后
.error, .seriousError {
  border: 1px #f00;
  background-color: #fdd;
}

.seriousError {
  border-width: 3px;
}
```

```scss
// 编译前
.error {
    border: 1px #f00;
    background-color: #fdd;
}
.error.intrusion {
    background-image: url("/image/hacked.png");
}
.seriousError {
    @extend .error;
    border-width: 3px;
}
```

```scss
// 编译后
.error, .seriousError {
    border: 1px #f00;
    background-color: #fdd;
}
.error.intrusion, .intrusion.seriousError {
    background-image: url("/image/hacked.png");
}
.seriousError {
    border-width: 3px;
}
```

### 2.6 混合

说明：混合指令用于定义可重复的样式，避免使用无语义的 `class`，如 `.clearfix`、`.float-left`

* 使用 `@mixin` 声明混合指令，通过` @include` 调用混合指令
* 当混合没被调用时，不会被解析出来 （推荐使用）
* 混合样式中也可以嵌套其他混合样式
* 当混合为无参时，推荐使用占位选择器来实现

```scss
// 编译前的 scss 文件
@mixin ellipsis ($width: 100px) {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    width: $width;
}

.text-ellipsis {
    color: #0ff;
    @include ellipsis;
}

.text2-ellipsis {
    color: #999;
    @include ellipsis($width: 300px);
}
```

```scss
// 编译后的 css 文件
.text-ellipsis {
    color: #0ff;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    width: 100px;
}

.text2-ellipsis {
    color: #999;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    width: 300px;
}
```

```scss
// 编译前
@mixin height-light {
	background: #fc0;
}

@mixin head-text {
	font-size: 24px;
}

@mixin computed {
	@include height-light;
	@include head-text;
}

span {
	@include computed
}
```

```scss
// 编译后
span {
    background: #fc0;
    font-size: 24px;
}
```

### 2.7 函数

* 使用 `@fuction funName (param) {}` 声明，通过 `funName()` 调用
* 可使用 `sass` 自带的函数，如 `lighten()`、`darken()` 等

```scss
// 编译前的 scss 文件
@function addSize ($size) {
    $baseSize: 12px;
    @return $baseSize+$size;
}

.link-1 {
    $blue: #00A3CF;
    font-size: addSize(10px);
    color: darken($blue, 25%);
}

.link-2 {
    $blue: #00A3CF;
    font-size: addSize(10px);
    color: lighten($blue, 20%);
}
```

```scss
// 编译后的 css 文件
.link-1 {
    font-size: 22px;
    color: #003f50;
}

.link-2 {
    font-size: 22px;
    color: #36d4ff;
}
```

### 2.8 其他

* 运算 可对遍历、数值、像素、颜色等进行四则运算（操作符前后需要空格）
* 三目运算 条件为真/假时，返回不同的值 （与函数有点类似）
* `@if` 条件成立后执行 `{}` 内的语句
* `@each` 遍历 `list` 变量、`map` 变量
* `@for` 在指定数值范围内遍历

```scss
// sass 运算、 @if、三目运算 示例
// 编译前的 scss 文件
$hasSubMenu: true;
$level: 2;
.wrapper {
    color: if($hasSubMenu, #f00, #00f);
    @if $hasSubMenu {
        background-image: url('/images/down-icon.jpg');
    }
    @if $level==2 {
        height: 60px * 2;
        background: #f0f;
    }
    @else if $level==3 {
        height: 60px * 3;
        background: #f00;
    }
}

// 编译后的 css 文件
.wrapper {
    background-image: url("/images/down-icon.jpg");
    height: 120px;
    color: #f00;
    background: #f0f;
}
```

```scss
// @each 示例
// 编译前的 scss 文件
$animal-list: puma, sea-slug, egret;
@each $animal in $animal-list {
    .#{$animal}-icon {
        background-image: url('/images/#{$animal}.png');
    }
}

// 编译后的 css 文件
.puma-icon {
    background-image: url("/images/puma.png");
}

.sea-slug-icon {
    background-image: url("/images/sea-slug.png");
}

.egret-icon {
    background-image: url("/images/egret.png");
}
```

```scss
// @for 示例
// 编译前的 scss 文件
@for $i from 1 to 4 {
    .content-#{$i} {
        font-size: {
            font-size: 14px + $i * 2px;
        }
    }
}

// 编译后的 css 文件
.content-1 {
    font-size: 16px;
}

.content-2 {
    font-size: 18px;
}

.content-3 {
    font-size: 20px;
}
```