---
title: LaTeX3个人学习经验
date: 2022-01-08 21:22:38
updated: 2022-01-20 13:40
tags: [LaTeX3杂谈]
category: [LaTeX3杂谈]
excerpt: 本文主要介绍个人学习LaTeX3过程中的一些心得体会。
---

## 前言

我在很久之前自己写过一个LaTeX3学习个人经验，整理了自己学习过程中的学习资料，并写了一些学习心得，只分享给了两位老师，一个原因是这两个老师当时就想学习LaTeX3，第二个原因是时间有点匆忙，我的文字叙述并不流畅和简洁（虽然口语化容易读懂，但是感觉写成书面文章还是需要适当的言简意赅）

后来郭李军学弟在LaTeX工作室发了一个[LaTeX3学习资料](https://www.latexstudio.net/index/details/index/mid/2280) ，我发现整理的和我差不多，但是多了两三个（然后我马上就码住了hhh）。其实现在市面上能找到的LaTeX3资料其实还是有不少的，对于LaTeX3入门应该是足够的，但是如果想要LaTeX3能更深入地学习下去（比如学习`interface3.pdf`介绍的一些LaTeX2e中不常见（对于一般用户来说比较少用）的模块，如regex、prg、msg、file等等）现在已有的资料某种意义上是不够的。

但是其实已经有很多优秀的宏包文类是用LaTeX3编写的，比如`ctex`宏集,`tabularray.sty`,`fduthesis.cls`等，但是这些都是一些大佬编写，本身并不是为了告诉你怎么使用LaTeX3而编写的，所以读起来会有一定难度，但是如果细品的话能够发现里面有很多细节和变量函数的使用是值得我们学习的，所以需要一个“引路人”能够带我们读懂这些代码，或是分析一些其中的细节。

而我的博客想做的主要就是这个事，我的代码水平其实非常的一般，但是我想把我能读懂的，我的一些体会分享出来，大家一起讨论学习，这样对我来说也是有很大的提高。

目前关于LaTeX3想做的主要但不限于下面几个：

- 分析一些LaTeX3实例，LaTeX3应用的代码
- 讲述、记录或分析我自己用LaTeX3写命令的想法和思路（也能够帮助我进一步优化）
- 一些LaTeX3的常用方法和技巧



## 个人推荐的LaTeX3学习顺序

### 了解背景和LaTeX3基础

#### 背景

- 曾祥东老师的知乎文章：[LaTeX3 教程（一）——背景知识](https://zhuanlan.zhihu.com/p/92851140)
- LaTeX工作室发布，项子越老师录制的b站视频：[LaTeX3 教程一：简介](https://www.bilibili.com/video/BV1L44y1676h?from=search&seid=14283996233698968390&spm_id_from=333.337.0.0)

#### 基本语法和知识

- 曾祥东老师的知乎文章：[LaTeX3 教程（二）——语法概要](https://zhuanlan.zhihu.com/p/92853481)
- LaTeX工作室发布，项子越老师录制的b站视频：[LaTeX3 教程二：变量，函数及基本程序结构](https://www.bilibili.com/video/BV1g5411u7wg?spm_id_from=333.999.0.0)

#### 宏展开

- LaTeX工作室发布，项子越老师录制的b站视频：[LaTeX3 教程三：宏展开](https://www.bilibili.com/video/BV1HP4y1s7CR?spm_id_from=333.999.0.0)

#### 一些示例的应用

- 曾祥东老师的知乎文章：[LaTeX3 教程（三）——从一个例子说起](https://zhuanlan.zhihu.com/p/92854940)

- LaTeX工作室发布，项子越老师录制的b站视频：[LaTeX3 编程 终章 - 教程四：常用库](https://www.bilibili.com/video/BV18Z4y1X7vK?spm_id_from=333.999.0.0)
- 项子越老师的个人博客中有一个[`latex3-tutorial` ](https://www.alanshawn.com/latex3-tutorial/)中有视频中没提到的一些内容，绝大部分函数模块都给了个例子，大致告诉你LaTeX3能够实现一些什么样的效果

#### 细节补充

- 知乎一位大佬写了一篇文章：[一份勉强简短的LaTeX3编程介绍](https://zhuanlan.zhihu.com/p/408331900)，如果完成上面部分学习的读者阅读这篇文章，就会发现这篇文章是对上面内容的一个“总结体”，并且补充了一些小细节，即使学完了上面的内容也推荐学习一下
- LaTeX3团队成员Joseph Wright写的[一些文章](https://www.texdev.net/tags/#programming-latex3)（不止LaTeX3），很多内容比如`xparse`宏包的声明命令和其它宏包的区别，[关于LaTeX3的宏展开的`f`,`o`,`x` 有什么区别](https://www.texdev.net/2012/04/29/programming-latex3-more-on-expansion/)等文章都非常值得一看

### 自己动手写小玩意

- 在学习了上面的内容后，读者已经完全能够自己动手写一点小玩意了

### 分析他人的作品、应用

- LaTeX工作室里也有一些用户上传的[作品](https://www.latexstudio.net/index/lists/barSearch/text/LaTeX3) ，可以自己尝试阅读一下代码看看
- 慢慢地可以开始读一些LaTeX3编写的宏包或文类了，看看大佬们是怎么用这些变量函数的，比如用`clist` 模块的函数来批量处理（比如批量声明新变量）等等

## 希望掌握的“技能”

### 养成良好的代码规范

- LaTeX3有自己的一套[代码规范](https://ctan.math.illinois.edu/macros/latex/contrib/l3kernel/l3styleguide.pdf) ，和[LaTeX2e的代码规范](https://gitee.com/zepinglee/latex2e-style-guide)有一定的区别，不管是为了自己写、修改方便还是为了代码分享的时候他人阅读方便，都希望读者养成良好的代码规范习惯

### 学习`xparse`宏包

- `xparse`宏包提供的命令不仅能够封装LaTeX3的代码，在LaTeX2e的使用过程中体验会更好
- `lshort-zh-cn.pdf`中有部分介绍，但是还是希望直接读`xparse`的官方文档

### 学会到论坛提问

- [tex.se](https://tex.stackexchange.com/) 国外的网站，大佬很多，只要你的问题描述清楚，基本都会有大佬反馈
- [LaTeX工作室提问](https://ask.latexstudio.net/) 国内的网站，也有很多大佬，听说雾月大佬回答非常详细，而且LaTeX3功力也极强

