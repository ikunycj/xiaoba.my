1.预览，使用 `pdf.js`（Mozilla 官方库）
2.预览，使用 `react-pdf` 库
**方案对比**

| **方案**                 | **优点**          | **缺点**        |
| ---------------------- | --------------- | ------------- |
| **原生 `pdf.js`**        | 高度灵活，无需依赖封装库    | 需自行处理渲染逻辑     |
| **`react-pdf`**        | 简单易用，React 生态友好 | 需要引入额外库       |
| **`react-pdf-viewer`** | 提供完整交互功能（缩放/翻页） | 功能较复杂，可能增加包体积 |
3.不预览，统一使用一个pdf的icon图标，点击后打开pdf

## 前端PDF预览
前端实现PDF预览目前有如下三种主流方案
1. 使用 HTML 标签
    - iframe 标签
    - embed 标签
    - object 标签
2. 使用第三方插件
    - PDF.js
    - PDFObject
3. PDF 文件转化成图片进行展示

- 第一类方案：**使用 HTML 标签**的实现预览效果最为简单，但兼容性最差，仅支持部分 PC 端的浏览器，移动端浏览器兼容非常差。

- 第二类方案：**使用第三方库**的功能强大，还可对 pdf 文件进行操作，PC 端兼容性比较好，但在移动端会有 pdf 文件显示不全的 bug 出现，而且在移动端浏览器兼容性一般。

- 第三类方案：**PDF 文件转化成图片进行展示**的兼容性最好，能同时在 PC 端和移动端预览，但由于是图片格式，所以选中 pdf 文件上的文字，无法复制黏贴。


## 1.使用 HTML 标签

该类方法实现简单，但兼容性很差，慎重选择。

### 1.1 iframe 标签

HTML 内联框架元素 iframe 表示嵌套的 browsing context。它能够将另一个 HTML 页面嵌入到当前页面中。

- [iframe 标签的属性含义请参照 MDN](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FHTML%2FElement%2Fiframe "https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe")
- [iframe 标签的兼容性请参照 Can I Use](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2Fmdn-html_elements_iframe "https://caniuse.com/mdn-html_elements_iframe") `iframe` 标签实现方案代码如下所示：

```html
<iframe src="./test.pdf" height="900px;" width="800px"></iframe>
```

### 1.2 embed 标签

HTML `embed` 元素将外部内容嵌入文档中的指定位置。此内容由外部应用程序或其他交互式内容源（如浏览器插件）提供。

- [embed 标签的属性含义请参照 MDN](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FHTML%2FElement%2Fiframe "https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe")
- [embed 标签的兼容性请参照 Can I Use](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2Fmdn-html_elements_embed "https://caniuse.com/mdn-html_elements_embed")

embed 标签实现方案代码如下所示：


```html
<embed src="./test.pdf" type="application/pdf" width="100%" height="100%" />
```

### 1.3 object 标签

HTML `object` 元素（或者称作 HTML 嵌入对象元素）表示引入一个外部资源，这个资源可能是一张图片，一个嵌入的浏览上下文，亦或是一个插件所使用的资源。

- [object 标签的属性含义请参照 MDN](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FHTML%2FElement%2Fobject "https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/object")
- [object 标签的兼容性请参照 Can I Use](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2Fmdn-html_elements_object "https://caniuse.com/mdn-html_elements_object")

object 标签实现方案代码如下所示：

```html
<object data="./test.pdf" type="application/pdf" width="100%" height="100%" ></object>
```

---

## 2.使用第三方插件

能实现实时预览 pdf 的插件还有许多种，例如PDF.js 、PDFObject、React-PDF

### 2.1 PDF.js

#### PDF.js 简介

PDF.js 是一款开源的 pdf 文档读取解析插件，可以实现在 html 下直接浏览 pdf 文档。

- pdf.js 是基于**Promise 对象**而实现的。
- pdf.js 渲染 pdf 时底层还使用了**Web Worker**（这会导致我们无法直接在本地运行官网下载的 demo，得在服务器上运行，详情见注意点处）[阮一峰老师关于 Web Worker 的文章](https://link.juejin.cn?target=http%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2018%2F07%2Fweb-worker.html "http://www.ruanyifeng.com/blog/2018/07/web-worker.html")。

PDF.js 主要分为 3 层： 显示层采用核心层，并公开更容易使用的 API 来呈现 pdf 和从文档中获取其他信息。

|层级|对应文件|作用|
|:-:|:-:|:-:|
|Core Layer（核心层）|pdf.worker.js|核心层用于解析和解释二进制 PDF 文档，这一层是所有后续层的基础。一般我不会直接操作核心层，而是去操作由核心层封装的展示层，操作核心层的高级用法可去参照[官网](https://link.juejin.cn?target=http%3A%2F%2Fmozilla.github.io%2Fpdf.js "http://mozilla.github.io/pdf.js")|
|Display Layer（展示层）|pdf.js|显示层是对核心层进行了一个封装，从而得到更容易使用的 API，用来展示 pdf 或从文档中获取其他信息。|
|Viewer Layer（查看器层）|viewer.html+viewer.css+viewer.js|查看器构建在显示层上，是 PDF 查看器的 UI。|

更多的细节请参照[官网](https://link.juejin.cn?target=http%3A%2F%2Fmozilla.github.io%2Fpdf.js "http://mozilla.github.io/pdf.js")

#### 使用 PDF.js 具体步骤

##### 方法一

该方法是以图片形式来展示 PDF 文档，所以不能选中文本或复制文本。

具体步骤如下：

1. 首先 npm i pdfjs-dist 下载 pdf.js 的 Prebuilt 包
2. 设置 PDFJS.GlobalWorkerOptions.workerSrc 的地址
3. 通过 PDFJS.getDocument（pdf 文件的 url） 处理 pdf 数据，返回一个 PDFDocumentLoadingTask
4. 通过 pdfDoc.getPage（i） 单独获取第 i 页的数据
5. 创建一个 canvas 元素，并设置元素的画布属性
6. 通过 page.render 方法，将数据渲染到画布上、

具体代码如下：

```js
// 第 2 步：设置 workerSrc 地址 （具体包的地址需要依自身项目决定） import * as PDFJS from "./build/pdf.js"; import pdfjsWorker from "./build/pdf.worker.js"; PDFJS.GlobalWorkerOptions.workerSrc = pdfjsWorker; const pdfUrl = "./test.pdf"; //具体路径由自身项目决定，另外这可能会涉及跨域问题可参照官网解决 console.log(pdfUrl); // 第 3 步：使用 PDFJS.getDocument（） 处理 pdf 文档 PDFJS.getDocument(pdfUrl).promise.then((pdfDoc) => { const totalPages = pdfDoc.numPages; // pdf 的总页数 const canvasContainer = document.getElementById("#canvasContainer"); //html中需创建一个相应的div容器，用于存放canvas元素 for (let i = 1; i <= totalPages; i++) { // 第4步：使用 pdfDoc.getPage（i） 获取第 i 页的数据 pdfDoc.getPage(i).then((page) => { let scaledViewport = page.getViewport({ scale: 1.5 }); //可通过scale来调节初始的缩放比 //第5步：创建一个 canvas 元素，并设置元素的画布属性 let canvas = document.createElement("canvas"); canvas.setAttribute("id", "the-canvas" + i); canvas.height = scaledViewport.height; canvas.width = scaledViewport.width; let context = canvas.getContext("2d"); let renderContext = { canvasContext: context, viewport: scaledViewport, }; //第 6 步: 使用 page.render 将数据渲染到画布上 page.render(renderContext).promise.then(() => {}); canvasContainer.appendChildren(canvas); //将canvas元素加入到容器中 }); } });
```

##### 方法二

直接使用官方封装好的 viewer.html 来展示自己的 PDF 文档，该方法比较简单，不用去操作 API；而且功能比较齐全，还可复制 pdf 中的文本。

具体步骤如下：

1. 去[官网下载](https://link.juejin.cn?target=http%3A%2F%2Fmozilla.github.io%2Fpdf.js%2Fgetting_started%2Fdownload "http://mozilla.github.io/pdf.js/getting_started/download")打包好的 Prebuilt 版本压缩包
2. 将需要打开的 PDF 文档放到与 viewer.html 文档的同一目录下
3. 新建一个 html 文件，使用 window.open 方法 或 iframe 标签 来打开 viewer.html，并使用 file 字段来传入 pdf 名字信息，该方法的[更多详细信息可参照博文](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fweixin_40507164%2Farticle%2Fdetails%2F122947377%3Fops_request_misc%3D%25257B%252522request%25255Fid%252522%25253A%252522166283283616800180636037%252522%25252C%252522scm%252522%25253A%25252220140713.130102334..%252522%25257D%26request_id%3D166283283616800180636037%26biz_id%3D0%26utm_medium%3Ddistribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-122947377-null-null.142%255Ev47%255Epc_rank_34_2%2C201%255Ev3%255Eadd_ask%26utm_term%3Dpdf.js%25E9%25A2%2584%25E8%25A7%2588%26spm%3D1018.2226.3001.4187 "https://blog.csdn.net/weixin_40507164/article/details/122947377?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166283283616800180636037%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166283283616800180636037&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-122947377-null-null.142%5Ev47%5Epc_rank_34_2,201%5Ev3%5Eadd_ask&utm_term=pdf.js%E9%A2%84%E8%A7%88&spm=1018.2226.3001.4187") 具体代码如下：

HTML

 体验AI代码助手

 代码解读

复制代码

`<!-- 使用iframe --> <!-- 该方法会受iframe标签兼容性限制 --> <iframe   src="./web/viewer.html?file=test.pdf"   frameborder="0"   style="height: 800px; width: 100%" ></iframe> <!-- 使用window.open--> <!-- 该方法会打开新窗口 --> <script type="text/javascript">     window.open("./web/viewer.html?file=test.pdf");//文件和 viewer.html 同路径时 </script>`

#### 2.1.3 使用 PDF.js 的注意事项

这里的内容非常重要，可以避免踩坑。我就是在这浪费了足足两天的时间，最终才找到解决办法。

##### 注意事项一：pdf.js 需要启动服务器才能运行，无法直接打开本地的文档

- 原因：因为 pdf.js 渲染 pdf 文档时使用了 Web Worker 技术，该 Web Worker 无法读取本地文件。[可参见阮一峰老师关于 Web Worker 的文章](https://link.juejin.cn?target=http%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2018%2F07%2Fweb-worker.html "http://www.ruanyifeng.com/blog/2018/07/web-worker.html")
    
- 报错信息：**Message: Missing PDF "file:///D:/%E6%A1%8C%E9%9D%A2/pdfjs/web/test.pdf"**
    
- 解决办法：可通过 live-server 这个插件在本地启动服务器然后打开相应的 html 文件。
    
- 具体步骤：
    
    1. npm i live-server 安装包
    2. live-server 启动服务器 [更多配置信息可参见此博客](https://link.juejin.cn?target=http%3A%2F%2Ft.zoukankan.com%2Fflame540-p-14084542.html "http://t.zoukankan.com/flame540-p-14084542.html")
    3. 打开相应的 html 文件

##### 注意事项二：我们的电脑上不能安装 IDM（Internet Download Manager）这类软件或插件。

- 报错信息 ： **Unexpected server response (204) while retrieving PDF**
- 原因：因为 IDM 会拦截可下载的资源，会导致页面无法预览。
- 解决办法：直接卸载或关闭相应的插件、软件；也可以对软件进行相应的设置。[更多信息可参照博文](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fchengoes%2Farticle%2Fdetails%2F106323091 "https://blog.csdn.net/chengoes/article/details/106323091")

另外使用 pdf.js 打开发票等文件时可能会出现字体显示不全的 bug，可参照该博客[解决 pdf.js 无法完全显示 pdf 文件内容的问题](https://link.juejin.cn?target=http%3A%2F%2Ft.zoukankan.com%2FKingJames-p-11763590.html "http://t.zoukankan.com/KingJames-p-11763590.html")。

### 2.2 PDFObject

相对 pdf.js 来说，PDFObject 的使用非常简单。但在手机 webview 使用兼容性不太好。 PDFObject 2.0 不向后兼容 1.0 版本，针对现代浏览器设计，支持 Chrome, Firefox, Safari (OS X and iOS), IE 9-11, and MS Edge。 更多信息可参照[官网地址](https://link.juejin.cn?target=https%3A%2F%2Fpdfobject.com%2F "https://pdfobject.com/")，和该篇博客[PDF 预览之 PDFObject.js 总结](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fbeyond__devil%2Farticle%2Fdetails%2F78826826 "https://blog.csdn.net/beyond__devil/article/details/78826826") 使用步骤:

1. 创建嵌入 PDF 的容器
2. 告诉 PDFObject，插入的 PDF 文件路径，以及插入到哪个容器
3. 可以选择使用 CSS 来指定视觉样式，包括维度、边框、边距等

html

 体验AI代码助手

 代码解读

复制代码

`<!-- 第1步：创建嵌入PDF的容器 --> <div id="pdf"></div> <!-- 第2步：告诉PDFObject，插入的PDF文件路径，以及插入到哪个容器 --> <script src="library/pdfobject.js"></script> <script>   PDFObject.embed("uploads/pdfs/dongxuemin.pdf", "#pdf"); </script> <!-- 第3步：可以选择使用CSS来指定视觉样式，包括维度、边框、边距等 --> <style>   .pdfobject-container {     height: 500px;   }   .pdfobject {     border: 1px solid #ccc;   } </style>`

另外还有许多第三方库可实现 pdf 预览，如：[vue-pdf](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fweixin_42678675%2Farticle%2Fdetails%2F109736497%3Fops_request_misc%3D%25257B%252522request%25255Fid%252522%25253A%252522166283723316782417062061%252522%25252C%252522scm%252522%25253A%25252220140713.130102334..%252522%25257D%26request_id%3D166283723316782417062061%26biz_id%3D0%26utm_medium%3Ddistribute.pc_search_result.none-task-blog-2~all~top_click~default-2-109736497-null-null.142%255Ev47%255Epc_rank_34_2%2C201%255Ev3%255Eadd_ask%26utm_term%3Dvue-pdf%26spm%3D1018.2226.3001.4187 "https://blog.csdn.net/weixin_42678675/article/details/109736497?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166283723316782417062061%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166283723316782417062061&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-109736497-null-null.142%5Ev47%5Epc_rank_34_2,201%5Ev3%5Eadd_ask&utm_term=vue-pdf&spm=1018.2226.3001.4187")、[jquery.media.js](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fweixin_30322405%2Farticle%2Fdetails%2F95288415%3Futm_medium%3Ddistribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-1-95288415-blog-105073609.topnsimilarv1%26spm%3D1001.2101.3001.4242.2%26utm_relevant_index%3D4 "https://blog.csdn.net/weixin_30322405/article/details/95288415?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-1-95288415-blog-105073609.topnsimilarv1&spm=1001.2101.3001.4242.2&utm_relevant_index=4")等等。

### 2.3 React-PDF
## 3.PDF 文件转化成图片进行展示

把 PDF 转换为图片也有很多控件处理，例如 Aspose.Pdf、Spire.Pdf、 pdfiumviewer 等等，不同的第三方类库使用的方法有所差异，不过思路都很类似。 由于转成图片将无法复制或选中 PDF 文档的文本，所以我没使用该方法，就没进行进一步研究。大家可去参照其他博客。

## 参考博课：

- [前端预览 PDF 文件（使用 PDFJS）](https://link.juejin.cn?target=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000040331855 "https://segmentfault.com/a/1190000040331855")
- [实现在线预览 PDF 的几种解决方案](https://link.juejin.cn?target=http%3A%2F%2Fwww.caotama.com%2F1802043.html "http://www.caotama.com/1802043.html")
- [PDFObject.js、jquerymedia.js、pdf.js 的对比](https://link.juejin.cn?target=https%3A%2F%2Fwww.cnblogs.com%2FiPing9%2Fp%2F7153713.html "https://www.cnblogs.com/iPing9/p/7153713.html")
- [PDF 预览之 PDFObject.js 总结](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fbeyond__devil%2Farticle%2Fdetails%2F78826826 "https://blog.csdn.net/beyond__devil/article/details/78826826")
