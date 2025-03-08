---
title: this的指向
tags: [Js系列]
category: Js系列
published: 2024-08-15 09:00:00
draft: false 
image: /前端/js系列/this的指向/cover.webp

---

## 前言:

在翻看众多关于介绍this的指向的博客文档的时候，发现有的介绍的很含糊其词，而有的呢则附带的代码输出都不对应

所以简单的总结了这篇关于this的指向，希望它能尽可能的清晰明了

**并且在我测试各部分代码的时候发现不同的Js在线运行器的运行结果也尽不相同**

**所以下述代码的运行结果皆为Goole控制台输出，以达到相对严谨的结果**



## 绑定规则:

根据不同的使用场合，`this`有不同的值，主要分为下面几种情况：

- 默认绑定

  在非严格模式下（non-strict mode），this的值会是全局对象。
  
  在浏览器环境中，全局对象是window。而在Node.js环境中，全局对象是global。
  
  如果你是在严格模式（strict mode）下运行这段代码，那么this将会是undefined。

  ```js
  console.log(this) // > [object Window]
  ```

  ![this的指向](/前端/js系列/this的指向/1.webp)

- 隐式绑定

  函数还可以作为某个对象的方法调用，这时`this`就指这个上级对象

  ```js
  function test(){
      console.log(this.x)
  }
  
  let obj = {}
  obj.x = "Hello!"
  obj.test = test
  obj.test() // > Hello!
  ```

  ![this的指向](/前端/js系列/this的指向/2.webp)

- new绑定

  通过构建函数`new`关键字生成一个实例对象，此时`this`指向这个实例对象

  ```js
  function test(){
   this.x = "Hello!"
  }
  
  let obj = new test()
  console.log(obj.x) // > Hello!
  ```

  ![this的指向](/前端/js系列/this的指向/3.webp)


- 显示绑定

  `apply()、call()、bind()`是函数的一个方法，作用是改变函数的调用对象。

  不过这三者又有些许区别，此处仅介绍`bind()`方法，三者的区别后续会单独介绍

  ```js
  const module = {
    x: 42,
    getX: function () {
      return this.x;
    },
  };
  
  const unboundGetX = module.getX;
  console.log(unboundGetX()); // The function gets invoked at the global scope
  // Expected output: undefined
  
  const boundGetX = unboundGetX.bind(module);
  console.log(boundGetX());
  // Expected output: 42
  
  ```
  ![this的指向](/前端/js系列/this的指向/4.webp)


- 箭头函数

  箭头函数的this首先我们得明确他没有自己的this，或者说它的this是继承自父级作用域的this

  打个比方来说，普通函数的this指向是你住宅的楼层的话，那么箭头函数的this指向的就是你所住的小区

  但this的最终指向无论如何到Window全局对象就停止了，这就是它的this作用域链的终点
  
  **普通函数**

    ```js
  function test(){
    console.log(this.x)
  }
  
  let obj = {}
  obj.x = "Hello!"
  obj.test = test
  obj.test() // > Hello!
  console.log(this) //> [object Window]

  ```
  
    **箭头函数**

  ```js
  const test = () => {
    console.log(this.x)
  }
  
  let obj = {}
  obj.x = "Hello!"
  obj.test = test
  obj.test() // > undefined
  console.log(this) // > [object Window]

  ```

  ![this的指向](/前端/js系列/this的指向/5.webp)

## 绑定优先级:

new绑定 > 显示绑定 > 隐式绑定 > 默认绑定

## 个人感悟:

- 默认绑定：this指向window全局对象
- 隐式绑定：this指向调用函数的对象
- new绑定：this指向实例对象
- 显示绑定：this指向传入的对象
- 箭头函数：this指向父级作用域的this

但是呢我觉得隐式绑定和new绑定有点类似，总体上都是相对隐式的，而显示绑定则相对显式(有点废话了)

且总体逻辑都是通过变量赋值来改变this的指向 ` obj.test = test ` ` let obj = new test() `




