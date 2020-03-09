# 块格式化上下文（BFC）

**块格式化上下文（Block Formatting Context，BFC）**是 Web 页面的可视化 CSS 渲染的一部分，是块盒子的布局过程发生的区域，也是**浮动元素**与其他元素交互的区域。

下列方式会创建**块格式化上下文**：
* 根元素 html
* float 不为 none 的浮动元素
* 绝对定位元素（即元素的 position 为 absolute 或 fixed）
* 行内块元素（即元素的 display 为 inline-block）
* 表格单元格（即元素的 display 为 table-cell，HTML 表格单元格默认为该值）
* 表格标题（即元素的 display 为 table-caption，HTML 表格标题默认为该值）
* 匿名表格单元格元素（即元素的 display为 table、table-row、 table-row-group、table-header-group、table-footer-group（分别是 HTML table、row、tbody、thead、tfoot 的默认属性）或 inline-table）
* overflow 不为 visible 的块元素
* display 为 flow-root 的元素
* contain 为 layout、content 或 paint 的元素
* 弹性元素（即元素 display 为 flex 或 inline-flex 的直接子元素）
* 多列容器（即元素 column-count 或 column-width 不为 auto，包括 column-count 为 1）
* column-span 为 all 的元素始终会创建一个新的 BFC，即使该元素没有包裹在一个多列容器中。

> **注意**：**块格式化上下文**包含创建它的元素内部的所有内容。

## 块格式化上下文（BFC）的重要性

**块格式化上下文**对**浮动定位**与**清除浮动**都很重要。浮动定位和清除浮动时只会应用于同一个 BFC 内的元素。浮动不会影响其它 BFC 中元素的布局，而清除浮动只能清除同一 BFC 中在它前面的元素的浮动。**外边距合并**也只会发生在属于同一 BFC 的块级元素之间。

## 块格式化上下文（BFC）的应用

### 让浮动内容和周围的内容等高

在下面的例子中，我们让 div 元素浮动。div 里的内容现在已经在浮动元素周围浮动起来了。由于浮动的元素比它旁边的元素 p 高，所以 div 穿出了浮动。即浮动脱离了文档流，所以 .box 元素的 background 仅仅包含了内容，不包含浮动。
```html
<style>
  .box {
    background: red;
  }

  .float {
    float: left;
    height: 100px;
    background: green;
  }
</style>

<div class="box">
    <div class="float">I am a floated box!</div>
    <p>I am content inside the container.</p>
</div>
```
显然，这不是我们想要的。

为了解决上述问题，创建一个会包含这个浮动元素的 BFC。通常的做法是设置父元素 overflow: auto 或者设置其他的非默认的 overflow: visible 的值。但是 overflow 可能会引发一些副作用，比如滚动条或者一些剪切的阴影。因此，创建无副作用的 BFC 才是正道，于是 display: flow-root 就派上用场了，display: flow-root
 是什么？ display: flow-root 就是一个专为创建无副作用 BFC 而生的一个 CSS 规则。
 
在父级块中使用 display: flow-root 创建新的 BFC：
```css
.box {
  display: flow-root;
  background: red;
}
```
现在，.box 元素可以包含浮动元素。

> **注意**：使用 overflow 来创建一个新的 BFC，是因为 overflow 属性告诉浏览器你想要怎样处理溢出的内容。当你使用这个属性只是为了创建 BFC 的时候，你可能会发现一些不想要的问题，比如滚动条或者一些剪切的阴影，需要注意。应优先考虑使用 display: flow-root 创建一个无副作用的 BFC。

### 避免两个相邻元素之间的外边距合并

创建新的 BFC 避免两个相邻 <div> 之间的外边距合并问题。

例如：
```html
<style>
div:nth-child(1) {
  margin-bottom: 13px;
}

div:nth-child(2) {
  margin-top: 87px;
}
</style>
 
<div>下边界范围会...</div>
<div>...会跟这个元素的上边界范围重叠。</div>
```
这个例子中，上下两个 div 元素的外边距会合并为 87px。

解决方法是，给任意一个 div 创建一个包装器元素 .wrapper，并在包装器元素 .wrapper 内创建 BFC。
```html
<style>
  div.top {
    margin-bottom: 13px;
  }

  .wrapper {
    display: flow-root;
  }

  .wrapper div {
    margin-top: 87px;
  }
</style>

<div class="top">下边界范围会...</div>
<div class="wrapper">
  <div>...会跟这个元素的上边界范围重叠。</div>
</div>
```
