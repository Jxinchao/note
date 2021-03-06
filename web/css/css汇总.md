## 王冲rem计算

```javascript
!(function (win, doc) {
  function setFontSize() {
    
    var winWidth = window.innerWidth;
    if (winWidth > 750) {
      return;
    }
    doc.documentElement.style.fontSize = (winWidth / 750) * 100 + 'px';
  }

  var evt = 'onorientationchange' in win ? 'orientationchange' : 'resize';

  var timer = null;

  win.addEventListener(evt, function () {
    clearTimeout(timer);

    timer = setTimeout(setFontSize, 300);
  }, false);

  win.addEventListener("pageshow", function (e) {
    if (e.persisted) {
      clearTimeout(timer);

      timer = setTimeout(setFontSize, 300);
    }
  }, false);

  setFontSize();

}(window, document));
```



## 郑吉芳的计算

```javascript
document.documentElement.style.fontSize = document.documentElement.clientWidth / 7.5 + 'px';
window.onresize = function() {
	document.documentElement.style.fontSize = document.documentElement.clientWidth / 7.5 + 'px';
};
```

## 李游rem计算

```javascript
(function(win){
   var ratio,scaleValue,renderTime,
         document=window.document,
         docElem=document.documentElement,
         vpm=document.querySelector('meta[name="viewport"]');
   if(vpm){
      var tempArray=vpm.getAttribute("content").match(/initial\-scale=(["']?)([\d\.]+)\1?/);
      if(tempArray){
         scaleValue=parseFloat(tempArray[2]);
         ratio=parseInt(1/scaleValue);
      }
   }else{
      vpm=document.createElement("meta");
      vpm.setAttribute("name","viewport");
      vpm.setAttribute("content","width=device-width,initial-scale=1,user-scalable=no,minimal-ui");
      docElem.firstElementChild.appendChild(vpm);
   }

   win.addEventListener("resize",function(){
      clearTimeout(renderTime);
      renderTime=setTimeout(initPage,300);
   },false);

   win.addEventListener("pageshow", function(e) {
      e.persisted && (clearTimeout(renderTime),renderTime=setTimeout(initPage,300));
   }, false);

   "complete"===document.readyState?document.body.style.fontSize =12*ratio+"px":
         document.addEventListener("DOMContentLoaded",function(){
            document.body.style.fontSize =12*ratio+"px";
         },false);
   initPage();
   function initPage(){
      var htmlWidth=docElem.getBoundingClientRect().width;
      if(htmlWidth>640) htmlWidth=640;
      htmlWidth/ratio>640 && (htmlWidth=640*ratio);
      win.rem=htmlWidth/16;
      docElem.style.fontSize=win.rem+"px";
   }
})(window);
```

## 微信禁止缩放字体大小

```javascript
// 禁止微信调整字体大小
(function() {    
    if (typeof WeixinJSBridge == "object" && typeof WeixinJSBridge.invoke == "function") {
            handleFontSize();
    } else {        
    if (document.addEventListener) {
        document.addEventListener("WeixinJSBridgeReady", handleFontSize, false);
    } else if (document.attachEvent) {
        document.attachEvent("WeixinJSBridgeReady", handleFontSize);
        document.attachEvent("onWeixinJSBridgeReady", handleFontSize);  }
    }    
    function handleFontSize() {        
       // 设置网页字体为默认大小
       WeixinJSBridge.invoke('setFontSizeCallback', { 'fontSize' : 0 });        
       // 重写设置网页字体大小的事件
       WeixinJSBridge.on('menu:setfont', function() {
           WeixinJSBridge.invoke('setFontSizeCallback', { 'fontSize' : 0 });
       });
    }
})();
```

## 媒体查询

/* 超小屏幕（手机，小于 768px） */

/* 没有任何媒体查询相关的代码，因为这在 Bootstrap 中是默认的（还记得 Bootstrap 是移动设备优先的吗？） */

/* 小屏幕（平板，大于等于 768px） */

```css
@media (min-width: @screen-sm-min) { ... }
```

/* 中等屏幕（桌面显示器，大于等于 992px） */

```css
@media (min-width: @screen-md-min) { ... }
```

/* 大屏幕（大桌面显示器，大于等于 1200px） */

```css
@media (min-width: @screen-lg-min) { ... }
```

## 移动端兼容

 a,button,input{-webkit-tap-highlight-color:rgba(255,0,0,0);}  去除A标签的默认蓝色框



1. IOS移动端click事件300ms的延迟响应

   ​	移动设备上的web网页是有300ms延迟的，玩玩会造成按钮点击延迟甚至是点击失效。这是由于区分单击事件和双击屏幕缩放的历史原因造成的,

   ​	2007年苹果发布首款iphone上IOS系统搭载的safari为了将适用于PC端上大屏幕的网页能比较好的展示在手机端上，使用了双击缩放(double tap to zoom)的方案，比如你在手机上用浏览器打开一个PC上的网页，你可能在看到页面内容虽然可以撑满整个屏幕，但是字体、图片都很小看不清，此时可以快速双击屏幕上的某一部分，你就能看清该部分放大后的内容，再次双击后能回到原始状态。

   双击缩放是指用手指在屏幕上快速点击两次，iOS 自带的 Safari 浏览器会将网页缩放至原始比例。

   ​	原因就出在浏览器需要如何判断快速点击上，当用户在屏幕上单击某一个元素时候，例如跳转链接<a href="#"></a>，此处浏览器会先捕获该次单击，但浏览器不能决定用户是单纯要点击链接还是要双击该部分区域进行缩放操作，所以，捕获第一次单击后，浏览器会先Hold一段时间t，如果在t时间区间里用户未进行下一次点击，则浏览器会做单击跳转链接的处理，如果t时间里用户进行了第二次单击操作，则浏览器会禁止跳转，转而进行对该部分区域页面的缩放操作。那么这个时间区间t有多少呢？在IOS safari下，大概为300毫秒。这就是延迟的由来。造成的后果用户纯粹单击页面，页面需要过一段时间才响应，给用户慢体验感觉，对于web开发者来说是，页面js捕获click事件的回调函数处理，需要300ms后才生效，也就间接导致影响其他业务逻辑的处理。

   解决方案：

   fastclick可以解决在手机上点击事件的300ms延迟

   zepto的touch模块，tap事件也是为了解决在click的延迟问题

   触摸事件的响应顺序为 touchstart --> touchmove --> touchend --> click,也可以通过绑定ontouchstart事件，加快对事件的响应，解决300ms延迟问题

2. 一些情况下对非可点击元素如(label,span)监听click事件，ios下不会触发，css增加cursor:pointer就搞定了。

3. 三星手机遮罩层下的input、select、a等元素可以被点击和focus(点击穿透)

   问题发现于三星手机，这个在特定需求下才会有，因此如果没有类似问题的可以不看。首先需求是浮层操作，在三星上被遮罩的元素依然可以获取focus、click、change)，有两种解决方案，

- 是通过层显示以后加入对应的class名控制，截断显示层下方可获取焦点元素的事件获取
- 是通过将可获取焦点元素加入的disabled属性，也可以利用属性加dom锁定的方式（disabled的一种变换方式）

 

4. h5底部输入框被键盘遮挡问题

   h5页面有个很蛋疼的问题就是，当输入框在最底部，点击软键盘后输入框会被遮挡。可采用如下方式解决

   复制代码

   ```javascript
   var oHeight = $(document).height(); //浏览器当前的高度
      $(window).resize(function(){
           if($(document).height() < oHeight){
           	$("#footer").css("position","static");
       	}else{
           	$("#footer").css("position","absolute");
           }
      });
   ```

   

关于Web移动端Fixed布局的解决方案，这篇文章也不错

<http://efe.baidu.com/blog/mobile-fixed-layout/>

复制代码

5. 不让 Android 手机识别邮箱

<meta content="email=no" name="format-detection" />

6. 禁止 iOS 识别长串数字为电话

<meta content="telephone=no" name="format-detection" />

7. 禁止 iOS 弹出各种操作窗口

   -webkit-touch-callout:none

8. 消除 transition 闪屏

   -webkit-transform-style: preserve-3d;     /*设置内嵌的元素在 3D 空间如何呈现：保留 3D*/

   -webkit-backface-visibility: hidden;      /*(设置进行转换的元素的背面在面对用户时是否可见：隐藏)*/

9. iOS 系统中文输入法输入英文时，字母之间可能会出现一个六分之一空格

   可以通过正则去掉:      

   this.value = this.value.replace(/\u2006/g, '');

10. 禁止ios和android用户选中文字

    -webkit-user-select:none

11. 在ios和andriod中,audio元素和video元素在无法自动播放

    应对方案：触屏即播

```javascript
$('html').one('touchstart',function(){
    audio.play()
})
```



12.ios下取消input在输入的时候英文首字母的默认大写

​	<input autocapitalize="off" autocorrect="off" />

13.android下取消输入语音按钮

​	input::-webkit-input-speech-button {display: none}

14. CSS动画页面闪白,动画卡顿

    复制代码

    解决方法:

- 尽可能地使用合成属性transform和opacity来设计CSS3动画，不使用position的left和top来定位

- 开启硬件加速

    -webkit-transform: translate3d(0, 0, 0);

  ​     -moz-transform: translate3d(0, 0, 0);

  ​      -ms-transform: translate3d(0, 0, 0);

  ​          transform: translate3d(0, 0, 0);

复制代码

16. fixed定位缺陷

    ios下fixed元素容易定位出错，软键盘弹出时，影响fixed元素定位

    android下fixed表现要比iOS更好，软键盘弹出时，不会影响fixed元素定位

    ios4下不支持position:fixed

    解决方案： 可用iScroll插件解决这个问题

17. 阻止旋转屏幕时自动调整字体大小

    html, body, form, fieldset, p, div, h1, h2, h3, h4, h5, h6 {-webkit-text-size-adjust:none;}

18. Input 的placeholder会出现文本位置偏上的情况

    input 的placeholder会出现文本位置偏上的情况：PC端设置line-height等于height能够对齐，而移动端仍然是偏上，解决是设置line-height：normal

19. 往返缓存问题

    点击浏览器的回退，有时候不会自动执行js，特别是在mobilesafari中。这与往返缓存(bfcache)有关系。

    解决方法 ：window.onunload = function(){};

20. calc的兼容性处理

    CSS3中的calc变量在iOS6浏览器中必须加-webkit-前缀，目前的FF浏览器已经无需-moz-前缀。

    Android浏览器目前仍然不支持calc，所以要在之前增加一个保守尺寸：

```css
div { 
    width: 95%; 
    width: -webkit-calc(100% - 50px); 
    width: calc(100% - 50px); 
}
```

21. iOS6下伪类:hover

    除了<a>之外的元素无效；在Android下则有效。类似

    div#topFloatBar_l:hover #topFloatBar_menu { display:block; }

    这样的导航显示在iOS6点击没有点击效果，只能通过增加点击侦听器给元素增减class来控制子元素。

22. 在移动端修改难看的点击的高亮效果，iOS和安卓下都有效：

		{-webkit-tap-highlight-color:rgba(0,0,0,0);}
		
		不过这个方法在现在的安卓浏览器下，只能去掉那个橙色的背景色，点击产生的高亮边框还是没有去掉，有待解决！
		
		一个CSS3的属性，加上后，所关联的元素的事件监听都会失效，等于让元素变得“看得见，点不着”。IE到11才开始支持，其他浏览器的当前版本基本都支持。详细介绍见这里：https://developer.mozilla.org/zh-CN/docs/Web/CSS/pointer-events
		
		pointer-events: none;

23. Zepto点透的解决方案

    zepto的tap是通过兼听绑定在document上的touch事件来完成tap事件的模拟的,及tap事件是冒泡到document上触发的,在点击完成时的tap事件(touchstart\touchend)需要冒泡到document上才会触发，而在冒泡到document之前，用户手的接触屏幕(touchstart)和离开屏幕(touchend)是会触发click事件的,因为click事件有延迟触发(这就是为什么移动端不用click而用tap的原因)(大概是300ms,为了实现safari的双击事件的设计)，所以在执行完tap事件之后，弹出来的选择组件马上就隐藏了，此时click事件还在延迟的300ms之中，当300ms到来的时候，click到的其实不是完成而是隐藏之后的下方的元素，如果正下方的元素绑定的有click事件此时便会触发，如果没有绑定click事件的话就当没click，但是正下方的是input输入框(或者select选择框或者单选复选框)，点击默认聚焦而弹出输入键盘，也就出现了上面的点透现象。

    引入fastclick.js，在页面中加入如下js代码

```javascript
window.addEventListener( "load", function() {

    FastClick.attach( document.body );

}, false );
```

​	或者有zepto或者jQuery的js里面加上

```javascript
$(function() {
    FastClick.attach(document.body);
});

```

当然require的话就这样：

- var FastClick = require('fastclick');
- FastClick.attach(document.body, options);

方案二：用touchend代替tap事件并阻止掉touchend的默认行为preventDefault()

```javascript
$("#cbFinish").on("touchend", function (event) {
     //很多处理比如隐藏什么的
     event.preventDefault();

});

```

方案三：延迟一定的时间(300ms+)来处理事件

```javascript
$("#cbFinish").on("tap", function (event) {
     setTimeout(function(){
     //很多处理比如隐藏什么的
     },320);
});    
```

24. user-select:none;造成iPhone5上输入框的光标不显示，而光标不显示造成无法连续输入。

- 外观

​       A、页面高度渲染错误

​    	在各移动端浏览器中经常会出现这种页面高度100%的渲染错误，页面低端和系统自带的导航条重合了，高度的不正确我们需要重置修正它，通过javascript代码重置掉：

```javascript
document.documentElement.style.height = window.innerHeight + 'px';
```

​      B、叠加区高亮

​       在部分android机型中点击页面某一块区域可能会出现如图所示的黄色框秒闪，这是部分机型系统自身的默认定制样式，给该元素一个CSS样式重置掉：

-webkit-tap-highlight-color:rgba(0,0,0,0);

- 行为

​    A、事件无法被触发

​    在部分android机型的微信环境中会出现事件无法触发、表单无法输入的情况，我们针对需要输入或者触发事件的元素设置样式：-webkit-transform: translate3d(0,0,0) ，不过新版本的微信已经直接修复了该问题。

​    B、:active 效果不兼容

​    在android 4.0版本以下CSS :active伪状态效果无法兼容，我们给该元素的touch系列的事件（touchstart/touchend/touchmove）绑定一个空匿名方法:

```javascript
var element=document.getElementsById(”btnShare”);

element.addEventListener(‘touchstart’,function(){},false);
```

- 应用

​    A、浏览器崩溃

    ```javascript
var act = function(){
	window.removeEventListener('devicemotion',act);
};
window.addEventListener('devicemotion',act,false);
    ```

解绑函数写在了事件处理中导致小米手机中的微信崩溃，那么我们不要将解绑时间写在事件处理中即可。

​    B、预加载、自动播放无效

​    如上表所示，经过简单的测试发现预加载、自动播放的有效性受操作系统、浏览器（webview）、版本等的影响，苹果官方规定必须由用户手动触发才会载入音频，那么我们捕捉一次用户输入后，让音频加载实现预加载:

```javascript
//play and pause it once

document.addEventListener('touchstart', function () {

 document.getElementsByTagName('audio')[0].play();

 document.getElementsByTagName('audio')[0].pause();

});
```

​    C、无法同时播放多音频

​    在android设备中,播放后一音频会打断前一音频，而不会同步播放,这个是目前系统资深决定的，我们只有采取优雅降权的方法让android选择不一样风格的音频前后切换播放而不是同时播放，达到与预期接近的音频效果。

​    D、不支持局部滚动

​    在android 4.0版本以下在body(html)元素之外的元素 overflow:scroll 样式设置滚动条无效，这里有两种解决方案:

   	1. 巧用布局直接设置样式滚动条在body(html)上，其他元素“错觉滚动”。

		2. 利用iscroll、自写js控制translate、scrollTop模拟

- 系统/硬件

  ​    A、怪异悬浮的表单

  ​    在部分android 机型中的输入框可能会出现如图怪异的多余的浮出表单，经过观察与测试发现只有input:password类型的输入框存在，那么我们只要使用input:text类型的输入框并通过样式-webkit-text-security: disc; 隐藏输入密码从而解决。

  ​    B、错误出现滚动条

  ​    在游戏内嵌页中出现了不应该出现的滚动条，而且内容并没有超出内容区宽度，经过测试overflow:hidden 无效，通过一系列尝试使用古老的 <body scroll="no"> 写法解决，多尝试一下不同的写法和属性会有不一样的惊喜哦！

  ​    C、链接打开系统浏览器    

  ​    在游戏内webview的部分android机型中可能会出现点击链接调用系统浏览器的情况，这是一个非常不好的体验。那么我们尝试给这个元素添加 target="_blank"' 属性有可能解决，如果还不能解决那么需要修改IOS或android原生系统函数了。

  ​    D、Flex box 不兼容   

  ​    在游戏内嵌webview中碰到Flex box布局不兼容的情况，图中所示下面部分的导航错位了，虽然之前有仔细查看过Flex box的兼容性，但是在游戏内嵌页中无法确定其调用的系统浏览器版本及兼容，所以导致错误，所以我们写完整历史版本呢的3种Flex box 解决。那么我们思考在写页面过程中还是本着保守稳定的方式书写样式可以减少不不要的麻烦。

## css引入外部字体

```css
@font-face{
    font-family:abc;
    src: url("../font/abc.ttf");
}
a{
    font-family:abc;
}
```

## Sass、LESS 和 Stylus区别总结

1. css预处理器是什么？

   CSS 预处理器是一种语言用来为 CSS 增加一些编程的的特性，无需考虑浏览器的兼容性问题，例如你可以在 CSS 中使用变量、简单的程序逻辑、函数等等在编程语言中的一些基本技巧，可以让CSS 更见简洁，适应性更强，代码更直观等诸多好处。

2. 基本语法区别

   在使用 CSS 预处理器之前最重要的是理解语法，幸运的是基本上大多数预处理器的语法跟 CSS 都差不多。

   首先 Sass 和 Less 都使用的是标准的 CSS 语法，因此如果可以很方便的将已有的 CSS 代码转为预处理器代码，默认 Sass 使用 .sass 扩展名，而 Less 使用 .less 扩展名。

   ```css
   h1 {   color: #0982C1; }
   ```

   /* style.scss or style.less */ 

   这是一个再普通不过的，不过 Sass 同时也支持老的语法，就是不包含花括号和分号的方式：

   /* style.sass */

   ```sass
    h1   color: #0982c1
   ```

   而 Stylus 支持的语法要更多样性一点，它默认使用 .styl 的文件扩展名，下面是 Stylus 支持的语法：

   /* style.styl */ h1 {   color: #0982C1; }  /* omit brackets */ h1   color: #0982C1;  /* omit colons and semi-colons */ h1   color #0982C1

   可以在同一个样式单中使用不同的变量，例如下面的写法也不会报错：

   h1 {   color #0982c1 } h2   font-size: 1.2em

3. 变量

   - sass

     Sass让人们受益的一个重要特性就是它为css引入了变量。你可以把反复使用的css属性值 定义成变量，然后通过变量名来引用它们，而无需重复书写这一属性值。

     ​     sass变量必须是以$开头的，然后变量和值之间使用冒号（：）隔开，和css属性是一样的，例如：

     ```sass
     $maincolor : #092873;
     $siteWidth : 1024px;
     $borderStyle : dotted;
     body {
       color: $maincolor;
       border: 1px $borderStyle $mainColor;
       max-width: $siteWidth;
     }
     ```

   - Less css:

     在less文件中，当一个值需要反复使用时，可以通过@符号定义变量。已经被赋值的变量以及其他的常量（如像素、颜色等）都可以参与运算。

      Less css中变量都是用@开头的，其余与sass都是一样的，例如：

     ```less
     @maincolor : #092873;
     @siteWidth : 1024px;
     @borderStyle : dotted;
     body {
       color: @maincolor;
       border: 1px @borderStyle @mainColor;
       max-width: @siteWidth;
     }
     ```

   - Stylus:

     stylus对变量是没有任何设定的，可以是以$开头，或者任何的字符，而且与变量之间可以用冒号，空格隔开， 但是在stylus中不能用@开头，例如：   

     ```styl
     maincolor = #092873
     siteWidth = 1024px
     borderStyle = dotted
     body 
       color maincolor
       border 1px borderStyle mainColor
       max-width siteWidth
     ```

   以上三种写法都如同一下这种css：

   ```css
   body {   
       color: #092873;   
       border: 1px dotted #092873;   
       max-width: 1024px; 
   }
   ```

4. 嵌套

   如果我们需要在CSS中相同的 parent 引用多个元素，这将是非常乏味的，你需要一遍又一遍地写 parent。例如：

   ```css
   div {   margin: 10px; } 
   div nav {   height: 25px; } 
   div nav a {   color: #0982C1; } 
   div nav a:hover {   text-decoration: underline; }
   ```

   如果用 CSS 预处理器，就可以少写很多单词，而且父子节点关系一目了然，并且sass，Less，stylus都支持下面这样的写法，且都是相同的：

   ```scss
   //scss style //----------------------------------- 
   nav { 
       ul { 
          margin: 0; 
          padding: 0; 
       } 
       li { 
          display: inline-block; 
       } 
       a { 
          display: block; 
          padding: 6px 12px; 
          text-decoration: none; 
       } 
   }
   //css style //----------------------------------- 
   nav ul { 
       margin: 0; 
       padding: 0; 
       list-style: none; 
   } 
   nav li { 
       display: inline-block; 
   } 
   nav a { 
       display: block; 
       padding: 6px 12px; 
       text-decoration: none; 
   }
   ```

   这样很直观~~~

5. 运算符

   在 CSS 预处理器中还是可以进行样式的计算如下：

   ```less
   body {   
   	margin: (14px/2);   
   	top: 50px + 100px;   
   	right: 80 * 10%; 
   }
   ```

   在sass,less与styuls中都是可以这么写的；

6. 颜色函数

   CSS 预处理器一般都会内置一些颜色处理函数用来对颜色值进行处理，例如加亮、变暗、颜色梯度等。

   - sass的颜色处理函数

     ```sass
     lighten($color, 10%); 
     darken($color, 10%);  
     saturate($color, 10%);   
     desaturate($color, 10%);
     grayscale($color);  
     complement($color); 
     invert($color); 
     mix($color1, $color2, 50%); 
     ```

     实例：

     ```sass
     $color: #0982C1;
     h1 {
       background: $color;
       border: 3px solid darken($color, 50%);
     }
     ```

   - Less css颜色处理函数：

     ```less
     lighten(@color, 10%); 
     darken(@color, 10%);  
     saturate(@color, 10%);  
     desaturate(@color, 10%); 
     spin(@color, 10); 
     spin(@color, -10); 
     mix(@color1, @color2);
     ```

     示例如下：

     ```less
     @color: #0982C1; 
     h1 {   
         background: @color;   
         border: 3px solid darken(@color, 50%); 
     }
     ```

   - Stylus函数处理函数：

     ```styl
     lighten(color, 10%); 
     darken(color, 10%);  
     saturate(color, 10%);  
     desaturate(color, 10%); 
     ```

     示例如下;

     ```styl
     color = #0982C1  
     h1   
     	background color   
     	border 3px solid darken(color, 50%)
     ```

7. 导入 (Import)

   很多 CSS 开发者对导入的做法都不太感冒，因为它需要多次的 HTTP 请求。但是在 CSS 预处理器中的导入操作则不同，它只是在语义上包含了不同的文件，但最终结果是一个单一的 CSS 文件，如果你是通过 @ import “file.css”; 导入 CSS 文件，那效果跟普通的 CSS 导入一样。

   注意：导入文件中定义的混入、变量等信息也将会被引入到主样式文件中，因此需要避免它们互相冲突。 

   例如： 

   ```css
   
   /* file.{type} 1.css*/
   body {
     background: #000;
   }
    
    /* file.{type} 2.css*/
   @ import "1.css";
   @ import "file.{type}";
   
   p {
     background: #092873;
   }
   
   /* file.{type} 1.css 最终生成的 CSS：*/
   @ import "1.css";
   body {
     background: #000;
   }
   p {
     background: #092873;
   }
   ```

8. 继承

   当我们需要为多个元素定义相同样式的时候，我们可以考虑使用继承的做法

   - sass

     sass可通过@extend来实现代码组合声明，使代码更加优越简洁。

     ```scss
     .message {
       border: 1px solid #ccc;
       padding: 10px;
       color: #333;
     }
     .success {
       @extend .message;
       border-color: green;
     }
     .error {
       @extend .message;
       border-color: red;
     }
     .warning {
       @extend .message;
       border-color: yellow;
     }
     ```

   - less

     这方面 Less 表现的稍微弱一些，更像是混入写法

     ```less
     .message {
       border: 1px solid #ccc;
       padding: 10px;
       color: #333;
     }
     .success {
       .message;
       border-color: green;
     }
     .error {
       .message;
       border-color: red;
     }
     .warning {
       .message;
       border-color: yellow;
     }
     
     ```

     ```css
     .message, .success, .error, .warning {
       border: 1px solid #cccccc;
       padding: 10px;
       color: #333;
     }
     .success {
       border-color: green;
     }
     .error {
       border-color: red;
     }
     .warning {
       border-color: yellow;
     }
     ```

     .message的样式将会被插入到相应的你想要继承的选择器中，但需要注意的是优先级的问题。

9. Mixins(混入)

   Mixins 有点像是函数或者是宏，当某段 CSS 经常需要在多个元素中使用时，可以为这些共用的 CSS 定义一个 Mixin，然后只需要在需要引用这些 CSS 地方调用该 Mixin 即可。

   - Sass 的混入语法：

     sass中可用mixin定义一些代码片段，且可传参数，方便日后根据需求调用。比如说处理css3浏览器前缀：

     ```scss
     @mixin error($borderWidth: 2px) {
       border: $borderWidth solid #F00;
       color: #F00;
     }
     .generic-error {
       padding: 20px;
       margin: 4px;
       @ include error(); //这里调用默认 border: 2px solid #F00;
     }
     .login-error {
       left: 12px;
       position: absolute;
       top: 20px;
       @ include error(5px); //这里调用 border:5px solid #F00;
     }
     ```

   - Less CSS 的混入语法： 

     less也支持带参数的混合以及有默认参数值的混合，如下面的例子所示：

     ```less
     .error(@borderWidth: 2px) {
       border: @borderWidth solid #F00;
       color: #F00;
     }
     .generic-error {
       padding: 20px;
       margin: 4px;
       .error(); //这里调用默认 border: 2px solid #F00;
     }
     .login-error {
       left: 12px;
       position: absolute;
       top: 20px;
       .error(5px); //这里调用 border:5px solid #F00;
     }
     ```

   - Stylus 的混入语法：

     ```styl
     error(borderWidth= 2px) {
       border: borderWidth solid #F00;
       color: #F00;
     }
     .generic-error {
       padding: 20px;
       margin: 4px;
       error(); 
     }
     .login-error {
       left: 12px;
       position: absolute;
       top: 20px;
       error(5px); 
     }
     ```

     最终都是这样呈现

     ```css
     .generic-error {
       padding: 20px;
       margin: 4px;
       border: 2px solid #f00;
       color: #f00;
     }
     .login-error {
       left: 12px;
       position: absolute;
       top: 20px;
       border: 5px solid #f00;
       color: #f00;
     }
     ```

10. 3D文本

    要生成具有 3D 效果的文本可以使用 text-shadows ，唯一的问题就是当要修改颜色的时候就非常的麻烦，而通过 mixin 和颜色函数可以很轻松的实现：

    - sass

      ```scss
      @mixin text3d($color) {
        color: $color;
        text-shadow: 1px 1px 0px darken($color, 5%),
                     2px 2px 0px darken($color, 10%),
                     3px 3px 0px darken($color, 15%),
                     4px 4px 0px darken($color, 20%),
                     4px 4px 2px #000;
      }
      
      h1 {
        font-size: 32pt;
        @ include text3d(#0982c1);
      }
      ```

    - less

      ```less
      .text3d(@color) {
        color: @color;
        text-shadow: 1px 1px 0px darken(@color, 5%),
                     2px 2px 0px darken(@color, 10%),
                     3px 3px 0px darken(@color, 15%),
                     4px 4px 0px darken(@color, 20%),
                     4px 4px 2px #000;
      }
      
      span {
        font-size: 32pt;
        .text3d(#0982c1);
      }
      ```

    - stylus

      ```styl
      text3d(color)
        color: color
        text-shadow: 1px 1px 0px darken(color, 5%), 
                     2px 2px 0px darken(color, 10%), 
                     3px 3px 0px darken(color, 15%), 
                     4px 4px 0px darken(color, 20%), 
                     4px 4px 2px #000
      span
        font-size: 32pt
        text3d(#0982c1)
      ```

      最后结果为

      ```css
      span {
        font-size: 32pt;
        color: #0982c1;
        text-shadow: 1px 1px 0px #097bb7,
                     2px 2px 0px #0875ae,
                     3px 3px 0px #086fa4,
                     4px 4px 0px #07689a,
                     4px 4px 2px #000;
      }
      ```

11. 列 (Columns)

    使用数值操作和变量可以很方便的实现适应屏幕大小的布局处理。

    - sass

      ```scss
      $siteWidth: 1024px;
      $gutterWidth: 20px;
      $sidebarWidth: 300px;
      body {
        margin: 0 auto;
        width: $siteWidth;
      }
      .content {
        float: left;
        width: $siteWidth - ($sidebarWidth+$gutterWidth);
      }
      .sidebar {
        float: left;
        margin-left: $gutterWidth;
        width: $sidebarWidth;
      }
      ```

    - less

      ```less
      @siteWidth: 1024px;
      @gutterWidth: 20px;
      @sidebarWidth: 300px;
      
      body {
        margin: 0 auto;
        width: @siteWidth;
      }
      .content {
        float: left;
        width: @siteWidth - (@sidebarWidth+@gutterWidth);
      }
      .sidebar {
        float: left;
        margin-left: @gutterWidth;
        width: @sidebarWidth;
      }
      ```

    - stylus

      ```styl
      siteWidth = 1024px;
      gutterWidth = 20px;
      sidebarWidth = 300px;
      
      body {
        margin: 0 auto;
        width: siteWidth;
      }
      .content {
        float: left;
        width: siteWidth - (sidebarWidth+gutterWidth);
      }
      .sidebar {
        float: left;
        margin-left: gutterWidth;
        width: sidebarWidth;
      }
      ```

      生成的css效果

      ```css
      body {
        margin: 0 auto;
        width: 1024px;
      }
      .content {
        float: left;
        width: 704px;
      }
      .sidebar {
        float: left;
        margin-left: 20px;
        width: 300px;
      }
      ```

12. 高级语法

    - 在sass中，还支持条件语句：

      ​    @if可一个条件单独使用，也可以和@else结合多条件使用 

      代码如下：

      ```scss
      $lte7: true;
      $type: monster;
      .ib{
          display:inline-block;
          @if $lte7 {
              *display:inline;
              *zoom:1;
          }
      }
      p {
        @if $type == ocean {
          color: blue;
        } @else if $type == matador {
          color: red;
        } @else if $type == monster {
          color: green;
        } @else {
          color: black;
        }
      }
      ```

      最终的css代码如下：

      ```css
      .ib{
          display:inline-block;
          *display:inline;
          *zoom:1;
      }
      p {
        color: green; 
      }
      ```

    - 除却条件语句，sass还支持for循环：

      for循环有两种形式，分别为：

      ```scss
      @for $var from <start> through <end>
      @for $var from <start> to <end>。
      ```

      其中$i表示变量，start表示起始值，end表示结束值，这两个的区别是关键字through表示包括end这个数，而to则不包括end这个数。

      ```scss
      @for $i from 1 to 10 {
        .border-#{$i} {
          border: #{$i}px solid blue;
        }
      }
      ```

      也支持while循环：

      ```scss
      $i: 6;
      @while $i > 0 {
        .item-#{$i} { width: 2em * $i; }
        $i: $i - 2;
      }
      ```

      同时支持each命令，作用与for类似：

      ```scss
      $animal-list: puma, sea-slug, egret, salamander;
      @each $animal in $animal-list {
        .#{$animal}-icon {
          background-image: url('/images/#{$animal}.png');
        }
      }
      ```

      css最终效果如下：

      ```scss
      .puma-icon {
        background-image: url('/images/puma.png'); 
      }
      .sea-slug-icon {
        background-image: url('/images/sea-slug.png'); 
      }
      .egret-icon {
        background-image: url('/images/egret.png'); 
      }
      .salamander-icon {
        background-image: url('/images/salamander.png'); 
      }
      ```

      

