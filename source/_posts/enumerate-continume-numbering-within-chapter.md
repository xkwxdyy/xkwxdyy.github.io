---

title: 实现chapter内多个enumerate环境连续编号并在新chapter重置
date: 2022-01-14 21:28:52
updated: 2022-01-15 11:33
tags: [LaTeX, 问题解决, enumerate]
category: 
- [LaTeX]
- [LaTeX问题解决]
excerpt: 实现了chapter内的enumerate环境可以连续编号并且enumi计数器在新的chapter会自动清零的效果
---

## 问题阐述

昨天群内万宏伟老师提出了一个需求：想要enumerate具有以下功能：

1. 一个chapter内的不同的enumerate环境可以连续编号
2. 在新的chapter的开始，enumerate环境会重置计数器

起因是他想一个chapter是一份试卷，每一份试卷都是从1开始编号，问题很容易理解

群里有老师说要重设计数器，我个人并不想要这种做法，理由如下：

- 重设计数器之后要怎么做？重新做一个list出来？
- `enumitem`宏包对`enumerate`环境这么好的增幅效果，这么好的轮子就不能用了

所以基本实现的想法是去修改`enumerate`环境的定义，具体实现如下：

## 实现的思路分析

### 功能1的实现

连续编号这个功能早在`enumitem`宏包的`resume*`键（带星号的会继承`label`的样式，不带星号的只继承计数器的值，考虑到一半都会修改`label`，所以采用了带星的）就可以实现，但是喜欢偷懒的我不想要每次都要输入`resume*`，如果把`enumerate`改成自己本身自带`resume*`选项不就行了？

但是直接`renew`的话，可是我们不知道enumerate本身咋定义的，难道还要去翻过来并且重新去弄list？那也太麻烦了，而且我们也不是改定义，只是想让

```tex
\begin{enumerate}

\end{enumerate}
```

具有

```tex
\begin{enumerate}[resume*]

\end{enumerate}
```

的效果，这咋搞？

我们重新看我们的需求，其实我们并不是要修改`enumerate`本身的定义，而是要在它之上加点东西而已。

这个时候，我想到了以前看乙醇哥的一篇博客《[统计一节中的列表项数量](https://syvshc.github.io/2021-11-10-compute-total-items/)》里面有一个做法我印象深刻：复制副本，简单来说就是

1. 先把命令`\foo`复制一个副本`\fooo`出来，也就是把定义放到一个新的命令里去，怎么定义的我不关心，我只知道我把它已经存起来了，随时可以被调用

2. 用`\def`修改原命令

   ```tex
   \def\foo{
     % something you want to add
     \fooo
   }
   ```

   由于`\fooo`和`\foo`是等效的，所以就是我们在原来的基础上加了东西，这个做法有迭代的味道在里面，也有数学或物理里的“看作整体”想法在。

但是这个地方我们是环境怎么办？那我们只需粗略但不严谨地知道，一般环境`foo`的定义其实是定义了两个宏`\foo`和`\endfoo`，那我分别储存这两个宏的副本就行。

{% note warning %}

PS： 虽然不属于本文的讨论范围，但是还是插一嘴，从上面的这个可以看出，其实传统方式定义`foo`环境的话其实定义了`\foo`命令，但是如果我想`newcommand`定义`\foo`的话就会报错冲突（不出意外是`already-defined`类型的），那如果我还是想要`\foo`和`foo`环境共存怎么办？这个时候要用`xparse`宏包的`\NewDocumentEnvironment`命令来定义环境了，这样就可以共存，详细请参看这篇文章：[LaTeX3 and document environments](https://www.texdev.net/2011/01/09/latex3-and-document-environments/)

（所以说`xparse`宏包牛！这个宏包的一些定义命令的效果和方式真是颠覆我传统的想法，第一次看就惊呼牛皮）

{% endnote %}

加上一点点小细节就变成了下面的代码：

```tex
\ExplSyntaxOn
\makeatletter
% 储存副本代码
\let\@enumerate\enumerate
\let\@endenumerate\endenumerate
% 重定义enumerate环境
\RenewDocumentEnvironment{enumerate}{ O{} !b }
  {
  	% 因为chapter最开始的enumerate环境不需要resume
  	% 所以加一个LaTeX3的简单整数判断bool从句
    \int_compare:nNnTF { \theenumi } = {0}
      { \@enumerate[#1] }
      { \@enumerate[resume*, #1] }
      #2
  }
  {
    \@endenumerate
  }
\makeatother
\ExplSyntaxOff
```

PS：`xparse`宏包的使用我在我的[LaTeX3个人学习经验](https://xkwxdyy.github.io/2022/01/08/LaTeX3-learning/)文章里也有呼吁大家去读文档学习，是我在见到`hlist`环境、TikZ、LaTeX3 后震惊的第四种代码（不经想到作者都是什么神仙怪物，怎么会发明出这么牛的命令）非常推荐大家学习

功能1其实就已解决了，可以直接使用`enumerate`环境也获得`resume*`的效果而不用手动添加

### 功能2的实现

代码其实很简单

```tex
\setcounter{enumi}{0}
```



然后我们的目的就转化为：如何在每个`\chapter`命令的时候自动加载这个代码？

也就是能不能把这个代码嵌入`\chapter`命令里面？

我们仿照功能1的思路就可以得到下面的代码：

```tex
\let\ch@pter\chapter
\def\chapter{
  \setcounter{enumi}{0}    % 注意这个要放在\ch@pter的前面，放在后面会报错，我也不懂TeX，也不知道为啥
  \ch@pter
}
```

{% note danger %}

特别要注意的是`\@chapter`这个命令在LaTeX的源代码里就已经被定义了，所以用其它的副本名字就行。

（别问我怎么知道的... 我就恰巧不巧取了`\@chapter`，一开始就很纳闷为什么bug了，在乙醇哥的指点下注意到了这个

{% endnote %}

还有一种方法就是用`etoolbox`宏包的hook命令`\pretocmd`，和上面一样只能用pre放在前面，而不能用`\apptocmd`放在后面，希望知道原因的大佬能留言告知下原因

```tex
% \usepackage{etoolbox}
\pretocmd{\chapter}{\setcounter{enumi}{0}}{}{}
```

### 功能2的代码完善

上面说到设置计数器的代码只能放在`\ch@pter`的前面，经过乙醇哥的提醒，发现问题出在：`\chapter`命令是需要接收参数的，如果像上面那样的方式不做处理直接把设置计数器的代码移到`\ch@pter`后面的话，`\chapter`就会自动识别后面的代码为参数，但是本身也不是纯文本的，所以会报错。

还有一个需要注意的是，我们上面的做法没有考虑到`\chapter*`，所以现在需要把这个加上。

知道原因了就好解决了，既然会影响参数设定，那我就提前“把参数喂给`\ch@pter`”，这样他就不会往后面“找吃的”了。

用上一点点LaTeX3的cs函数和xparse的s指定（用来处理带*命令），我们便得到了下面的代码：

（有一个重要的知识：`\chapter[]{}`与`\chapter*{}`，也就是带`*`命令没有可选参数，虽然源代码是定义了两个命令分别控制，但是为了帮助记忆和理解，就相当于这里的`*`作为了`\chapter`的可选参数已经被“吃掉了”，就只剩下必选参数）

```tex
\cs_set_eq:NN \chapter:nn \chapter    % 相当于我们之前的把\chapter复制给\ch@pter一样
\cs_new:Npn \chapter_star:n #1
  {
    \chapter:nn * {#1}
  }

\RenewDocumentCommand{ \chapter }{ s O{#3} m }   % 注意#2的默认值是#3，如果为空的话目录和页眉就没有标题了
  {
    \IfBooleanTF {#1}
      { \chapter_star:n {#3} }
      { \chapter:nn [#2] {#3} }
    \setcounter{enumi}{0}
  }
```

查一下`\chapter`定义的源码知道，不能直接通过`\apptocmd`来直接处理`\chapter`，因为它不是显式定义的，而是对`\@chapter`和`\@schapter`命令的包装，所以如果`\apptpcmd`到`\chapter`的尾部的话，代码就会挡住“参数的入口”，反而被误当参数而吃掉了。但是`\@chapter`和`\@schapter`是显式定义的（也就是我们通常的`\newcommand`或`\def`定义的，对于参数怎么处理我都约定好了，apptocmd到这两个命令的话，就是我们所期待的效果，而且不会挡住“入口”，这样的话`\chapter`接收到的参数就会根据定义来分配给`\@chapter`或`\@schapter`命令。

之所以放在前面是可行的，是因为设置计数器部分的代码并没有“挡住进食口”，所以原来咋样就咋样。

{% note warning %}

注意，其实用最初的`pretocmd`或者`\def`的方法已经能够实现效果，我之所以要“大费周章”地还要研究把计数器代码放在后面，是因为这本身就是学习的过程，我不是为了完成任务而去处理这段代码，而是希望在写这段代码的过程中也能学到东西，对LaTeX的理解能够更深一点。

{% endnote %}

## 一个完整的MWE

```tex
\documentclass{book}
\usepackage{enumitem}
\usepackage{etoolbox}
\ExplSyntaxOn
\makeatletter
\let\@enumerate\enumerate
\let\@endenumerate\endenumerate

\RenewDocumentEnvironment{enumerate}{ O{} !b }
  {
    \int_compare:nNnTF { \theenumi } = {0}
      { \@enumerate[#1] }
      { \@enumerate[resume*, #1] }
      #2
  }
  {
    \@endenumerate
  }

% 下面两种方法都行
% 第一种是用etoolbox的pretocmd
% \pretocmd{\chapter}{\setcounter{enumi}{0}}{}{}

% 第二种是copy一个副本出来重定义chapter
\let\ch@pter\chapter
\cs_set_eq:NN \chapter:nn \chapter
\cs_new:Npn \chapter_star:n #1
  {
    \chapter:nn * {#1}
  }
% \def\chapter{
%   \setcounter{enumi}{0}
%   \ch@pter
% }

\RenewDocumentCommand{ \chapter }{ s O{#3} m }   % 注意#2的默认值是#3，如果为空的话目录和页眉就没有标题了
  {
    \IfBooleanTF {#1}
      { \chapter_star:n {#3} }
      { \chapter:nn [#2] {#3} }
    \setcounter{enumi}{0}
  }
\makeatother
\ExplSyntaxOff


\begin{document}

\tableofcontents

\chapter{1}

\begin{enumerate}[label = \roman*.]
  \item 1
  \item 2
\end{enumerate}

\begin{enumerate}
  \item 3
  \item 4
\end{enumerate}


\chapter{2}

\begin{enumerate}
  \item 1
  \item 2
\end{enumerate}

\begin{enumerate}
  \item 3
  \item 4
\end{enumerate}

\end{document}
```

{% gi 2 2 %}

![](https://raw.githubusercontent.com/xkwxdyy/image/main/postimage/image-hosting/enumerate-continume-numbering-within-chapter-MWE1.jpg)

![](https://raw.githubusercontent.com/xkwxdyy/image/main/postimage/image-hosting/enumerate-continume-numbering-within-chapter-MWE2.jpg)

{% endgi %}

## 相关链接

本文多处是在乙醇哥的帮助下完善的，特此感谢！

欢迎读者关注他的博客：[无锤乙醇](https://syvshc.github.io/)和Github：[syvshc](https://github.com/syvshc)

