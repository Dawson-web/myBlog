---
title: 你真的了解v-if和v-for吗？
tags: [Vue系列]
category: Vue系列
published: 2024-09-04 21:00:00
draft: false 
image: /前端/vue系列/你真的了解v-if和v-for吗？/cover.webp

---

---
## 前言

最近翻了翻Vue的官方文档，发现对于v-for 与 v-if的介绍时与之前所了解到的v-for 与 v-if的优先级恰恰相反，详细查询了相关资料后，发现大多数对于此出的介绍都是基于Vue2文档的，在Vue3中，v-if的优先级高于v-for
> vue2版本，v-for高于v-if且两者不可一同使用，vue3是可以一起用的，vue3更新了优先级，v-if高于v-for

## Vue2中的v-if与v-for

在Vue2中，v-for的优先级高于v-if，这意味着v-for会先执行，然后再执行v-if，这可能会导致一些意想不到的问题。例如，如果你在一个列表中使用了v-for和v-if，那么v-if会先执行，这可能会导致列表中的元素被过滤掉，然后再执行v-for，这可能会导致列表中的元素被重复渲染。


```html
<div v-for="item in items" v-if="item.show" :key="item.id">
  {{ item.text }}
</div>
```
当v-if的判断条件依赖于v-for的某个值时(item,index)为了避免这种情况，我们建议按下述方法：

> 相对于操作Dom树，我们直接过滤不符合条件的数据，这样以JS的运算成本去换取Dom的渲染成本，大大节省了开销

```js
<template>
  <div class="hello">
    <!-- 2. 然后这里去循环已经被过滤的属性 -->
     <div  v-for="(item) in ListArr" :key="item" ></div>
  </div>
</template>

<script>
export default {
  name: "IndexCom",
  data(){
    return {
      list:[1,2,3,4,5,6,7,8,9,10]
    }
  },
  computed:{
    //1. 在computed里先做好判断，这里过滤的成本远比v-if的成本低
    ListArr(){
        return this.list.filter((_,index) => index === 1)
    }
  }
};
</script>

<style scoped>
</style>

```
当v-if的判断条件不依赖于v-for的值时，我们建议将v-if写在v-for的外层，这样就可以让v-if判断优先

```html
<div v-if="shouldShow">
  <div  v-for="item in items" :key="item.id">
  {{ item.text }}
  </div>
</div>
```

## 尾言

> 在Vue3中，v-if的优先级高于v-for，这意味着v-if会先执行，然后再执行v-for，这可能会导致一些意想不到的问题。

但无论是Vue2还是Vue3，我们都建议外面包一层template岂不更好，无论从于性能还是于代码可读性都是不错的选择

参考：
https://cn.vuejs.org/guide/essentials/list.html#v-for-with-v-if
https://juejin.cn/post/7106437556354416677

---