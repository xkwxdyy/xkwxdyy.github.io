---
title: 个人作品分析：choices宏包
date: 2022-01-09 10:10:03
updated: 2022-01-09 13:55
tags: [LaTeX3, 实例分析, 宏包]
category: 
  - [个人作品]
  - [LaTeX3实例分析]
excerpt: 主要分析一下编写思路以及思考如何进一步优化
---

## 宏包介绍

### 宏包基本情况

Package: LaTeX package for flexibly LaTeXing choice items based on hlist and LaTeX3.

Repository: [Github仓库](https://github.com/xkwxdyy/choices-l3)

Repository: [Gitee仓库](https://gitee.com/xkwxdyy/choices-l3)

Author: Kangwei Xia [E-mail](mailto:kangweixia_xdyy@163.com)

### 宏包说明

`choices`宏包是一个用于排版选项的宏包, 包含但不限于以下特点：
- 可以排版任意数量的选项
- 可以方便切换标签风格`label-style`
- 可以更改标签`label`与内容的相对位置
- 可以调整标签`label`的偏移
- 自动识别是否使用 `includegraphics` 命令并自行调整 `anchor` 的位置（仅 `choices*` 有此功能,  之所以会有 `choices*` 命令, 就是因为基于 `hlist` 环境的 `choices` 只有一个方位, 无法更改, 所以需要用另外的方法处理（ `choices*` 是用 `coffin` 进行处理））.
**此功能的实现需要将 `expl3` 宏包更新至最新（至少是2021-11-12后）, 否则可能无法使用且会报错.**

在需要排版选项的情况中（比如试卷、问卷排版等）`choices`宏包可以发挥重要作用, 旨在让用户更多关注在内容本身, 契合LaTeX的内容与样式分离的思想. 

`choices`宏包是基于`hlist`环境与LaTeX3开发的LaTeX宏包, 它提供了`choices(*)` 两个主命令与`coffinchoice`、`hlistchoice`和`quan`三个副命令. `choices`和`choices*`是利用了`xparse`宏包对`hlistchoice`与`coffinchoice`命令进行封装. 其中`choices`等效于`hlistchoice`, `choices*`等效于`coffinchoice`. 

  通常情况下使用 `choices` 命令就够了, 但是用户如果有排版图片的需求, 可能需要将 `label` 置于内容的上方或下方, 而 `choices` 命令基于 `hlist` 环境编写, 所以`label` 只能置于内容的左侧, 这个时候只需要使用`choices*`, 会自动更改`anchor`为`south`, 如果需要修改自动的`anchor`为其它的方位可以使用`\choicesetup{autopic = north}` .

更多宏包细节与命令欢迎阅读宏包手册`choices_usrmanual.pdf`（用xelatex编译`choices_usrmanual.tex`即可）

### 如果使用过程中发现了issue或者有什么建议, 欢迎提出

- [Github issues](https://github.com/xkwxdyy/choices-l3/issues)
- [Gitee issues](https://gitee.com/xkwxdyy/choices-l3/issues)

---

## 宏包设计和构想思路

宏包的产生背景我其实在`choices_usrmanual.pdf`中解释过，在这里简要地再提一下：

- 对于选择题的选项排版
  - 已有的选项排版命令受限于参数的个数，比如四个参数的命令必须要有四个选项
  - 对于多选题，问卷的选项排版，选项个数不确定，如果要从1-9个参数都自定义命令那非常麻烦，而且如果按照传统的自定义命令方式最多有9个必选参数，如果有更多的排版需求，那么这些方法已经失效
  - 已有的命令无法切换label的样式

基于上述的背景，我一开始基于`hlist`环境整合了一些功能，用LaTeX3的`seq`模块来切割选项，解决了参数个数问题，产生了`\choices`命令（本质是`\hlistchoice`命令）。

但是`\choices`命令有一个小问题：label的位置非常局限，即使是上下偏移都无法通过`hlist`宏包本身来实现，而且还遇到一个问题：图片类型的选项排版。

图片排版也是中学老师经常碰到的一种问题，我目前了解的情况来看，大部分都是通过表格来完成的，但是“使用表格”本身有一丢丢违背LaTeX的“内容与样式分离”的想法，因为我的目的还是为了排版选项，而不是表格。

对于图片的排版，label通常情况下更多放置在图片的正上方或正下方。基于上述原因，我就需要开发另一种选项排版的命令`\choices*`（本质是`\coffinchoice`）可以方便地切换不同的anchor（借用了TikZ中的术语），满足不同需求。受耿楠教授的[`hanzibox`宏包](https://gitee.com/nwafu_nan/hanzibox-l3)启发，我决定使用LaTeX3的coffin来完成此命令的研发，最后在几天的调试下基本实现了想要的功能。

## 宏包代码优化

李泽平老师当时看了我的代码和我说，一个命令的代码不应该超过一个屏幕，所以我需要对代码进行优化。

其实我最开始设计的时候就是想有优化的：也就是一些算法可以单独抽离出来成为单独的函数，其实很多成熟的宏包都是这么做的，但是我没这么弄过，没有任何经验，最开始弄的时候直接出bug，然后想着“先实现再完美”（因为我一直以来都有点完美主义，导致了不少的拖延hhh），现在功能基本实现，就尝试优化一下代码。

### 键值优化

键值的有一定规范，比如使用`-`来分隔使得更容易阅读，比如`random-items`就是控制`items`的乱序。

