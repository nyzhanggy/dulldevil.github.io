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