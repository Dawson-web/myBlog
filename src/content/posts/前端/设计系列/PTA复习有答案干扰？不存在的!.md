---
title: PTA复习有答案干扰？不存在的!
tags: [前端分享]
category: 前端分享
published: 2024-11-24 19:29:25
draft: false 
image: /前端/设计系列/PTA复习有答案干扰？不存在的!/cover.webp

---

## 前言

最近期末月在复习PTA上面的题老是有做出来了的答案干扰，就很烦，然后突然奇想写了几段代码来解决这个事情，还算实用有趣，我想大家肯能也和我一样有同样是诉求

## 介绍
先让我看看效果：

> 使用前:
![PTA复习有答案干扰？不存在的!](/前端/设计系列/PTA复习有答案干扰？不存在的!/1.png)

> 使用后:
![PTA复习有答案干扰？不存在的!](/前端/设计系列/PTA复习有答案干扰？不存在的!/2.png)

## 实现
### 去除单选框

```ts
// 使用querySelectorAll方法查找所有符合条件的input元素
const radioInputs = document.querySelectorAll('input[type="radio"]');

// 遍历找到的每个元素并将其从DOM树中删除
radioInputs.forEach(function(input) {
    input.parentNode.removeChild(input);
});
```
### 去除多选框

```ts
// 使用querySelectorAll方法查找所有符合条件的input元素
const checkboxInputs = document.querySelectorAll('input[type="checkbox"]');

// 遍历找到的每个元素并将其从DOM树中删除
checkboxInputs.forEach(function(input) {
    input.parentNode.removeChild(input);
});
```
### 去除答案是否正确结果显示
```ts
 const targetDivs = document.querySelectorAll('div.space-y-4.text-sm.bg-bg-light.p-4.rounded-lg');

    // 遍历找到的每个元素并将其从DOM树中删除
    targetDivs.forEach(function(div) {
        div.parentNode.removeChild(div);
    });

```
### 去除填空题
```ts
    // 使用querySelectorAll方法查找所有符合条件的input元素,填空题的匹配较简单可能会出现错误删除请自行辨别
    const targetInputs = document.querySelectorAll('input');

    // 遍历找到的每个元素并清空其value属性值
    targetInputs.forEach(function(input) {
        input.value = "";
    });
```
### 一把梭哈!!!
```ts
  // 使用querySelectorAll方法查找所有符合条件的input元素
const radioInputs = document.querySelectorAll('input[type="radio"]');

// 遍历找到的每个元素并将其从DOM树中删除
radioInputs.forEach(function(input) {
    input.parentNode.removeChild(input);
});

// 使用querySelectorAll方法查找所有符合条件的input元素
const checkboxInputs = document.querySelectorAll('input[type="checkbox"]');

// 遍历找到的每个元素并将其从DOM树中删除
checkboxInputs.forEach(function(input) {
    input.parentNode.removeChild(input);
});

const targetDivs = document.querySelectorAll('div.space-y-4.text-sm.bg-bg-light.p-4.rounded-lg');

   // 遍历找到的每个元素并将其从DOM树中删除
   targetDivs.forEach(function(div) {
       div.parentNode.removeChild(div);
   });

   // 使用querySelectorAll方法查找所有符合条件的input元素,填空题的匹配较简单可能会出现错误删除请自行辨别
const targetInputs = document.querySelectorAll('input');

// 遍历找到的每个元素并清空其value属性值
targetInputs.forEach(function(input) {
    input.value = "";
});
```

### 进阶版-隐藏
```ts
const radioInputs = document.querySelectorAll('input[type="radio"]'); //单选
const checkboxInputs = document.querySelectorAll('input[type="checkbox"]'); //多选
const targetDivs = document.querySelectorAll('div.space-y-4.text-sm.bg-bg-light.p-4.rounded-lg'); //结果
const targetInputs = document.querySelectorAll('input'); //填空

radioInputs.forEach(function(input) {
    input.style.opacity = 0;
});

checkboxInputs.forEach(function(input) {
    input.style.opacity = 0;
});

targetDivs.forEach(function(div) {
    div.style.opacity = 0;
});

targetInputs.forEach(function(input) {
    input.style.color = "transparent";
});
```

### 进阶版-显示
```ts
const radioInputs = document.querySelectorAll('input[type="radio"]'); //单选
const checkboxInputs = document.querySelectorAll('input[type="checkbox"]'); //多选
const targetDivs = document.querySelectorAll('div.space-y-4.text-sm.bg-bg-light.p-4.rounded-lg'); //结果
const targetInputs = document.querySelectorAll('input'); //填空

radioInputs.forEach(function(input) {
    input.style.opacity = 100;
});

checkboxInputs.forEach(function(input) {
    input.style.opacity = 100;
});

targetDivs.forEach(function(div) {
    div.style.opacity = 100;
});

targetInputs.forEach(function(input) {
    input.style.color = "black";
});
```
