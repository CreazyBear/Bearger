# CSS



## 盒子模型(Box Model)

<img src="CSS.assets/box-model.gif" alt="img"  />

### 不同部分的说明：

- **Margin(外边距)** - 清除边框外的区域，外边距是透明的。
- **Border(边框)** - 围绕在内边距和内容外的边框。
- **Padding(内边距)** - 清除内容周围的区域，内边距是透明的。
- **Content(内容)** - 盒子的内容，显示文本和图像。

### 宽高

* 当指定一个 CSS 元素的宽度和高度属性时，只是设置内容区域的宽度和高度



## Position(定位)

元素可以使用的顶部，底部，左侧和右侧属性定位。然而，这些属性无法工作，除非是先设定position属性。他们也有不同的工作方式，这取决于定位方法。

- [static](https://www.runoob.com/css/css-positioning.html#position-static):HTML 元素的默认值，即没有定位，遵循正常的文档流对象。静态定位的元素不会受到 top, bottom, left, right影响。
- [relative](https://www.runoob.com/css/css-positioning.html#position-relative):相对定位元素的定位是相对其正常位置。
- [fixed](https://www.runoob.com/css/css-positioning.html#position-fixed):元素的位置相对于浏览器窗口是固定位置。即使窗口是滚动的它也不会移动
- [absolute](https://www.runoob.com/css/css-positioning.html#position-absolute):绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于`<html>`
- [sticky](https://www.runoob.com/css/css-positioning.html#position-sticky):滚动粘附到一个位置的效果



## Flex Box

https://www.runoob.com/css3/css3-flexbox.html



























