---
title: JS 实现继承的几种方式
categories: JavaScript 编程题
tags: [编程题]
date: 2021-03-12 15:32:28
---

# JS 实现继承的几种方式

[TOC]

## 1. 原型链继承

```js
function SuperType() {
  this.property = true;
}
SuperType.prototype.getSuperValue = function () {
  return this.property;
};

function SubType() {
  this.subproperty = false;
}

//继承了 SuperType
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.getSubValue = function () {
  return this.subproperty;
};

var instance = new SubType();
console.log(instance.getSubValue()); // false
console.log(instance.getSuperValue()); // true
```

**缺点：**
1. 包含引用类型值的原型属性会被所有实例共享，原型链上的属性为引用类型时，某个实例修改原型链上的属性会导致其他实例也会受到影响
2. 在创建子类型的实例时，不能向超类型的构造函数中传递参数

## 2. 借用构造函数继承

```js
function SuperType(){
 this.colors = ["red", "blue", "green"];
}
function SubType(){
 //继承了 SuperType
 SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors); //"red,blue,green,black"

var instance2 = new SubType();
console.log(instance2.colors); //"red,blue,green" 
```

**缺点：**
1. 无法复用函数，因为方法都在构造函数中定义（没有通过原型链引用）
2. 在超类型的原型中定义的方法，对子类型而言也是不可见的，结果所有类型都只能使用构造函数模式

## 3. 原型链 + 借用构造函数继承

```js
function SuperType(name){
  this.name = name;
  this.colors = ["red", "blue", "green"];
 }
 SuperType.prototype.sayName = function(){
  console.log(this.name);
 };

 function SubType(name, age){
  //继承属性
  SuperType.call(this, name);
  this.age = age;
 }
 //继承方法
 SubType.prototype = new SuperType();
 SubType.prototype.constructor = SubType;
 SubType.prototype.sayAge = function(){
  console.log(this.age);
 };

 var instance1 = new SubType("Nicholas", 29);
 instance1.colors.push("black");
 console.log(instance1.colors); //"red,blue,green,black"
 instance1.sayName(); //"Nicholas";
 instance1.sayAge(); //29
 
 var instance2 = new SubType("Greg", 27);
 console.log(instance2.colors); //"red,blue,green"
 instance2.sayName(); //"Greg
```

说明：组合继承避免了原型链和借用构造函数的缺陷，融合了它们的优点，成为 JavaScript 中最常用的继承模式

缺点：
1. 会调用两次超类型构造函数

## 4. 混入式继承

```js
function MyClass() {
  SuperClass.call(this);
  OtherSuperClass.call(this);
}

// 继承一个类
MyClass.prototype = Object.create(SuperClass.prototype);
// 混合其它
Object.assign(MyClass.prototype, OtherSuperClass.prototype);
// 重新指定constructor
MyClass.prototype.constructor = MyClass;

MyClass.prototype.myMethod = function() {
  // do something
};
```

## 5. 原型式继承
**原型式继承**适用于一个对象继承自另一个对象的情况，可以不必引入构造函数

ECMAScript5 通过新增 Object.create()方法规范化了**原型式继承**。这个方法接收两个参数：一个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象

```js
var person = {
 name: "Nicholas",
 friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = Object.create(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");
alert(person.friends); // "Shelby,Court,Van,Rob,Barbie" 

// 模拟 Object.create() 方法
Object.$create = function(original) {
  var fn = function() {};
  fn.prototype = original;
  return new fn();
}
```

> 在没有必要兴师动众地创建构造函数，而只想让一个对象与另一个对象保持类似的情况下，原型式继承是完全可以胜任的，不过别忘了，包含引用类型值的属性始终都会共享相应的值，就像使用原型模式一样

## 6. 寄生式继承

**寄生式继承**是结合了原型式继承和工厂模式的一种方式，即创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真地是它做了所有工作一样返回对象。适用于通过函数返回一个增强了的对象。

```js
function createAnother(original) {
  var clone = Object.create(original); // 通过调用函数创建一个新对象
  clone.sayHi = function () {  //以某种方式来增强这个对象
    console.log("hi");
  };
  return clone; //返回这个对象
}

var person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"],
};
var anotherPerson = createAnother(person);
anotherPerson.sayHi(); //"hi"
```

缺点：
1. 需要在构造函数中给每个对象添加函数，导致不能复用函数

## 7. 寄生 + 原型链继承

**寄生 + 原型链是引用类型最理想的继承范式**

```js
function inheritPrototype(subType, superType) {
  var prototype = Object.create(superType.prototype); //创建对象
  prototype.constructor = subType; //增强对象
  subType.prototype = prototype; //指定对象
}

function SuperType(name) {
  this.name = name;
  this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function () {
  console.log(this.name);
};

function SubType(name, age) {
  SuperType.call(this, name);
  this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function () {
  console.log(this.age);
};
```

## 8. Class 继承

```js
class Parent {
  constructor(name) {
    this.name = name;
  }
  static sayHello() {
    console.log("hello");
  }
  sayName() {
    console.log("my name is " + this.name);
    return this.name;
  }
}
class Child extends Parent {
  constructor(name, age) {
    super(name);
    this.age = age;
  }
  sayAge() {
    console.log("my age is " + this.age);
    return this.age;
  }
}

let parent = new Parent("Parent");
let child = new Child("Child", 18);

console.log("parent: ", parent); // parent:  Parent {name: "Parent"}
Parent.sayHello(); // hello
parent.sayName(); // my name is Parent
console.log("child: ", child); // child:  Child {name: "Child", age: 18}
Child.sayHello(); // hello
child.sayName(); // my name is Child
child.sayAge(); // my age is 18

// 1、构造器原型链
Child.__proto__ === Parent; // true
Parent.__proto__ === Function.prototype; // true
Function.prototype.__proto__ === Object.prototype; // true
Object.prototype.__proto__ === null; // true
// 2、实例原型链
child.__proto__ === Child.prototype; // true
Child.prototype.__proto__ === Parent.prototype; // true
Parent.prototype.__proto__ === Object.prototype; // true
Object.prototype.__proto__ === null; // true

```