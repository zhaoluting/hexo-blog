---
title: chrome调试指南
date: 2017-10-12
tags: chrome
categories: 前端
---

# 简介
**工欲善其事,必先利其器。**
轻量 便捷 实时

> Chrome 开发者工具是一套内置于Google Chrome中的Web开发和调试工具，可用来对网站进行迭代、调试和分析。

<!--more-->

## 如何打开？
- 在Chrome菜单中选择 更多工具 > 开发者工具
- 在页面元素上右键点击，选择 “检查”
- 使用 快捷键 Ctrl+Shift+I (Windows) 或 Cmd+Opt+I (Mac)

# 设备模式

**构建完全响应式，移动优先的网络体验**

> 点击它可以切换到不同的终端(移动端和pc端)进行开发模式。    
> 还可以选择不同的移动终端设备，同时可以选择不同的尺寸比例。  
> chrome浏览器的模拟移动设备和真实的设备相差不大，是非常好的选择。    

## HOW TO USE？
![image](/image/devTool/device1_2.png)  

除了上图标出的功能，右上角可以发现移动端调试的更多功能哦!  
<img src="/image/devTool/device2.png" width="300"/>

- Show device frame : 显示设备外壳
- Show media queries : 显示媒体查询(对屏幕大小的判断生成响应式布局)
- Show rulers : 显示标尺
- Add device pixel ratio : 调整设备的像素比例
- Add device type : 调整设备类型
- Capture screenshot : 捕捉当前屏幕截图
- Capture full size screenshot : 捕捉完整屏幕截图
- Reset to defaults : 恢复默认


# 元素面板

**使用元素面板可以自由的操作DOM和CSS来迭代布局和设计页面**

> 该面板可以用来查看、修改页面上的元素，包括DOM标签以及css样式的查看、修改(ctrl + z 可撤销修改)，还可以展示相关盒模型的图形信息。    

## HOW TO USE？
![image](/image/devTool/elments.png)  

```
🚩Tips：在 HTML DOM （文档对象模型）中，每个部分都是节点：  
1、文档本身是文档节点  
2、所有 HTML 元素是元素节点  
3、所有 HTML 属性是属性节点  
4、HTML 元素内的文本是文本节点（包括回车符也是属于文本节点）  
5、注释是注释节点  
Element 对象可以拥有类型为元素节点、文本节点、注释节点的子节点；  
元素也可以拥有属性，属性是属性节点。  
✔️总结：元素是元素节点，是节点中的一种，但元素节点中可以包含很多的节点。  
```
![image](/image/devTool/elements1.png)

点击箭头按钮可选择元素,激活后，Elements窗口会根据在页面中鼠标的位置自动移动到相应的代码段并显示相关信息。  

### 区域①：Html的DOM树结构，可以进行查看定位编辑；
- 页面自动标出鼠标悬浮所在代码片段元素
- 点击DOM树视图里面的节点，区域2显示相关信息
- 直接在某个节点上双击即可修改，且能实时在浏览器里面得到反馈。
- 拖拽节点可以直接调整顺序，也可以拖拽节点到编辑器
- 浏览器对例如Video、Password等组件进行了封装，无法查看到组件的详细代码，不利于调试。Settings → Preferences → Elements → 勾选Show user agent shadow DOM,这样能够在元素标签器中显示被隐藏的组件代码
### 区域②：编辑修改并实时显示当前选中元素的CSS样式、属性等。
- Styles标签页: 显示用户定义的样式，还有开发者工具添加的样式；
    - 顶部"Filter"输入框 : 快速查找样式
    - 顶部":hov"按钮 : 可强制改变元素状态,:active(当元素被鼠标按下还没有松开，处于激活状态时所显示的状态),:hover(鼠标悬停时显示的状态),:focus(元素获得光标焦点时的状态),:visited(元素被点击后显示的状态)
    - 顶部".cls"按钮 : 快速修改元素class
    - 顶部"+"按钮 : 快速添加class
    - 单击即可修改样式
    - 在每个属性后面单击可以添加新的样式
    - 灰色的element.style同样可以进行添加和书写，唯一区别是在这里添加的样式是添加到了该元素内部，即style属性
    - 点击样式区域颜色前面的小方块可调出自定义调色板,支持在rgba、hsl和hexadecimal来回切换颜色模式和实时编辑预览页面颜色。
    - 每行右下角的"┇"可以快速添加阴影、字体颜色、背景颜色
- Computed标签页: 显示开发者工具计算好的元素样式（盒式模型）；
    - 双击数字即可修改
- Event Listeners标签页: 显示当前DOM节点和其祖先节点的所有JavaScript事件监听器,即查看元素上绑定了哪些事件；
    - 勾选Ancestors可显示其祖先节点的事件监听器
    - 勾选Framework listeners:解析框架绑定的事件监听器
    - 每个事件会有对应的几个属性 handler,useCapture等
        - handler是处理函数, 右键可以看到这个函数定义的位置
        - useCapture 是 addEventListener 的第三个参数，如果是true，表示在事件捕获阶段调用事件处理程序；如果是false，表示在事件冒泡阶段调用事件处理程序。
- DOM Breakpoints标签页: 列出所有的DOM断点；
    - 在区域①右键可设置断点
- Properties标签页: 全面地列出当前选中元素的属性；

### dom对象的辅助功能
<img src="/image/devTool/elements2.png" width="300"/>
- Add attribute : 添加元素属性
- Edit attribute : 编辑元素属性
- Edit as HTML : 直接对元素的HTML进行编辑，所有的修改都会即时在页面上得到呈现
- Copy : 复制HTML代码
    - Copy outerHTML : 复制元素内所有的html和文本内容
    - Copy selector : 复制元素结构
    - Copy xpath : 复制元素路径(爬虫神器)
    - Cut element : 剪切元素
    - Copy element : 复制元素
    - Paste element : 粘贴元素
- Hide element : 隐藏元素
- Delete element : 删除元素
- Expand all : 展开元素所有代码
- Collapse all : 折叠元素所有代码
- Scroll into view : 滚动浏览器窗口或容器元素，以便在当前视窗的可见范围看见当前元素。
- Break on : 可以对某个元素进行监听，在JS对元素的属性或者HTML进行修改的时候，直接触发断点，跳转到对改元素进行修改的JS代码处
    - subtree modifications : 当添加／改变／删除子元素时触发
    - attribute modifications : 当元素属性被改变时触发
    - node removal : 当移除元素时触发

# 控制台面板

**在开发期间，可以使用控制台面板记录诊断信息，或者使用它作为shell在页面上与JavaScript交互**

> console 中有很多对我们调试代码有帮助的函数，我们可以在开发环境中配合 console 来调试代码，使得我们测试更加便利。
> 注： 当需要换到下一行而不是回车的时候，请按Shift+Enter。

## HOW TO USE？
![image](/image/devTool/console.png)  

代码实例 | 用途
---|---
console.log("这是平凡的字符串");|打印字符串
console.info("这是温柔的提示");|打印提示消息
console.warn("这是严肃的警告");|打印警告消息
console.error("这是错误消息");|打印错误消息
console.debug("这是调试信息");|打印调试信息,console.log方法的别称
console.log(console);|打印对象
console.assert(1 == 0);|判断真假情况
console.group('报数');  console.log("一！");  console.log("二！");  console.groupEnd();|分组输出
console.groupCollapsed();|创建一个新的分组.随后输出到控制台上的内容都会被添加一个缩进,表示该内容属于当前分组,直到调用console.groupEnd() 之后,当前分组结束。
var data = [{'姓名': 'zlt', '数量': 1}];    console.table(data);|显示关联数组信息
console.dir(document.body);|将一个 JavaScript 对象的所有属性和属性值显示成一个可交互的列表,以树的结构进行输出
var test = document.getElementById('test');   console.dirxml(test);|查看页面中对应元素的 html/xml 内容
console.log('%cMy name is zlt.', 'color: #fff; background: #000; font-size: 24px;');|特殊的标示符%c，对输出的文字可以附加特殊的样式,background属性的url()中添加图片路径就可以实现图片的输出了 
$('table')	|返回与指定的CSS选择器相匹配的第一个元素，等同于document.querySelector();如果你使用的库，如jQuery使用$，那么这个功能将被覆盖，并且$将对应用该库的实现
$$('table')	|返回与指定的CSS选择器相匹配的所有元素的数组，等同于document.querySelectorAll()
$_	|引用最近的一个表达式,功能与按向上的方向键再回车一样
$0 ~ $4	|最近选择过的5个DOM节点。$0会返回最近一次点选的DOM结点。以此类推，$1返回的是上上次点选的DOM节点。最多可以保存5个，如果不满5个，则返回undefined。
$x('//*[@id="console"]/div[2]/table[2]/tbody/tr[4]')|返回与指定的XPath相匹配的所有元素的数组
var data = {name: 'zlt',num: 1};   keys(data);	|返回传入对象所有属性名组成的数据
var data = {name: 'zlt',num: 1};   values(data);	|返回所有属性值组成的数组
inspect(document.body)	|Elements面板跳到需要查看的对象上  
getEventListeners(document.getElementById  ("eventListeners")).click[0].listener|查看某个DOM对象上的事件  
monitorEvents(document.body, "click")	|监控相关的事件,unmonitorEvents便是用来停止这一监控
console.clear()	|清理函数
clear()	|用 chrome 的 command line api 来清理控制台
copy(document.body)	|在控制台获取到的内容复制到剪贴板
document.designMode = "on"	|直接修改网页内容
**monitor(function) 接收一个函数名作为参数，比如function a,每次a被执行了，都会在控制台输出一条信息，里面包含了函数的名称a及执行时所传入的参数。 而unmonitor(function)便是用来停止这一监听。**
``` javascript
function sayHello(name) {
  alert('hello,' + name);
}
monitor(sayHello);
sayHello('zlt');
unmonitor(sayHello);
sayHello('zlttt');
```
**调试一个方法，当该方法调用时会暂停并且在源码中定位，效果和断点类似。undebug可以停止调试。**
``` javascript
function sayHello(name) {
    alert('hello,' + name); 
}
debug(sayHello);
sayHello('zlt');
```

**记录次数**
``` javascript
function hi(name) {
  console.count(name);
  return "hi " + name;
}
for(var i = 0; i < 10; i++) {
  if(i < 4) {
    hi("person");
  } else {
    hi("zlt");
  }
}
```

**记录代码运行所花费的时间,评估某段代码或是某个函数的性能**
``` javascript
console.time("神机妙算");
  (function () {
    for(var i = 0; i < 10; i++) {
      var sum = (function () {
        var flog = 0;
        for(var i = 0; i < 10; i++) {
          flog+=i;
         }
      })();
    }
  })();
console.timeEnd("神机妙算");
```
**评估某段代码或是某个函数的性能,结果显示在 Memory面板的profile中**
 ``` javascript
console.profile("更高级的神机妙算");
(function () {
    for(var i = 0; i < 10; i++) {
      var sum = (function () {
        var flog = 0;
        for(var i = 0; i < 10; i++) {
          flog+=i;
        }
      })();
    }
})();
console.profileEnd("更高级的神机妙算");
```
**追踪函数的调用过程**
``` javascript
function add(num) {
  if (0 < num) {
    console.trace("现在num的值为", num);
    return num + add(num - 1);
  } else {
    return 0;
  }
}
var a =3;
add(3);
```

# 源代码面板

> 在源代码面板中设置断点来调试 JavaScript ，或者通过Workspaces（工作区）连接本地文件来使用开发者工具的实时编辑器。

## HOW TO USE？
![image](/image/devTool/sources.png)  
### 区域①：Sources Panel 的左侧分别是 Sources 和 Content scripts和Snippets
- Sources : 显示加载的JS、CSS、HTML资源列表，点击后会在区域2显示对应的源代码
    - Workspaces是Chrome DevTools的一个强大功能，这使DevTools变成了一个真正的IDE。只需打开Sources选项，然后右击左边面板的任何一个地方，选择Add Folder To Worskpace，或者只是把你的整个工程文件夹拖放入Developer Tool。
    - Workspaces会将Sources选项卡中的文件和本地项目中的文件进行匹配，所以你可以直接编辑和保存，而不必复制/粘贴外部改变的文件到编辑器。
- Content scripts : 这是 Chrome 的一种扩展程序， 它是按照扩展的ID来组织的，这些文件也是嵌入在页面中的资源，这类文件可以 读写和操作我们的资源，需要调试这些扩展文件，则可以在这个目录下打开相关 文件调试（但是我们的项目一般没有相关的扩展文件）
- Snippets : 点击创建一个新的片段文件，写完后把右键文件Run，再结合控制台查看相关信息

### 区域②：显示源码
- 如果当前代码是经过压缩的话，可以点击左下方的花括号{}来增强可读性
- 点击JS代码块前面的数字可以设置断点，所有的断点都会列出右侧的断点区；
- 除了常规断点外, 还有个条件断点(右键 conditional breakpoint), 在设置的条件为 true 时才会断点, 在循环中需要断点时比较有用.
- 在当前的代码执行区域，在调试中如果发现需要修改的地方，可以立即修改，修改后保存即可生效

### 区域③：调试的功能区域
- 最上面一排按钮分别是：
    - 暂停(F8快捷键):继续执行脚本的命令,如果后面还有断点,直接执行到后面的断点,没有断点直接执行到代码最后面
    - 单步执行(F10快捷键):直接执行完函数进行下一步,不进入函数
    - 单步跳入此执行块(F11快捷键):进入函数逐步执行函数
    - 单步跳出此执行块(shift + F11快捷键):跳出函数
    - 禁用所有断点:注释断点,仍旧显示但是不起作用
    - 启用所有断点:图标激活之后,在代码出错之后会直接跳转到代码出错的那一行
- Watch：可以添加需要监听的变量，执行断点时可以观察变量的变化情况；
    - 选中变量后右键add selected text to watches可以添加监听
    - Watch窗口可直接添加需要监听的变量
- Call Stack：查看断点执行的堆栈情况，可以快速定位断点执行入口；
    - 若想重新从某个调用方法处执行，可以右键Restart Frame， 断点就会跳到此处开头重新执行，Scope 中的变量值也会依据代码重新更改
- Scope : 查看此时局部变量和全局变量的值
- Breakpoints: 所有当前js的断点都会展示在这个区域，你可以点击按钮用来“去掉/加上”此处断点，也可以点击下方的代码表达式，跳到相应的程序代码处来查看
- XHR Breakpoints: 点击右侧的+号，可以添加请求的URL，一旦 XHR 调用触发时就会在 request.send() 的地方中断
- DOM Breakpoints: 监听和查看某个元素的变化的DOM断点列表
- Event Listener Breakpoints：勾选你要监听的事件，在指定的事件执行时，断点就会有触发；

# 网络面板

**使用网络面板了解请求和下载的资源文件并优化网页加载性能**
> 可以看到所有的资源请求，包括网络请求，图片资源，html,css，js文件等请求，可以根据需求筛选请求项，一般多用于网络请求的查看和分析，分析后端接口是否正确传输，获取的数据是否准确，请求头，请求参数的查看。

## HOW TO USE？
![image](/image/devTool/network.png)  
### 区域①：Controls工具栏
用来控制Network的功能及外观,从左往右功能依次为：
- (红色圆点)Record Network Log: 红色表示此时正在记录资源请求信息；
- (灰色🚫)Clear: 清空所有的资源请求信息；
- (摄像标志)捕获快照：选中某一个快照，在概览/请求列表出现的黄色竖线，就是该快照被捕捉的真实时间，双击快照可以放大。
- (过滤标志)Filter: 过滤资源请求信息，选择后，会出现区域2所显示的过滤条件；
- Perserve Log: 勾选后，再次记录请求的信息时不擦除之前的资源信息；
- Disable cache: 勾选后，不允许缓存，所有资源均重新加载
- offline: 勾选后，模拟断开网络
- Online: 选择模拟网络状态

### 区域②：Filters筛选栏
根据筛选条件筛选请求列表，按住command/ctrl键可多选;筛选框可以实现很多定制化的筛选，比如字符串匹配，关键词筛选等，其中关键词筛选主要有如下几种：
- domain：筛选出指定域名的请求，不仅支持自动补全，还支持*匹配。
- has-response-header：筛选出包含指定响应头的请求。
- is：通过is:running找出WebSocket请求。
- larger-than：筛选出请求大于指定字节大小的请求，其中1000表示1k。
- method：筛选出指定HTTP方法的请求，比如GET请求、POST请求等。
- mime-type：筛选出指定文件类型的请求。
- mixed-content：筛选出混合内容的请求（不懂啥意思）。
- scheme：筛选出指定协议的请求，比如scheme:http、scheme:https。
- set-cookie-domain：筛选出指定cookie域名属性的包含Set-Cookie的请求。
- set-cookie-name：筛选出指定cookie名称属性的包含Set-Cookie的请求。
- set-cookie-value：筛选出指定cookie值属性的包含Set-Cookie的请求。
- status-code：筛选出指定HTTP状态码的请求。

### 区域③：Overviews 概览
资源被加载过来的时间线
- 如果多条时间线垂直堆叠，表示多个资源被并行加载。

### 区域④：Request Table 请求列表
- 该视窗列出了所有的资源请求，默认按时间顺序排序，点击某个资源，可以查看更详细的信息。
- Name: 资源名称以及URL路径；
- Status: HTTP状态码；
- Type: 请求资源的MIME类型；
- Initiator解释请求是怎么发起的，有四种可能的值：
    - Parser：请求是由页面的HTML解析时发送的;
    - Redirect：请求是由页面重定向发送的;
    - Script：请求是由script脚本处理发送的;
    - Other：请求是由其他过程发送的，比如页面里的link链接点击。
- Size: Size是响应头部和响应体结合起来的大小；
- Time: Time是从请求开始到接收到最后一个字节的总时长,可利用区域3进行过滤；
- Waterfall: 显示网络请求的可视化瀑布流，鼠标悬停在某一个时间线上，可以显示整个请求各部分花费的时间
- 右键配置可请求列表
- 按时shift键，鼠标hover在请求上，可以查看请求的上游和下游，例如hover在common.js上，可以看到有一个绿色请求、一个红色请求。其中绿色请求表示common.js的上游请求，即谁触发了common.js请求，红色请求表示common.js的下游请求，即common.js又触发了什么请求。

### 区域⑤：Summary 总览
汇总了请求数量，传输数据大小，加载时间等信息,从左往右依次显示为：
- X requests:请求总个数
- X transferred:所有请求的大小
- Finish:加载完毕时间
- DOMContentLoaded:页面文档完全加载并解析完毕之后，会触发DOMContentLoaded事件，它在两个地方都有体现：概览视窗的蓝色竖线，总览视窗的触发时间。
- Load:当所有资源加载完成后触发的，它在三个地方有体现：概览视窗的红色竖线，请求列表视窗的红色竖线，总览视窗的触发时间。

![image](/image/devTool/network2.png)  
点击左侧某一个具体请求URL，可以看到该请求的详细HTTP请求情况：
- Headers：请求头信息和响应头信息
- Preview：预览结果，如果是文件可以查看这个文件；如果是图片可以预览这个图片；如果是从服务器返回来的JSON数据，可以查看格式话后的JSON
- Response：从服务器返回的响应结果
- Cookies：请求和响应的Cookie
    - Name：cookie的名称。
    - Value：cookie的值。
    - Domain：cookie所属域名。
    - Path：cookie所属URL。
    - Expire/Max-Age：cookie的存活时间。
    - Size：cookie的字节大小。
    - HTTP：表示cookie只能被浏览器设置，而且JS不能修改。
    - Secure：表示cookie只能在安全连接上传输。
- Timing：查看资源请求的生命周期，包含Queing/Stalled/Request/Response/Request sent/Waiting/Content Download各个阶段

# 性能面板
**使用性能面板可以通过记录和查看网站生命周期内发生的各种事件来提高页面的运行时性能**
> 在 Chrome 57 之后时间线(Timeline)面板更名为性能(Performance)面板。

## HOW TO USE？
![image](/image/devTool/performance2.png)  
使用 Chrome DevTools 的 Performance 面板记录程序运行时的所有行为，并用于加以分析是解决程序性能问题的最好方案。 由以下四个子面板构成：
- 控制面板（Controls）: 开启记录，停止记录，配置记录期间需要记录的内容。
- 概括（Overview）: 对页面表现（行为）的一个概述。
    - 概括区域由三个图形记录组成:
        - FPS（Frames Per Second）:绿色的柱越高， FPS 值也越高。FPS 图表上方的红色小块指明了长帧(long frame)，这些可能是卡顿
        - CPU(CPU Resources):这个面积图(area chart)表明了哪种事件在消耗 CPU 资源。
        - NET:每种不同颜色的条代表一种资源。
            - 条越长表明获取该资源所花的时间越长。
            - 每个条中的浅色部分代表等待时间（资源请求被发送到收到第一个响应字节的时间），深色部分代表文件传输时间（从收到第一个字节到这个资源完全被下载好）
            - 蓝色 代表 HTML 文件，黄色 代表 Script 文件，紫色 代表 Stylesheets 文件， 绿色 代表 Media 文件，灰色 代表其他资源。
- 火焰图（Flame Chart）: 可视化 CPU 堆栈(stack)信息记录。
    - 从不同的角度分析框选区域 。例如：Network，Frames, Interactions, Main等
    - 在 Flame Chart 面板上你可能看到三根垂直的线，蓝线代表 DOMContentLoaded 事件，绿线代表渲染开始的时间( time to first paint)，红线代表 load 事件。
- 详细信息（Detail）: 当有具体事件被选择时，该面板展示这个事件的更多详细信息。如果没有事件被选择，该面板展示当前所选时间段的一些信息。
    - 精确到毫秒级的分析，以及按调用层级，事件分类的整理
        - Summary面板:从宏观层面概括了浏览器加载的总时间
            - 颜色：蓝色	；英文： Loading；含义：加载
            - 颜色：黄色	；英文： Scripting；含义：	脚本
            - 颜色：紫色	；英文： Rendering；含义：	渲染
            - 颜色：绿色	；英文： Painting；含义：	绘制
            - 颜色：深灰	；英文： Other；含义：	其他
            - 颜色：浅灰	；英文： Idle；含义：	空闲
        - Bottom-Up面板：The Heavy (Bottom Up) view is available in the Bottom-Up tab
            - Self Time代表函数本身执行消耗时间，Total Time则是函数本身消耗再加上在调用它的函数中消耗的总时间，Activity是浏览器活动。
            - Group面板可以很清晰明了得分析按照活动、目录、域、子域、URL和Frame进行分组的前端性能。
        - Call Tree面板：the Tree (Top Down) view is available in the Call Tree tab
            - Bottom-Up类似事件冒泡，Call Tree类似事件捕获。自上而下的Call-Tree更符合我们的人类正常思维，可以更直观地分析浏览器对页面的build精确到毫秒级的情况
        - Event Log面板 :展示所有阶段包括loading、javascripting、rendering、painting中各事件的耗时情况，并提供了filter输入框和按钮供你快速过滤。

# 内存面板
**如果需要比性能面板提供的更多信息，可以使用内存面板，例如跟踪内存泄漏**
> 在 Chrome 57 之后分析（Profiles）面板更名为内存（Memory）面板。

## HOW TO USE？
![image](/image/devTool/memory.png)  
如果在页面中出现卡顿现象，可以使用 CPU 分析器，它准确地记录调用了哪些函数和每个函数花费的时间，
- Take Heap Snapshot:创建堆快照用来显示网页上的JS对象和相关的DOM节点的内存分布情况。
    - 利用该工具你可以创建JS的堆快照、内存分析图、对比堆快照以及定位内存泄漏问题。
    - 选中Take Heap Snapshot,点击Take Snapshot按钮即可获取快照，在每一次获取快照前都会自动执行垃圾回收操作。
    - 堆快照提供了不同的视角来进行查看：
        - Summary : 该视图按照构造函数进行分组，用它可以捕获对象和它们使用的内存情况，对于跟踪定位DOM节点的内存泄漏特别有用。
        - Comparison : 对比两个快照的差别，用它可以对比某个操作前后的内存快照。分析操作前后的内存释放情况以及它的引用计数，便于你确认内存是否存在泄漏以及造成的原因。
        - Containment : 该视图可以探测堆的具体内容，它提供了一个更适合的视图来查看对象结构，有助于分析对象的引用情况，使用它可以分析闭包和进行更深层次的对象分析。
        - Statistics : 统计视图。
- Record Allocation Profile:从JS函数角度记录内存的分配信息。
    - 点击Start按钮，执行你想要去深入分析的页面操作，当你完成你的操作后点击Stop按钮。然后会显示一个按JS函数进行内存分配的分解图.
- Record Allocation Timeline 从整个Heap角度记录内存的分配信息的时间轴信息，利用这个可以实现隔离内存泄漏问题。
    - 选中Record Allocation Timeline按钮，点击Start按钮之后，执行你认为可能会引起内存泄漏的操作，操作之后点击左上角的停止按钮即可。
    - 你可以在蓝色竖线上通过缩放来过滤构造器窗格来仅仅显示在指定的时间帧内的被分配的对象。
    - 录制过程中，在时间线上会出现一些蓝色竖条，这些蓝色竖条代表一个新的内存分配，这个新的内存分配都可以会有潜在的内存泄露问题。
    - 通过展开对象并点击它的值则可以在Object窗格中查看更多新分配的对象细节。

# 应用面板

**使用应用面板可以检查加载的所有资源**
> 在 Chrome 52 之后资源(Resources)面板更名为应用(Application)面板. 该面板可以检查和修改IndexedDB与Web SQL数据库，本地和会话存储，cookie，应用程序缓存，图像，字体和样式表。 点击一次按钮即可清除所有存储、数据库、缓存和服务工作线程。

## HOW TO USE？
![image](/image/devTool/application.png)  
- Local Storage : 如果你在开发过程中使用了local storage来存储键值对(KVPs)，那么你就可以通过Local Storage窗格来检查、新增、修改、删除这个键值对。
- Application Cache : 你可以使用Application Cache窗格去查看通过Application Cache API创建的资源。
- Frames : 将页面上的资源按frame类别进行组织显示。在资源上右击后在弹出菜单选择Reveal in Network Panel，就会跳转到Network面板并定位到该资源的位置。


# 安全面板

**使用安全面板调试混合内容问题，证书问题等等。**
> 使用 Security Overview 可以立即查看当前页面是否安全。 检查各个源以查看连接和证书详情（安全源）或找出具体哪些请求未受保护（非安全源）。

## HOW TO USE？
![image](/image/devTool/security.png)  
- 如果网页是安全的，则会显示这样一条消息：This page is secure (valid HTTPS)。
    - 通过点击View certificate可以查看main origin的服务器证书信息。
    - 点击左侧可以查看指定源的连接和证书详情。
- 如果网页是不安全的，则会显示：This page is not secure。该面板可以区分两种类型的不安全的页面：
    - 如果被请求的页面通过HTTP提供服务，那么这个主源就会被标记为不安全。
    - 如果被请求的页面是通过HTTPS获取的，但这个页面接着通过HTTP继续从其他来源检索内容， 那么这个页面仍然被标记为不安全。这就是所谓的混合内容页面,混合内容页面只是部分受到保护, 因为HTTP内容(非加密的内容)可以被嗅探者入侵,容易受到中间人攻击。


# 检查面板

**对当前网页进行网络利用情况、网页性能方面的诊断，并给出一些优化建议**
> 选中Performance, Best Practices, Accessibility, and Progressive Web App，点击Run按钮，将会对当前页面进行网络利用率和页面的性能优化作出诊断，并给出相应的优化建议。 这个对于优化前端页面、加速网页加载速度很有用（相当于Yslow）

## HOW TO USE？
![image](/image/devTool/audits.png) 
- Progressive Web App（在移动端利用提供的标准化框架，在网页应用中实现和原生应用相近的用户体验）: 检查网页是否符合PWA的标准。
- Performance:检查网页加载时间
- Best Practices : 检查页面是否遵循Google Web开发最佳实践
- Accessibility : 检查网页能否给残障人士使用


# 开发者工具主菜单

**自定义并控制DevTools，也叫开发者工具主菜单**
> 单击DevTools窗口右上角的“┇”按钮即可打开主菜单。

## HOW TO USE？
<img src="/image/devTool/more.png" width="300"/>
- Dock side: 调整DevTools的位置
- Show console drawer: 打开console的抽屉式选项卡
- Search all files : 在所有文件中搜索
- Open file : 打开指定文件
- More tools: 在抽屉式选项卡中打开更多工具
    - Animations:动画检查器
    - Coverage:使用动态分析（Dynamic Analysis）法来收集代码运行时的覆盖率
    - JavaScript Profiler:查看JavaScript函数的执行性能
    - Layers:看到页面中的复合层。（查找页面原因的时候非常有用）
    - Legacy Audits:旧版检查面板,面板的优化建议参照的是雅虎前端工程师的十四条黄金建议。
        - Network Utillzation(网络利用率)、Web Page Performance(界面性能)
        - Audit Present State(针对目前网页状态分析)、Reload Page and Audit on load(重新加载网页进行分析)
    - Network conditions:设置网络节流
    - Quick source:打开Source的抽屉式选项卡
    - Remote devices:连接远程设备
    - Rendering: 渲染设置
    - Request blocking:拦截请求
    - Search:在所有资源中搜索
    - Sensors:仿真面板,模拟移动设备的传感器
- Shortcuts:查看快捷键
- Settings:打开设置面板

![image](/image/devTool/more2.png) 
- Preferences :首选项
    - Appearance:外观设置
        - Show third party URL badges:显示第三方URL标记？？？
        - Theme:改变devtool主题(简洁白／炫酷黑)
        - Panel layout:改变面板布局(横版／竖版／自动)
        - Enable Ctrl + 1-9 shortcut to switch panels:用快捷键Ctrl + 1-9来切换面板
        - Don't show Chrome Data Saver warning:不显示chrome的节省数据流量警告
        - Disable paused state overlay: 禁用暂停悬浮框状态？？？
    - Sources:源代码面板设置
        - Automatically reveal files in navigator:在导航栏自动显示文件
        - Enable JavaScript source maps:在调试时如果有JS报错，浏览器就会通过解析map文件来重新merge压缩后的JS,使开发者可以用未压缩前的代码来调试。只有Chrome浏览器支持这个功能😲
        - Detect indentation:自动检测缩进
        - Autocompletion:自动补全
        - Bracket matching:括号自动匹配
        - Show whitespace characters:空白（空格、TAB、回车）显示设置
        - Display variable values inline while debugging:当前调试点及时预览 JavaScript 变量的内联值
        - Enable CSS source maps:在调试时如果有CSS报错，浏览器就会通过解析map文件来重新merge压缩后的CSS,使开发者可以用未压缩前的代码来调试。
        - Default indentation: 自定义默认缩进
        - Search in anonymous and content scripts:在匿名和内容脚本中搜索
    - Elements:元素面板设置
        - Color format:设置颜色格式
        - Show user agent shadow DOM:在元素标签器中显示被隐藏的组件代码
        - Word wrap:自动换行
        - Show HTML comments:显示HTML注释
        - Reveal DOM node on hover:在悬停状态下显示DOM节点
        - Show rulers: 鼠标悬停在某个元素上或者选中一个元素的时候会显示
    - Network:网络面板设置
        - Preserve log:保留请求日志
        - Color-code resource types: 不同色彩作标记资源类型
        - Group network log by frame:逐帧分组网络日志
        - Disable cache (while DevTools is open):禁用缓存
        - Enable request blocking:使请求阻塞
    - Performance:性能面板设置
        - Flamechart mouse wheel action:查看火焰图时鼠标滚轮的作用(缩放/滚动)
        - High resolution CPU profiling: 提高记录分析数据的准确性，能够以十分之一毫秒的度量来查看火焰图的数据
        - Record heap allocation stack traces:使用 Allocation 视图查看内存分配
        - Hide chrome frame in Layers view:Layers视图中隐藏谷歌浏览器内嵌框架(通过Internet Explorer的用户界面，以Chrome内核的渲染方式浏览网页)
        - Show native functions in JS Profile:使用 profiler 查看原生函数的执行性能
    - Console:控制台面板设置
        - Hide network messages:默认情况下，控制台将报告网络问题。启用此设置将指示控制台不显示这些错误的日志。例如，将不会记录 404 和 500 系列错误。
        - Selected context only:仅显示当前上下文的信息(top iframe worker extension)
        - User messages only: 仅显示console API方法的信息
        - Log XMLHttpRequests:确定控制台是否记录每一个 XMLHttpRequest。
        - Show timestamps:在调用时向显示的每条控制台消息追加一个时间戳。对于发生特定事件时的调试非常实用。这会停用消息堆叠。
        - Autocomplete from history:历史记录提示
        - Enable custom formatters:控制 JavaScript 对象的格式设置。
        - Preserve log upon navigation:在页面刷新或导航时保留控制台历史记录。
    - Extensions:扩展
        - Link handling:链接处理
    - Debugger:断点设置
        - Disable JavaScript:禁用JavaScript
        - Disable async stack traces:禁用异步堆栈跟踪
    - DevTools
        - Auto-open DevTools for popups:当新的标签/新窗口打开时,自动打开浏览器开发工具
- Workspace :把本地服务器的资源映射为硬盘上的文件，实现调试 JS 和 CSS 的同时自动保存文件
- Blackbox:屏蔽JS文件
    - 库代码（被屏蔽的文件）里抛出异常时不会暂停（当设置为Pause on exceptions时）
    - 调试时Stepping into/out/over都会忽略库代码
    - 事件断点也会忽略库代码
    - 库代码里设置的任何断点也不会起作用
    - 最终的结果就是只会调试应用代码而忽略第三方代码（配置了Blackbox的代码）。
- Emulated Devices:管理模拟设备
- Throttling:设置网络延迟与带宽
- Shortcuts:查看快捷键


### 最后还有！

**小技能**

- 在浏览器地址栏输入：chrome://about/,你看到得这些地址几乎包含了所有谷歌浏览器的实用功能，下面来详解部分常用功能。
- 清除缓存、硬重载:如果你请求一个硬刷新，在开发者工具打开的情况下点击并按住 Chromes 的刷新按钮。你应该会看见一个下拉菜单，它允许你进行清除缓存和并进行硬重载。
- 任务管理器：在浏览器工具栏中点击 Chrome 菜单，选择更多工具，选择任务管理器。可以让你深入了解任何选项卡对应的 GPU，CPU 以及 JavaScript 内存使用状况，CSS 和脚本缓存使用状况。
- [JSRunTime](https://plus.google.com/+AddyOsmani/posts/ih85hKCyGve)：开发者工具检索 JavaScript 对象的拓展,它可以在内存中检索可用的 JavaScript 对象并生成相应的图，还可以根据值或者名称来进行匹配。
- [Vue.js devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)：墙裂推荐，调试vue应用的神器

> 这些只是chrome Devtool的其中一部分功能，还有待补充。目前只能算入门篇。
so,欢迎来找我讨论补充～   
