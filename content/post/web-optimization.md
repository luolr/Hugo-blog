+++
categories = ["前端优化"]
date = "2017-04-02"
description = "什么是前端性能优化，前端性能优化要做些什么"
tags = ["前端优化"]
title = "前端优化归纳总结"
author = "罗椋仁"
+++

>作为一名合格的web开发人员，不应该仅仅满足于功能的实现，前端性能的优化也是我们必须要关注的问题。我们都不希望自己辛辛苦苦写成的代码，却因为优化不到位的问题，带给用户不好的体验。为此，我们需要做不少的工作。

对于前端性能优化的讨论，一直都不是一个过时的话题，前有雅虎的35条黄金法则，后有O’Reilly 出版的两本性能优化圣经《高性﻿能网站建设指南》、《高性能网站建设进阶指南》，这些都将会是我们在探索性能优化道路上的指路明灯。那么，我们首先来看看雅虎的优化法则吧，我选取了其中的部分。
<!--more-->

### [雅虎 17 条性能优化原则及其分类](https://developer.yahoo.com/performance/rules.html)：

| 优化方向 | 优化手段  |
| ------ | ------ |
| 请求数量 | 合并脚本和样式表，CSS Sprites，拆分初始化负载，划分主域 |
| 请求带宽 | 开启 GZip，精简 JavaScript，移除重复脚本，图像优化 |
| 缓存利用 | 使用 CDN，使用外部 JavaScript 和 CSS，添加 Expires 头，减少 DNS 查找，配置 ETag，使 AjaX 可缓存 |
| 页面结构 | 将样式表放在顶部，将脚本放在底部，尽早刷新文档的输出，延迟加载 JavaScript |
| 代码校验 | 避免 CSS 表达式，避免重定向 |

### 优化原则细则：

+ 请求数量：
  - 合并脚本和样式表：通过将脚本和样式表合并的方式，可以有效的减少http请求的次数，从而减少DNS请求所耗费的时间和对服务器的压力。
  - `CSS Sprites`：即CSS 雪碧图，通过将众多细小的图片整合成一个文件，从而减少http请求次数。 
  - 拆分初始化负载：对于页面中一开始加载时不需要执行的资源，可以将其分离出来等到需要时再加载。
  - 划分主域：众所周知，在 HTTP/1.x 协议中「浏览器客户端在同一时间，针对同一域名下的请求有一定数量限制。超过限制数目的请求会被阻塞」，而不同浏览器对该限制的数目也不尽相同，划分主域、配置静态资源专用域，目的就是变相的解决浏览器针对同一域名的请求限制阻塞问题。但不易过多，否则 DNS 的查询时间也是个问题。
+ 请求宽带：
  - 开启`GZip`：服务端启用 Gzip 可以有效的减少数据传输量，Gzip 对于基于文本的文件（CSS、JavaScript、HTML）压缩效果最好，所有现代浏览器都支持 Gzip 压缩并将自动请求该压缩
精简JavaScript，移除重复脚本：这个就不必细说了，减少重复的代码，避免无意义的脚本执行。
  - 图像优化：对于要上传的图片，尽量保证该图片已经是经过压缩优化过的。
+ 缓存利用：
  - 使用`CDN`：利用内容分发网络能够有效的减少资源响应时间，提升用户体验。
使用外部JavaScript和CSS：浏览器可以将外部的 JavaScript 和 CSS 文件进行缓存，而如果将其写成内联样式，嵌入至 HTML 文档里，那么每次请求该文档时它们都会被下载。而且如果多个页面都用到了相同的脚本，那么使用浏览器缓存的外部JavaScript和CSS必然是更好的选择。
  - 添加 `Expires` 头：对于一些不怎么改变的静态资源，可以设置文件头过期时间Expires的值为“Never expire”（永不过期），而对于一些动态的资源，可以使用恰当的Cache-Control文件头来帮助浏览器进行选择性的请求，通过这样的方式，可以有效的利用浏览器的缓存机制来减少HTTP请求的大小和次数以加快页面访问速度。
  - 减少 `DNS` 查找：由于DNS的解析需要花费一定的时间，减少DNS查找次数可以节省响应时间，减少主机名的数量可以减少DNS查找次数。
  - 配置 `ETag`：服务端配置 ETag 是一种有效的 Web 缓存验证机制，并且允许客户端进行缓存协商。这就使得缓存变得更加高效，而且节省带宽。如果资源的内容没有发生改变，服务器就不需要发送一个完整的响应。ETag 也可用于乐观并发控制，作为一种防止资源同步更新而相互覆盖的方法。
  - 使 AjaX 可缓存：Ajax缓存和HTTP缓存效果相同，可以通过设置Expires、Last-Modified、Cache-Control 等HTTP响应头的方式实现，具体的实现难度偏高。
+ 页面结构：
  - 将样式表放在顶部，将脚本放在底部：Js的下载以及执行会阻塞DOM与CSSOM的下载与构建，因此将样式表放在body的顶部，而将脚本放在底部，用于优先加载html和css。<br>「推荐阅读：[渲染树构建、布局及绘制](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction?hl=zh-cn) 」
  - 延迟加载 JavaScript：默认情况下，JavaScript 执行会阻塞解析器：当浏览器在文档中遇到一个 script，它必须暂停 DOM 构建，移交控制权给 JavaScript 运行时，让脚本先执行，然后才继续处理 DOM。为了优先构建渲染树，有时候需要编写额外的代码来延迟脚本的执行<br>「推荐阅读：[如何延迟加载JS](http://web.jobbole.com/82317/) 」
+ 代码校验：
  - 避免 CSS 表达式：CSS 表达式是动态设置 CSS 属性的强大（但危险）方法。表达式的问题就在于它的计算频率要比我们想象的多。不仅仅是在页面显示和缩放时，就是在页面滚动、乃至移动鼠标时都会要重新计算一次，可能会对页面的性能产生影响。因此CSS表达式的使用需要慎重。
  - 避免重定向：当页面发生了重定向，在新的HTMl文档到达之前，当前页面的资源不会被加载，可能呈现空白页面。
<br>
在了解Yahoo军规后，想必对web性能优化有了一个大致的了解。当然，除了军规之外，也有不少其他要注意的细节。

### JavaScript 优化 ——— Repaint 和 Reflow

  首先澄清两个概念——Repaint 和 Reflow：Repaint 也叫 Redraw，它指的是一种不会影响当前 DOM 的结构和布局的一种重绘动作。如下动作会产生 Repaint 动作：
  
  - 不可见到可见（visibility 样式属性）
  - 颜色或图片变化（background, border-color, color 样式属性）
  - 不改变页面元素大小，形状和位置，但改变其外观的变化

  Reflow 比起 Repaint 来讲就是一种更加显著的变化了。它主要发生在 DOM 树被操作的时候，任何改变 DOM 的结构和布局都会产生 Reflow。但一个元素的 Reflow 操作发生时，它的所有父元素和子元素都会放生 Reflow，最后 Reflow 必然会导致 Repaint 的产生。举例说明，如下动作会产生 Reflow 动作：
    
  + 浏览器窗口的变化
  + DOM 节点的添加删除操作
  + 字体大小的改变
  + 添加、删除Class，设置style样式
  + 内容改变，包括输入框中的文字输入
    
  实际上，Reflow 比起 Repaint 会消耗更多的资源，因此，我们应该尽量减少 Reflow 的发生，或者将其转化为只会触发 Repaint 操作的代码。

    var tipBox = document.createElement('div');
    document.body.appendChild('tipBox'); //reflow
    var tip1 = document.createElement('div');
    var tip2 = document.createElement('div');
    tipBox.appendChild(tip1); //reflow
    tipBox.appendChild(tip2); //reflow  
    
  如上代码会产生三次reflow，优化后的代码如下：
    
    var tipBox = document.createElement('div');
    	  tip1 = document.createElement('div');
    	  tip2 = document.createElement('div');
    tipBox.appendChild(tip1);
    tipBox.appendChild(tip2);
    document.body.appendChild('tipBox'); //reflow 
    ```
经过优化，只会产生一次reflow，减少了不必要的开销。

#### HTML 优化

1. 使用合适的的元素可保证代码的易读性
  - 当网站支持HTML5时，使用`<header>`，`<footer>`，`<nav>`等语义化标签，可以很清晰的体现出该内容的功能。
  - 使用`<h1>(<h2>,<h3>…)`表示标题，`<ul>`或`<ol>`实现列表。
  - 使用`<em>`和`<strong>`标签替代`<i>`和`<b>`标签。
  - 使用`<label>`元素，可以增强使用、、用户的使用体验。

2. 避免不合理的布局
    - 使用`<p>`元素修饰文本，而不是布局；默认`<p>`是自动提供边缘，而且其他样式也是浏览器默认提供的。
    - 避免使用`<br>`分行，可以使用`block`元素或CSS显示属性来代替。
    - 避免使用`<hr>`来添加水平线，可使用CSS的border-bottom 来代替。
    - 尽可能的避免滥用`div`。
    - 尽量少用Tables来布局。
    - 可以多多使用Flex Box。
    - 内联元素中不可嵌套块级元素。

#### CSS 优化
一般来说，页面渲染所需的时间将取决于文档大小、应用的样式，以及运行文档的设备：文档越大，浏览器需要完成的工作就越多；样式越复杂，绘制需要的时间就越长。虽然现在各大浏览器的渲染引擎经过无数版本的优化，都已经十分强大，CSS对于页面渲染的性能影响基本已经很小。不过，不放弃任何可能的优化，不正是是我们广大程序员的一大优点嘛。

+ 对CSS进行合并、压缩
+ 不使用CSS表达式
+ 移除空的CSS规则
+ 不滥用Float
+ 不声明过多的Font-size
+ 值为0时不需要任何单位
+ 选用合适的CSS选择器：
    对于CSS选择器的使用建议遵循以下原则：使用时能具体则具体，不过要注意避免层级过深，对于一些相似的样式可以将其中相同的部分抽取出来单独作为一个样式。
    
#### 其他优化

除了以上优化之外，许多大公司（例如腾讯）会选择对网络以及服务器端进行优化，感兴趣的可以Google相关资料。
「推荐阅读：[16毫秒的优化](http://velocity.oreilly.com.cn/2013/ppts/16_ms_optimization--web_front-end_performance_optimization.pdf) 」

#### 参考资料

+ [前端工程与性能优化](http://fex.baidu.com/blog/2014/03/fis-optimize/)
+ [移动H5前端性能优化指南](https://isux.tencent.com/h5-performance.html)
+ [你不可不知的HTML优化技巧](http://www.imooc.com/article/4307)
