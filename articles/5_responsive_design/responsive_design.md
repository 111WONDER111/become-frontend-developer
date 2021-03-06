# CSS布局与响应式设计

[TOC]

在移动设备出现之前，人们访问网站需要通过PC端的浏览器来实现。Web前端工程师主要解决的是页面在各型浏览器之间适配的问题。后来，随着平板电脑和智能手机的普及，移动端的访问量呈爆炸式增长，前端工程师面临的问题变成了如何在不同大小的设备上呈现同样的网页。能不能"一次设计，到处适用"，让同一张网页自动适应不同大小屏幕，自动调整布局？让网页在较小的屏幕上也能有较好的用户体验，于是**响应式设计**就应运而生了。在开始后面的技术细节讨论之前，我们一定要抓住一个中心思想：响应式设计的**核心思想**是让你的网站内容在任何设备上都以最佳的方式呈现。

# 一. 准备工作

我们不可能买下市面上所有型号的移动设备来做设计，而是要使用手边的各种服务，比如[在线浏览器适配服务](<https://www.browserstack.com/>)。如果你使用的是Chrome浏览器，那么就可以使用开发者工具中的模拟器来进行移动设备模拟。在Chrome的开发者工具中那个手机图标就是模拟器，单击它就可以打开，并且我们可以通过下拉菜单来选择具体的设备。



![](./simulator.png)



模拟器能自动设置好尺寸和用户代理信息，设备的像素比例并能模拟触控。如果手里面有可供开发调试的手机，我们也可以设置移动设备的远程调试功能。比如如果要适配安卓设备，那么我们就可以准备一台安卓机，USB连接线和开发设备（笔记本电脑）。安卓设备需要设置成开发者模式，然后打开USB调试模式。

接着，在开发设备上打开[inspect](chrome://inspect)页面，然后在安卓设备上用Chrome浏览器打开要调试的页面，用USB连接两个设备，在弹出的对话框中选择确认允许USB调试。在开发设备上就能看到已连接的设备了，我们可以在开发设备上对安卓设备进行各种操作，也可以看到移动设备上打开的页面，相当于把移动设备的屏幕投射到开发使用的笔记本电脑上。在笔记本电脑上进行的操作可以同步在两台设备上显示。



# 二. 像素与视口

在移动设备上访问网页时，我们常常会遇到一种很不愉快的用户体验：整个网页被缩放以适应较小的设备屏幕，一切都变小了，字变小了，图片变小了，最要命的是连按钮都变小了，虽然看上去页面和PC端一样，但什么都看不清点不了，这是因为没有遵循响应式页面设计：窄屏幕设备（比如移动设备）在一个虚拟视口中渲染页面，这个虚拟视口通常比屏幕要宽，例如，如果移动设备的屏幕宽度为640px，那么如果以980px的虚拟视口来渲染网页，就需要缩小页面来适应640px的窗口大小，这就是页面看起来小的原因。要想理解响应式设计，我们先要来重新认识一下像素与视口的概念。

## 2.1 像素

> A pixel is not a pixel

首先我们要来厘清"像素"的概念。在不同的上下文中所指的"像素"可能具有不同的含义。对于浏览器而言使用的像素是CSS样式代码中使用的逻辑像素，叫"CSS像素"，属于Web编程中的概念。1个CSS像素在不同的设备上对应的物理像素可能不同，这是由设备像素比（Device Pixel Ratio）决定的。设备像素则指的是设备的物理像素，单位是`px`。所以当我们说某某移动设备的分辨率为1920*1080像素时，这里的像素实际上指的就是物理像素。

## 2.2 视口（viewport）

浏览器的视口（viewport）是可以看到Web内容的窗口区域。通常与渲染出的页面大小是不同的，除非你通过某种方式告诉浏览器你的网页将会在更小的屏幕上显示，否则浏览器不会知道。所以如果我们要在不同的设备上获得一致的视觉体验，我们就需要：

1. 设置设备像素比DPR
2. 设置视口viewport

设置设备像素比和视口的方法就是在html中为head元素增加viewport标签：

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

上面这行代码允许网页宽度自动调整：网页的默认宽度为屏幕宽度，原始缩放比例为1:1，即网页初始大小占屏幕面积100%。与此对应的，也有`height`和`device-height`属性，可以对视口高度进行调整，但不常用。只要把缩放比例控制到1:1，此后我们在页面布局时就只需要关心CSS像素即可。

# 三. CSS布局初探

在CSS中控制布局最重要的属性是`display`属性。各种各样的新式布局都从设置它开始，比如我们后面要讲到的弹性（flex）布局和网格（grid）布局。这里先不讨论这些 高大上的布局，我们先来打好基础。所有的元素都有一个默认的display值：block和inline。block表示该元素为块级元素，这类元素会重新开始一行并尽量撑满容器（即其父元素）；inline表示该元素为行内元素，它不打乱段落的布局，可以用来在段落中包裹一些内容。display属性有一些比较tricky的小技巧，比如如果设置为none则可以在不删除元素的情况下隐藏元素，这种隐藏是不占据显示空间的，即如果你不查看网页源代码你完全感觉不到这个元素的存在。

即然可以避免元素从左到右撑满容器，那么通过设定max-width为固定值并设置左右外边距，我们就能将元素居中显示，剩余的部分将被平均分为两块分列元素的两边：

```css
.box {
  max-width: 600px;
  margin: 0 auto;
}
```

CSS的基础模型是盒子模型，当我们设置元素尺寸时内边距和边框的大小会对元素的尺寸造成影响，这是因为默认的box-sizing属性是content-box，为了避免做各种心算，我们可以设置border-box使内边距和边框不增加宽度：

```css
* {
  -webkit-box-sizing: border-box;
     -moz-box-sizing: border-box;
          box-sizing: border-box;
}
```

## 3.1 position定位

position属性可以用来实现各种复杂的布局，主要有三种：相对，固定和绝对。如果不设置这个属性，那么它的默认值就是static。**如果我们使用其他任何值设置了元素的这个属性，我们就称这个元素为positioned**，记住这句话很重要，因为绝对定位会用到这个概念。

### 3.1.1 相对定位

通过设置position为relative来实现相对定位。这个元素的top，right，bottom和left属性会使得元素偏离正常位置，其他元素的位置不会受其影响来弥补它偏移后留出的空隙：

```css
.relative1 {
  position: relative;
}
.relative2 {
  position: relative;
  top: -20px;
  left: 20px;
  background-color: white;
  width: 500px;
}
```

![](./relative.png)

### 3.1.2 固定定位

通过设置position为fixed来实现固定定位。固定定位是相对于视口定位的，无论你怎么拖动滚动条，它永远都显示在相对于视口的一个位置上（脱离文档流）。

```css
.fixed {
  position: fixed;
  bottom: 0;
  right: 0;
  width: 200px;
  background-color: white;
}
```

### 3.1.3 绝对定位

通过设置position为absolute来实现绝对定位。如果绝对定位元素的祖先元素是positioned的，它就相对于该祖先元素绝对定位，否则将相对于body元素绝对定位并随页面滚动而移动。

```css
.relative {
  position: relative;
  width: 600px;
  height: 400px;
}
.absolute {
  position: absolute;
  top: 120px;
  right: 0;
  width: 300px;
  height: 200px;
}
```

![](./absolute.png)

## 3.2 浮动属性

在没有出现flex和grid布局前的黑暗年代里，float属性主要用来实现布局，而现在它主要用来实现文字环绕图片：

```css
img {
  float: right;
  margin: 0 0 1em 1em;
}
```

![](./float.png)

但是在使用float属性时经常需要解决一些稀奇古怪的问题，比如需要将其他元素移动到浮动元素下面避免覆盖，或者避免浮动元素溢出到容器的外面。clear属性可以用来移动到浮动元素的下面：

![](./clear1.png)

```css
.box {
  float: left;
  width: 200px;
  height: 100px;
  margin: 1em;
}
.after-box {
  clear: left;
}
```

![](./clear2.png)

而overflow属性可以避免浮动元素溢出到容器的外面。比如下面这种情况，这个图片比包含它的元素要高， 因为它是浮动的，所以它就溢出到了容器外面：

![](./clearfix1.png)

加入下面的CSS样式就好多了：

```css
.clearfix {
  overflow: auto;
}
```

![](./clearfix2.png)

## 3.3 百分比宽度和相对大小

相对宽度是一种相对于父容器的计量单位。响应式设计需要采用相对宽度，不能使用绝对宽度，因为网页会根据页面宽度调整布局，所以编写CSS的时候我们指定`width`要使用百分比：

```css
width: 100%
```

或者：

```css
width: auto
```

字体也只能使用`em`这样的相对大小单位：

```css
body {
	font: normal 100% Helvetica, Arial, sans-serif;
}

h1 {
	font-size: 1.5em; 
}

small {
  font-size: 0.875em;
}
```

对于页面上的输入控件（例如按钮）至少要保证48 * 48px的大小，并设定外边距为40px，这样才能保证按钮不至于太小而不能被手指点击到。还可以使用max-width和min-width来限制最大/最小宽度。

## 3.4 媒体查询

媒体查询（media query）要解决的问题就是在不同的设备上应用不同的CSS样式。媒体查询可以根据设备的宽度，高度或者像素比来应用不同的样式，可以修改从背景图片到页面布局的任何内容。只需要在页面里添加额外的样式表，并附上媒体查询即可：

```html
<link rel="stylesheet" media="screen and (min-width: 300px)" href="patterns.css">
```

下面就来详细介绍下媒体查询的使用方法。

### 3.4.1 基本媒体查询

媒体查询最基本的用法就是在新的CSS文件链接时使用，例如我们指定当宽度至少为500px时使用over500.css，就可以这样写：

```html
<link rel="stylesheet" media="screen and (min-width:500px)" href="over500.css">
```

另外一种方法就是在CSS文件中使用`@media`标签嵌入：

```css
@media screen and (min-width: 500px) {
  body { background-color: green; }
}
```

两种方法各有利弊，第一种方法可能会产生很多小的css文件，并需要多次http请求；而第二种方法使用的http请求更少，但产生的CSS文件更大。无论如何都要避免使用`@import`，因为这种方式影响性能。

### 3.4.2 断点

媒体查询用于页面改变布局的发生点称为断点，这是前端工程师完成响应式设计的基本手段。那么，要在哪里设置断点呢？一个指导原则是根据内容来设置断点，让页面内容来告诉我什么时候该设置断点。

在页面设计时，首先设置好视口和默认样式，然后将窗口设置成尽可能的窄，当我们不断增加网页宽度时，会发现某个位置适合设置断点，我们将该位置的分辨率记下并设计新样式。然后继续拉宽窗口，然后在下一个适合设置断点的位置引入新的CSS样式文件，直到完成为止。还可以编写控制的更精细的复杂查询，比如要求宽度在500px以上，600px以下时起作用，那么可以这样写：

```css
@media screen and (min-width: 500px) and (max-width: 600px) {
  color: green;
}
```

## 3.5 其他常用布局技巧

我们可以通过将display属性设置为inline-block来实现铺满浏览器的小格子：

```css
.box2 {
  display: inline-block;
  width: 200px;
  height: 100px;
  margin: 1em;
}
```

![](./inline-block.png)

以及使用新的CSS属性column实现文字多列布局：

```css
.three-column {
  padding: 1em;
  -moz-column-count: 3;
  -moz-column-gap: 1em;
  -webkit-column-count: 3;
  -webkit-column-gap: 1em;
  column-count: 3;
  column-gap: 1em;
}
```

![](./column.png)

# 四. 弹性框布局

flex布局可以很轻松的实现以往需要各种复杂技巧才能实现的页面布局。开启flex布局的方式很简单，直接将元素的display属性设置为flex即可，此时其子元素的float，clear和vertical-align会失效。对块级元素可以设置flex，对行内元素同样可以使用inline-flex。下面先讲解基本概念和各种属性，然后我们通过实战来展示一下flex布局的强大。

## 4.1 基本概念

![](./flex-basic.png)

采用flex布局的元素称为flex容器（以下简称容器），它为容器的直接子元素提供了"弹性上下文"（注意display属性是不可继承的）。容器中所有的子元素被称为flex项（item，以下简称项）。容器中有两个轴：主轴（main）和交叉轴（cross）。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross start，结束位置叫做cross end。项默认沿主轴排列。单个项占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。

## 4.2 容器属性

### 4.2.1 flex-direction

![](./flex-direction.png)

flex布局是一种单向的一维布局方法。flex-direction属性决定main轴的方向，它有4个值：

1. row：水平方向，起点左端
2. row-reverse：水平方向，起点右端
3. column：垂直方向，起点上端
4. Column-reverse：垂直方向，起点下端

### 4.2.2 flex-wrap

![](flex-wrap.png)

flex-wrap属性决定轴线排不下如何换行，nowrap表示不换行：

![](./nowrap.png)

wrap表示换行，第一行在上方：

![](./wrap.png)

wrap-reverse表示换行，第一行在下方：

![](./wrap-reverse.png)

### 4.2.3 flex-flow

flex-flow属性是是flex-direction和flex-wrap的简写，默认值为row nowrap。

### 4.2.4 justify-content

justify-content属性决定项在主轴的对齐方式，有五个值：

1. flex-start：左对齐（默认）
2. flex-end：右对齐
3. center：居中
4. space-between：两端对齐，项之间间隔相等
5. space-around：每个项两侧的间隔相等，所以项之间的间隔比项与边框的间隔大一倍
6. space-evenly：每个项以及项和边缘之间的间隔均匀分布

![](./justify-content.png)



### 4.2.5 align-items

![](./align-items.png)

align-items属性决定项在交叉轴如何对齐，有如下五个值：

1. flex-start：交叉轴的起点对齐。

2. flex-end：交叉轴的终点对齐。

3. center：交叉轴的中点对齐。

4. baseline: 项目的第一行文字的基线对齐。

5. stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

### 4.2.6 align-content

![](./align-content.png)

align-content属性决定多根轴线的对齐方式，如果项只有一根轴线，该属性不起作用。

1. flex-start：与交叉轴的起点对齐。

2. flex-end：与交叉轴的终点对齐。

3. center：与交叉轴的中点对齐。

4. space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。

5. space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。

6. stretch（默认值）：轴线占满整个交叉轴。

## 4.3 项属性

### 4.3.1 order

![](./order.png)

order属性定义项的排列顺序，数值越小排列越靠前，默认为0。

### 4.3.2 flex-grow

![](./flex-grow.png)

flex-grow属性定义项的放大比例，默认为0，即便存在剩余空间也不放大。如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

### 4.3.3 flex-shrink

![](flex-shrink.jpg)

flex-shrink属性定义了项的缩小比例，默认为1，即如果空间不足，该项将缩小。如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小，负值对该属性无效。

###4.3.4 flex-basis

flex-basis属性定义了在分配多余空间之前，项占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项的本来大小。它可以设为跟width或height属性一样的值（比如350px，20%和5em等），则项将占据固定空间。

### 4.3.5 flex

flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

### 4.3.6 align-self

![](align-self.png)

align-self属性允许单个项有与其他项不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

## 4.4 布局实战

flex布局能够实现各种常见的布局，比如圣杯布局，输入框布局，媒体对象布局和固定的底栏。下面我们来看看这几种布局如何用flex布局实现。

### 4.4.1 圣杯布局

![](holy-grail.png)

圣杯布局是一种很常见的布局，它分为三部分：header，body和footer，其中body又包含left，center和right三个部分。html代码如下：

```html
<body class="HolyGrail">
  <header>...</header>
  <div class="HolyGrail-body">
    <main class="HolyGrail-content">...</main>
    <nav class="HolyGrail-nav">...</nav>
    <aside class="HolyGrail-ads">...</aside>
  </div>
  <footer>...</footer>
</body>
```

css代码如下：

```css
.HolyGrail {
  display: flex;
  min-height: 100vh;
  flex-direction: column;
}

header,
footer {
  flex: 1;
}

.HolyGrail-body {
  display: flex;
  flex: 1;
}

.HolyGrail-content {
  flex: 1;
}

.HolyGrail-nav, .HolyGrail-ads {
  /* 两个边栏的宽度设为12em */
  flex: 0 0 12em;
}

.HolyGrail-nav {
  /* 导航放到最左边 */
  order: -1;
}
```

如果屏幕尺寸比较小，则变为三栏垂直叠加：

```css
@media (max-width: 768px) {
  .HolyGrail-body {
    flex-direction: column;
    flex: 1;
  }
  .HolyGrail-nav,
  .HolyGrail-ads,
  .HolyGrail-content {
    flex: auto;
  }
}
```

### 4.4.2 输入框布局

![](./input-box.png)

主要是实现输入框前方添加提示，后方添加按钮，html代码如下：

```html
<div class="InputAddOn">
  <span class="InputAddOn-item">...</span>
  <input class="InputAddOn-field">
  <button class="InputAddOn-item">...</button>
</div>
```

css代码如下：

```css
.InputAddOn {
  display: flex;
}

.InputAddOn-field {
  flex: 1;
}
```

### 4.4.3 媒体对象布局

![](media-object.png)

有时候需要在文字的左边或者右边增加一个图片栏，而且要避免文字对图片的环绕。用flex的方式实现要比其他方式简洁，html代码如下：

```html
<div class="Media">
  <img class="Media-figure" src="" alt="">
  <p class="Media-body">...</p>
</div>
```

css代码如下：

```css
.Media {
  display: flex;
  align-items: flex-start;
}

.Media-figure {
  margin-right: 1em;
}

.Media-body {
  flex: 1;
}
```

###4.4.4 固定的底栏

![](sticky-footer.png)

有时，页面内容太少，无法占满一屏的高度，底栏就会抬高到页面的中间。需要让底栏总是出现在页面的底部。html代码如下：

```html
<body class="Site">
  <header>...</header>
  <main class="Site-content">...</main>
  <footer>...</footer>
</body>
```

css代码如下：

```css
.Site {
  display: flex;
  min-height: 100vh;
  flex-direction: column;
}

.Site-content {
  flex: 1;
}
```



## 4.5 布局模式

一种响应式设计的技巧是"从小处开始"：准备多套设计稿，先从最小屏幕开始设计，然后更大一点的，逐步加大。从最小屏幕开始设计，可以对页面内容优先级进行排序，在设计的时候就考虑需要对用户展示哪些重要信息，不容易漏掉重要的信息，这对优化性能也有帮助，最后，把针对多种屏幕尺寸的设计使用媒体查询联系起来即可。flex布局和响应式设计相结合，有4种常见的布局模式：掉落列（column drop），大体流动（mostly fluid），活动布局（layout shifter）和画布溢出（off canvas）。

### 4.5.1 掉落列模型

![](column-drop.png)

掉落队列模型在视口最窄的时候每个元素纵向堆放，第一个断点处，前两个元素并排显示，第三个元素在下面。第二个断点处，重排成三列布局。当视口达到最大宽度，列也达到最大宽度，在两侧添加外边距。html代码如下：

```html
<div class="container">
  <div class="box dark_blue"></div>
  <div class="box light_blue"></div>
  <div class="box green"></div>
</div>
```

基础css代码如下：

```css
.container {
  display: flex;
  flex-wrap: wrap;
}
.box {
  width: 100%;
}


```

第一个断点位于450px视口宽度：

```css
@media screen and (min-width: 450px) {
  .dark_blue {
    width: 25%;
  }
  .light_blue {
    width: 75%;
  }
}
```

第二个断点位于550px视口宽度：

```css
@media screen and (min-width: 550px) {
  .dark_blue, .green {
    width: 25%;
  }
  .light_blue {
    width: 50%;
  }
}
```

### 4.5.2 大体流动模型

![](./mostly-fluid.png)

大体流动模型在视口最窄时仍然为竖直堆放布局。随着视口变宽，网格模型开始出现，当视窗达到最大宽度，两边出现外边距，内容不再延展。html代码如下：

```html
<div class="container">
  <div class="box dark_blue"></div>
  <div class="box light_blue"></div>
  <div class="box green"></div>
  <div class="box red"></div>
  <div class="box orange"></div>
</div>
```

基础css代码如下：

```css
.container {
  display: flex;
  flex-wrap: wrap;
}
.box {
  width: 100%;
}
```

第一个断点处的css代码：

```css
@media screen and (min-width: 450px) {
  .light_blue, .green {
    width: 50%;
  }
}
```

第二个断点处的css代码：

```css
@media screen and (min-width: 550px) {
  .dark_blue, .light_blue {
    width: 25%;
  }
  .green, .red, .orange {
    width: 33.333333%;
  }
}
```

当视口宽度大于700px时，增加外边距：

```css
@media screen and (min-width: 700px) {
  .container {
    width: 700px;
    margin-left: auto;
    margin-right: auto;
  }
}
```

### 4.5.3 活动布局模型

![](./layout-shifter.png)

活动布局模型是最灵活的响应式模型，有很多适用于不同设备的断点。它的亮点在于利用了order属性重排元素顺序，每个布局可变化的地方比较多。html代码如下：

```html
<div class="container">
  <div class="box dark_blue"></div>
  <div class="container" id="container2">
    <div class="box light_blue"></div>
    <div class="box green"></div>
  </div>
  <div class="box red"></div>
</div>
```

基本的css代码如下：

```css
.container {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
}
.box {
  width: 100%;
}
```

第一个断点设置在500px视口宽度：

```css
@media screen and (min-width: 500px) {
  .dark_blue {
    width: 50%;
  }
  #container2 {
    width: 50%;
  }
}
```

第二个断点设置在600px视口宽度：

```css
@media screen and (min-width: 600px) {
  .dark_blue {
    width: 25%;
    order: 1;
  }
  #container2 {
    width: 50%;
  }
  .red {
    width: 25%;
    order: -1;
  }
}
```

### 4.5.4 画布溢出模型

![](off-canvas.png)

画布溢出模型是最复杂的。它的内容并不是竖直堆放的，诸如导航栏和应用菜单这样不常用内容需要放在屏幕之外，只有屏幕足够大的时候才显示出来。小尺寸屏幕上溢出画布的内容通常会在用户点击菜单按钮时出现。html代码如下：

```html
<nav id="drawer" class="dark_blue"></nav>
<main class="light_blue"></main>
```

基本的css代码如下：

```css
html, body, main {
  height: 100%;
  width: 100%; 
}

nav {
  width: 300px;
  height: 100%;
  position: absolute;
  /* 该变换将抽屉移出画布. */
  transform: translate(-300px, 0);
  /* 我们还可以为抽屉添加动画。 */
  transition: transform 0.3s ease;
}

nav.open {
  transform: translate(0, 0);
}
```

断点只有一个，在600px视口处显示侧边的导航栏：

```css
@media screen and (min-width: 600px) {
  nav {
    position: relative;
    transform: translate(0, 0);
  }
  
  body {
    display: flex;
    flex-flow: row nowrap;
  }
  
  main {
    width: auto;
    flex-grow: 1;
  }
}
```

这个布局需要使用一点点js代码来实现动态效果：

```css
var menu = document.querySelector('#menu');
var main = document.querySelector('main');
var drawer = document.querySelector('#drawer');

menu.addEventListener('click', function(e) {
  drawer.classList.toggle('open');
  e.stopPropagation();
});

main.addEventListener('click', function() {
  drawer.classList.remove('open');
});
```

以上就是flex布局的基本内容，我们需要在实践中多用多实践，熟练掌握这种强大的布局技术。flex布局适合小规模的布局，而更加强大的网格布局则适合大规模布局。

# 五. 网格布局

CSS的网格布局是一种二维的，基于网格的前端布局技术，完全改变了设计用户界面的方式。从布局发展的过程来看，从一开始基于tables，到float，再到positioning和inline-block，这些方法中充斥着大量的奇技淫巧，但连最基本的居中定位都做的不好。flex布局是一种很强大的基于轴线布局的一维布局方式，而网格布局则真正做到了基于行列单元格的二维布局，实际上flex搭配grid能工作的很好。网格布局与media query相结合同样也能通过寥寥数行代码实现响应式设计。

网格布局将网页划分为不同的网格，通过组合不同网格做出各种各样的布局。以前通过复杂css框架达成的效果现在浏览器内置了，所以称它为2019年必学的新布局方法，但目前浏览器的支持还存在一定问题，但以后会越来越好。

## 5.1 基本概念

同样地，在网格布局中只要给html元素增加了相应的display属性，该元素就变成**网格容器**。而容器的直接子元素被称为**项**（item），注意是直接子元素，因为display属性不可继承。网格布局还有一些专门的术语：行和列，网格线，网格轨道（track）和网格区域，下面来一一介绍。

### 5.1.1 行和列

在网格布局中，水平区域称之为**行**，垂直区域称之为**列**。**单元格**是行和列的交叉区域。

![](./row-and-col.png)

### 5.1.2 网格线

网格布局中的重要概念，分为水平网格线和垂直网格线，用于根据网格线定位位置。

![](./grid-lines.png)

### 5.1.3 网格轨道（grid track）

网格轨道是两相邻网格线之间的区域。

![](./grid-track.png)

### 5.1.4 网格区域

网格区域是4条网格线之间包围的区域，由1个或多个单元格构成。

![](./grid-area.png)

## 5.2 容器属性

用display属性可以将元素定义为网格容器，并给它所包含的内容创建一个网格布局的上下文环境：

```css
.container {
  display: grid | inline-grid;
}
```

grid代表块级网格，inline-grid代表行内级网格，后者表示该容器本身体现为行内元素，但内容却是网格布局的。开启网格布局之后，接下来我们来看看如何对布局内容进行精细化调整。

### 5.2.1 行高和列宽

grid-template-rows和grid-template-columns分别用于定义行高和列宽。值表示网格大小，值之间的空格表示网格线。语法格式如下：

```css
.container {
  grid-template-columns: <track-size> ... | <line-name> <track-size> ...;
  grid-template-rows: <track-size> ... | <line-name> <track-size> ...;
}
```

比如，定义一个3行5列的网格布局最后一行和中间一列使用auto关键字来表示由浏览器自己决定长度：

```css
.container {
  grid-template-columns: 40px 50px auto 50px 40px;
  grid-template-rows: 25% 100px auto;
}
```

![](./grid-template-rc.png)

上图中的每条网格线都得到了自动命名的4个名称。但我们可以使用方括号指定每一根网格线的名字，方便以后的引用。网格布局允许同一根线有多个名字，比如[fifth-line row-5]，给上面的布局重新定义一下网格线名称，可以这么写：

```css
.container {
  grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
  grid-template-rows: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
}
```

![](./named-grid-template-rc.png)

如果觉得这么写麻烦，还可以使用repeat()函数来指定行高和列宽。例如这样的写法：

```css
.container {
  grid-template-columns: repeat(3, 20px [col-start]);
}
```

等价于：

```css
.container {
  grid-template-columns: 20px [col-start] 20px [col-start] 20px [col-start];
}
```

fr关键字是fraction的缩写，如果两列的宽度分别为1fr和2fr，就表示后者按前者的两倍分配空闲空间，例如设置诶每个项占满容器的1/3：

```css
.container {
  grid-template-columns: 1fr 1fr 1fr;
}
```

因为可以与绝对长度的单位结合使用，空闲空间是在排除任何固定尺寸的项之后才计算的。比如下面这个例子中fr的计算就没有包含50px：

```css
.container {
grid-template-columns: 1fr 50px 1fr 1fr;
}
```

minmax()函数产生一个长度范围，表示长度就在这个范围之中，它接受两个参数，分别为最小值和最大值。比如：

```css
grid-template-columns: 1fr 1fr minmax(100px, 1fr);
```

上面这行代码中`minmax(100px, 1fr)`	表示长度不小于100px，不大于1fr。

还有个auto-fill关键字可以用来表示自动填充。有时单元格的大小是固定的，但是容器的大小不确定。如果希望每一行（或每一列）容纳尽可能多的单元格，就需要用到自动填充。

```css
.container {
  display: grid;
  grid-template-columns: repeat(auto-fill, 100px);
}
```

表示每列宽度`100px`，然后自动填充，直到容器不能放置更多的列。很多常见的实例用网格布局都能几行代码轻松搞定，比如两栏式布局，其中左边栏70%，右边栏30%：

```css
.wrapper {
  display: grid;
  grid-template-columns: 70% 30%;
}
```

十二网格布局：

```css
grid-template-columns: repeat(12, 1fr);
```

### 5.2.2 行间距与列间距

grid-row-gap属性定义行与行的间隔；grid-column-gap属性定义列与列的间隔，这个属性相当于是定义了网格线的大小。语法规则如下：

```css
.container {
  grid-column-gap: <line-size>;
  grid-row-gap: <line-size>;
}
```

下面这例子设置列间距为10px，行间距为15px：

```css
.container {
  grid-template-columns: 100px 50px 100px;
  grid-template-rows: 80px auto 80px; 
  grid-column-gap: 10px;
  grid-row-gap: 15px;
}
```

![](grid-gap.png)



grid-gap属性将成为二者的合并简写形式。如果grid-gap省略了第二个值，浏览器将认为第二个值等于第一个值。

> 根据最新标准，上面三个属性名的grid-前缀已经删除，grid-column-gap和grid-row-gap写成column-gap和row-gap，grid-gap写成gap

### 5.2.3 grid-template-areas属性

grid-template-areas属性通过引用由grid-area属性（下面会介绍）定义的网格区域名称来定义一个网格模板。重复指定的名称表示该区域跨越多个单元格。点号`.`表示空的单元格（不需要利用的区域）。该语法展示的是网格的结构形状。例如下面这段css代码：

```css
.item-a {
  grid-area: header;
}
.item-b {
  grid-area: main;
}
.item-c {
  grid-area: sidebar;
}
.item-d {
  grid-area: footer;
}

.container {
  display: grid;
  grid-template-columns: 50px 50px 50px 50px;
  grid-template-rows: auto;
  grid-template-areas: 
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
```

将定义如下的4列宽，3列高的网格布局。第一行由header全部占满；第二行为一个跨两列的main区域以及一个sidebar区域，最后一行由footer全部占满。

![](./grid-template-areas.png)

注意，区域的命名会影响到网格线。每个区域的起始网格线，会自动命名为区域名-start，终止网格线自动命名为区域名-end。比如，区域名为header，则起始位置的水平网格线和垂直网格线叫做header-start，终止位置的水平网格线和垂直网格线叫做header-end。

grid-template属性是grid-template-rows，grid-template-columns和grid-template-areas的简写形式，因此：

```css
.container {
  grid-template:
    [row1-start] "header header header" 25px [row1-end]
    [row2-start] "footer footer footer" 25px [row2-end]
    / auto 50px auto;
}
```

其实等价于：

```css
.container {
  grid-template-rows: [row1-start] 25px [row1-end row2-start] 25px [row2-end];
  grid-template-columns: auto 50px auto;
  grid-template-areas: 
    "header header header" 
    "footer footer footer";
}
```

### 5.2.4 grid-auto-flow属性

划分网格以后，容器的子元素会按照顺序，自动放置在每一个网格中，该属性指定放置的顺序，若取值为row则表示"先行后列"，若取值为column则表示"先列后行"。如果再加上了dense则表示"尽可能紧密填满”，也就是说如果有尺寸较小的元素，则尽量往前面放。

取值：

1. row：行优先布局，按次序填满一行，如果有需要则增加新的行（默认）
2. column：列优先布局，按次序填满一列，如果有需要则增加新的列
3. dense：如果有较小的项放在后面，算法会优先考虑将其放到前面来填补网格中的空余空间

用下面的html代码举个例子：

```html
<section class="container">
  <div class="item-a">item-a</div>
  <div class="item-b">item-b</div>
  <div class="item-c">item-c</div>
  <div class="item-d">item-d</div>
  <div class="item-e">item-e</div>
</section>
```

定义一个行优先的，2行5列的网格布局如下：

```css
.container {
  display: grid;
  grid-template-columns: 60px 60px 60px 60px 60px;
  grid-template-rows: 30px 30px;
  grid-auto-flow: row;
}
```

然后我们放置网格项：

```css
.item-a {
  grid-column: 1;
  grid-row: 1 / 3;
}
.item-e {
  grid-column: 5;
  grid-row: 1 / 3;
}
```

就能得到：

![](./auto-flow-row.png)

如果改为列优先，则变为：

![](./auto-flow-col.png)

### 5.2.5 新增网格的列宽和行高

有时候，一些项目的指定位置，在现有网格的外部。比如网格只有3列，但是某一个项目指定在第5行。这时，浏览器会自动生成多余的网格（称为隐式网格轨道）以便放置项目，如果不指定grid-auto-columns和grid-auto-rows这两个属性，浏览器会完全根据单元格内容的大小，决定新增网格的列宽和行高。

grid-auto-columns属性指定浏览器自动创建的多余网格的列宽，grid-auto-rows属性指定浏览器自动创建的多余网格的行高。可以使用长度，百分比和fr作为单位。比如有如下的2*2网格：

```css
.container {
  grid-template-columns: 60px 60px;
  grid-template-rows: 90px 90px
}
```

![](./grid-auto-rc.png)

现在有一个网格的位置被放置在了这个2*2的网格外面：

```css
.item-a {
  grid-column: 1 / 2;
  grid-row: 2 / 3;
}
.item-b {
  grid-column: 5 / 6;
  grid-row: 2 / 3;
}
```

这些自动生成的网格轨道的大小会完全根据所放置的项的大小来决定（注意第3列和第4列）：

![](./grid-auto-rc2.png)

我们可以给这些隐式网格轨道指定列宽或者行高：

```css
.container {
  grid-auto-columns: 60px;
}
```

![](./grid-auto-rc3.png)

### 5.2.6 grid属性

grid属性是grid-template-rows，grid-template-columns，grid-template-areas，grid-auto-rows，grid-auto-columns和grid-auto-flow五个属性的简写形式。

取值：

1. **none**：设置所有的5个属性为默认值
2. **<grid-template>**：与grid-template简写属性相同
3. **<grid-template-rows> / [ auto-flow && dense? ] <grid-auto-columns>?**：设置grid-template-rows的值；如果auto-flow出现在反斜杠的右边，则表示先列后行；如果再加上dense关键字则表示紧凑模式；如果不设置grid-auto-columns，则默认为auto
4. **[ auto-flow && dense? ] <grid-auto-rows>? / <grid-template-columns>**：设置grid-template-columns的值；如果auto-flow出现在反斜杠的左边，则表示先行后列；如果再加上dense关键字则表示紧凑模式；如果不设置grid-auto-rows，则默认为auto

下面的css是等价的：

```css
.container {
    grid: 100px 300px / 3fr 1fr;
}

.container {
    grid-template-rows: 100px 300px;
    grid-template-columns: 3fr 1fr;
}
```

下面这段css也是等价的：

```css
.container {
    grid: auto-flow / 200px 1fr;
}

.container {
    grid-auto-flow: row;
    grid-template-columns: 200px 1fr;
}
```

以及：

```css
.container {
    grid: auto-flow dense 100px / 1fr 2fr;
}

.container {
    grid-auto-flow: row dense;
    grid-auto-rows: 100px;
    grid-template-columns: 1fr 2fr;
}
```

还有：

```css
.container {
    grid: 100px 300px / auto-flow 200px;
}

.container {
    grid-template-rows: 100px 300px;
    grid-auto-flow: column;
    grid-auto-columns: 200px;
}
```

grid属性还有一种更复杂的写法：一次性把grid-template-areas，grid-template-rows和grid-template-columns的设置都搞定，所有其他的设置都保持默认值，比如下面这两段css是等价的：

```css
.container {
    grid: [row1-start] "header header header" 1fr [row1-end]
          [row2-start] "footer footer footer" 25px [row2-end]
          / auto 50px auto;
}

.container {
    grid-template-areas: 
      "header header header"
      "footer footer footer";
    grid-template-rows: [row1-start] 1fr [row1-end row2-start] 25px [row2-end];
    grid-template-columns: auto 50px auto;    
}
```

这个属性的用法比较灵活，需要多实践才能熟练掌握。

### 5.2.7 单元格对齐方式

单元格的对齐有垂直和水平两个方向。水平方向的单元格对齐使用justify-items属性，垂直方向的单元格对齐使用align-items属性。

它们都有4种取值：

* start：对齐单元格的起始边缘。

* end：对齐单元格的结束边缘。

* center：单元格内部居中。

* stretch：拉伸，占满单元格的整个宽度（默认值）

start对齐单元格的起始边缘（对于水平方向来说是左边缘，对于垂直方向来说是上边缘）：

```css
.container {
  justify-items: start;
}
```

![](./start1.png)

```css
.container {
  align-items: start;
}
```

![](./start2.png)

end对齐单元格的结束边缘（对于水平方向来说是右边缘，对于垂直方向来说是下边缘）：

```css
.container {
  justify-items: end;
}
```

![](./end1.png)

```css
.container {
  align-items: end;
}
```

![](./end2.png)

center将单元格内部居中：

```css
.container {
  justify-items: center;
}
```

![](./center1.png)

```css
.container {
  align-items: center;
}
```

![](./center2.png)

stretch将单元格拉伸，占满单元格整个宽度（默认值）：

```css
.container {
  justify-items: stretch;
}

.container {
  align-items: stretch;
}
```

![](./stretch.png)

place-items属性是align-items属性和justify-items属性的合并简写形式，如果省略第二个值，则浏览器认为与第一个值相等。

### 5.2.8 内容区域对齐方式

如果所有的网格项的大小都是用固定的单位比如px指定的，那么网格本身的尺寸就有可能比容纳网格的容器尺寸更小，这个时候我们可以通过一些属性来设置网格容器中的网格如何对齐，对齐的方式仍然包含水平和垂直两个方向。

justify-content属性用于水平方向对齐，而align-content属性用于垂直方向对齐。它们都可以选择如下取值：

1. start：对齐容器的起始边框
2. end：对齐容器的结束边框
3. center：容器内部居中
4. stretch：项的大小没有指定时，拉伸占据整个网格容器
5. space-around：每个项两侧的间隔相等。所以，项之间的间隔比项与容器边框的间隔大一倍
6. space-between：项与项的间隔相等，项与容器边框之间没有间隔
7. space-evenly：项与项的间隔相等，项与容器边框之间也是同样长度的间隔

start对齐容器的起始边框（水平方向上是左边框，垂直方向上是上边框）：

```css
.container {
  justify-content: start;
}
```

![](content-start.png)

```css
.container {
  align-content: start;	
}
```

![](./content-start2.png)

end对齐容器的结束边框（水平方向上是右边框，垂直方向上是下边框）：

```css
.container {
  justify-content: end;	
}
```

![](./content-end.png)

```css
.container {
  align-content: end;	
}
```

![](./content-end2.png)

center将网格在容器内居中显示：

```css
.container {
  justify-content: center;	
}
```

![](./content-center.png)

```css
.container {
  align-content: center;	
}
```

![](./content-center2.png)

stretch使得网格拉伸占据整个容器：

```css
.container {
  justify-content: stretch;	
}
```

![](./content-stretch.png)

```css
.container {
  align-content: stretch;	
}
```

![](./content-stretch2.png)

space-around使得两侧的间隔相等，因此中间部分的要比两边的间隔大一倍：

```css
.container {
  justify-content: space-around;	
}
```

![](./content-space-around.png)

```css
.container {
  align-content: space-around;	
}
```

![](./content-space-around2.png)

space-between使得项与项之间间隔相等：

```css
.container {
  justify-content: space-between;	
}
```

![](./content-space-between.png)

```css
.container {
  align-content: space-between;	
}
```

![](./content-space-between2.png)

space-evenly使得项与项之间间隔相等，也包括项与边框之间的间隔：

```css
.container {
  justify-content: space-evenly;	
}
```

![](./content-space-evenly.png)

```css
.container {
  align-content: space-evenly;	
}
```

![](./content-space-evenly2.png)

## 5.3 项属性

对于一个网格项而言，`float`，`display: inline-block`，`display: table-cell`，`vertical-align` 和 `column-*`属性都将失效。

### 5.3.1 根据网格线定位项的位置

网格布局是通过网格线来定位网格项的位置的。grid-column-start属性定位左边框所在的垂直网格线，grid-column-end属性定位右边框所在的垂直网格线，有一个grid-column属性能够提供同时定义这两个属性的简写形式。grid-row-start属性定义上边框所在的水平网格线，grid-row-end属性定义下边框所在的水平网格线，有一个grid-row属性能够提供同时定义这两个属性的简写形式。这四个属性的值，除了指定为第几个网格线，还可以指定为网格线的名字。这四个属性的值还可以使用span关键字，表示"跨越"，即左右边框（上下边框）之间跨越多少个网格。四个属性都可以使用auto关键字表示自动放置/跨越/默认单位为1的跨越。使用这四个属性，如果产生了项目的重叠，则使用z-index属性指定项目的重叠顺序。

举例1:

```css
.item-a {
  grid-column-start: 2;
  grid-column-end: five;
  grid-row-start: row1-start
  grid-row-end: 3;
}
```

![](./grid-rc1.png)

```css
.item-b {
  grid-column-start: 1;
  grid-column-end: span col4-start;
  grid-row-start: 2
  grid-row-end: span 2
}
```

![](./grid-rc2.png)

使用简写形式：

```css
.item-c {
  grid-column: 3 / span 2;
  grid-row: third-line / 4;
}
```

![](./grid-rc.png)

### 5.3.2 grid-area属性

grid-area属性用于给项命名，该名称可以在grid-template-areas中被引用到。或者用作grid-row-start、grid-column-start、grid-row-end、grid-column-end的合并简写形式，直接指定项目的位置。

举例，第一种用法是给网格项命名：

```css
.item-d {
  grid-area: header;
}
```

第二种用法：

```css
.item-d {
  grid-area: 1 / col4-start / last-line / 6;
}
```

![](./grid-area.png)

### 5.3.3 设置单元格内容位置

设置单元格内容位置跟设置单元格对齐方式很像，都分为水平方向和垂直方向。区别在于前者是针对单个的项，而后者针对的是全部的项。

justify-self属性在一个单元格中对齐项的水平位置，align-self属性在一个单元格中对齐项的垂直位置。这两个属性都可以取4个值：

* start：对齐单元格的起始边缘。

* end：对齐单元格的结束边缘。

* center：单元格内部居中。

* stretch：拉伸，占满单元格的整个宽度（默认值）

start对齐单元格的起始边缘（水平方向上的左边缘和垂直方向上的上边缘）：

```css
.item-a {
  justify-self: start;
}
```

![](./self-start.png)

```css
.item-a {
  align-self: start;
}
```

![](./self-start2.png)

end对齐单元格的结束边缘（水平方向的右边缘和垂直方向的下边缘）：

```css
.item-a {
  justify-self: end;
}
```

![](./self-end.png)

```css
.item-a {
  align-self: end;
}
```

![](./self-end2.png)

center在单元格内部居中：

```css
.item-a {
  justify-self: center;
}
```

![](./self-center.png)

```css
.item-a {
  align-self: center;
}
```

![](./self-center2.png)

stretch拉伸并占满单元格的整个宽度：

```css
.item-a {
  justify-self: stretch;
}
```

![](./self-stretch.png)

```CSS
.item-a {
  align-self: stretch;
}
```

![](./self-stretch2.png)

place-self属性是align-self属性和justify-self属性的合并简写形式，如果省略第二个值，place-self属性会认为这两个值相等。以上就是网格布局所有的属性，要熟练掌握网格布局，还是需要在实践中多加练习。有了网格布局，原来我们要实现的很多复杂的响应式布局就能够得到极大的简化，[这篇文章](<https://blog.theodo.fr/2018/03/stop-using-bootstrap-layout-thanks-to-css-grid/>)介绍了如何用网格布局来完全替代Bootstrap框架。

# 	六. 表格和字体的响应式设计

响应式设计中表格，字体和图片也是要讨论的重要主题。这里简单介绍下响应式设计中如何处理表格和字体。下一部分会介绍响应式设计中的图片。

## 6.1 响应式表格

对于网页中出现的表格而言，如果列数超出一定的范围，那么可能需要两个方向的滚动条左右上下拖动来查看，这是一种很不好的用户体验。要尽量避免出现两个方向上的滚动条，不同的情况有不同的解决方案。对于表格有三种响应式设计的技巧：隐藏列（hidden columns），无表格设计（no more tables）以及表格内滚动（contained tables）。

### 6.1.1 隐藏列

隐藏列的设计技巧是当视口尺寸缩小时，根据信息的重要性选择隐藏一些列。沿着"从小处开始"的设计技巧，先思考什么信息最重要，对这部分信息进行保留，然后用display: none隐藏其他相对不那么重要的信息。这个技巧最大的问题是它向用户隐瞒了一些信息，所以使用时要谨慎。如果有可能的话，尽量使用缩写，而不是隐藏它。

比如下面这个表格：

```html
<table class="scores__table">
  <thead>
  </thead>
</table>
```



我们可以设计当视口宽度小于等于499px时隐藏gametime列：

```css
@media screen and (max-width: 499px) {
  .gametime {
    display: none;
  }
}
```

### 6.1.2 无表格设计

当视口宽度小于一定值时，表格将使用CSS重组成长列表而不是数据表。这种解决方案的好处在于所有数据都是可见的。以下面这个棒球得分表为例：

![](./team-table.png)

```html
<table>
  <thead>
    <tr>
      <th>Team</th>
      <th>1st</th>
      <th>2nd</th>
      <th>3rd</th>
      <th>4th</th>
      <th>5th</th>
      <th>6th</th>
      <th>7th</th>
      <th>8th</th>
      <th>9th</th>
      <th>Final</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-th="Team">Toronto</td>
      <td data-th="1st">0</td>
      <td data-th="2nd">0</td>
      <td data-th="3rd">0</td>
      <td data-th="4th">4</td>
      <td data-th="5th">0</td>
      <td data-th="6th">1</td>
      <td data-th="7th">0</td>
      <td data-th="8th">0</td>
      <td data-th="9th">0</td>
      <td data-th="Final">5</td>
    </tr>
    <tr>
      <td data-th="Team">San Francisco</td>
      <td data-th="1st">0</td>
      <td data-th="2nd">0</td>
      <td data-th="3rd">0</td>
      <td data-th="4th">4</td>
      <td data-th="5th">0</td>
      <td data-th="6th">0</td>
      <td data-th="7th">0</td>
      <td data-th="8th">0</td>
      <td data-th="9th">0</td>
      <td data-th="Final">4</td>
    </tr>
  </tbody>
</table>
```

当视口宽度小于500px时变成长列表：

![](./team-table2.png)

```css
table {
  border: 1px solid #ddd;
}

tr:nth-child(odd) {
  background-color: #f9f9f9;
}
      
@media screen and (max-width: 500px) {
  table, thead, tbody, th, td, tr {
    display: block;
  }
  thead tr {
    position: absolute;
    top: -9999px;
    left: -9999px;
  }
  td { 
    position: relative;
    padding-left: 50%; 
  }
  td:before { 
    position: absolute;
    left: 6px;
    content: attr(data-th);
    font-weight: bold;
  }
  td:first-of-type {
    font-weight: bold;
  }
}
```

### 6.1.3 表格内滚动

表格内滚动是一种为超出视口宽度的表格添加水平滚动条的方法。首先将表格包括在div中，然后使用CSS将这个div设置成100%宽并设置`overflow-x: auto`即可。

```css
div {
  width: 100%;
  overflow-x: auto;
}
```

## 6.2 响应式字体

网页上每一行文字的字数不能太长也不能太短。太短的话人为割裂了词组会导致句子含义难以理解；太长的话不容易定位下一行，用户读着读着就没耐心开始略读了。一个好的建议是网页上每行大约65个字符，字体足够大：至少16像素，行高至少1.2em。我们可以设置一些次要的断点媒体查询来改善字体的响应式设计，比如当视口宽度增加时给一些内容增加字号，增加内边距并增大图标的尺寸。

# 七. 图片的响应式设计

在打开网页所需的平均字节数中，图片的加载要耗去60%。所以我们在设计网页时对图片的使用一定要注意。响应式图片设计的目的是用最少的字节传输最高质量的图片，因为对于移动设备以及不太好的网络环境而言，图片精度太高容易增加页面的加载延迟。我们要避免出现图片放大后像素失真，图片无法加载和裁剪后看不到图像全貌等问题，使得图片在各种尺寸设备上表现良好，从而提升用户体验。

> Create a product, don't re-imagine one for small screens, great mobile products are created, never ported.   --Brian Fling

## 7.1 图片加载的性能优化

我们通常要考虑图片的质量和大小。然而对于网页上的图片而言，我们只需要考虑大小就可以了，即图片的压缩等级和实际的分辨率。图片文件的大小往往取决于像素数和每个像素所占的比特数量。所以为了提高网站的性能，我们要使用尽可能小的图片尺寸和尽可能高的图片压缩率。常见的错误是使用了过大的图片尺寸和过高的图片质量。有人统计过，平均每个网页要发出56个左右的请求来加载图片，每次请求对页面加载来说都是一项成本，一个小的页面加载延迟都可能造成明显的流量和经济损失。Google页面加载每增加0.4到0.9秒，将导致流量和广告收入降低20%；Amazon页面加载延迟每增加100ms就意味着1%销售额损失。

### 7.1.1 不使用固定大小图片

不要使用固定大小的图片，因为它无法根据视口的尺寸来改变自身大小，应该使用百分比相对大小，比如max-wdith: 100%。对于台式机或笔记本电脑而言，不要假设视口尺寸和屏幕尺寸相同，也不要假设视口会一直保持相同的大小。使用max-width是一种优雅地响应视图区域变化的方法。如果想要两张图片并列对齐，使每个图像为可用宽度的一半，并留有10px的间隔，那么就可以使用calc()函数：

```css
img {
  width: calc((100% - 10px) / 2);
}

img: last-of-type {
  margin-right: 0;
}
```

### 7.1.2 使用特殊的CSS单位

vh和vw两种单位分别用来表示视口的高度和宽度。一个vh单位对应1%视口高度，一个vw单位对应1%视口宽度，所以100vh表示100%视口高度，100vw表示100%视口宽度。另一种常见的响应式用例是调整图片尺寸来适应视口宽度或者高度的较小者或较大者，就可以分别使用vmin和vmax。

### 7.1.3 栅格图和矢量图

有两种不同的基础方法创建和存储图片，一种叫栅格图，另一种叫矢量图。前者是一种点阵图片，我们平时从照相机，扫描仪中得到的图片都是栅格图。而后者是用特殊格式描述的线条组成的矢量图形。矢量图片优于栅格图片的一点是浏览器可以渲染任意尺寸的矢量图片，当视口尺寸增加时，矢量图不会失真而产生锯齿。为了提升性能，建议为照片使用jpeg格式，为矢量图使用svg格式，如果是商标等logo图案，如果不能使用svg，则使用png。

### 7.1.4 使用专业工具优化图片

有一些专业的工具可以提供对图片的批量处理。使用ImageMagick可以转换图片格式，裁剪或者应用滤镜。可以生成同一幅图片的多种版本或不同的尺寸和格式。还可以使用Grunt任务来使用ImageMagick，比如Grunt的响应式图片插件，能够一次性得到许多不同质量的图片。还有其他一些工具可以使用，ImageOptim可以利用很多开源工具来生成无损图片。

ImageMagick:

* [ImageMagick](http://www.imagemagick.org/)

* [Mac 上的一个简单的 ImageMagick 安装包](http://cactuslab.com/imagemagick/)

* [GraphicsMagick](http://www.graphicsmagick.org/) (ImageMagick 的一个分叉)

Grunt:

* [Grunt 简介](http://gruntjs.com/getting-started)

* [Grunt 使用入门](http://24ways.org/2013/grunt-is-not-weird-and-hard/)

* [用 Grunt 生成不同分辨率的图片](http://addyosmani.com/blog/generate-multi-resolution-images-for-srcset-with-grunt/)

* [用于生成多张图片的 grunt-responsive-images 插件](https://github.com/andismith/grunt-responsive-images)

* [用于响应式图片工作流的 grunt-respimg 插件](https://www.npmjs.com/package/grunt-respimg)

图片处理工具：

* [ImageOptim](http://imageoptim.com/) (Mac)

* [Trimage](http://trimage.org/) - 和 ImageOptim 类似 (Windows, Mac, Linux)

* [ImageAlpha](https://github.com/pornel/ImageAlpha)

### 7.1.5 对页面进行优化检查

怎样才能检查页面上所有的图片都被优化了呢？有一个在线检查图片优化情况的工具叫PageSpeed Insights。可以用它的网页办来检查网站，还可以在开发者工具中使用它。最厉害的是它还有API接口，可以在终端下通过curl命令来检查。也可以把这个检查过程写入到推送代码到仓库时的构建测试中，使用grunt task runner来实现。grunt有PageSpeed Insights插件可供使用，参考资料：

* [PageSpeed Insights 示例](https://developers.google.com/speed/pagespeed/insights/?url=simpl.info%2Fcssfilters)

* [Grunt PageSpeed 插件](https://www.npmjs.com/package/grunt-pagespeed)

* [PageSpeed Node module](https://github.com/addyosmani/psi/)

* [cURL 示例](http://www.thegeekstuff.com/2012/04/curl-examples/)

* [PageSpeed Insights Node module](https://github.com/addyosmani/psi/)

## 7.2 标记图片的技巧

对移动网络而言，文件请求次数和请求文件的大小同样重要。我们还需要进一步优化减少请求图片的次数，而不仅仅是关注图片的大小。**性能**是真正响应式设计的基本组成部分，既要压缩图片，又要减少图片的数量。这一节我们将介绍一些减少图片请求次数的技巧。

### 7.2.1 不要将文字保存成图片

首先，不要将文字保存成图片，因为放大后会失真，还会使网页文件变大，造成延迟。使用图片形式的文字还有一个问题就是无法被搜索引擎找到，也不能被屏幕阅读器读取。正确的做法应该是直接把文本覆盖在图片上面，这样图片和文字就有更好的显示和放大效果，文本也可以被选中，并且这样还能更方便的使用CSS来添加效果，文件尺寸也更小。

### 7.2.2 使用CSS技巧

除了用来调整样式属性，CSS还可以用来实现其他图形效果，比如渐变，阴影，圆角或动画效果。但是要注意使用CSS生成这些视觉效果是有处理和渲染成本的，这在移动设备上尤其明显，要谨慎使用。

关于如何提升网站在移动设备上的响应速度，可以参考[这篇文章](<https://www.smashingmagazine.com/2013/04/build-fast-loading-mobile-website/>)。

### 7.2.3 使用符号字符和图标字体

还有一种方法可以避免使用图片并保持网站的响应性。如果要使用一些图形标志，比如箭头，星星或者桃心，可以从unicode字符集中找到这样的字符。

* [Unicode 字符集](https://unicode-table.com/cn/sets/)

* [Unicode 符号集合](http://en.wikipedia.org/wiki/List_of_Unicode_characters)

另外，图标字体让修饰网站的常用图片和图标使用起来更方便。图标字体相比图片有很多优点，它们是矢量图形，可以无限缩放，整套图像可以以一套字体的形式下载，而只消耗很小的下载量。

* [Zocial](http://zocial.smcllns.com/)

* [Font Awesome](http://fontawesome.io/)

* [We Love Icon Fonts!](http://weloveiconfonts.com/)

* [Icon fonts on CSS-Tricks](https://css-tricks.com/examples/IconFont/)

### 7.2.4 内嵌图片

如果想要减少网页的文件请求数量，还可以利用代码实现内嵌图片，有两种实现方式：SVG或者数据URI。内嵌的SVG具有很好的移动端和PC端浏览器支持，优化工具还能极大减少SVG的字节。数据URI提供了一种将文件（比如图片）内嵌为base64编码的字符串的机制，它也具有很好的浏览器支持。它们也可以嵌入到CSS里。

一些参考资料：

* [行内 SVG 浏览器支持](http://caniuse.com/#feat=svg-html5)

* [数据 URI 浏览器支持](http://caniuse.com/datauri)

* [SVG 优化工具](http://petercollingridge.appspot.com/svg-optimiser)

* [Trajan 的 Column SVG 例子](http://upload.wikimedia.org/wikipedia/commons/6/6c/Trajans-Column-lower-animated.svg)

* [20 个让你惊艳不已的 SVG 例子](http://www.creativebloq.com/design/examples-svg-7112785)

* [SVG 动画示例](http://codepen.io/chrisgannon/)

## 7.3 完全响应式

在不同的上下文环境中都使用同一张图片可能不是一个好的做法。使用媒体查询有它自身的一些局限性，首先它不一定能支持未来出现的一些平台，其次媒体查询只参考了视窗的大小而不是图片的实际尺寸。下面介绍的一些方法采用了另一种截然不同的新思路：给浏览器提供信息以让它在多张图片中做出最好的选择。

### 7.3.1 srcset属性

img元素的src属性只能提供一个图片文件，而srcset属性给同一张图片提供可选择的多个文件，可以为更高DPI显示器选择更高分辨率的图片文件，否则使用低分辨率图片文件。这样浏览器可以根据视口尺寸和设备性能做出最合适的选择。`srcset` 有两种自定义方式，一种使用 `x` 来区分设备像素比 (DPR)，另一种使用 `w` 来描述图像的宽度。对设备像素比的反应：

```html
<img src="image_2x.jpg" srcset="image_2x.jpg 2x, image_1x.jpg 1x" alt="a cool image">
```

将 `srcset` 设置为与逗号分隔的一连串 `filename multiplier` 对相等，其中每个 `multiplier` 必须是后跟 `x` 的整数。例如，`1x` 表示 1 倍显示屏，`2x` 表示像素密度为两倍的显示屏，如 Apple 的 Retina 显示屏。浏览器会下载与其 DPR 对应的最佳图片。另请注意，有一个作为备用的 `src` 属性。对图片宽度的反应：

```html
<img src="image_200.jpg" srcset="image_200.jpg 200w, image_100.jpg 100w" alt="a cool image">
```

将 `srcset` 设置为与逗号分隔的一连串 `filename widthDescriptor` 对相等，其中每个 `widthDescriptor` 都以像素为测量单位， 并且必须是后跟 `w` 的整数。在这里，`widthDescriptor` 指定每个图片文件的自然宽度，使浏览器能够根据窗口大小和 DPR 选择要请求的最适当的图片。（如果没有 `widthDescriptor`，浏览器需要下载图片才能知道其宽度！）

* [一篇关于 srcset 的有趣文章](http://ericportis.com/posts/2014/srcset-sizes/)

* [设备像素密度列表](http://pixensity.com/list/phone)

* [关于像素密度的更多信息](http://www.html5rocks.com/en/mobile/high-dpi/)

* [Working with h units](https://github.com/ResponsiveImagesCG/picture-element/issues/86)

### 7.3.2 sizes属性

有一点要说明的是，在 JavaScript 中，可以通过 `currentSrc` 获得 `img` 元素的来源。`sizes` 属性为浏览器提供了有关图片元素显示大小的信息，它实际上不会导致图片大小调整。该操作是在 CSS 中执行的！

包含大小的图片宽度

如果图片不以全窗口宽度显示会怎样？那么解析完HTML后，在解析CSS前浏览器就会预加载图片。问题是浏览器对图片尺寸一无所知，我们需要告诉浏览器图片的实际显示尺寸。除了 `srcset` 外，还需要sizes属性。向包含媒体查询的图片添加一个 `sizes` 属性和一个 `vw` 值。`srcset` 和 `sizes` 合起来可让浏览器知道图片的自然宽度以及图片相对于窗口宽度的显示宽度。 知道图片的显示宽度和可用图片文件的宽度后，浏览器在解析HTML时将获得下载具有满足其需求的适当分辨率且尽可能小的图片所需的信息。 这里是一个srcset与sizes配合使用的语法示例：

```html
<img  src="images/great_pic_800.jpg"
      sizes="(max-width: 400px) 100vw, (min-width: 401px) 50vw"
      srcset="images/great_pic_400.jpg 400w, images/great_pic_800.jpg 800w"
      alt="great picture">
```

`sizes` 由以逗号分隔的 `mediaQuery width` 对组成。`sizes` 会在加载流程初期告诉浏览器，该图片会在点击 `mediaQuery` 时以某个 `width` 显示。实际上，如果 `sizes` 缺失，浏览器会将 `sizes` 默认为 `100vw`，表示它预计图片将以全窗口宽度显示。`sizes` 会为浏览器额外提供一条信息，以确保它根据图片的最终显示宽度下载正确的图片文件。说明一下，它实际上*不会*调整图片的大小 - 这是 CSS 的工作。在本示例中，如果浏览器的窗口宽度等于或小于 400 像素，浏览器知道图片将为全窗口宽度；如果窗口宽度大于 400 像素，则为一半窗口宽度。浏览器知道它具有两个图片选项：一个具有 400 像素的自然宽度，另一个具有 800 像素。

### 7.3.3 picture元素

新增的picture元素可以通过source元素提供可选择的源文件：

```html
<picture>
  <source srcset="kittens.webp" type="image/webp">
  <source srcset="kittens.jpeg" type="image/jpeg">
  <img src="kittens.jpeg" alt="Two grey tabby kittens">
</picture>
```

如果浏览器可以使用第一个资源，则使用它。否则就沿着列表查询下去。浏览器可以根据设备性能选择文件。上面这个HTML文件为支持[webp](https://developers.google.com/speed/webp/?csw=1)的浏览器选择webp文件，jpeg作为备选资源。这种方式使得支持webp的平台充分利用高性能webp格式。为不支持它的平台提供替代方案。

值得注意的是，我们一定要为image元素增加alt属性，这是一种**无障碍性承诺**，这是一种对于视觉障碍者友好的方式。关于 alt 属性的一般建议：

* 对于重要图片来说，`alt` 属性应该具有描述性；

* 对于纯装饰性的图片，`alt` 属性应该为空；

* 应该为每张图片设置 `alt` 属性。

# 八. 参考文献

1. 特别鸣谢：[Udacity前端工程师纳米学位](<https://cn.udacity.com/fend>)

2. 视口相关基础知识：

   [在移动浏览器中使用viewport元标签控制布局](<https://developer.mozilla.org/zh-CN/docs/Mobile/Viewport_meta_tag>)

   [A tale of two viewports - part one](<https://www.quirksmode.org/mobile/viewports.html>)

   [两个viewport的故事-第一部分(桌面版)](<https://www.zcfy.cc/article/a-tale-of-two-viewports-part-one-2375.html>)

3. CSS布局基础：

   [学习CSS布局](<http://zh.learnlayout.com/>)

4. [响应式网页设计](<http://www.ruanyifeng.com/blog/2012/05/responsive_web_design.html>)

5. Flex布局相关：

   [Flex布局教程：语法篇](<http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html>)

   [Flex布局教程：实例篇](<http://www.ruanyifeng.com/blog/2015/07/flex-examples.html>)

   [A Complete Guide to Flexbox](<https://css-tricks.com/snippets/css/a-guide-to-flexbox/>)

   [交互式Flex布局教程](https://scrimba.com/g/gflexbox)

6. Grid布局相关：

   [CSS Grid 网格布局教程](<http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html>)

   [A Complete Guide to Grid](<https://css-tricks.com/snippets/css/complete-guide-grid/>)

   [How I stopped using Bootstrap’s layout thanks to CSS Grid](<https://blog.theodo.fr/2018/03/stop-using-bootstrap-layout-thanks-to-css-grid/>)

   [交互式Grid布局教程](<https://scrimba.com/g/gR8PTE>)