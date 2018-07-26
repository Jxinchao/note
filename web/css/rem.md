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

