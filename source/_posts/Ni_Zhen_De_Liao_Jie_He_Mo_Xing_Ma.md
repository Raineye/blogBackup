layout:     post
author:     Rainey
title:      你真的了解盒模型吗？
date:       2016-07-02 16:24:54
catalog:    true
tags:
    - 前端开发
---


其实在文档解析过程中每个元素都会被描述为一个盒模型，然后一个盒子套进另外一个盒子，又会按照某种神秘的规则摆放，最后才形成了井井有条的页面。

<!-- more -->

# 简单盒模型

通常情况下，被更多人认知的盒模型就是简单盒模型，简单盒模型在早期IE时期有一种怪异模式下的解析方式，也就是现在的`border-box`，后来W3C制定的标准以后规定在标准解析模式下使用标准盒模型(`content-box`)。

然后在CSS3推出的时候，增加了一个属性，也就是`box-sizing`，然后在查资料的时候在知乎看到一个段子，分享一下，哈哈。

> IE：我觉得盒模型应该是这样的，blahblah。

> W3C：明显应该是这样的才对，blahblah。

> 结果是 IE 在怪异模式下用了「不标准」的盒模型，标准模式下用了「标准」的盒模型。
围观群众：听说 IE 的盒模型不标准。

> ……多年过去……

> W3C：感觉还是 IE 的那个模型比较好。但我们已经回不去了……算了加个属性支持一下 IE 那种模式吧。

> `box-sizing` 这货就是用来擦屁股的，我从来没见过有人用 `padding-box` 的……

> 作者：顾轶灵

> 链接：https://www.zhihu.com/question/25509268/answer/30949718

> 来源：知乎

> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


所以在当前W3C标准中盒模型是可以通过`box-sizing`自由的进行切换的。然后到底这两种模式有什么区别呢，先看一张从chrome开发者工具中截的图。

![](http://7xpp66.com1.z0.glb.clouddn.com/C7C7F2CF-9F8F-46F2-BC66-11FED054A114.png)


- content-box（标准盒模型）

	- width = 内容的宽度

	- height = 内容的高度

- border-box（怪异盒模型）

	- width = border + padding + 内容的宽度

	- height = border + padding + 内容的高度


# 视觉格式化模型(visual formatting model)

> CSS 视觉格式化模型(visual formatting model)是用来处理文档并将它显示在视觉媒体上的机制。

简单盒模型需要进一步加工才能成为真正可以进行格式化的盒子，处理过程主要取决于一个css属性：`display`。

## 块级盒、快容器盒、块盒、匿名块盒

当`display`的值为`block` `list-item` `table` 时,这些盒子会被标记为块级元素，在竖直方向一个接一个的排列，同时参与块级格式化上下文，

每一个块级元素都至少生成一个块级盒，也可能是一个块容器盒，块容器盒所描述的是它和它的子元素之间的表现方式，块级盒所描述的是它与兄弟元素的表现方式。

一个块容器盒只包含其他块级盒，或生成一个行内格式化上下文来只包含行内盒。当然你也许见过一段代码中某一个块容器盒同时包含行内盒和块级盒的情况，但实质上在这种情况下产生了一种新的匿名块盒来解决这个问题。

先来看一段代码：

```html
  <div class="blockContainerBox" style="background:red;height:100px;">
    <div class="blockLevelBox" style="background:blue;height:20px;width:20px;">

    </div>
    <div class="inlineBlockBox" style="background:green;height:20px;width:20px;display:inline-block;">

    </div>
    <div class="inlineBlockBox" style="background:green;height:20px;width:20px;display:inline-block;">

    </div>
  </div>

```

首先blockContainerBox是一个块级盒同时也是一个块容器盒，这种情况下我们会把它称为块盒。ok，根据刚才的说法它只能包含块级盒或者生产一个行内格式化上下文来包含行内盒，而此时的代码不仅有blockLevelBox也有inlineBlockBox。此时浏览器会生成一个匿名块盒来包裹两个inlineBlockBox来生成一个行内格式化上下文。

## 行内级盒、行内盒、匿名行内盒

当 `display` 的值为 `inline` `inline-block` `inline-table` 时，这些盒子将被标记为行内级元素，在水平方向一个接一个排列，如果宽度不够排列将生成多行。

当行内级盒参与行内格式化上下文以后被称为行内盒。

> 所有display:inline 的非替换元素生成的盒是行内盒。而不参与生成行内格式化上下文的行内级盒称为原子行内级盒(atomic inline-level boxes)。

匿名行内盒盒匿名块盒的原理类似，都是浏览器自动生成的补充性盒，简单看一段代码理解一下匿名行内盒是如何产生的。




```html
<p>
   台湾是<strong>中国不可分割的领土</strong>
</p>
```
此时“台湾是”就生成了一个匿名行内盒，然后与strong元素一起处于行内格式化上下文以后的p元素下，水平排列。


## 文档流

这部分内容在#6已经讲过，在此不再赘述。

<!-- ## 其他

当盒子制造好以后，如果不按照一定的规则进行摆放，那只能杂乱无章的堆砌起来。之所以页面会井井有条，原因就在于这个神秘的规则，也就是视觉格式化。 -->


# 这些盒子如何摆放呢？

生成了这么多的盒子，那么我们需要把它们按照一定的规则摆放好，然后才能把整齐的盒子渲染成页面给用户看。盒子在摆放过程中，会通过盒的类型生成格式化上下文。也就是Block Formatting Context（块级格式化上下文)和Inline Formatting Context(行内格式化上下文)。

## Block Formatting Context

创建了BFC的元素规定了内部的块级盒如何布局，并且使该盒子在页面上形成一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然。


> 下列情况将创建一个块格式化上下文：

> - 根元素或其它包含它的元素
- 浮动 (元素的 float 不为 none)
- 绝对定位元素 (元素的 position 为 absolute 或 fixed)
- 行内块 inline-blocks (元素的 display: inline-block)
- 表格单元格 (元素的 display: table-cell，HTML表格单元格默认属性)
- 表格标题 (元素的 display: table-caption, HTML表格标题默认属性)
- overflow 的值不为 visible的元素
- 弹性盒子 flex boxes (元素的 display: flex 或 inline-flex)

> 摘自 MDN

> 链接：https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context

在一个块级格式化上下文的元素内部盒子

- 内部的Box会在垂直方向，从顶部开始一个接一个地放置。
- Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生叠加
- 每个元素的margin 子元素盒子的的左边， 与包含块border 父元素盒的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
- BFC的区域不会与float box叠加。
- 计算BFC的高度时，浮动元素也参与计算。

### margin塌陷的解释

所谓的塌陷其实是两个BFC的相邻盒或者父子盒相互作用时产生的。

在形成BFC的两个盒子会取两个盒子相邻边的最大margin作为相邻边的共用maring。

### 元素浮动时BFC的应用

浮动元素会从正常文档流中删除掉，这也是为什么其他正常元素会看不见浮动的原因，也是为什么有父级塌陷的原因（因为在正常父级看来，浮动元素不在正常流中父级就什么都没有了，所以高度为0）。

清楚浮动一种方法是使用overflow：auto/hidden，使用后整体形成了bfc，相当于清除了浮动。

简单看一段代码：

```
<div style="width:100px;">
    <div style="background:red;
		         width:50px;
		         height:50px;
		         float:left;"></div>
    <div class="aroundBox">我要环绕那个红色块我要环绕那个红色块我要环绕那个红色块我要环绕那个红色块我要环绕那个红色块我要环绕那个红色块我要环绕那个红色块我要环绕那个红色块我要环绕那个红色块我要环绕那个红色块</div>
</div>
```

---

<div style="width:100px;background:blue;color:white;"><div style="background:red;width:50px;height:50px;float:left;"></div><div class="aroundBox">我要环绕那个红色块我要环绕那个红色块</div></div>

---

因为红色块浮动所以产生了文字环绕的样式，如果说我们让aroundBox产生一个BFC，会变成什么样子呢？现在我们给aroundBox增加`overflow:hidden`

---

<div style="width:100px;background:blue;color:white;"><div style="background:red;width:50px;height:50px;float:left;"></div><div style="overflow:hidden;">我要环绕那个红色块我要环绕那个红色块</div></div>

---

红色块因为浮动产生了一个BFC，aroundBox因为`overflow:hidden`产生了一个BFC，因为BFC的区域不会与float box叠加。所以有了这样的变化。


## Inline Formatting Context

相对于块格式化上下文，在行内格式化上下文中，盒子一个接一个地水平排列，起点是包含块的顶部。

水平方向上的 margin，border 和 padding 在盒子之间得到保留。

盒子在垂直方向上可以以不同的方式对齐：它们的顶部或底部对齐，或根据其中文字的基线对齐。

对于非替换元素，比如a，span等标签可以设置水平方向上的margin但是无法设置垂直方向的margin，至于border和padding，垂直方向可以设置，但是当border-top和padding-top到达页面顶部后就不在增加了。

而对于替换元素比如input、img等标签，是可以正常使用margin、border、padding的。


--

待续。。
