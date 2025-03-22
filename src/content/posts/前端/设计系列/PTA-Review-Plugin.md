---
title: PTA-Review-Plugin
tags: [猜想设计]
category: 猜想设计
published: 2025-3-22 8:24:25
draft: false 
image: /前端/设计系列/PTA-Review-Plugin/cover.webp

---

## 前言

之前在去年11月份的时候写过一篇文章叫 PTA复习有答案干扰？不存在的! ，里面记录一些通过F12打开控制台手动操作dom，从而实现PTA题目的显示与隐藏，但一方面是操作起来比较麻烦，另一方面是PTA的页面结构经常变动，导致之前的代码失效，且如果每次都要去复制相应的代码，那也太麻烦了，所以当时我就想能不能写一个插件，通过插件来控制PTA题目的显示与隐藏，从而实现自动化的操作，当时这个想法被搁置了，直到最近才重新拾起，于是PTA-Review-Plugin诞生！

> 目前仅支持Chrome浏览器，其他浏览器暂未适配

## 功能

- 隐藏/显示单选按钮
- 隐藏/显示多选按钮
- 隐藏/显示结果区域
- 隐藏/显示填空答案
- 过滤回答正确题目
- 导出做题答案（使用前请先关闭对应的题目隐藏，否则导出的答案对应部分也会被隐藏）
- 导入做题答案（使用前请先关闭对应的题目隐藏，且务必搭配导出做题答案使用，请用户自行检查两者题目是否一致避免题集为随机题目。目前支持：判断、单/多选、填空，其他相应类型如主观题、编程题考虑到每页只有一道，用户自行复制粘贴更方便，暂未研发）
  

## 下载
下载地址：https://github.com/Dawson-web/PTA-Review-Plugin?tab=readme-ov-file
![PTA-Review-Plugin](/前端/设计系列/PTA-Review-Plugin/1.webp)

目前有两个版本，一个是最新版本（v1.4），一个是稳定版本（v1.3），建议使用稳定版本，最新版本的导入功能暂可能存在一些问题，后续会继续优化，如有问题请及时反馈（版本会不定时更新，博客文档会有滞后性，请以GitHub仓库为主），进入后选择对应版本下载即可，建议选择zip文件，当然你也可以直接fork项目
![PTA-Review-Plugin](/前端/设计系列/PTA-Review-Plugin/2.webp)


## 配置
下载此扩展的zip文件并解压
打开Chrome浏览器，进入扩展管理页面(chrome://extensions/)
![PTA-Review-Plugin](/前端/设计系列/PTA-Review-Plugin/3.webp)

打开开发者模式（右上角的开关）
点击"加载已解压的扩展程序"按钮
![PTA-Review-Plugin](/前端/设计系列/PTA-Review-Plugin/4.webp)

选择解压后的文件夹后会出现
![PTA-Review-Plugin](/前端/设计系列/PTA-Review-Plugin/5.webp)

可以选择固定扩展
![PTA-Review-Plugin](/前端/设计系列/PTA-Review-Plugin/6.webp)

7.开始使用

![PTA-Review-Plugin](/前端/设计系列/PTA-Review-Plugin/7.webp)


![PTA-Review-Plugin](/前端/设计系列/PTA-Review-Plugin/8.webp)


![PTA-Review-Plugin](/前端/设计系列/PTA-Review-Plugin/9.webp)

## 使用方法
在PTA网站上，点击Chrome工具栏中的扩展图标
使用开关按钮控制各元素的显示/隐藏状态
设置会自动保存，并在页面刷新后保持

> 目前发现有些时候配置似乎会失效？不过刷新后重新进行隐藏就可以了

## 注意事项
- 本扩展仅用于学习和复习，请勿用于考试作弊
- 目前仅适用于PTA平台(pintia.cn)
- 如果页面动态加载新内容，扩展将自动应用设置到新元素
- 由于PTA页面结构经常变动，插件可能无法正常使用，请用户自行检查，如发现插件失效，请及时反馈

## 贡献
- 如果您有任何建议或改进，请随时提交Pull Request或创建Issue
- 如果您遇见任何问题，也欢迎直接通过GitHub或者QQ联系我