---
title: call apply bind
tags: [Js系列]
categories: Js系列
published: 2024-08-16 19:17:45
draft: false
image: /前端/js系列/call apply bind/cover.webp

---

---
## 前言：

在上一个章节简单的介绍了关于this指向的显示绑定

显示绑定主要分为：

- call
- apply
- bind

但是在此基础上又可以再次分类为

立即调用：
- call
- apply

延迟调用：
- bind

在了解显示绑定的实现时，我们需要明确显示绑定是针对函数的，可以根据MDN的描述看出

` Function.prototype.call() ` ` Function.prototype.apply() ` ` Function.prototype.bind() `

并且显示绑定都只会改变当前this的指向不会影响当下次你调用函数时this的指向

## call

call() 方法使用一个指定的 this 值和单独给出的一个或多个参数来调用一个函数。

```js
function foo(...args) {
  console.log(this.a);
  console.log(...args)
}
var obj = {
  a: 2,
};
foo.call(obj,1,3); // > 2  > 1 3
foo() // undefined
```

## apply

apply() 方法调用一个具有给定 this 值的函数，以及以一个数组（或类数组对象）的形式提供的参数。

```js
function foo(...args) {
  console.log(this.a);
  console.log(...args)
}
var obj = {
  a: 2,
};
foo.apply(obj,[1,3]); // > 2  > 1 3
foo() // undefined
```

## bind

bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调

用时使用。但值得注意的是bind()不会立即调用函数，而是返回一个函数，并且这个函数的this指向了bind()的第一个参数

所以当我们调用返回函数时此时的this指向的是bind()的第一个参数不会改变，但当我们调用原函数时this指向的是全局对象


```js
function foo(...args) {
  console.log(this.a);
  console.log(...args)
}
var obj = {
  a: 2,
};
var bar = foo.bind(obj,1,3);
bar(); // > 2 > 1 3
bar(); // > 2 > 1 3
foo(); // > undefined
```

## 总结

call、apply、bind 都是用来改变函数的 this 指向的，区别在于：

- call 是在调用函数时立即执行，并且可以传入多个参数
- apply 是在调用函数时立即执行，并且可以传入一个数组作为参数
- bind 是在调用函数时不会立即执行，而是返回一个新的函数，并且可以传入多个参数
  
**总的来说bind使用最多，因为很多时候我们都不想让他立即执行**

---
