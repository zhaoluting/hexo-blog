---
title: 性能优化专题-理论篇
date: 2018-07-22 22:19:41
tags: performance
categories: 性能优化
---

{% cq %}
**Fast is better than slow.**   – [Ten things we know to be true](https://www.jianshu.com/p/162454cf2241)
{% endcq %}

<!--more-->

# 优化标准-RAIL模型
## TL;DR 
- 以用户为中心；最终目标不是让您的网站在任何特定设备上都能运行很快，而是使用户满意。
- 立即响应用户；在 100 毫秒以内确认用户输入。
- 设置动画或滚动时，在 10 毫秒以内生成帧。
- 最大程度增加主线程的空闲时间。
- 持续吸引用户；在 1000 毫秒以内呈现交互内容。

要根据 RAIL 指标评估您的网站，请使用 [Chrome DevTools performance](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/) 工具记录用户操作。然后根据这些关键 RAIL 指标检查记录时间。

## 延迟与用户反应
让用户成为您的性能工作的中心。用户花在网站上的大多数时间不是等待加载，而是在使用时等待响应。了解用户如何评价性能延迟：
<style>
table th:first-of-type {
    width: 150px;
}
</style>

延迟时间 | 用户体验
---|---
0 ~ 16 ms | 用户可以感知每秒渲染 60 帧的平滑动画转场，也就是每帧 16 毫秒。<br/>留给应用大约 10 毫秒的时间来生成一帧。
0 ~ 100 ms | 在此时间窗口内响应用户操作，他们会觉得可以立即获得结果。时间再长，操作与反应之间的连接就会中断。
100 ~ 300 ms | 轻微可觉察的延迟。
300 ~ 1000 ms | 延迟感觉像是任务自然和持续发展的一部分(用户觉得这是正常流，但不会觉得快)。
1000+ ms (>1s) | 用户的注意力将离开他们正在执行的任务。
10,000+ ms (>10s) | 用户感到失望，可能会放弃任务；之后他们或许不会再回来。

Google团队提出了RAIL模型作为优化的目标。 Google把交互的过程分成4个独立的模块，称为 RAIL。RAIL 是一种以用户为中心的性能模型。每个网络应用均具有与其生命周期有关的四个不同方面，且这些方面以不同的方式影响着性能：response （响应）、 animation（动画）、idle（浏览器空置状态）和 load（加载）。
![RAIL](/image/performance-theory/RAIL.png)

## RAIL模型
### {% label info@response响应 – 在100ms内响应 %}
在用户注意到滞后之前您有 100 毫秒的时间可以响应用户输入。这适用于大多数输入，不管他们是在点击按钮、切换表单控件还是启动动画。但不适用于触摸拖动或滚动。

**要得到响应式的回应，我们需要：**
- 输入延迟时间（从按下到绘制）小于 100 毫秒。
- 如果最终结果需要花长时间才能得到，就要给一个“加载”的标识来告诉用户“还在处理中”。

### {% label info@Animation动画 – 在10ms内生成一帧 %}
在数学上来说，人眼感受到的帧数为60帧/s，则会认为是流畅的动画（1s/60 = 1000ms/60 = 16ms/帧）；也就是说加上每一帧的预算是16ms，减去浏览器绘制帧的时间，留给我们的大约只有10ms/帧。 如果超过这个时间，用户眼中动画的流畅度就会降低。动画包含了以下概念:
- 视觉动画 ：这个包括了动画的开始和退出，状态改变时的动画，还有加载标识。 
- 滚动 ：当用户开始滚动页面，页面出现猛动的情况。 
- 拖拽 ：当我们需要对用户的拖拽交互在100毫秒以内做出响应时，比如平移地图或者缩放屏幕时，我们需要依赖动画。 

**要合理地生成动画，我们需要：**
- 每个帧的工作（从 JS 到绘制）完成时间小于 16 毫秒。 

### {% label info@IDLE 空闲 – 利用空闲的时间完成推迟的工作 %}
**要合理地应用浏览器空闲时间，我们需要：**
- 尽可能减少预加载数据，以便您的应用快速加载。不是需要立刻传达给用户的，所以可以在浏览器空闲的时候再处理这些任务。
- 推迟的工作应分成每个耗时约 50 毫秒的多个块，因为应用应在 100 毫秒内给出响应。
- 如果用户开始交互，优先级最高的事项是响应用户。

### {% label info@LOAD 加载 – 在1000ms内呈现内容 %}
**页面加载时间是最常见的性能话题。 要尽快将页面加载出来，我们需要：**
- 把最需要传达的、对用户来说最有意义的内容在 1000ms 内渲染出来。
- 要持续响应用户，绝对不允许出现在滚动页面、轻触或者看动画的时候卡顿。
- 优先考虑关键渲染路径，将所有不需要在加载时处理的任务延迟到浏览器空闲时再处理（或根据需求拦加载）。 

---

# 相关原理
## 浏览器渲染进程
要想知道网页会卡顿在哪个阶段、哪些阶段可以进行性能优化，那么必须要对浏览器的渲染流程有个大体的了解。废话不多说，直接进入正题。

### 浏览器的渲染进程是多线程的
#### {% label info@GUI渲染线程 %}
- 负责渲染浏览器界面，解析HTML，CSS，构建DOM树和RenderObject树，布局和绘制等。
- 当界面需要重绘（Repaint）或由于某种操作引发回流(reflow)时，该线程就会执行。
- 注意，**GUI渲染线程与JS引擎线程是互斥的**，当JS引擎执行时GUI线程会被挂起（相当于被冻结了），GUI更新会被保存在一个队列中 **等到JS引擎空闲时**立即被执行。

#### {% label info@JS引擎线程 %}
- 也称为JS内核，负责处理Javascript脚本程序，例如V8引擎。
- JS引擎线程负责解析Javascript脚本，运行代码。
- JS引擎一直等待着任务队列中任务的到来，然后加以处理，一个Tab页（renderer进程）中无论什么时候都只有一个JS线程在运行JS程序。
- 同样注意，**GUI渲染线程与JS引擎线程是互斥的**，所以如果JS执行的时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞。

#### {% label info@事件触发线程 %}
- 归属于浏览器而不是JS引擎，用来控制事件循环（可以理解，JS引擎自己都忙不过来，需要浏览器另开线程协助）。
- 当JS引擎执行代码块如setTimeOut时（也可来自浏览器内核的其他线程,如鼠标点击、AJAX异步请求等），会将对应任务添加到事件线程中。
- 当对应的事件符合触发条件被触发时，该线程会把事件添加到待处理队列的队尾，等待JS引擎的处理。
- 注意，由于JS的单线程关系，所以这些待处理队列中的事件都得排队等待JS引擎处理（当JS引擎空闲时才会去执行）。

#### {% label info@定时触发器线程 %}
- 传说中的setInterval与setTimeout所在线程。
- 浏览器定时计数器并不是由JavaScript引擎计数的,（因为JavaScript引擎是单线程的, 如果处于阻塞线程状态就会影响记计时的准确）。
- 因此通过单独线程来计时并触发定时（计时完毕后，添加到事件队列中，等待JS引擎空闲后执行）。
- 注意，W3C在HTML标准中规定，规定要求setTimeout中低于4ms的时间间隔算为4ms。

#### {% label info@异步http请求线程 %}
- 在XMLHttpRequest在连接后是通过浏览器新开一个线程请求。
- 将检测到状态变更时，如果设置有回调函数，异步线程就 **产生状态变更事件**，将这个回调再放入事件队列中。再由JavaScript引擎执行。

### 浏览器渲染流程
- 浏览器输入url，浏览器主进程接管，开一个下载线程。
- 然后进行http请求（略去DNS查询，IP寻址等等操作）。
- 接着等待响应，获取内容。
- 随后将内容通过RendererHost接口转交给Renderer进程，接着浏览器渲染流程开始。

![render](/image/performance-theory/render.png)

1. 处理 HTML 标记并构建 DOM 树、处理 CSS 标记并构建 CSSOM 树 。
2. 结合DOM树和CSSDOM树形成render树， 也就是渲染树。
3. 布局render树（Layout/reflow），负责各元素尺寸、位置的计算。
4. 绘制render树（paint），绘制页面像素信息。
5. 浏览器会将各层的信息发送给GPU，GPU会将各层合成（composite），显示在屏幕上。

**_优化关键渲染路径_就是指最大限度缩短执行上述第 1 步至第 5 步耗费的总时间**

---

# 性能检测工具
## Chrome Devtools – Network
> 使用网络面板可以了解请求和下载的资源文件并优化网页加载性能。

### 打开
打开Chrome Devtools，选择Network标签。

### Request Table 请求列表
该视窗列出了所有的资源请求，默认按时间顺序排序，点击某个资源，可以查看更详细的信息：
- Headers：请求头信息和响应头信息。
- Preview：预览结果。
- Response：从服务器返回的响应结果。
- Cookies：请求和响应的Cookie。
- Timing：查看资源请求的生命周期。
    - Queueing：浏览器会在以下情况对请求进行排队：有更高优先级的请求；在这个域下，已经有6个TCP连接了，达到Chrome最大限制数量。此条规则仅适用 在HTTP/1.0和HTTP/1.1。
    - Stalled：发送请求之前等待的时间。它可能因为进入队列的任意原因而被阻塞。这个时间包括代理协商的时间。
    - Proxy negotiation：浏览器与代理服务器协商消耗的时间。
    - DNS Lookup：浏览器对请求的IP地址进行DNS查找所消耗的时间。
    - Initial conncection：发起连接所消耗的时间。
    - Request sent：请求发送消耗的时间。
    - Waiting (TTFB)：浏览器等待响应的时间，TTFB表示 Time To First Byte。
    - Content Download：资源下载所消耗的时间。

### Summary 总览
面板底部汇总了请求数量，传输数据大小，加载时间等信息,从左往右依次显示为：
- X requests：请求总个数。
- X transferred：所有请求的大小。
- Finish：加载完毕时间， 是页面上所有 http 请求发送到响应完成的时间。
- DOMContentLoaded： DOM树构建完成时间。它在两个地方都有体现：概览视窗的蓝色竖线，总览视窗的触发时间。
- Load：所有资源加载完成的时间。它在三个地方有体现：概览视窗的红色竖线，请求列表视窗的红色竖线，总览视窗的触发时间。

> 结合DOM文档加载的加载步骤，DOMContentLoaded事件/Load事件触发时机如下：
1、解析HTML结构。
2、加载外部脚本和样式表文件。
3、解析并执行脚本代码。        // 部分脚本会阻塞页面的加载
4、DOM树构建完成。            //DOMContentLoaded 事件
5、加载图片等外部文件。
6、页面加载完毕。             //load 事件

## Chrome Devtools –  Performance
> 使用性能面板可以通过记录和查看网站生命周期内发生的各种事件来提高页面的运行时性能。

### 打开
1. 打开[指定网页](https://googlechrome.github.io/devtools-samples/jank/)， 打开Chrome Devtools，然后选择Performance标签。
2. 点击【record】开始录制，或点击reload按钮录制页面载入过程。
3. 稍等片刻就会出现检测结果。

### 分析结果 – 分析每一秒的帧
FPS（frames per second）是用来分析动画的一个主要性能指标。能保持在60的FPS的话，那么用户体验就是不错的。
1. 观察FPS图表，如果你发现了一个红色的长条，那么就说明这些帧存在严重问题，有可能导致非常差的用户体验。一般来说，绿色的长条越高，说明FPS越高，用户体验越好。 
![FPS](/image/performance-theory/tool-fps.png)
2. 在Frames图表中，把鼠标移动到绿色条状图上，Devtools会展示这个帧的FPS。
![Frames](/image/performance-theory/tool-frames.png)
3. 勾选顶部的screenShots就可以看到各个时间点的界面截图，可以用来分析动画的各个细节。
4. 另外一个好用的小工具就是实时FPS面板，它可以实时展示页面的FPS指标。
    - 打开more tools 里面 Randering面板，勾选 FPSmeter，FPS实时面板就出现在页面的右上方了。
5. 就在FPS图表下方，你会看到CPU图表。
    - 在CPU图表中的各种颜色与Summary面板里的颜色是相互对应的，Summary面板就在Performance面板的下方。
    - CPU图表中的各种颜色代表着在这个时间段内，CPU在各种处理上所花费的时间。
    - 如果你看到了某个处理占用了大量的时间，那么这可能就是一个可以找到性能瓶颈的线索。详细看[Chrome调试指南](https://zhaoluting.github.io/2017/10/12/devTool/)。
![CPU](/image/performance-theory/tool-cpu.png)
6. 展开Main图表，Devtools展示了主线程运行状况。
    - X轴代表着时间。每个长条代表着一个event,长条越长就代表这个event花费的时间越长。
    - Y轴代表了调用栈。在栈里，上面的event调用了下面的event。
![Main](/image/performance-theory/tool-main.png)

## Chrome Devtools –  Audits
> Audit标签使用了Google开源的一个自动化测试工具 – Lighthouse。可对当前网页进行网络利用情况、网页性能方面的诊断，并给出一些优化建议。

### 打开
注意：这个工具需要翻墙，不然会卡在“lighthouse is warming up”。
1. 打开Chrome Devtools，然后选择Audits标签。
2. 点击【Perform an audit...】，一般只检查【Performance】和【Best practices】即可，接着点击 Run audit 。
3. 只需要等待一会儿，我们就完成了性能检查并且能把它当成衡量指标来提升网站的性能。

### 分析结果 - 综合分析
- {% label info@Metrics 指标 %} —— Metrics下列举了基本的性能指标，并且给出页面加载时间的概览。
    - **First meaningful paint** —— 首次有效绘制：代表用户看到初始内容所需的时间。请尽量让它的值小于1秒。
    - **First interactive** —— 首次互动：代表可交互元素从展示到响应所需的时间，即cpu有空闲能力来处理用户交互操作的时间。
    - **Consistently Interactive** —— 持续互动：页面中的大多数网络资源完成加载并且CPU在很长一段时间都很空闲的所需的时间。
    - **Perceptual Speed Index** —— 速度指标：页面加载性能指标，页面展示可见部分所需时间的平均值。以毫秒为单位并且取决于窗口的大小，应尽量减少到1250毫秒以内。
    - **Estimated Input Latency** —— 预计输入延迟时间： 应用有 100 毫秒的时间响应用户输入。如果超过此时间，用户就会认为应用反应迟缓。
- {% label info@Opportunities 可优化项 %} —— 这项里面的内容指的是LightHouse发现的一些可以直接优化的点，你可以对应这些点来进行优化。
- {% label info@Diagnostics 手动诊断项目 %} —— 这些项目表示LightHouse并不能替你决定当前是好是坏，但是把详情列出来，由你手动排查每个项目的情况。
- {% label info@Passed Audits 通过的审查项目 %} —— 这里列出的都是你做的好的地方，比如：减少阻塞渲染的脚本、图片懒加载、样式脚本压缩等等。
- {% label info@Best Practice 最佳实践 %} —— 本项指标是指LightHouse用业界公认的几项最佳实践来评估站点。

## 其他检测工具推荐
性能检测方面，Chrome DevTools已经很强大了，完全够用，除了我们常用的network、performance、auditing，More tools里面的Rendering、JavaScript Profiler、performance monitor等工具也很强大，所以，我就稍微介绍下其他工具：
- [WebPageTest](https://www.cnblogs.com/strick/p/6677836.html)，很强大，也很复杂，有兴趣的可以去研究下；
- [tools.pingdom.com](https://tools.pingdom.com/) 可在线检测，兼具了Audits与Network标签对应的功能。

---

# 参考文章
[RAIL](https://developers.google.com/web/fundamentals/performance/rail)
[关键渲染路径](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/)
[从浏览器多进程到JS单线程，JS运行机制最全面的一次梳理](https://segmentfault.com/a/1190000012925872)
[chrome-devtools官方文档](https://developers.google.com/web/tools/chrome-devtools/)
[Chrome 谷歌浏览器的开发者工具-Break驿站](https://www.breakyizhan.com/chromeconsole/1249.html)