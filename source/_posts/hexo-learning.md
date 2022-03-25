---
title: Hexo与Fluid常用设置汇总
date: 2022-01-08 10:39:58
updated: 2022-01-09 13:50
math: true
tags: [Hexo, Fluid]
excerpt: 从官网指南上选取的部分设置，方便个人需要的时候查找
---

### 使用数学公式

[在 Hexo 的 Fluid 主题中使用 LaTeX 公式](http://dongjiyinhe.com/posts/21980/)

$x^2$

$$\lim_{n \to \infty} x^n = 0$$

### 图床

[PicGo](https://picgo.github.io/PicGo-Doc/zh/guide/)

### 组图

如果想把多张图片按一定布局组合显示，你可以在 markdown 中按如下格式：

```markdown
{% gi total n1-n2-... %}
  ![](url)
  ![](url)
  ![](url)
  ![](url)
  ![](url)
{% endgi %}
```

total：图片总数量，对应中间包含的图片 url 数量
n1-n2-...：每行的图片数量，可以省略，默认单行最多 3 张图，求和必须相等于 total，否则按默认样式

### Tag 插件

#### [#](https://hexo.fluid-dev.com/docs/guide/#便签)便签

在 markdown 中加入如下的代码来使用便签：

```markdown
{% note success %}
文字 或者 `markdown` 均可
{% endnote %}

# 注意：上面第一行和第三行要单独成行
```

或者使用 HTML 形式：

```html
<p class="note note-primary">标签</p>
```

可选便签：

{% note primary %}

primary

{% endnote %}

{% note  secondary %}

secondary

{% endnote %}

{% note  success %}

success

{% endnote %}

{% note  danger %}

danger

{% endnote %}

{% note  warning %}

warning

{% endnote %}

{% note  info %}

info

{% endnote %}

{% note  light %}

light

{% endnote %}



#### [#](https://hexo.fluid-dev.com/docs/guide/#行内标签)行内标签

在 markdown 中加入如下的代码来使用 Label：

```markdown
{% label primary @text %}
```

或者使用 HTML 形式：

```html
<span class="label label-primary">Label</span>
```

可选 Label：

{% label primary @primary %}

{% label default @default %}

{% label info @info %}

{% label success @success %}

{% label warning @warning %}

{% label danger @danger %}

{% note warning %}

若使用

```markdown
{% label primary @text %}
```

那么其中的text 不能以 @ 开头

{% endnote %}

#### [#](https://hexo.fluid-dev.com/docs/guide/#勾选框)勾选框

在 markdown 中加入如下的代码来使用 Checkbox：

```markdown
{% cb text, checked?, incline? %}
```

text：显示的文字
checked：默认是否已勾选，默认 false
incline: 是否内联（可以理解为后面的文字是否换行），默认 false

示例：

{% cb 普通示例 %}

{% cb 默认选中, true %}

{% cb 内联示例, false, true %} 后面文字不换行

{% cb false %} 也可以只传入一个参数，文字写在后边（这样不支持外联）



#### [#](https://hexo.fluid-dev.com/docs/guide/#按钮)按钮

你可以在 markdown 中加入如下的代码来使用 Button：

```markdown
{% btn url, text, title %}
```

或者使用 HTML 形式：

```html
<a class="btn" href="url" title="title">text</a>
```

url：跳转链接
text：显示的文字
title：鼠标悬停时显示的文字（可选）

[text](javascript:;)

### 社交页图标

在**主题配置**中设置：

```yaml
about:
  icons:
    - { class: 'iconfont icon-github-fill', link: 'https://github.com', tip: 'GitHub' }
    - { class: 'iconfont icon-douban-fill', link: 'https://douban.com', tip: '豆瓣' }
    - { class: 'iconfont icon-wechat-fill', qrcode: '/img/favicon.png' }
```

- `class`: 图标的 css class，主题内置图标详见[这里](https://hexo.fluid-dev.com/docs/icon/)
- `link`: 跳转链接
- `tip`: 鼠标悬浮在图标上显示的提示文字
- `qrcode`: 二维码图片，当使用此字段后，点击不再跳转，而是悬浮二维码

### 其它一些小细节

#### 关于苹果的Mac搭建博客中需要注意的

- `npm install -g hexo-cli`安装如果不成功，要使用管理员权限，输入`sudo -s`回车输密码，然后再安装

#### 如何链接邮箱

```markdown
# 注意要加一个mailto:
[E-mail](mailto: kangweixia_xdyy@163.com)
```

## 参考

- [Fluid配置指南](https://hexo.fluid-dev.com/docs/guide/)

- [Front-matter](https://hexo.io/zh-cn/docs/front-matter)
- 博客的搭建步骤主要参考[乙醇写的文章](https://syvshc.github.io/2021-03-03-build-github-io/)

