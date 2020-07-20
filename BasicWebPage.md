---
$typora-root-url: Typora图库\BasicWeb
typora-root-url: Typora图库\BasicWeb
---

# HTML

## 入门

推荐使用线上 [CodePan]( https://codepen.io/pen?editors=1010 )

默认框架：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>我的测试站点</title>
  </head>
  <body>
    <p>这是我的页面</p>
  </body>
</html>
```

特殊字符：

| 原义字符 | 等价字符引用 |
| :------- | :----------- |
| <        | &lt;         |
| >        | &gt;         |
| "        | &quot;       |
| '        | &apos;       |
| &        | &amp;        |

注释：

```html
<!-- <p>我在注释内！</p> -->
```

在HTML中应用CSS和JavaScript :

-  link 元素经常位于文档的头部。这个link元素有2个属性，rel="stylesheet"表明这是文档的样式表，而 href包含了样式表文件的路径：

  ```html
  <link rel="stylesheet" href="my-css-file.css">
  ```

- script 部分没必要非要放在文档头部；实际上，把它放在文档的尾部（在 /body标签之前）是一个更好的选择，这样可以确保在加载脚本之前浏览器已经解析了HTML内容（如果脚本加载某个不存在的元素，浏览器会报错）。

  ```html
  <script src="my-js-file.js"></script>
  ```

## 文字处理

段落：p

标题：h1

列表：

- 无序：

  ```html
  <ul>
    <li>豆浆</li>
    <li>油条</li>
    <li>豆汁</li>
    <li>焦圈</li>
  </ul>
  ```

  

- 有序：

  ```html
  <ol>
    <li>沿着条路走到头</li>
    <li>右转</li>
    <li>直行穿过第一个十字路口</li>
    <li>在第三个十字路口处左转</li>
    <li>继续走 300 米，学校就在你的右手边</li>
  </ol>
  ```

- 自定义列表： 以 dl 标签开始。每个自定义列表项以 dt 开始。每个自定义列表项的定义以 dd 开始。 

  ```html
  <dl>
    <dt>内心独白</dt>
      <dd>戏剧中，某个角色对自己的内心活动或感受进行念白表演，这些台词只面向观众，而其他角色不会听到。</dd>
    <dt>语言独白</dt>
      <dd>戏剧中，某个角色把自己的想法直接进行念白表演，观众和其他角色都可以听到。</dd>
    <dt>旁白</dt>
      <dd>戏剧中，为渲染幽默或戏剧性效果而进行的场景之外的补充注释念白，只面向观众，内容一般都是角色的感受、想法、以及一些背景信息等。</dd>
  </dl>
  ```

斜体：em或i

粗体：strong或b

下划线：u

换行：br

 水平分割线 ：hr

## 链接

 通过将文本（或其他内容)转换为a元素内的链接来创建基本链接 。

- href：链接地址

- title： 关于链接的补充有用信息 。 当鼠标指针悬停在链接上时，标题将作为提示信息出现 

-  块状链接：想要将一个图像转换为链接，你只需把图像元素放到a标签中间。 

  ```html
  <a href="https://www.mozilla.org/en-US/">
    <img src="mozilla-image.png" alt="mozilla logo that links to the mozilla homepage">
  </a>
  ```

-  download : 默认的保存文件名 

文档片段： 链接到HTML文档的特定部分 。 要做到这一点，你必须首先给要链接到的元素分配一个`id`属性。例如，如果你想链接到一个特定的标题，可以这样做： 

```html
<h2 id="Mailing_address">Mailing address</h2>

<!-->链接到那个特定的id，您可以在URL的结尾使用一个井号指向它<-->
<p>Want to write us a letter? Use our <a href="contacts.html#Mailing_address">mailing address</a>.</p>

<!-->甚至可以在同一份文档下，通过链接文档片段，来链接到同一份文档的另一部分：<-->
<p>The <a href="#Mailing_address">company mailing address</a> can be found at the bottom of this page.</p>
```

电子邮件链接 ： mailto:link （链接）  。链接简单说明收件人的电子邮件地址。 

```html
<a href="mailto:nowhere@mozilla.org">Send email to nowhere</a>
```

 实际上，邮件地址甚至是可选的。如果你忘记了（也就是说，你的`href`仅仅只是简单的"`mailto:`"），一个新的发送电子邮件的窗口也会被用户的邮件客户端打开，只是没有收件人的地址信息，这通常在“分享”链接是很有用的，用户可以发送给他们选择的地址邮件 

 除了电子邮件地址，您还可以提供其他信息。事实上，任何标准的邮件头字段可以被添加到你提供的邮件URL。 其中最常用的是主题(subject)、抄送(cc)和主体(body) (这不是一个真正的头字段，但允许您为新邮件指定一个短内容消息)。 每个字段及其值被指定为查询项。 

```html
<a href="mailto:nowhere@mozilla.org?cc=name2@rapidtables.com&bcc=name3@rapidtables.com&subject=The%20subject%20of%20the%20email&body=The%20body%20of%20the%20email">
  Send mail with cc, bcc, subject and body
</a>
```

>  **注意:** 每个字段的值必须是URL编码的。 也就是说，不能有非打印字符（不可见字符比如制表符、换行符、分页符）和空格 [percent-escaped](http://en.wikipedia.org/wiki/Percent-encoding). 同时注意使用问号（`?`）来分隔主URL与参数值，以及使用&符来分隔`mailto:`中的各个参数。 这是标准的URL查询标记方法。阅读 [The GET method](https://developer.mozilla.org/en-US/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data#The_GET_method) 以了解哪种URL查询标记方法是更常用的。 

## 图片

 用img元素来把图片放到网页上。它是一个空元素（它不需要包含文本内容或闭合标签），最少只需要一个 src（一般读作其全称 *source）*来使其生效。

-  src 属性：包含了指向我们想要引入的图片的路径，可以是相对路径或绝对URL，就像a 元素的href属性一样。
- alt： 对图片的文字描述，用于在图片无法显示或不能被看到的情况。 
- width：宽度
- height：高度
- title： 给我们一个鼠标悬停提示，看起来就像链接标题 

```html
<div class="figure">
  <img src="/images/dinosaur_small.jpg"
     alt="一只恐龙头部和躯干的骨架，它有一个巨大的头，长着锋利的牙齿。"
     width="400"
     height="341">
  <p>曼彻斯特大学博物馆展出的一只霸王龙的化石</p>
</div>
```

 有一个更好的做法是使用 HTML5 的figure和$ <figcaption元素，它正是为此而被创造出来的：为图片提供一个语义容器，在标题和图片之间建立清晰的关联。我们之前的例子可以重写为: 

```html
<figure>
  <img src="https://raw.githubusercontent.com/mdn/learning-area/master/html/multimedia-and-embedding/images-in-html/dinosaur_small.jpg"
     alt="一只恐龙头部和躯干的骨架，它有一个巨大的头，长着锋利的牙齿。"
     width="400"
     height="341">
  <figcaption>曼彻斯特大学博物馆展出的一只霸王龙的化石</figcaption>
</figure>
```

 这个figcaption元素 告诉浏览器和其他辅助的技术工具这段说明文字描述了figure元素的内容. 

注意figure里不一定要是一张图片，只要是一个这样的独立内容单元：

- 用简洁、易懂的方式表达意图。
- 可以置于页面线性流的某处。
- 为主要内容提供重要的补充说明。

 figure可以是几张图片、一段代码、音视频、方程、表格或别的。

## 音频和视频

视频video： 嵌入一段视频。 

- src： 指向你想要嵌入网页当中的视频资源 

- controls： 用户必须能够控制视频和音频的回放功能。你可以使用 `controls` 来包含浏览器提供的控件界面，同时你也可以使用合适的JavaScript API 创建自己的界面。界面中至少要包含开始、停止以及调整音量的功能。 

- video **标签内的内容** ： 这个叫做**后备内容** — 当浏览器不支持 video  标签的时候，就会显示这段内容  ，这使得我们能够对旧的浏览器提供回退内容。你可以添加任何后备内容，在这个例子中我们提供了一个指向这个视频文件的链接，从而使用户至少可以访问到这个文件，而不会局限于浏览器的支持。 

  ```html
  <video src="rabbit320.webm" controls>
    <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
  </video>
  ```

 如果你尝试使用像 Safari 或者 Internet Explorer 这些浏览器来访问上面的链接。视频并不会播放，这是因为不同的浏览器对视频格式的支持不同。像 MP3、MP4、WebM这些术语叫做容器格式。他们定义了构成媒体文件的音频轨道和视频轨道的储存结构，其中还包含描述这个媒体文件的元数据，以及用于编码的编码译码器等等。 

- WebM 容器通常包括了 Opus 或 Vorbis 音频和 VP8/VP9 视频。这在所有的现代浏览器中都支持，除了他们的老版本。
- MP4 容器通常包括 AAC 以及 MP3 音频和 H.264 视频。这在所有的现代浏览器中都支持，还有 Internet Explorer。
- 老式的 Ogg 容器往往支持 Ogg Vorbis  音频和 Ogg Theora 视频。主要在 Firefox 和 Chrome 当中支持，不过这个容器已经被更强大的 WebM 容器所取代。

 使用几个不同格式的文件来兼容不同的浏览器。 

```html
<video controls>
  <source src="rabbit320.mp4" type="video/mp4">
  <source src="rabbit320.webm" type="video/webm">
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
</video>
```

 在这个例子当中，浏览器将会检查source标签，并且播放第一个与其自身 codec 相匹配的媒体。你的视频应当包括 WebM 和 MP4 两种格式，这两种在目前已经足够支持大多数平台和浏览器。  每个source标签页含有一个 type 属性，这个属性是可选的，但是建议你添加上这个属性 — 它包含了视频文件的MIME types，同时浏览器也会通过检查这个属性来迅速的跳过那些不支持的格式。如果你没有添加 type属性，浏览器会尝试加载每一个文件，直到找到一个能正确播放的格式，这样会消耗掉大量的时间和资源。 

新的特征：

- width和height：视频尺寸
- autoplay： 这个属性会使音频和视频内容立即播放，即使页面的其他部分还没有加载完全。建议不要应用这个属性在你的网站上，因为用户们会比较反感自动播放的媒体文件。 
- loop： 可以让音频或者视频文件循环播放。同样不建议使用，除非有必要。 
- muted： 导致媒体播放时，默认关闭声音。 
- poster： 指向了一个图像的URL，这个图像会在视频播放前显示。通常用于粗略的预览或者广告。 
- preload：用来缓冲较大的文件，有3个值可选：
  - `"none"` ：不缓冲
  - `"auto"` ：页面加载后缓存媒体文件
  - `"metadata"` ：仅缓冲文件的元数据

```html
<video controls width="400" height="400"
       autoplay loop muted
       poster="poster.png">
  <source src="rabbit320.mp4" type="video/mp4">
  <source src="rabbit320.webm" type="video/webm">
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
</video>
```

audio和video 使用方式几乎完全相同，有一些细微的差别。

-  不支持 `width`/`height` 属性 — 由于其并没有视觉部件，也就没有可以设置 `width`/`height` 的内容。 
-  也不支持 `poster` 属性 — 同样，没有视觉部件。 

## 其他嵌入

iframe 元素旨在允许您将其他Web文档嵌入到当前文档中。这很适合将第三方内容纳入您的网站 ， 例如来自在线视频提供商的视频等评论系统，在线地图提供商，广告横幅等。 

- allowfullscreen： 可以通过全屏API 设置为全屏模式（稍微超出本文的范围）。 
- frameborder： 如果设置为1，则会告诉浏览器在此框架和其他框架之间绘制边框，这是默认行为。0删除边框。不推荐这样设置，因为在 CSS中 可以更好地实现相同的效果。 （border：none）
- src：URL路径
- width和height：宽度和高度
- 备选内容： 可以在iframe></iframe标签之间包含备选内容，如果浏览器不支持iframe，将会显示备选内容，这种情况下，我们已经添加了一个到该页面的链接。现在您几乎不可能遇到任何不支持iframe的浏览器。 
- sandbox: 该属性需要在已经支持其他iframe功能（例如IE 10及更高版本）但稍微更现代的浏览器上才能工作，该属性可以提高安全性设置。 给嵌入的内容仅能完成自己工作的权限*。*  一个允许包含在其里的代码以适当的方式执行或者用于测试，但不能对其他代码库（意外或恶意）造成任何损害的容器称为sandbox。  应该使用没有参数的`sandbox`属性来强制执行所有可用的限制 。 如果绝对需要，您可以逐个添加权限（`sandbox=""`属性值内）  其中重要的一点是，你**永远不应该**同时添加`allow-scripts`和`allow-same-origin`到你的`sandbox`属性中-在这种情况下，嵌入式内容可以绕过阻止站点执行脚本的同源安全策略，并使用JavaScript完全关闭沙盒。 

 **注意**：为了提高速度，在主内容完成加载后，使用JavaScript设置iframe的`src`属性是个好主意。这使您的页面可以更快地被使用，并减少您的官方页面加载时间 

embed和object 是用来嵌入多种类型的外部内容的通用嵌入工具，其中包括像Java小程序和Flash，PDF（可在浏览器中显示为一个PDF插件）这样的插件技术，甚至像视频，SVG和图像的内容！ 

 然而，您不太可能使用这些元素 - Applet几年来一直没有被使用；由于许多原因，Flash不再受欢迎  ；PDF更倾向于被链接而不是被嵌入；其他内容，如图像和视频都有更优秀、更容易元素来处理。插件和这些嵌入方法真的是一种传统技术，我们提及它们主要是为了以防您在某些情况下遇到问题，比如内部网或企业项目等。 

 如果您发现自己需要嵌入插件内容，那么您至少需要一些这样的信息： 

![嵌入信息](/嵌入信息.png)

注意：object需要data属性，type属性或两者。 如果您同时使用这两个，您也可以使用该`typemustmatch`属性  。`typemustmatch`保持嵌入文件不运行，除非`type`属性提供正确的媒体类型。`typemustmatch`因此，当您嵌入来自不同来源的内容（可以防止攻击者通过插件运行任意脚本）时，可以赋予重要的安全优势。 

[举例1]( https://mdn.github.io/learning-area/html/multimedia-and-embedding/other-embedding-technologies/embed-flash.html )和[举例2](http://mdn.github.io/learning-area/html/multimedia-and-embedding/other-embedding-technologies/object-pdf.html)：

```html
<!-->使用该embed元素嵌入Flash影片的示例<-->
<embed src="whoosh.swf" quality="medium"
       bgcolor="#ffffff" width="550" height="400"
       name="whoosh" align="middle" allowScriptAccess="sameDomain"
       allowFullScreen="false" type="application/x-shockwave-flash"
       pluginspage="http://www.macromedia.com/go/getflashplayer">
       
<!-->一个object将PDF嵌入一个页面的例子<-->
<object data="mypdf.pdf" type="application/pdf"
        width="800" height="1200" typemustmatch>
  <p>You don't have a PDF plugin, but you can <a href="myfile.pdf">download the PDF file.</a></p>
</object>
```

canvas用于JavaScript生成的2D和3D图形 . 默认情况下 canvas 元素没有边框和内容。 

```html
<canvas id="myCanvas" width="200" height="100"
style="border:1px solid #000000;">
</canvas>
```

 canvas 元素本身是没有绘图能力的。所有的绘制工作必须在 JavaScript 内部完成： 

```javascript
var c=document.getElementById("myCanvas"); //找到canvas元素
var ctx=c.getContext("2d"); //创建context对象
//再对context对象进行处理
```

## 矢量图形

 在网上，你会和两种类型的图片打交道 — 位图和矢量图: 

- 位图使用像素网格来定义 — 一个位图文件精确得包含了每个像素的位置和它的色彩信息。流行的位图格式包括 Bitmap (`.bmp`), PNG (`.png`), JPEG (`.jpg`), and GIF (`.gif`.)
- 矢量图使用算法来定义 — 一个矢量图文件包含了图形和路径的定义，电脑可以根据这些定义计算出当它们在屏幕上渲染时应该呈现的样子。SVG格式可以让我们创造用于 Web 的精彩的矢量图形。

 我们来一个例子 , 并排展示了两个看起来一致的图像，一个红色的五角星以及黑色的阴影。不同的是，左边的是 PNG，而右边的是 SVG 图像。  当你放大网页的时候，区别就会变得明显起来 — 随着你的放大，PNG 图片变得像素化了，因为它存储是每个像素的颜色和位置信息 — 当它被放大时，每个像素就被放大以填满屏幕上更多的像素，所以图像就会开始变得马赛克感觉。矢量图像看起来仍然效果很好且清晰，因为无论它的尺寸如何，都使用算法来计算出图像的形状，仅仅是根据放大的倍数来调整算法中的值。 

![raster-vector-default-size](/raster-vector-default-size.png)

![raster-vector-zoomed](/raster-vector-zoomed.png)

 此外，矢量图形相较于同样的位图，通常拥有更小的体积，因为它们仅需储存少量的算法，而不是逐个储存每个像素的信息。 

SVG是用于描述矢量图像的XML语言。 它基本上是像HTML一样的标记，只是你有许多不同的元素来定义要显示在图像中的形状，以及要应用于这些形状的效果。 SVG用于标记图形，而不是内容。 非常简单，你有一些元素来创建简单图形，如circle和rect。更高级的SVG功能包括feColorMatrix（使用变换矩阵转换颜色）animate（矢量图形的动画部分）和mask（在图像顶部应用模板） 

```html
<svg version="1.1"
     baseProfile="full"
     width="300" height="200"
     xmlns="http://www.w3.org/2000/svg">
  <rect width="100%" height="100%" fill="black" /> //黑色矩形
  <circle cx="150" cy="100" r="90" fill="blue" /> //蓝色圆形
</svg>
```

 从上面的例子可以看出，SVG很容易手工编码。  但是对于复杂的图像，这很快就开始变得非常困难。  为了创建SVG图像，大多数人使用矢量图形编辑器，如 [Inkscape](https://inkscape.org/en/) 或 [Illustrator](https://en.wikipedia.org/wiki/Adobe_Illustrator)。 这些软件包允许您使用各种图形工具创建各种插图，并创建照片的近似值（例如Inkscape的跟踪位图功能）。 

SVG除了迄今为止所描述的以外还有其他优点：

- 矢量图像中的文本仍然可访问。
- SVG 可以很好地适应样式/脚本，因为图像的每个组件都是可以通过CSS或通过JavaScript编写的样式的元素。

那么为什么会有人想使用光栅图形而不是SVG？ 其实 SVG 确实有一些缺点：

- SVG非常容易变得复杂，这意味着文件大小会增加; 复杂的SVG也会在浏览器中占用很长的处理时间。
- SVG可能比栅格图像更难创建，具体取决于您尝试创建哪种图像。
- 旧版浏览器不支持SVG，因此如果您需要在网站上支持旧版本的 IE，则可能不适合（SVG从IE9开始得到支持）。

由于上述原因，光栅图形更适合照片那样复杂精密的图像。



将SVG添加到页面：

快捷方式： 需要一个`height`或`width`属性（或者如果您的SVG没有固有的宽高比）。 

```html
<img 
    src="equilateral.svg" 
    alt="triangle with all three sides equal"
    height="87px"
    width="100px" />
```

优点：

- 快速，熟悉的图像语法与`alt`属性中提供的内置文本等效。
- 可以通过在img元素嵌套`src`，使图像轻松地成为超链接。

缺点：

- 无法使用JavaScript操作图像。
- 如果要使用CSS控制SVG内容，则必须在SVG代码中包含内联CSS样式。 （从SVG文件调用的外部样式表不起作用）
- 不能用CSS伪类来重设图像样式（如`:focus`）。

 对于不支持SVG（IE 8及更低版本，Android 2.3及更低版本）的浏览器，您可以从`src`属性引用PNG或JPG，并使用`srcset`属性 只有最近的浏览器才能识别）来引用SVG。 

```html
<img src="equilateral.png" alt="triangle with equal sides" srcset="equilateral.svg">
```

 还可以在文本编辑器中打开SVG文件，复制SVG代码，并将其粘贴到HTML文档中 - 这有时称为将**SVG内联**或**内联SVG**。确保您的SVG代码在svg></svg标签中（不要在外面添加任何内容）。 

```html
<svg width="300" height="200">
    <rect width="100%" height="100%" fill="green" />
</svg>
```

优点:

- 将 SVG 内联减少 HTTP 请求，可以减少加载时间。
- 您可以为 SVG 元素分配`class`和`id`，并使用 CSS 修改样式，无论是在SVG中，还是 HTML 文档中的 CSS 样式规则。 实际上，您可以使用任何SVG外观属性作为CSS属性。
- 内联SVG是唯一可以让您在SVG图像上使用CSS交互（如`:focus`）和CSS动画的方法（即使在常规样式表中）。
- 您可以通过将 SVG 标记包在<a>元素中，使其成为超链接。

缺点:

- 这种方法只适用于在一个地方使用的SVG。多次使用会导致资源密集型维护（resource-intensive maintenance）。
- 额外的 SVG 代码会增加HTML文件的大小。
- 浏览器不能像缓存普通图片一样缓存内联SVG。
- 您可能会在foreignObject元素中包含回退，但支持 SVG 的浏览器仍然会下载任何后备图像。你需要考虑仅仅为支持过时的浏览器，而增加额外开销是否真的值得。



可以使用iframe 嵌入 SVG 文档 :

```html
<iframe src="triangle.svg" width="500" height="500" sandbox>
    <img src="triangle.png" alt="Triangle with three unequal sides" />
</iframe>
```

这绝对不是最好的方法：

缺点:

- 如你所知， `iframe`有一个回退机制，如果浏览器不支持`iframe`，则只会显示回退。
- 此外，除非 SVG 和您当前的网页具有相同的origin，否则你不能在主页面上使用 JavaScript 来操纵 SVG。

## 响应式图片

不同屏幕上图片大小不一样。实际上，CSS是比HTML更好的响应式设计的工具。

 我们可以使用两个新的属性——`srcset` 和 `sizes`——来提供更多额外的资源图像和提示 

```html
<img srcset="elva-fairy-320w.jpg 320w,
             elva-fairy-480w.jpg 480w,
             elva-fairy-800w.jpg 800w"
     sizes="(max-width: 320px) 280px,
            (max-width: 480px) 440px,
            800px"
     src="elva-fairy-800w.jpg" alt="Elva dressed as a fairy">
```

**srcset**定义了我们允许浏览器选择的图像集，以及每个图像的大小。在每个逗号之前，我们写：

1. 一个**文件名** (`elva-fairy-480w.jpg`.)
2. 一个空格
3. **图像的固有宽度**（以像素为单位）（480w）——注意到这里使用`w`单位，而不是你预计的`px`。这是图像的真实大小，可以通过检查你电脑上的图片文件找到（例如，在Mac上，你可以在Finder上选择这个图像，然后按 Cmd + I 来显示信息）。

 **sizes**定义了一组媒体条件（例如屏幕宽度）并且指明当某些媒体条件为真时，什么样的图片尺寸是最佳选择 。有了这些属性，浏览器会：

1. 查看设备宽度
2. 检查`sizes`列表中哪个媒体条件是第一个为真
3. 查看给予该媒体查询的槽大小
4. 加载`srcset`列表中引用的最接近所选的槽大小的图像

## 表格

最好不要用html设计表格。

```html
<table>
  <tr>
    <td>&nbsp;</td>
    <td>Knocky</td>
    <td>Flor</td>
    <td>Ella</td>
    <td>Juan</td>
  </tr>
  <tr>
    <td>Breed</td>
    <td>Jack Russell</td>
    <td>Poodle</td>
    <td>Streetdog</td>
    <td>Cocker Spaniel</td>
  </tr>
  <tr>
    <td>Age</td>
    <td>16</td>
    <td>9</td>
    <td>10</td>
    <td>5</td>
  </tr>
  <tr>
    <td>Owner</td>
    <td>Mother-in-law</td>
    <td>Me</td>
    <td>Me</td>
    <td>Sister-in-law</td>
  </tr>
  <tr>
    <td>Eating Habits</td>
    <td>Eats everyone's leftovers</td>
    <td>Nibbles at food</td>
    <td>Hearty eater</td>
    <td>Will eat till he explodes</td>
  </tr>
</table>
```

结果显示：

![表格显示](/表格显示.png)

 使用 `colspan` 让 "Animals", "Hippopotamus", 和 "Crocodile" 占 2 个单元格的宽度。  使用 `rowspan` 让 "Horse" 和 "Chicken" 占 2 个单元格的高度。 

```html
    <table>
      <tr>
        <th colspan="2">Animals</th>
      </tr>
      <tr>
        <th colspan="2">Hippopotamus</th>
      </tr>
      <tr>
        <th rowspan="2">Horse</th>
        <td>Mare</td>
      </tr>
      <tr>
        <td>Stallion</td>
      </tr>
      <tr>
        <th colspan="2">Crocodile</th>
      </tr>
      <tr>
        <th rowspan="2">Chicken</th>
        <td>Hen</td>
      </tr>
      <tr>
        <td>Rooster</td>
      </tr>
    </table>

```

![表格显示2](/表格显示2.png)

 HTML有一种方法可以定义整列数据的样式信息：就是col和 colgroup 元素。 它们存在是因为如果你想让一列中的每个数据的样式都一样，那么你就要为每个数据都添加一个样式 .

```html
<table>
  <colgroup>
    <col> //第一列没什么
    <col style="background-color: yellow"> //第二列是黄的
  </colgroup>
  <tr>
    <th>Data 1</th>
    <th>Data 2</th>
  </tr>
  <tr>
    <td>Calcutta</td>
    <td>Orange</td>
  </tr>
  <tr>
    <td>Robots</td>
    <td>Jazz</td>
  </tr>
</table>
```

![1595165828283](/表格显示3.png)

 可以为你的表格增加一个标题，通过caption元素，再把caption元素放入table元素中. 

```html
<table>
  <caption>Dinosaurs in the Jurassic period</caption>

  ...
</table>
```

 使用thead,tfoot ,和 tbody, 这些元素允许你把表格中的部分标记为表头、页脚、正文部分。 

- thead需要嵌套在 table 元素中，放置在头部的位置，因为它通常代表第一行，第一行中往往都是每列的标题，但是不是每种情况都是这样的。如果你使用了 col>/<colgroup 元素，那么thead> $元素就需要放在它们的下面。
-  tfoot需要嵌套在 table 元素中，放置在底部 (页脚)的位置，一般是最后一行，往往是对前面所有行的总结，比如，你可以按照预想的方式将 tfoot放在表格的底部，或者就放在 thead的下面。(浏览器仍将它呈现在表格的底部)
-  tbody需要嵌套在 table 元素中，放置在 thead的下面或者是 tfoot的下面，这取决于你如何设计你的结构。(tfoot放在thead下面也可以生效.)

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>My spending record</title>
    <link href="minimal-table.css" rel="stylesheet" type="text/css">
    <style>
        tbody {
          font-size: 90%;
          font-style: italic;
        }

        tfoot {
          font-weight: bold;
        }
    </style>
  </head>
  <body>
    <h1>My spending record</h1>

      <table>
        <caption>How I chose to spend my money</caption>
        <thead>
          <tr>
            <th>Purchase</th>
            <th>Location</th>
            <th>Date</th>
            <th>Evaluation</th>
            <th>Cost (€)</th>
          </tr>
        </thead>
        <tfoot>
          <tr>
            <td colspan="4">SUM</td>
            <td>118</td>
          </tr>
        </tfoot>
        <tbody>
          <tr>
            <td>Haircut</td>
            <td>Hairdresser</td>
            <td>12/09</td>
            <td>Great idea</td>
            <td>30</td>
          </tr>
          <tr>
            <td>Lasagna</td>
            <td>Restaurant</td>
            <td>12/09</td>
            <td>Regrets</td>
            <td>18</td>
          </tr>
          <tr>
            <td>Shoes</td>
            <td>Shoeshop</td>
            <td>13/09</td>
            <td>Big regrets</td>
            <td>65</td>
          </tr>
          <tr>
            <td>Toothpaste</td>
            <td>Supermarket</td>
            <td>13/09</td>
            <td>Good</td>
            <td>5</td>
          </tr>
        </tbody>
    </table>

  </body>
</html>
```

![表格显示4](/表格显示4.png)

 scope 属性 :标注是行标题或列标题。

```html
<thead>
  <tr>
    <th scope="col">Purchase</th>
    <th scope="col">Location</th>
    <th scope="col">Date</th>
    <th scope="col">Evaluation</th>
    <th scope="col">Cost (€)</th>
  </tr>
</thead>

<tr>
  <th scope="row">Haircut</th>
  <td>Hairdresser</td>
  <td>12/09</td>
  <td>Great idea</td>
  <td>30</td>
</tr>
```

 `scope` 还有两个可选的值 ： `colgroup` 和 `rowgroup`。这些用于位于多个列或行的顶部的标题。 

如果要替代 `scope` 属性，可以使用 `id` 和 `headers` 属性来创造标题与单元格之间的联系。使用方法如下:

1. 为每个` 元素添加一个唯一的 `id 。
2. 为每个 ` 元素添加一个 `headers` 属性。每个单元格的`headers 属性需要包含它从属于的所有标题的id，之间用空格分隔开。

这会给你的HTML表格中每个单元格的位置一个明确的定义。像一个电子表格一样，通过 headers 属性来定义属于哪些行或列。为了让它工作良好，表格同时需要列和行标题。

```html
<thead>
  <tr>
    <th id="purchase">Purchase</th>
    <th id="location">Location</th>
    <th id="date">Date</th>
    <th id="evaluation">Evaluation</th>
    <th id="cost">Cost (€)</th>
  </tr>
</thead>
<tbody>
<tr>
  <th id="haircut">Haircut</th>
  <td headers="location haircut">Hairdresser</td>
  <td headers="date haircut">12/09</td>
  <td headers="evaluation haircut">Great idea</td>
  <td headers="cost haircut">30</td>
</tr>

  ...

</tbody>
```

 **注意**: 这个放进为标题单元格和数据单元格之间创造了非常精确的联系。但是这个方法使用了大量的标记，所以容错率比较低。 

# React

 如果您最近几年没有使用JavaScript，那么以下三点应该为您提供足够的知识，让您轻松阅读React文档： 

- 我们使用let和const语句定义变量。就React文档而言，您可以将它们视为与等效var。
- 我们使用`class`关键字定义JavaScript类。关于它们，有两件事值得记住。首先，不同于对象，你*不要*需要把类的方法定义之间的逗号。其次，与许多其他带有类的语言不同，在JavaScript中`this`，方法的值取决于调用方法的方式。
- 有时我们`=>`用来定义箭头功能。它们就像常规函数，但更短。例如，`x => x * 2`大致等于`function(x) { return x * 2; }`。重要的是，箭头函数没有自己的this值，因此当您希望`this`从外部方法定义中保留值时，它们非常方便。

## JSX

 JSX 可以很好地描述 UI 应该呈现出它应有交互的本质形式。 React 认为渲染逻辑本质上与其他 UI 逻辑内在耦合，比如，在 UI 中需要绑定处理事件、在某些时刻状态发生变化时需要通知到 UI，以及需要在 UI 中展示准备好的数据。React 并没有采用将*标记与逻辑进行分离到不同文件*这种人为地分离方式，而是通过将二者共同存放在称之为“组件”的松散耦合单元之中，来实现关注点分离。

 在 JSX 语法中，你可以在大括号内放置任何有效的 JavaScript表达式。

```javascript + html
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

 可以通过使用引号，来将属性值指定为字符串字面量： 

```javascript
const element = <div tabIndex="0"></div>;
```

 也可以使用大括号，来在属性值中插入一个 JavaScript 表达式： 

```javascript
const element = <img src={user.avatarUrl}></img>;
```

 在属性中嵌入 JavaScript 表达式时，不要在大括号外面加上引号。你应该仅使用引号（对于字符串值）或大括号（对于表达式）中的一个，对于同一属性不能同时使用这两种符号。 