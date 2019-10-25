## 对Web标准的理解
结构:html
表现:css
行为:对象模型、ECMAScript
我们使用Web标准去编写代码目的是write less，do more。我们的代码更加规范，更加合乎标准，我们的页面就更容易被搜索引擎抓取收录。

## 浏览器内核
浏览器的内核是分为两个部分的，一是渲染引擎，另一个是JS引擎。现在JS引擎比较独立，内核更加倾向于说渲染引擎。
目前市面上常见的浏览器内核可以分为这四种： Trident（IE）、Gecko（火狐）、Blink（Chrome、Opera）、Webkit（Safari）

KTHML, Opera -> Webkit -> Blink (Chome, Opera, Safari)
Netscape -> Firefox
Trident or whatever -> IE 

## 渲染原理
渲染过程
https://juejin.im/entry/59e1d31f51882578c3411c77

1. 处理 HTML 标记并构建 DOM 树。
2. 处理 CSS 标记并构建 CSSOM 树。
3. 将 DOM 与 CSSOM 合并成一个渲染树 Render Tree。
4. 根据渲染树来布局，以计算每个节点的几何信息。
5. 将各个节点绘制到屏幕上。

Render Tree会把一些不可见的结点去除掉，所以 Render Tree的结构并不一定等于DOM树

阻塞渲染：CSS 与 JavaScript

渲染树（Render-Tree）的关键渲染路径中，要求同时具有 DOM 和 CSSOM，之后才会构建渲染树。即，HTML 和 CSS 都是阻塞渲染的资源。

现代浏览器总是并行加载资源。例如，当 HTML 解析器（HTML Parser）被脚本阻塞时，解析器虽然会停止构建 DOM，但仍会识别该脚本后面的资源，并进行预加载。

默认情况下，CSS 被视为阻塞渲染的资源，这意味着浏览器将不会渲染任何已处理的内容，直至 CSSOM 构建完毕。

当浏览器遇到一个 script 标记时，DOM 构建将暂停，直至脚本完成执行。

CSSOM 构建时，JavaScript 执行将暂停，直至 CSSOM 就绪。

实际使用时，可以遵循下面两个原则：
1. CSS 优先：引入顺序上，CSS 资源先于 JavaScript 资源。
2. JavaScript 应尽量少影响 DOM 的构建。

async 与 defer 属性对于 inline-script 都是无效的

defer 属性表示延迟执行引入的 JavaScript，即这段 JavaScript 加载时 HTML 并未停止解析，这两个过程是并行的。整个 document 解析完毕且 defer-script 也加载完成之后（这两件事情的顺序无关），会执行所有由 defer-script 加载的 JavaScript 代码，然后触发 DOMContentLoaded 事件。

async 属性表示异步执行引入的 JavaScript，与 defer 的区别在于，如果已经加载好，就会开始执行——无论此刻是 HTML 解析阶段还是 DOMContentLoaded 触发之后。需要注意的是，这种方式加载的 JavaScript 依然会阻塞 load 事件。


- Repaint ——屏幕的一部分要重画，比如某个CSS的背景色变了。但是元素的几何尺寸没有变。
- Reflow ——意味着元件的几何尺寸变了，我们需要重新验证并计算Render Tree。

reflow有如下的几个原因：
- Initial。网页初始化的时候。
- Incremental。一些Javascript在操作DOM Tree时。
- Resize。其些元件的尺寸变了。
- StyleChange。如果CSS的属性发生变化了。
- Dirty。几个Incremental的reflow发生在同一个frame的子树上。

## 兼容性
https://juejin.im/post/5b3da006e51d4518f140edb2
浏览器的兼容性无非还是样式兼容性（css），交互兼容性（javascript），浏览器 hack 三个方面。

css：
- 重置样式：因为历史原因，不同的浏览器样式存在差异，可以定制自己的 reset.css，
- 前缀：CSS3还没有成为真正的标准时，浏览器厂商就开始支持部分属性的使用了。CSS3样式语法还存在波动时，浏览器厂商提供了针对浏览器的前缀，直到现在还是有部分的属性需要加上浏览器前缀。IE浏览器	-ms；Firefox	-moz；Opera	-o；Chrome和Safari	-webkit

javascript：
- 事件兼容的问题，我们通常需要会封装一个适配器的方法，过滤事件句柄绑定、移除、冒泡阻止以及默认事件行为处理
- new Date()构造函数使用，'2018-07-05'是无法被各个浏览器中，使用new Date(str)来正确生成日期对象的。 正确的用法是'2018/07/05'.
- 获取 scrollTop 通过 document.documentElement.scrollTop 兼容非chrome浏览器

浏览器 hack：
- 快速判断 IE 浏览器版本
```
 <!--[if IE 8]> ie8 <![endif]-->
 
 <!--[if IE 9]> 骚气的 ie9 浏览器 <![endif]-->
```
- 判断是否是 Safari 浏览器
```
 /* Safari */
 var isSafari = /a/.__proto__=='//';
```
- 判断是否是 Chrome 浏览器
```
 /* Chrome */
 var isChrome = Boolean(window.chrome);
```

## hack
CSS hack是通过在CSS样式中加入一些特殊的符号，让不同的浏览器识别不同的符号。简单的说，CSS hack的目的就是使你的CSS代码兼容不同的浏览器。当然，我们也可以反过来利用CSS hack为不同版本的浏览器定制编写不同的CSS效果。

CSS Hack常见的有三种形式：CSS属性Hack、CSS选择符Hack以及IE条件注释Hack。

CSS hack是因为现有浏览器对标准的解析不同，为了兼容各浏览器，所采用的一种补救方法。

写CSS hack需要遵循以下三条原则：
- 有效： 能够通过 Web 标准的验证
- 只针对太古老的/不再开发的/已被抛弃的浏览器， 而不是目前的主流浏览器
- 代码要丑陋。让人记住这是一个不得已而为之的 Hack, 时刻记住要想办法去掉它。

## 盒模型
盒模型分为IE盒模型和W3C标准盒模型。
1. W3C 标准盒模型：
属性width,height只包含内容content，不包含border和padding。

2. IE 盒模型：
属性width,height包含border和padding，指的是content+padding+border。

box-sizing：content-box（标准盒模型）、border-box（IE盒模型）

我们在编写页面代码时应尽量使用标准的W3C模型(需在页面中声明DOCTYPE类型)，这样可以避免多个浏览器对同一页面的不兼容。

外边距重叠：当两个垂直外边距相遇时，他们将形成一个外边距，合并后的外边距高度等于两个发生合并的外边距的高度中的较大者。注意：只有普通文档流中块框的垂直外边距才会发生外边距合并，行内框、浮动框或绝对定位之间的外边距不会合并。

BFC的原理（渲染规则）

https://segmentfault.com/a/1190000013069516

BFC元素垂直方向的边距会发生重叠。属于不同BFC外边距不会发生重叠
BFC的区域不会与浮动元素的布局重叠。
BFC元素是一个独立的容器，外面的元素不会影响里面的元素。里面的元素也不会影响外面的元素。
计算BFC高度的时候，浮动元素也会参与计算(清除浮动)

如何创建BFC

overflow不为visible;
float的值不为none；
position的值不为static或relative；
display属性为inline-blocks,table,table-cell,table-caption,flex,inline-flex;

https://www.cnblogs.com/fsjohnhuang/p/5259121.html

## CSS预处理器
解决CSS的问题
- 语法不够强大，比如无法嵌套书写导致模块化开发中需要书写很多重复的选择器；
- 没有变量和合理的样式复用机制，使得逻辑上相关的属性值必须以字面量的形式重复输出，导致难以维护。

Sass、Less 和 Stylus 

## CSS Modules

解决的问题
- 全局样式冲突
- 嵌套层次过深的选择器
- 代码的冗余

## Flex

父容器：display: flex

主轴方向 flex-direction ：row | row-reverse | column | column-reverse;

换行方式 flex-wrap：nowrap | wrap | wrap-reverse;

主轴对齐方式 justify-content： flex-start | flex-end | center | space-between | space-around;

交叉轴对齐方式 align-items: flex-start | flex-end | center | baseline | stretch;

多根轴线的对齐方式  align-content: flex-start | flex-end | center | space-between | space-around | stretch;

子元素

排列顺序 order：数值越小，排列越靠前，默认为0。

放大比例 flex-grow：默认为0，即如果存在剩余空间，也不放大。所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

缩小比例 flex-shrink：默认为1，即如果空间不足，该项目将缩小。如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。

项目占据的主轴空间 flex-basis：属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。

单个项目的对齐方式  align-self： 可覆盖align-items属性。

## css 权重规则

- !important 优先级最高，但也会被权重高的important所覆盖
- 行内样式总会覆盖外部样式表的任何样式(除了!important)
- 单独使用一个选择器的时候，不能跨等级使css规则生效
- 如果两个权重不同的选择器作用在同一元素上，权重值高的css规则生效
- 如果两个相同权重的选择器作用在同一元素上：以后面出现的选择器为最后规则.
- 权重相同时，与元素距离近的选择器生效
