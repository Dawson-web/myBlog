---
title: Js原型链
tags: [Js系列]
categories: Js系列
published: 2024-08-30 21:17:45
draft: false
image: /前端/js系列/Js原型链/cover.webp

---
---

## 前言

最近停摆了快一个周左右，一是开学了各种杂七杂八的事情比较多，另一个是我之前在想是否有必要写这些面经，也是很早就遇见的问题了
网上的资源丰富总能找到写的远比我好的，那我写出来的东西有什么意义呢？或许是接触的人太优秀，不甘于自己的平庸
想了想，虽然写出来可能帮助不了别人，但至少让我更加清晰这些概念了，写过和看过是不一样的，后续也会继续更新的

另外发现Echo的各位大家最近似乎都在准备考研和实习的事情，大家加油！（此外特别感谢苏雨只会喵喵，帮我解答了一些面试的疑问，对面试有一个新的认知）

好了言归正传，今天来聊聊Js的原型链，之前一直没有碰这一部分因为prototype和_proto_以及对象实例这些的联系感觉很懵，今天我们来啃一啃这个硬骨头


## 原型

JavaScript 常被描述为一种基于原型的语言——每个对象拥有一个原型对象

当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾

**准确地说，这些属性和方法定义在Object的构造器函数（constructor functions）之上的prototype属性上，而非实例对象本身**

```js

function Box(value) {
  this.value = value;
}
Box.prototype.getValue = function () {
  return this.value;
};
const box = new Box(1);

// 在创建实例后修改 Box.prototype
Box.prototype.getValue = function () {
  return this.value + 1;
};
box.getValue(); // 2

```

可见当创建实例后修改原型方法后，实例对象的调用方法也会改变，这是因为实例对象在创建时，会从原型上继承方法，当原型上的方法改变时，实例对象上的方法也会改变，也就解释了**准确地说，这些属性和方法定义在Object的构造器函数（constructor functions）之上的prototype属性上，而非实例对象本身**


## Prototype

每个对象都有一个指向它的原型对象的内部链接。这个链接存在于对象内部的[[Prototype]]属性中

这个链接有什么用呢？当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾

> 注意：原型属性——有一个例外，箭头函数没有默认的原型属性

```js

function doSomething() {}
// 你如何声明函数并不重要；
// JavaScript 中的函数总有一个默认的
// 原型属性——有一个例外：
// 箭头函数没有默认的原型属性：
doSomething.prototype.foo = "bar";
console.log(doSomething.prototype);

```
其结果为：

```js

{
  foo: "bar",
  constructor: ƒ doSomething(),
  [[Prototype]]: {
    constructor: ƒ Object(),
    hasOwnProperty: ƒ hasOwnProperty(),
    isPrototypeOf: ƒ isPrototypeOf(),
    propertyIsEnumerable: ƒ propertyIsEnumerable(),
    toLocaleString: ƒ toLocaleString(),
    toString: ƒ toString(),
    valueOf: ƒ valueOf()
  }
}

```
上述我们提到了**当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾**

> 思考： 那我们是否可以由下面的例子两个` [[Prototype]] `包含关系得出该结论呢

```js

function doSomething() {}
doSomething.prototype.foo = "bar"; // 向原型上添加一个属性
const doSomeInstancing = new doSomething();
doSomeInstancing.prop = "some value"; // 向该对象添加一个属性
console.log(doSomeInstancing);


```
```js

{
  prop: "some value",
  [[Prototype]]: {
    foo: "bar",
    constructor: ƒ doSomething(),
    [[Prototype]]: {
      constructor: ƒ Object(),
      hasOwnProperty: ƒ hasOwnProperty(),
      isPrototypeOf: ƒ isPrototypeOf(),
      propertyIsEnumerable: ƒ propertyIsEnumerable(),
      toLocaleString: ƒ toLocaleString(),
      toString: ƒ toString(),
      valueOf: ƒ valueOf()
    }
  }
}

```
而上面这个对象，就是大家常说的原型对象

可以看到，原型对象有一个自有属性constructor，这个属性指向该函数，如下图关系展示

![Js原型链](/前端/js系列/Js原型链/1.webp)

##  _proto_

原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为原型链 (prototype chain)，它解释了为何一个对象会拥有定义在其他对象中的属性和方法

在对象实例和它的构造器之间建立一个链接（它是__proto__属性，是从构造函数的prototype属性派生的），之后通过上溯原型链，在构造器中找到这些属性和方法

## 原型链

上述概念可以先看一张图来方便理解

![Js原型链](/前端/js系列/Js原型链/2.webp)

此处的Person为构造函数，Person.prototype为原型对象，person为实例对象

然后我们先来理一下之间的关系

> 构造函数是一个函数对象，是通过 Function构造器产生的 ` Person.__proto__ === Function.prototype `

```js
person.__proto__ === Person.prototype
Person.prototype === Person.prototype //....什么鬼......
Person.prototype.__proto__ === Object.prototype
// ......
Object.prototype.__proto__ === null
```
下面作出总结：

一切对象都是继承自Object对象，Object 对象直接继承根源对象null

一切的函数对象（包括 Object 对象），都是继承自 Function 对象

Object 对象直接继承自 Function 对象

Function对象的__proto__会指向自己的原型对象，最终还是继承自Object对象

其简单的逻辑为：构造函数继承于Function 对象，其实例对象指向构造函数的原型对象，原型对象最终继承自Object对象，Object 对象继承自null

> 讲的不是很清晰有点杂靠大家的悟性了（哎嘿嘿.....，反正我因该是悟了）

## 参考
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain#%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0

https://vue3js.cn/interview/JavaScript/prototype.html#%E4%B8%89%E3%80%81%E6%80%BB%E7%BB%93

---