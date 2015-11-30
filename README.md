#  H5的开发经验
## 移动端自适应方案
### 写页面时的一些规范
1. css、html、js文件编码统一使用UTF-8 <br>
2. 尽量使用HTML5新标签，使代码更加语义化<br>
3. 禁止使用#定义样式，避免与js的id发生冲突，统一使用类<br>
4. 不在style中定义样式，除特殊情况（百分比之类）<br>
5. js脚本统一放到body之后<br>
6. 样式大块之间需要添加注释，且空一行<br>
7. 交互和动画需要添加注释说明<br>
8. 特殊图标使用网页代码，例如： ©使用&copy<br>
9. 样式属性之间、括弧与代码之间不留空格<br>
10. 样式命名需要见名知意，建议不要使用1、2、3、4或者a、b、c、d之类<br>
11. 提炼通用结构，使用扩展类进行不同样式的定义<br>
12. 大背景图片大小控制在60kb以内（100kb）<br>
13. 模块的标题使用h1-h6，根据模块重要程度选择使用<br>
14. 尽量简化标签结构层次<br>
15. html结构合理简单清晰<br>
16. 能用代码实现尽量不用使用图片，用以减少请求数量<br>
17. 列表结构尽量使用ul ol dl<br>
18. 模块结构划分合理，不能讲同一块信息分在不同结构上<br>
19. 切图需要分析哪些元素需要动态获取的（img数据类的），哪些元素是固定不变的（bg修饰类的）以达到结构可扩展性<br>
20.给不确定的字段留够空间，超出使用<br>
21.合理使用绝对和相对定位<br>
22.多尝试使用css3盒布局和其他新属性<br>
23.考虑多种状态的存在（列表页、无数据提示、loading提示、报错等等）<br>
24.扩展性要求高的结构宽高不要定死<br>
25.能加title的地方尽量加上title<br>
26.纯图片的结构里边需要有文字，使用text-indent隐藏<br>
27.合并雪碧图，目前我们有使用字体图标，base64、图片<br>

### 关于viewport
先来了解一点关于viewport的知识，通常我们采用如下代码设置viewport:<br>
  `<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">`
这样整个网页在设备内显示时的页面宽度就会等于设备逻辑像素大小，也就是device-width。这个device-width的计算公式为：<br>

设备的物理分辨率/(devicePixelRatio * scale)，在scale为1的情况下，device-width = 设备的物理分辨率/devicePixelRatio 。dpr是devicePixelRatio的简写，也就是屏幕分辩比。<br>

历史原因，由于苹果retina的产生，使得清晰度提升，主要是因为设备像素提升了一倍，因此可以用更多像素去绘画更清晰的图像.<br>
坊间对于dpr更通俗的说法叫<br>
. 一倍屏<br>
. 两倍屏<br>
. 三倍屏<br>
devicePixelRatio称为设备像素比，每款设备的devicePixelRatio都是已知，并且不变的，目前高清屏，普遍都是2，不过还有更高的，比如2.5, 3 等，我魅族note的手机的devicePixelRatio就是3。淘宝触屏版布局的前提就是viewport的scale根据devicePixelRatio动态设置：在devicePixelRatio为2的时候，scale为0.5,在devicePixelRatio为3的时候，scale为0.3333.<br>
<br>
这么做目的当然是为了保证页面的大小与设计稿保持一致了，比如设计稿如果是750的横向分辨率，那么实际页面的device-width，以iphone6来说，也等于750，这样的话设计稿上标注的尺寸只要除以某一个值就能够转换为rem了。通过js设置viewport的方法如下：<br>
<br>
<pre>
    <code>
    var scale = 1 / devicePixelRatio;document.querySelector('meta[name="viewport"]').setAttribute('content','initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
    </code>
</pre>

这里给出完美视口<br>
`<meta name="viewport" content="initial-scale=1.0,width=device-width,user-scalable=0,maximum-scale=1.0"/>`

在移动端，低端无定制的需求，都可以用这个完美视口完成。<br>

### 把视觉稿中的px转换成rem

在html上增加一个font-size样式。之后页面中的元素，都可以用rem单位来设置。html上的font-size就是rem的基准像素。<br>
首先，目前视觉稿大小分为640，750以及，1125这三种。<br>
当前方案会把这3类视觉稿分成100份来看待（为了以后兼容vh，vw单位）。每一份被称为一个单位a。同时，1rem单位认定为10a。拿750的视觉稿举例：
<pre>
<code>
1a = 7.5px 
1rem = 75px 
</code>
</pre>
因此，对于视觉稿上的元素的尺寸换算，只需要原始px值除以rem基准px值即可。例如240px * 120px的元素，最后转换为3.2rem * 1.6rem。
手动设置rem基准值的方法<br>
输出如下css样式即可：<br>
<pre>
<code>
html {font-size: 60px!important;}
</code>
</pre>

在实际项目中，把与元素尺寸有关的css，如width,height,line-height,margin,padding等都以rem作为单位，这样页面在不同设备下就能保持一致的网页布局。举例来说，网页有一个.item类，设置了width为3.4rem，然后根据不同的页面尺寸用css3媒体查询去更改设置font-size值。<br>
各个屏幕的rem基准值：
<pre><code>
    html{font-size:10px;}
    @media screen and (min-width:321px) and (max-width:375px){html{font-size:11px;}}
    @media screen and (min-width:376px) and (max-width:414px){html{font-size:12px;}}
    @media screen and (min-width:415px) and (max-width:639px){html{font-size:15px;}}
    @media screen and (min-width:640px) and (max-width:719px){html{font-size:20px;}}
    @media screen and (min-width:720px) and (max-width:749px){html{font-size:22.5px;}}
    @media screen and (min-width:750px) and (max-width:799px){html{font-size:23.5px;}}
    @media screen and (min-width:800px){html{font-size:25px;}}
</code></pre>
(1). item类在所有设备下的width都是3.4rem，但在不同分辨率下的实际像素是不一样的，所以在有些分辨率下，width的界面效果不一定合适，有可能太宽，有可能太窄，这时候就要对width进行调整，那么就需要针对.item写媒介查询的代码，为该分辨率重新设计一个rem值。然而，这里有7种媒介查询的情况，css又有很多跟尺寸相关的属性，哪个属性在哪个分辨率范围不合适都是不定的，最后会导致要写很多的媒介查询才能适配所有设备，而且在写的时候rem都得根据某个分辨率html的font-size去算，这个计算可不见得每次都那么容易，比如40px / 23.5px，这个rem值口算不出来吧！由此可见这其中的麻烦有多少。 
<br>
(2). 以上代码中给出的7个范围下的font-size不一定是合适的，这7个范围也不一定合适，实际有可能不需要这么多，所以找出这些个范围，以及每个范围最合适的font-size也很麻烦 
<br>
(3). 设计稿都是以分辨率来标明尺寸的，前端在根据设计稿里各个元素的像素尺寸转换为rem时，该以哪个font-size为准呢？这需要去写才能知道。<br>
[详细关于rem的说明请参考腾讯ISUS] (http://isux.tencent.com/web-app-rem.html)
<br>

### 字体设置
使用无衬线字体
<pre>
<code>
body {
    font-family: "Helvetica Neue", Helvetica, STHeiTi, sans-serif;
}
</code>
</pre>
iOS 4.0+ 使用英文字体 Helvetica Neue，之前的iOS版本降级使用 Helvetica。中文字体设置为华文黑体STHeiTi。<br> 需补充说明，华文黑体并不存在iOS的字体库中(http://support.apple.com/kb/HT5878)， 但系统会自动将华文黑体 STHeiTi<br> 兼容命中系统默认中文字体黑体-简或黑体-繁<br>
Heiti SC Light 黑体-简 细体 （iOS 7后废弃）<br>
Heiti SC Medium 黑体-简 中黑<br>
Heiti TC Light 黑体-繁 细体<br>
Heiti TC Medium 黑体-繁 中黑<br>
原生Android下中文字体与英文字体都选择默认的无衬线字体<br>
4.0 之前版本英文字体原生 Android 使用的是 Droid Sans，中文字体原生 Android 会命中 Droid Sans Fallback<br>
4.0 之后中英文字体都会使用原生 Android 新的 Roboto 字体<br>
其他第三方 Android 系统也一致选择默认的无衬线字体<br>

#### [字体不使用rem的方法](https://github.com/amfe/lib-flexible)
JS动态计算（常见做法）<br>
根据不同屏幕宽度计算不同字号大小。<br>
1. 定基准值,设计稿是750宽度（2倍屏）,字体的大小是24px.
2. 计算指定宽度的字体大小。
var fontSize = width / 750 * 24 ;<br>
根据dpr设定 （比较好的做法）<br>
ps : 一般时初始化时设置为根元素html的attribute，<br>
   window.document.documentElement.setAttribute('dpr',window.devicePixelRatio)<br>
然后css这样写<br>
   [dpr=1] { font-size=16px;}<br>
  [dpr=2] {font-size=32px; }<br>
字体的大小不推荐用rem作为单位。所以对于字体的设置，仍旧使用px作为单位，并配合用data-dpr属性来区分不同dpr下的的大小。<br>
例如：<br>
div {width: 1rem;  height: 0.4rem;font-size: 12px; // 默认写上dpr为1的fontSize}<br>
[data-dpr="2"] div { font-size: 24px;}<br>
[data-dpr="3"] div {font-size: 36px;}<br>
手动配置dpr<br>
引入执行js之前，可以通过输出meta标签方式来手动设置dpr。语法如下：<br>
<meta name="flexible" content="initial-dpr=2,maximum-dpr=3" /><br>
其中initial-dpr会把dpr强制设置为给定的值，maximum-dpr会比较系统的dpr和给定的dpr，取最小值。注意：这两个参数只能选其一。<br>

### 使用Flexible实现H5页面的终端适配

Flex是Flexible Box的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。<br>
伸缩盒模型（flexbox）是一个新的盒子模型，主要优化了UI布局。作为实际布局的第一个CSS模块（浮动真的应该主要用来制作文本围绕图片这样的效果），它使很多任务容易多。Flexbox的功能主要包手：简单使用一个元素居中（包括水平垂直居中），可以让扩大和收缩元素来填充容器的可利用空间，可以改变源码顺序独立布局，以及还有其他的一些功能。<br>
flexbox的版本：<br>
<pre>
<code>
  display: -webkit-box;  /* 老版本语法: Safari,  iOS, Android browser, older WebKit browsers.  */
  display: -moz-box;    /* 老版本语法: Firefox (buggy) */
  display: -ms-flexbox;  /* 混合版本语法: IE 10 */
  display: -webkit-flex;  /* 新版本语法： Chrome 21+ */
  display: flex;       /* 新版本语法： Opera 12.1, Firefox 22+ */
  <br>
  /*垂直居中*/   
  /*老版本语法*/
  -webkit-box-align: center;
  -moz-box-align: center;
  /*混合版本语法*/
  -ms-flex-align: center;
  /*新版本语法*/
  -webkit-align-items: center;
  align-items: center;

  /*水平居中*/
  /*老版本语法*/
  -webkit-box-pack: center;
  -moz-box-pack: center;
  /*混合版本语法*/
  -ms-flex-pack: center;
  /*新版本语法*/
  -webkit-justify-content: center;
  justify-content: center;
<br><br>
/*实现文本垂直居中*/
h1 {
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;

  -webkit-box-align: center;
  -moz-box-align: center;
  -ms-flex-align: center;
  -webkit-align-items: center;
  align-items: center;//最新版本

  height: 10rem;
}
</code></pre>
横向的弹性布局<br>

        <div class="flex-box">
          <div class="flex"></div>
          <div class="flex"></div>
          <div class="flex"></div>
        </div>

横向弹性布局css样式
<pre>
<code>
.flex-box{display:-webkit-box;display:-ms-flexbox;width: 100%;-webkit-box-sizing: border-box;} 
.flex{-webkit-box-flex:1;-ms-flex:1;width: 0;}
.vc{-webkit-box-align: center;}
.hc{-webkit-box-pack: center;}
<code>
</pre>
纵向的弹性布局<br>

        <div class="ui-row-flex">
            <div class="ui-col ui-col-3"></div>
            <div class="ui-col ui-col-2"></div>
        </div>

纵向弹性布局css样式
<pre>
<code>
.ui-row-flex {display: -webkit-box;width: 100%;-webkit-box-sizing: border-box; box-sizing: border-box;}
.ui-row-flex-ver {-webkit-box-orient: vertical;}
.ui-row-flex .ui-col { -webkit-box-flex: 1;}
.ui-row-flex-ver .ui-col {width: 100%;height: 0;}
.ui-row-flex .ui-col-2 {-webkit-box-flex: 2;}
.ui-row-flex .ui-col-3 {-webkit-box-flex: 3;}
</code>
</pre>
[一个完整的Flexbox指南](http://www.w3cplus.com/css3/a-guide-to-flexbox-new.html)<br>
[W3C Flexbox API](http://www.w3.org/html/ig/zh/css-flex-1/)<br>
[CSS3弹性盒模型生成器](http://the-echoplex.net/flexyboxes/)<br>
[CSS3 flexbox弹性布局实例](http://caibaojian.com/flexbox-example.html#t3)<br>
[Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)<br>
[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)<br>
#### 腾讯淘宝各大前端团队对flexbox的开发详解<br>
[淘宝前端团队（FED）移动端开发小记 – Flexbox]<br>
(http://taobaofed.org/blog/2015/11/11/flexbox-in-mobile-web/)<br>
[腾讯全端 AlloyTeam 团队 -浅谈flexbox的弹性盒子布局]<br>
(http://www.alloyteam.com/2015/05/xi-shuo-flexbox-dan-xing-he-zi-bu-ju/)<br>

###  基于display:table的CSS布局

    <ul class="table-equal demo">
        <li>手机</li>
        <li>联系人</li>
        <li>信息</li>
        <li>主屏</li>
    </ul>

<pre><code>  
  .table-equal {
  display: table;
  table-layout: fixed;
  width: 100%;
}
.table-equal li {
  display: table-cell;
} 
</code>
</pre>
### CSS 模块
[详解:](http://glenmaddern.com/articles/css-modules)<br>
#### 像局部一样无需考虑全局冲突
在 CSS 模块中,请注意所有的类都是相互独立的，这里并不存在一个“ 基类 ”然后其它的类集成并“ 覆盖 ”它的属性这种情况。在 CSS 模块中 每一个类都必须包含这个元素需要的所有样式，每一个文件被编译成独立的文件。这样我们就只需要使用通用简单的类选择器名字就好了。我们不需要担心它会污染全局的变量。下面我就我们编写一个拥有四个状态的按钮来说明这个功能。<br>
如：按钮模块化<br>
按主流设计风格，移动端操作按钮大概可以分出来以上类型的按钮，<br>
根据网页重构常用思想：具有超强复用性和扩展性的组件，可以为项目节省大量重构时间和开发成本。<br>
那么 操作按钮 的重构也遵循这样的一个最基本的原则——高复用性和强扩展型。<br>

那么这些不同类型的按钮都是在一个原生的按钮基础上衍生出来的，那就是btn。<br>
代码如下：<br>

/* =============================
   名称：btn
   使用说明：操作按钮
            1.标签可使用a或者是button；
            2.标签为button时，type可设置为button、submit、reset；
            3.点击态给标签追加class="active"；
            4.按钮默认为直角，圆角按钮可在外围标签追加class="btn-corner"；
            5.按钮默认无阴影，带阴影按钮可在外围标签追加class="btn-shadow"；
            6.按钮默认块级元素，内联按钮可在外围标签追加class="btn-inline"。
   ============================= */
/* btn 基类 */
<pre>
<code>
.btn{display:block;width:100%;position:relative;margin:.2em 0;padding:.86em 1em;line-height:16px;text-align:center;vertical-align:middle;text-overflow:ellipsis;overflow:hidden;white-space:nowrap;-webkit-box-sizing:border-box;box-sizing:border-box;}
.btn .icon{width:20px;height:20px;display:inline-block;background:#666666;vertical-align:middle;margin:-3px 5px 0 5px;border-radius:20px;-webkit-border-radius:20px;}
.btn .icon img{width:100%;height:100%;}
.btn-icon-left .icon{float:left;margin:0;}
.btn-icon-right .icon{float:right;margin:0;}
.btn-notxt .btn{text-indent:-90em;}
/* corner-all 圆角按钮 */
.btn-corner .btn{border-radius:4px;-webkit-border-radius:4px;}
/* btn-shadow 带阴影按钮 */<br>
.btn-shadow .btn{box-shadow:0 3px 3px rgba(0,0,0,0.3);}
/* btn-inline 内联按钮 */
.btn-inline .btn{display:inline-block;width:auto;}
/* mini-btn 微型按钮 */
.btn-mini .btn{padding:.5em 1em;font-size:0.8em;}
/* btn-green 绿色按钮扩展类 */
.btn-green{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #36CC36), color-stop(1, #19A819));background-image:linear-gradient(to bottom, #36CC36, #19A819);background-color:#19A819;color:#FFFFFF;}<br>
.btn-green.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #19A819), color-stop(1, #36CC36));background-image:linear-gradient(to bottom, #19A819, #36CC36);}
/* btn-red 红色按钮扩展类 */
.btn-red{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #C50C0C), color-stop(1, #AE0B0B));background-image:linear-gradient(to bottom, #C50C0C, #AE0B0B);background-color:#BA0C0C;color:#FFFFFF;}<br>
.btn-red.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #AE0B0B), color-stop(1, #C50C0C));background-image:linear-gradient(to bottom, #AE0B0B, #C50C0C);}
/* btn-blue 蓝色按钮扩展类 */
.btn-blue{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #425ebe), color-stop(1, #2f4691));background-image:linear-gradient(to bottom, #425ebe, #2f4691);background-color:#BA0C0C;color:#FFFFFF;}<br>
.btn-blue.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #425ebe), color-stop(1, #425ebe));background-image:linear-gradient(to bottom, #425ebe), color-stop, #425ebe);}
/* btn-red-s 红色小按钮扩展类 */
.btn-red-s, .btn-red-s.active{height:30px;min-width:80px;max-width:100px;line-height:30px;background-color:#E04241;border:1px solid #CC0000;box-shadow:0 0 3px rgba(0,0,0,0.7);text-shadow:0 -1px 0 #621D1D;}
/* btn-gray 灰色按钮扩展类 */
.btn-gray{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #F8F8F8), color-stop(1, #E1E1E1));background-image:linear-gradient(to bottom, #F8F8F8, #E1E1E1);background-color:#EBEBEB;color:#555555;}<br>
.btn-gray.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #E1E1E1), color-stop(1, #F8F8F8));background-image:linear-gradient(to bottom, #E1E1E1, #F8F8F8);}
/* btn-disable 禁用按钮扩展类 */
.btn-disable, .btn-disable.active{background-image:none;background-color:#E1E1E1;color:#919191}<br>
/* btn-white 白色按钮扩展类 */
.btn-white{background-color:#FFFFFF;color:#333333;}
.btn-white.active{background-color:#FFFFFF;color:#333333;}
/* btn-white-s 白色小按钮扩展类 */
.btn-white-s{background-color:#FFFFFF;border:1px solid #CEC9B4;color:#333333;}
.btn-white-s.active{background-color:#FFFFFF;border:1px solid #CEC9B4;color:#333333;}
/* btn-del 删除按钮扩展类 */
.btn-del{width:40px;height:23px;line-height:23px;font-size:12px;background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #FF424F), color-stop(1, #EC1214));background-image:linear-gradient(to bottom, #FF424F, #EC1214);background-color:#AC000C;border:1px solid #AC000C;border-radius:3px;}
.btn-del.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #EC1214), color-stop(1, #FF424F));background-image:linear-gradient(to bottom, #EC1214, #FF424F);}
</code>
</pre>
所有按钮除了都是基于btn开发进行扩展之外，还有<br>
点击态：<br>
  如果使用系统默认点击态，那么btn再添加一个属性即可：-webkit-tap-highlight-color:rgba(0,0,0,0.1);/* 设置点击链接或按钮时遮罩层为10%透明 */；<br>
  ​如果自定义点击态样式，追加active；<br>
不可点击态：追加disabled。<br>
庆幸的是，类似.btn-white.active（.A.B.C）的连接（多类）class书写形式，移动端是支持的非常好的，所以完全可以大胆使用。
按钮形态虽多，但在正式项目中，很少会同时出现N多种形态。<br>
所以操作按钮算是移动端网页中最容易开发的页面元素了。<br>
之所以一一例举出来，只是能够为大家整理出来一些移动端可能会遇到的操作按钮提供参考，当然也很是实用的哦。<br>
按钮开发虽易，维护不易，且行且珍惜。<br>
