---
typora-root-url: D:\Typora文件库\Typora图库\Web前端
---

# 基础

## ⼀、HTML、HTTP、web综合问题

### 1 前端需要注意哪些SEO

- 合理的title 、description 、keywords ：搜索对着三项的权重逐个减⼩， title值**强调重点即可，重要关键词出现不要超过2次，⽽且要靠前**，不同⻚⾯title 要有所不同； description 把⻚⾯内容⾼度概括，⻓度合适，不可过分堆砌关键词，不同⻚⾯description 有所不同； keywords 列举出重要关键词即可
- 语义化的HTML 代码，符合**W3C规范**：语义化代码让搜索引擎容易理解⽹⻚
- 重要内容HTML 代码放在最前：搜索引擎抓取HTML 顺序是从上到下，有的搜索引擎对抓取⻓度有限制，保证重要内容⼀定会被抓取
- 重要内容不要⽤js 输出：爬⾍不会执⾏js获取内容
- 少⽤iframe ：搜索引擎不会抓取iframe 中的内容
- ⾮装饰性图⽚必须加alt
- 提⾼⽹站速度：⽹站速度是搜索引擎排序的⼀个重要指标

### 2 `<img>` 的title 和alt 有什么区别？strong与em的异同？

- 通常当⿏标滑动到元素上的时候显示
- alt 是<img> 的特有属性，是图⽚内容的等价描述，**⽤于图⽚⽆法加载时显示**、读屏器阅读图⽚。可提图⽚⾼可访问性，**除了纯装饰图⽚外都必须设置有意义的值**，搜索引擎会重点分析。
- strong :粗体强调标签，强调，表示内容的重要性
- em :斜体强调标签，更强烈强调，表示内容的强调点

### 3 HTTP的⼏种请求⽅法⽤途

GET ⽅法：发送⼀个请求来取得服务器上的某⼀资源
POST ⽅法：向URL 指定的资源提交数据或附加新的数据
PUT ⽅法：跟POST ⽅法很像，也是向服务器提交数据。但是，它们之间有不同。**PUT 指定了资源在服务器上的位置，⽽POST 没有**

HEAD ⽅法：只请求⻚⾯的⾸部
DELETE ⽅法：删除服务器上的某资源
OPTIONS ⽅法：它⽤于获取当前URL 所⽀持的⽅法。如果请求成功，会有⼀个Allow 的头包含类
似“GET,POST” 这样的信息
TRACE ⽅法：TRACE ⽅法被⽤于激发⼀个远程的，应⽤层的请求消息回路
CONNECT ⽅法：把请求连接转换到透明的TCP/IP 通道

### 4 从浏览器地址栏输⼊url到显示⻚⾯的步骤

基础版本：

- 浏览器根据请求的URL 交给DNS 域名解析，找到真实IP ，向服务器发起请求；
- 服务器交给后台处理完成后返回数据，浏览器接收⽂件（ HTML、JS、CSS 、图象等）；
- 浏览器对加载到的资源（ HTML、JS、CSS 等）进⾏语法解析，建⽴相应的内部数据结构（如HTML 的DOM ）；
- 载⼊解析到的资源⽂件，渲染⻚⾯，完成。

详细版：

1. 在浏览器地址栏输⼊URL
2. 浏览器查看缓存，如果请求资源在缓存中并且新鲜，跳转到转码步骤
   1. 如果资源未缓存，发起新请求
   2. 如果已缓存，检验是否⾜够新鲜，⾜够新鲜直接提供给客户端，否则与服务器进⾏验证。
   3. 检验新鲜通常有两个HTTP头进⾏控制Expires 和Cache-Control ：
     - HTTP1.0提供Expires，值为⼀个绝对时间表示缓存新鲜⽇期
     - HTTP1.1增加了Cache-Control: max-age=,值为以秒为单位的最⼤新鲜时间

3. 浏览器解析URL获取协议，主机，端⼝，path
4. 浏览器组装⼀个HTTP（GET）请求报⽂
5. 浏览器获取主机ip地址，过程如下：
   1. 浏览器缓存
   2. 本机缓存
   3. hosts⽂件
   4. 路由器缓存
   5. ISP DNS缓存
   6. DNS递归查询（可能存在负载均衡导致每次IP不⼀样）

6. 打开⼀个socket与⽬标IP地址，端⼝建⽴TCP链接，三次握⼿如下：
   1. 客户端发送⼀个TCP的SYN=1，Seq=X的包到服务器端⼝
   2. 服务器发回SYN=1， ACK=X+1， Seq=Y的响应包
   3. 客户端发送ACK=Y+1， Seq=Z
7. TCP链接建⽴后发送HTTP请求
8. 服务器接受请求并解析，将请求转发到服务程序，如虚拟主机使⽤HTTP Host头部判断请求的服务程序
9. 服务器检查HTTP请求头是否包含缓存验证信息如果验证缓存新鲜，返回304等对应状态码
10. 处理程序读取完整请求并准备HTTP响应，可能需要查询数据库等操作
11. 服务器将响应报⽂通过TCP连接发送回浏览器
12. 浏览器接收HTTP响应，然后根据情况选择关闭TCP连接或者保留重⽤，关闭TCP连接的四
    次握⼿如下：
    1. 主动⽅发送Fin=1， Ack=Z， Seq= X报⽂
    2. 被动⽅发送ACK=X+1， Seq=Z报⽂
    3. 被动⽅发送Fin=1， ACK=X， Seq=Y报⽂
    4. 主动⽅发送ACK=Y， Seq=X报⽂
13. 浏览器检查响应状态吗：是否为1XX，3XX， 4XX， 5XX，这些情况处理与2XX不同
14. 如果资源可缓存，进⾏缓存
15. 对响应进⾏解码（例如gzip压缩）
16. 根据资源类型决定如何处理（假设资源为HTML⽂档）
17. 解析HTML⽂档，构件DOM树，下载资源，构造CSSOM树，执⾏js脚本，这些操作没有严格的先后顺序，以下分别解释
18. 构建DOM树：
    1. Tokenizing：根据HTML规范将字符流解析为标记
    2. Lexing：词法分析将标记转换为对象并定义属性和规则
    3. DOM construction：根据HTML标记关系将对象组成DOM树
19. 解析过程中遇到图⽚、样式表、js⽂件，启动下载
20. 构建CSSOM树：
    1. Tokenizing：字符流转换为标记流
    2. Node：根据标记创建节点
    3. CSSOM：节点创建CSSOM树
21. 根据DOM树和CSSOM树构建渲染树:
    1. 从DOM树的根节点遍历所有可⻅节点，不可⻅节点包括：1） script , meta 这样本身不可⻅的标签。2)被css隐藏的节点，如display: none
    2. 对每⼀个可⻅节点，找到恰当的CSSOM规则并应⽤
    3. 发布可视节点的内容和计算样式
22. js解析如下：
    1. 浏览器创建Document对象并解析HTML，将解析到的元素和⽂本节点添加到⽂档中，此时document.readystate为loading
    2. HTML解析器遇到没有async和defer的script时，将他们添加到⽂档中，然后执⾏⾏内或外部脚本。这些脚本会同步执⾏，并且在脚本下载和执⾏时解析器会暂停。这样就可以⽤document.write()把⽂本插⼊到输⼊流中。同步脚本经常简单定义函数和注册事件处理程序，他们可以遍历和操作script和他们之前的⽂档内容
    3. 当解析器遇到设置了async属性的script时，开始下载脚本并继续解析⽂档。脚本会在它下载完成后尽快执⾏，但是解析器不会停下来等它下载。异步脚本禁⽌使⽤document.write()，它们可以访问⾃⼰script和之前的⽂档元素
    4. 当⽂档完成解析，document.readState变成interactive
    5. 所有defer脚本会按照在⽂档出现的顺序执⾏，延迟脚本能访问完整⽂档树，禁⽌使⽤document.write()
    6. 浏览器在Document对象上触发DOMContentLoaded事件
    7. 此时⽂档完全解析完成，浏览器可能还在等待如图⽚等内容加载，等这些内容完成载⼊并且所有异步脚本完成载⼊和执⾏，document.readState变为complete，window触发load事件
23. 显示⻚⾯（HTML解析过程中会逐步显示⻚⾯）

详细简版：

1. 从浏览器接收url 到开启⽹络请求线程（这⼀部分可以展开浏览器的机制以及进程与线程之间的关系）
2. 开启⽹络线程到发出⼀个完整的HTTP 请求（这⼀部分涉及到dns查询， TCP/IP 请求，五层因特⽹协议栈等知识）
3. 从服务器接收到请求到对应后台接收到请求（这⼀部分可能涉及到负载均衡，安全拦截以及后台内部的处理等等）
4. 后台和前台的HTTP 交互（这⼀部分包括HTTP 头部、响应码、报⽂结构、cookie 等知识，可以提下静态资源的cookie 优化，以及编码解码，如gzip 压缩等）
5. 单独拎出来的缓存问题， HTTP 的缓存（这部分包括http缓存头部， ETag ， catchcontrol
    等）

6. 浏览器接收到HTTP 数据包后的解析流程（解析html -词法分析然后解析成dom 树、解析css ⽣成css 规则树、合并成render 树，然后layout 、painting 渲染、复合图层的合成、GPU 绘制、外链资源的处理、loaded 和DOMContentLoaded 等）
7. CSS 的可视化格式模型（元素的渲染规则，如包含块，控制框， BFC ， IFC 等概念）
8. JS 引擎解析过程（ JS 的解释阶段，预处理阶段，执⾏阶段⽣成执⾏上下⽂， VO ，作⽤域链、回收机制等等）
9. 其它（可以拓展不同的知识模块，如跨域，web安全， hybrid 模式等等内容）

### 5 如何进⾏⽹站性能优化

content ⽅⾯：减少HTTP 请求：合并⽂件、CSS 精灵、inline Image；减少DNS 查询： DNS 缓存、将资源分布到恰当数量的主机名；减少DOM 元素数量
Server ⽅⾯：使⽤CDN；配置ETag；对组件使⽤Gzip 压缩

Cookie ⽅⾯：减⼩cookie ⼤⼩

css ⽅⾯：将样式表放到⻚⾯顶部不使⽤CSS 表达式；使⽤`<link>` 不使⽤@import
Javascript ⽅⾯：将脚本放到⻚⾯底部；将javascript 和css 从外部引⼊；压缩javascript 和css；删除不需要的脚本；减少DOM 访问

图⽚⽅⾯：优化图⽚：根据实际颜⾊需要选择⾊深、压缩；优化css 精灵不要在HTML 中拉伸图⽚

### 6 HTTP状态码及其含义

1XX ：信息状态码

- 100 Continue 继续，⼀般在发送post 请求时，已发送了http header 之后服务端将返回此信息，表示确认，之后发送具体参数信息

2XX ：成功状态码

- 200 OK 正常返回信息
- 201 Created 请求成功并且服务器创建了新的资源
- 202 Accepted 服务器已接受请求，但尚未处理

3XX ：重定向

- 301 Moved Permanently 请求的⽹⻚已永久移动到新位置。
- 302 Found 临时性重定向。
- 303 See Other 临时性重定向，且总是使⽤ GET 请求新的 URI 。
- 304 Not Modified ⾃从上次请求后，请求的⽹⻚未修改过。

4XX ：客户端错误

- 400 Bad Request 服务器⽆法理解请求的格式，客户端不应当尝试再次使⽤相同的内容发起请求。
- 401 Unauthorized 请求未授权。
- 403 Forbidden 禁⽌访问。
- 404 Not Found 找不到如何与 URI 相匹配的资源。

5XX: 服务器错误

- 500 Internal Server Error 最常⻅的服务器端错误。
- 503 Service Unavailable 服务器端暂时⽆法处理请求（可能是过载或维护）。

### 7 语义化的理解

⽤正确的标签做正确的事情！
HTML 语义化就是让⻚⾯的内容结构化，便于对浏览器、搜索引擎解析；
在没有样式CSS 情况下也以⼀种⽂档格式显示，并且是容易阅读的。
搜索引擎的爬⾍依赖于标记来确定上下⽂和各个关键字的权重，利于 SEO 。
使阅读源代码的⼈对⽹站更容易将⽹站分块，便于阅读维护理解

### 8 介绍⼀下你对浏览器内核的理解？

主要分成两部分：渲染引擎( layout engineer 或Rendering Engine )和JS 引擎

- 渲染引擎：负责取得⽹⻚的内容（ HTML 、XML 、图像等等）、整理讯息（例如加⼊CSS 等），以及计算⽹⻚的显示⽅式，然后会输出⾄显示器或打印机。浏览器的内核的不同对于⽹⻚的语法解释会有不同，所以渲染的效果也不相同。所有⽹⻚浏览器、电⼦邮件客户端以及其它需要编辑、显示⽹络内容的应⽤程序都需要内核

- JS 引擎：解析和执⾏javascript 来实现⽹⻚的动态效果

最开始渲染引擎和JS 引擎并没有区分的很明确，后来JS引擎越来越独⽴，内核就倾向于只指渲染引擎

### 9 HTML5 的离线储存怎么使⽤，⼯作原理能不能解释⼀下？

在⽤户没有与因特⽹连接时，可以正常访问站点或应⽤，在⽤户与因特⽹连接时，更新⽤户机器上的缓存⽂件

原理： HTML5 的离线存储是基于⼀个新建的.appcache ⽂件的缓存机制(不是存储技术)，通过这个⽂件上的解析清单离线存储资源，这些资源就会像cookie ⼀样被存储了下来。之后当⽹络在处于离线状态下时，浏览器会通过被离线存储的数据进⾏⻚⾯展示

如何使⽤：

- ⻚⾯头部像下⾯⼀样加⼊⼀个manifest 的属性；
- 在cache.manifest ⽂件的编写离线存储的资源
- 在离线状态时，操作window.applicationCache 进⾏需求实现

浏览器是怎么对HTML5 的离线储存资源进⾏管理和加载的呢：

- 在线的情况下，浏览器发现html 头部有manifest 属性，它会请求manifest ⽂件，如果是第⼀次访问app ，那么浏览器就会根据manifest⽂件的内容下载相应的资源并且进⾏离线存储。如果已经访问过app 并且资源已经离线存储了，那么浏览器就会使⽤离线的资源加载⻚⾯，然后浏览器会对⽐新的manifest ⽂件与旧的manifest ⽂件，如果⽂件没有发⽣改变，就不做任何操作，如果⽂件改变了，那么就会重新下载⽂件中的资源并进⾏离线存储。
- 离线的情况下，浏览器就直接使⽤离线存储的资源。

### 10  cookies ， sessionStorage 和 localStorage 的区别？

- cookie 是⽹站为了标示⽤户身份⽽储存在⽤户本地终端（Client Side）上的数据（通常经过加密）
- cookie数据始终在同源的http请求中携带（即使不需要），记会在浏览器和服务器间来回传递
- sessionStorage 和localStorage 不会⾃动把数据发给服务器，仅在本地保存
- 存储⼤⼩：

  - cookie 数据⼤⼩不能超过4k

  - sessionStorage 和localStorage 虽然也有存储⼤⼩的限制，但⽐cookie ⼤得多，可以达到5M或更⼤
- 有期时间：
	- localStorage 存储持久数据，浏览器关闭后数据不丢失除⾮主动删除数据
	- sessionStorage 数据在当前浏览器窗⼝关闭后⾃动删除
	- cookie 设置的cookie 过期时间之前⼀直有效，即使窗⼝或浏览器关闭

### 11 iframe有那些缺点？

- iframe 会阻塞主⻚⾯的Onload 事件
- 搜索引擎的检索程序⽆法解读这种⻚⾯，不利于SEO
- iframe 和主⻚⾯共享连接池，⽽浏览器对相同域的连接有限制，所以会影响⻚⾯的并⾏加载
- 使⽤iframe 之前需要考虑这两个缺点。如果需要使⽤iframe ，最好是通过javascript 动态给iframe 添加src 属性值，这样可以绕开以上两个问题

### 12 WEB标准以及W3C标准是什么?

标签闭合、标签⼩写、不乱嵌套、使⽤外链css 和js 、结构⾏为表现的分离

### 13 Doctype作⽤? 严格模式与混杂模式如何区分？它们有何意义?

- ⻚⾯被加载的时， link 会同时被加载，⽽@imort ⻚⾯被加载的时， link 会同时被加载，⽽@import 引⽤的CSS 会等到⻚⾯被加载完再加载 import 只在IE5 以上才能识别，⽽link 是XHTML 标签，⽆兼容问题 link ⽅式的样式的权重 ⾼于@import 的权重
- `<!DOCTYPE>` 声明位于⽂档中的最前⾯，处于 `<html>` 标签之前。告知浏览器的解析器， ⽤什么⽂档类型 规范来解析这个⽂档
- 严格模式的排版和 JS 运作模式是 以该浏览器⽀持的最⾼标准运⾏
- 在混杂模式中，⻚⾯以宽松的向后兼容的⽅式显示。模拟⽼式浏览器的⾏为以防⽌站点⽆法⼯作。 DOCTYPE 不存在或格式不正确会导致⽂档以混杂模式呈现

### 14 ⾏内元素有哪些？块级元素有哪些？ 空(void)元素有那些？⾏内元素和块级元素有什么区别？

- ⾏内元素有： a b span img input select strong
- 块级元素有： div ul ol li dl dt dd h1 h2 h3 h4… p
- 空元素：` <br> <hr> <img> <input> <link> <meta>`
- ⾏内元素不可以设置宽⾼，不独占⼀⾏
- 块级元素可以设置宽⾼，独占⼀⾏

### 15 HTML全局属性(global attribute)有哪些

- class :为元素设置类标识
- data-* : 为元素增加⾃定义属性
- draggable : 设置元素是否可拖拽
- id : 元素id ，⽂档内唯⼀
- lang : 元素内容的的语⾔
- style : ⾏内css 样式
- title : 元素相关的建议信息

### 16 Canvas和SVG有什么区别？

- svg 绘制出来的每⼀个图形的元素都是独⽴的DOM 节点，能够⽅便的绑定事件或⽤来修改。canvas 输出的是⼀整幅画布
- svg 输出的图形是⽮量图形，后期可以修改参数来⾃由放⼤缩⼩，不会失真和锯⻮。⽽canvas 输出标量画布，就像⼀张图⽚⼀样，放⼤会失真或者锯⻮

### 17 如何在⻚⾯上实现⼀个圆形的可点击区域？

svg
border-radius
纯js 实现 需要求⼀个点在不在圆上简单算法、获取⿏标坐标等等

### 18 ⽹⻚验证码是⼲嘛的，是为了解决什么安全问题

- 区分⽤户是计算机还是⼈的公共全⾃动程序。可以防⽌恶意破解密码、刷票、论坛灌⽔
- 有效防⽌⿊客对某⼀个特定注册⽤户⽤特定程序暴⼒破解⽅式进⾏不断的登陆尝试

### 19 viewport

```javascript
<meta name="viewport" content="width=device-width, initial-scale=1.0, minimu
// width 设置viewport宽度，为⼀个正整数，或字符串‘device-width’
// device-width 设备宽度
// height 设置viewport⾼度，⼀般设置了宽度，会⾃动解析出⾼度，可以不⽤设置
// initial-scale 默认缩放⽐例（初始缩放⽐例），为⼀个数字，可以带⼩数
// minimum-scale 允许⽤户最⼩缩放⽐例，为⼀个数字，可以带⼩数
// maximum-scale 允许⽤户最⼤缩放⽐例，为⼀个数字，可以带⼩数
// user-scalable 是否允许⼿动缩放
```

- 延伸提问
  - 怎样处理 移动端 1px 被 渲染成 2px 问题
- 局部处理
- mate 标签中的 viewport 属性 ， initial-scale 设置为 1
- rem 按照设计稿标准⾛，外加利⽤transfrome 的scale(0.5) 缩⼩⼀倍即可；
- 全局处理
  - mate 标签中的 viewport 属性 ， initial-scale 设置为 0.5
  - rem 按照设计稿标准⾛即可

### 20 渲染优化

- 禁⽌使⽤iframe （阻塞⽗⽂档onload 事件）
  - iframe 会阻塞主⻚⾯的Onload 事件
  - 搜索引擎的检索程序⽆法解读这种⻚⾯，不利于SEO
  - iframe 和主⻚⾯共享连接池，⽽浏览器对相同域的连接有限制，所以会影响⻚⾯的并⾏加载
  - 使⽤iframe 之前需要考虑这两个缺点。如果需要使⽤iframe ，最好是通过javascript
  - 动态给iframe 添加src 属性值，这样可以绕开以上两个问题
- 禁⽌使⽤gif 图⽚实现loading 效果（降低CPU 消耗，提升渲染性能）
- 使⽤CSS3 代码代替JS 动画（尽可能避免重绘重排以及回流）
- 对于⼀些⼩图标，可以使⽤base64位编码，以减少⽹络请求。但不建议⼤图使⽤，⽐较耗费CPU
  
    - ⼩图标优势在于：减少HTTP 请求；避免⽂件跨域；修改及时⽣效
- ⻚⾯头部的`<style></style> <script></script>` 会阻塞⻚⾯；（因为 Renderer进程中 JS 线程和渲染线程是互斥的）
- ⻚⾯中空的 href 和 src 会阻塞⻚⾯其他资源的加载 (阻塞下载进程)
- ⽹⻚gzip ， CDN 托管， data 缓存 ，图⽚服务器
- 前端模板 JS+数据，减少由于HTML 标签导致的带宽浪费，前端⽤变量保存AJAX请求结果，每次操作本地变量，不⽤请求，减少请求次数
- ⽤innerHTML 代替DOM 操作，减少DOM 操作次数，优化javascript 性能
- 当需要设置的样式很多时设置className ⽽不是直接操作style
  
-  少⽤全局变量、缓存DOM 节点查找的结果。减少IO 读取操作

### 21 你做的⻚⾯在哪些浏览器测试过？这些浏览器的内核分别是什么?

- IE : trident 内核
- Firefox ： gecko 内核

- Safari : webkit 内核
- Opera :以前是presto 内核， Opera 现已改⽤Google - Chrome 的Blink 内核
- Chrome:Blink (基于webkit ，Google与Opera Software共同开发)

### 22 div+css的布局较table布局有什么优点？

改版的时候更⽅便 只要改css ⽂件。
⻚⾯加载速度更快、结构化清晰、⻚⾯显示简洁。
表现与结构相分离。
易于优化（ seo ）搜索引擎更友好，排名更容易靠前。

### 23 你能描述⼀下渐进增强和优雅降级之间的不同吗

渐进增强：针对低版本浏览器进⾏构建⻚⾯，保证最基本的功能，然后再针对⾼级浏览器进⾏效果、交互等改进和追加功能达到更好的⽤户体验。
优雅降级：⼀开始就构建完整的功能，然后再针对低版本浏览器进⾏兼容。

区别：优雅降级是从复杂的现状开始，并试图减少⽤户体验的供给，⽽渐进增强则是从⼀个⾮常基础的，能够起作⽤的版本开始，并不断扩充，以适应未来环境的需要。降级（功能衰减）意味着往回看；⽽渐进增强则意味着朝前看，同时保证其根基处于安全地带

### 24 为什么利⽤多个域名来存储⽹站资源会更有效？

CDN 缓存更⽅便
突破浏览器并发限制
节约cookie 带宽
节约主域名的连接数，优化⻚⾯响应速度
防⽌不必要的安全问题

### 25 简述⼀下src与href的区别

- src ⽤于替换当前元素，href⽤于在当前⽂档和引⽤资源之间确⽴联系。
- src 是source 的缩写，指向外部资源的位置，指向的内容将会嵌⼊到⽂档中当前标签所在位置；在请求src 资源时会将其指向的资源下载并应⽤到⽂档内，例如js 脚本，img 图⽚和frame 等元素
  - `<script src ="js.js"></script>` 当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执⾏完毕，图⽚和框架等元素也如此，类似于将所指向资源嵌⼊当前标签内。这也是为什么将js脚本放在底部⽽不是头部
- href 是Hypertext Reference 的缩写，指向⽹络资源所在位置，建⽴和当前元素（锚点）或当前⽂档（链接）之间的链接，如果我们在⽂档中添加`<link href="common.css" rel="stylesheet"/>`那么浏览器会识别该⽂档为css ⽂件，就会并⾏下载资源并且不会停⽌对当前⽂档的处理。这也是为什么**建议使⽤link ⽅式来加载css ，⽽不是使⽤@import ⽅式**

### 26 知道的⽹⻚制作会⽤到的图⽚格式有哪些？

- png-8 、png-24 、jpeg 、gif 、svg
- Webp： WebP 格式，⾕歌（google）开发的⼀种旨在加快图⽚加载速度的图⽚格式。图⽚压缩体积⼤约只有JPEG 的2/3 ，并能节省⼤量的服务器带宽资源和数据空间。Facebook Ebay 等知名⽹站已经开始测试并使⽤WebP 格式。
  在质量相同的情况下，WebP格式图像的体积要⽐JPEG格式图像⼩40% 。
- Apng：全称是“Animated Portable Network Graphics” , 是PNG的位图动画扩展，可以实现png格式的动态图⽚效果。04年诞⽣，但⼀直得不到各⼤浏览器⼚商的⽀持，直到⽇前得到 iOS safari 8 的⽀持，有望代替GIF 成为下⼀代动态图标准

### 27 从⽤户刷新⽹⻚开始，⼀次js请求⼀般情况下有哪些地⽅会有缓存处理？

dns 缓存， cdn 缓存，浏览器缓存，服务器缓存

### 28 优化图⽚的加载

- **图⽚懒加载**，在⻚⾯上的未可视区域可以添加⼀个滚动事件，判断图⽚位置与浏览器顶端的距离与⻚⾯的距离，如果前者⼩于后者，优先加载。
- 如果为幻灯⽚、相册等，可以使⽤**图⽚预加载**技术，将当前展示图⽚的前⼀张和后⼀张优先下载。
- 如果图⽚为css图⽚，可以使⽤CSSsprite ， SVGsprite ， Iconfont 、Base64 等技术。
- 如果图⽚过⼤，可以使⽤特殊编码的图⽚，加载时会先加载⼀张压缩的特别厉害的缩略图，以提⾼⽤户体验。
- 如果图⽚展示区域⼩于图⽚的真实⼤⼩，则因在服务器端根据业务需要先⾏进⾏图⽚压缩，图⽚压缩后⼤⼩与展示⼀致。

### 29 web开发中会话跟踪的⽅法

cookie
session
url 重写
隐藏input
ip 地址

### 30 HTTP request报⽂结构

1. ⾸⾏是Request-Line包括：**请求⽅法，请求URI，协议版本，CRLF**
2. ⾸⾏之后是若⼲⾏请求头，包括general-header，request-header或者entity-header，每个⼀⾏以CRLF结束
3. 请求头和消息实体之间有⼀个CRLF分隔
4. 根据实际请求需要可能包含⼀个消息实体

⼀个请求报⽂例⼦如下：

```
GET /Protocols/rfc2616/rfc2616-sec5.html HTTP/1.1
Host: www.w3.org
Connection: keep-alive
Cache-Control: max-age=0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML,
Referer: https://www.google.com.hk/
Accept-Encoding: gzip,deflate,sdch
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6
Cookie: authorstyle=yes
If-None-Match: "2cc8-3e3073913b100"
If-Modified-Since: Wed, 01 Sep 2004 13:24:52 GMT
name=qiu&age=25
```

### 31 HTTP response报⽂结构

- ⾸⾏是状态⾏包括：HTTP版本，状态码，状态描述，后⾯跟⼀个CRLF
- ⾸⾏之后是若⼲⾏响应头，包括：通⽤头部，响应头部，实体头部
- 响应头部和响应实体之间⽤⼀个CRLF空⾏分隔
- 最后是⼀个可能的消息实体

响应报⽂例⼦如下：

```
HTTP/1.1 200 OK
Date: Tue, 08 Jul 2014 05:28:43 GMT
Server: Apache/2
Last-Modified: Wed, 01 Sep 2004 13:24:52 GMT
ETag: "40d7-3e3073913b100"
Accept-Ranges: bytes
Content-Length: 16599
Cache-Control: max-age=21600
Expires: Tue, 08 Jul 2014 11:28:43 GMT
P3P: policyref="http://www.w3.org/2001/05/P3P/p3p.xml"
Content-Type: text/html; charset=iso-8859-1
{"name": "qiu", "age": 25}
```

## 二、JavaScript

### 1. 闭包



# 高阶部分

## React专题

### 1 React 中 keys 的作⽤是什么？

Keys 是 React ⽤于追踪哪些列表中元素被修改、被添加或者被移除的辅助标识。

在开发过程中，我们需要保证某个元素的 key 在其同级元素中具有唯⼀性。在 React Diff 算法中React 会借助元素的 Key 值来判断该元素是新近创建的还是被移动⽽来的元素，从⽽减少不必要的元素重渲染。此外React 还需要借助 Key 值来判断元素与本地状态的关联关系

### 2 传⼊ setState 函数的第⼆个参数的作⽤是什么？

该函数会在setState 函数调⽤完成并且组件开始重渲染的时候被调⽤，我们可以⽤该函数来监听渲染是否完成

```javascript
this.setState(
	{ username: 'tylermcginnis33' },
	() => console.log('setState has finished and the component has re-rendere
)
this.setState((prevState, props) => {
	return {
		streak: prevState.streak + props.count
	}
})
```

### 3 React 中 refs 的作⽤是什么

- Refs 是 React 提供给我们的安全访问 DOM 元素或者某个组件实例的句柄
- 可以为元素添加ref 属性然后在回调函数中接受该元素在 DOM 树中的句柄，该值会作为回调函数的第⼀个参数返回

### 4 在⽣命周期中的哪⼀步你应该发起 AJAX 请求

我们应当将AJAX 请求放到 componentDidMount 函数中执⾏，主要原因有下:

- React 下⼀代调和算法 Fiber 会通过开始或停⽌渲染的⽅式优化应⽤性能，其会影响到componentWillMount 的触发次数。对于 componentWillMount 这个⽣命周期函数的调⽤次数会变得不确定， React 可能会多次频繁调⽤ componentWillMount 。如果我们将 AJAX 请求放到 componentWillMount 函数中，那么显⽽易⻅其会被触发多次，⾃然也就不是好的选择。
- 如果我们将AJAX 请求放置在⽣命周期的其他函数中，我们并不能保证请求仅在组件挂载完毕后才会要求响应。如果我们的数据请求在组件挂载之前就完成，并且调⽤了setState 函数将数据添加到组件状态中，对于未挂载的组件则会报错。⽽在componentDidMount 函数中进⾏ AJAX 请求则能有效避免这个问题

### 5 shouldComponentUpdate 的作⽤

shouldComponentUpdate 允许我们⼿动地判断是否要进⾏组件更新，根据组件的应⽤场景设置函数的合理返回值能够帮我们避免不必要的更新

### 6 如何告诉 React 它应该编译⽣产环境版

通常情况下我们会使⽤ Webpack 的 DefinePlugin ⽅法来将 NODE_ENV变量值设置为 production 。编译版本中 React 会忽略 propType 验证以及其他的告警信息，同时还会降低代码库的⼤⼩， React 使⽤了 Uglify插件来移除⽣产环境下不必要的注释等信息

### 7 概述下 React 中的事件处理逻辑

为了解决跨浏览器兼容性问题， React 会将浏览器原⽣事件（ Browser Native Event ）封装为合成事件（ SyntheticEvent ）传⼊设置的事件处理器中。这⾥的合成事件提供了与原⽣事件相同的接⼝，不过它们屏蔽了底层浏览器的细节差异，保证了⾏为的⼀致性。另外有意思的是， React 并没有直接将事件附着到⼦元素上，⽽是以单⼀事件监听器的⽅式将所有的事件发送到顶层进⾏处理。这样 React 在更新 DOM 的时候就不需要考虑如何去处理附着在 DOM 上的事件监听器，最终达到优化性能的⽬的

### 8 createElement 与 cloneElement 的区别是什么

createElement 函数是 JSX 编译之后使⽤的创建 React Element 的函数，⽽ cloneElement 则是⽤于复制某个元素并传⼊新的 Props

### 9 redux中间件

中间件提供第三⽅插件的模式，⾃定义拦截 action -> reducer 的过程。变为 action -> middlewares -> reducer 。这种机制可以让我们改变数据流，实现如异步action ， action 过滤，⽇志输出，异常报告等功能

- redux-logger ：提供⽇志输出
- redux-thunk ：处理异步操作
- redux-promise ：处理异步操作， actionCreator 的返回值是promise

### 10 redux有什么缺点

- ⼀个组件所需要的数据，必须由⽗组件传过来，⽽不能像flux 中直接从store 取。
- 当⼀个组件相关数据更新时，即使⽗组件不需要⽤到这个组件，⽗组件还是会重新render ，可能会有效率影响，或者需要写复杂的shouldComponentUpdate 进⾏判断。

### 11 react组件的划分业务组件技术组件？

- 根据组件的职责通常把组件分为UI组件和容器组件。
- UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。
- 两者通过React-Redux 提供connect ⽅法联系起来

### 12 react⽣命周期函数

初始化阶段

- getDefaultProps :获取实例的默认属性
- getInitialState :获取每个实例的初始化状态
- componentWillMount ：组件即将被装载、渲染到⻚⾯上
- render :组件在这⾥⽣成虚拟的DOM 节点
- omponentDidMount :组件真正在被装载之后

运⾏中状态

- componentWillReceiveProps :组件将要接收到属性的时候调⽤
- shouldComponentUpdate :组件接受到新属性或者新状态的时候（可以返回false，接收数据后不更新，阻⽌render 调⽤，后⾯的函数不会被继续执⾏了）
- componentWillUpdate :组件即将更新不能修改属性和状态
- render :组件重新描绘
- componentDidUpdate :组件已经更新

销毁阶段

- componentWillUnmount :组件即将销毁

### 13 react性能优化是哪个周期函数

shouldComponentUpdate 这个⽅法⽤来判断是否需要调⽤render⽅法重新描绘dom。因为dom的描绘⾮常消耗性能，如果我们能在shouldComponentUpdate⽅法中能够写出更优化的dom diff 算法，可以极⼤的提⾼性能

### 14 为什么虚拟dom会提⾼性能

虚拟dom 相当于**在js 和真实dom 中间加了⼀个缓存**，利⽤dom diff 算法避免了没有必要的dom 操作，从⽽提⾼性能

具体实现步骤如下

- ⽤ JavaScript 对象结构表示 DOM 树的结构；然后⽤这个树构建⼀个真正的 DOM 树，插到⽂档当中
- 当状态变更的时候，重新构造⼀棵新的对象树。然后⽤新的树和旧的树进⾏⽐较，记录两棵树差异
- 把2所记录的差异应⽤到步骤1所构建的真正的DOM 树上，视图就更新

### 15 diff算法

- 把树形结构按照层级分解，只⽐较同级元素。
- 给列表结构的每个单元添加唯⼀的key 属性，⽅便⽐较。
- React 只会匹配相同 class 的 component （这⾥⾯的class 指的是组件的名字）
- 合并操作，调⽤ component 的 setState ⽅法的时候, React 将其标记为 - dirty .到每⼀个事件循环结束, React 检查所有标记 dirty 的 component 重新绘制.
- 选择性⼦树渲染。开发⼈员可以重写shouldComponentUpdate 提⾼diff 的性能

### 16 react性能优化⽅案

重写shouldComponentUpdate 来避免不必要的dom操作
使⽤ production 版本的react.js
使⽤key 来帮助React 识别列表中所有⼦组件的最⼩变化

### 17 简述flux 思想

Flux 的最⼤特点，就是数据的"单向流动"。

- ⽤户访问 View
- View 发出⽤户的 Action
- Dispatcher 收到Action ，要求 Store 进⾏相应的更新
- Store 更新后，发出⼀个"change" 事件
- View 收到"change" 事件后，更新⻚⾯

### 18 说说你⽤react有什么坑点？

1. JSX做表达式判断时候，需要强转为boolean类型；如果不使⽤ !!b 进⾏强转数据类型，会在⻚⾯⾥⾯输出 0 。

   ```javascript
   render() {
   	const b = 0;
   	return <div>
   		{
   			!!b && <div>这是⼀段⽂本</div>
   		}
   	</div>
   }
   ```

2. 尽量不要在 componentWillReviceProps ⾥使⽤ setState，如果⼀定要使⽤，那么需要判断结束条件，不然会出现⽆限重渲染，导致⻚⾯崩溃

3. 给组件添加ref时候，尽量不要使⽤匿名函数，因为当组件更新的时候，匿名函数会被当做新的prop处理，让ref属性接受到新函数的时候，react内部会先清空ref，也就是会以null为回调参数先执⾏⼀次ref这个props，然后在以该组件的实例执⾏⼀次ref，所以⽤匿名函数做ref的时候，有的时候去ref赋值后的属性会取到null

4. 遍历⼦节点的时候，不要⽤ index 作为组件的 key 进⾏传⼊

### 19 我现在有⼀个button，要⽤react在上⾯绑定点击事件，要怎么做？

```javascript
class Demo {
	render() {
		return <button onClick={(e) => {
			alert('我点击了按钮')
		}}>
		按钮
		</button>
	}
}
```

你觉得你这样设置点击事件会有什么问题吗？

由于onClick 使⽤的是匿名函数，所有每次重渲染的时候，会把该onClick 当做⼀个新的prop 来处理，会将内部缓存的onClick 事件进⾏重新赋值，所以相对直接使⽤函数来说，可能有⼀点的性能下降

修改：

```javascript
class Demo {
	onClick = (e) => {
		alert('我点击了按钮')
	}
	render() {
		return <button onClick={this.onClick}>
		按钮
		</button>
	}
```

### 20 react 的虚拟dom是怎么实现的

为了实现虚拟DOM ，我们需要把每⼀种节点类型抽象成对象，每⼀种节点类型有⾃⼰的属性，也就是prop，每次进⾏diff 的时候， react 会先⽐较该节点类型，假如节点类型不⼀样，那么react 会直接删除该节点，然后直接创建新的节点插⼊到其中，假如节点类型⼀样，那么会⽐较prop 是否有更新，假如有prop 不⼀样，那么react会判定该节点有更新，那么重渲染该节点，然后在对其⼦节点进⾏⽐较，⼀层⼀层往下，直到没有⼦节点

### 21 react 的渲染过程中，兄弟节点之间是怎么处理的？也就是key值不⼀样的时候

通常我们输出节点的时候都是map⼀个数组然后返回⼀个ReactNode ，为了⽅便react 内部进⾏优化，我们必须给每⼀个reactNode 添加key ，这个key prop 在设计值处不是给开发者⽤的，⽽是给react⽤的，⼤概的作⽤就是给每⼀个reactNode 添加⼀个身份标识，⽅便react进⾏识别，在重渲染过程中，如果key⼀样，若组件属性有所变化，则react 只更新组件对应的属性；没有变化则不更新，如果key不⼀样，则react先销毁该组件，然后重新创建该组件

### 22 给我介绍⼀下react

1. 以前我们没有jquery的时候，我们⼤概的流程是**从后端通过ajax获取到数据然后使⽤jquery⽣成dom结果然后更新到⻚⾯当中**，但是随着业务发展，我们的项⽬可能会越来越复杂，我们每次请求到数据，或则数据有更改的时候，我们⼜需要重新组装⼀次dom结构，然后更新⻚⾯，这样我们**⼿动同步dom和数据的成本就越来越⾼**，⽽且频繁的操作dom，也使我们**⻚⾯的性能慢慢的降低**。
2. 这个时候mvvm出现了，**mvvm的双向数据绑定可以让我们在数据修改的同时同步dom的更新，dom的更新也可以直接同步我们数据的更改，这个特定可以⼤⼤降低我们⼿动去维护dom更新的成本**，mvvm为react的特性之⼀，虽然react属于单项数据流，需要我们⼿动实现双向数据绑定。
3. 有了mvvm还不够，因为**如果每次有数据做了更改，然后我们都全量更新dom结构的话，也没办法解决我们频繁操作dom结构(降低了⻚⾯性能)的问题**，为了解决这个问题，react内部实现了⼀套==虚拟dom结构==，也就是⽤js实现的⼀套dom结构，他的作⽤是讲真实dom在js中做⼀套缓存，每次有数据更改的时候，react内部先使⽤算法，也就是鼎鼎有名的diff算法对dom结构进⾏对⽐，**找到那些我们需要新增、更新、删除的dom节点，然后⼀次性对真实DOM进⾏更新，这样就⼤⼤降低了操作dom的次数**。 那么diff算法是怎么运作的呢，⾸先，diff针对类型不同的节点，会直接判定原来节点需要卸载并且⽤新的节点来装载卸载的节点的位置；针对于节点类型相同的节点，会对⽐这个节点的所有属性，如果节点的所有属性相同，那么判定这个节点不需要更新，如果节点属性不相同，那么会判定这个节点需要更新，react会更新并重渲染这个节点。
4. react**设计之初是主要负责UI层的渲染**，虽然每个组件有⾃⼰的state，state表示组件的状态，当状态需要变化的时候，需要使⽤setState更新我们的组件，但是，我们想通过⼀个组件重渲染它的兄弟组件，我们就需要将组件的状态提升到⽗组件当中，让⽗组件的状态来控制这两个组件的重渲染，当我们组件的层次越来越深的时候，状态需要⼀直往下传，⽆疑加⼤了我们代码的复杂度，我们需要⼀个==状态管理中⼼==，来帮我们管理我们状态state。
5. 这个时候，redux出现了，我们可以将所有的state交给redux去管理，当我们的某⼀个state有变化的时候，依赖到这个state的组件就会进⾏⼀次重渲染，这样就解决了我们的我们需要⼀直把state往下传的问题。redux有action、reducer的概念，**action为唯⼀修改state的来源，reducer为唯⼀确定state如何变化的⼊⼝**，这使得redux的数据流⾮常规范，同时也暴露出了redux代码的复杂，本来那么简单的功能，却需要完成那么多的代码。
6. 后来，社区就出现了另外⼀套解决⽅案，也就是mobx，它推崇代码简约易懂，只需要定义⼀个可观测的对象，然后哪个组价使⽤到这个可观测的对象，并且这个对象的数据有更改，那么这个组件就会重渲染，⽽且mobx内部也做好了是否重渲染组件的⽣命周期shouldUpdateComponent，不建议开发者进⾏更改，这使得我们使⽤mobx开发项⽬的时候可以简单快速的完成很多功能，连redux的作者也推荐使⽤mobx进⾏项⽬开发。但是，随着项⽬的不断变⼤，mobx也不断暴露出了它的缺点，就是数据流太随意，出了bug之后不好追溯数据的流向，这个缺点正好体现出了redux的优点所在，所以**针对于⼩项⽬来说，社区推荐使⽤mobx，对⼤项⽬推荐使⽤redux**