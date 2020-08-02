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

### 2 `<img>` 的title 和alt 有什么区别

- 通常当⿏标滑动到元素上的时候显示
- alt 是<img> 的特有属性，是图⽚内容的等价描述，**⽤于图⽚⽆法加载时显示**、读屏器阅读图⽚。可提图⽚⾼可访问性，**除了纯装饰图⽚外都必须设置有意义的值**，搜索引擎会重点分析。

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

### 9 html5有哪些新特性、移除了那些元素？



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

