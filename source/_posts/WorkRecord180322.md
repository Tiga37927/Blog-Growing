---
title: 工作中问题记录180322
id: 20180322
tags:
  - 浏览器版本
categories:
  - 其他
  - 前端问题

date: 2018-03-022 03:36:45
---

### 浏览器版本信息

```javascript
function getBrowserInfo(){
    var browser = window.navigator.userAgent.toLowerCase();
    var isIE = browser.indexOf("compatible") > -1 && userAgent.indexOf("MSIE") > -1; //判断是否IE<11浏览器
    var isEdge = userAgent.indexOf("Edge") > -1 && !isIE; //判断是否IE的Edge浏览器  
    var isIE11 = userAgent.indexOf('Trident') > -1 && userAgent.indexOf("rv:11.0") > -1;
    if(isIE) {
        var reIE = new RegExp("MSIE (\\d+\\.\\d+);");
        reIE.test(userAgent);
        var fIEVersion = parseFloat(RegExp["$1"]);
        if(fIEVersion == 7) {
            return 'ie7';
        } else if(fIEVersion == 8) {
            return 'ie8';
        } else if(fIEVersion == 9) {
            return 'ie9';
        } else if(fIEVersion == 10) {
            return 'ie10';
        } else {
            return 'ie6';//IE版本<=7
        }   
    } else if(isEdge) {
        return 'edge';//edge
    } else if(isIE11) {
        return 'ie11'; //IE11  
    } else if (browser.indexOf("firefox") >= 0) {
        var ver = explorer.match(/firefox\/([\d.]+)/)[1];
        return "Firefox" + ver;
    } else if (browser.indexOf("chrome") >= 0) {
        var ver = browser.match(/chrome\/([\d.]+)/)[1];
        return "Chrome" + ver;
    } else if (browser.indexOf("opera") >= 0) {
        var ver = browser.match(/opera.([\d.]+)/)[1];
        return "Opera" + ver;
    } else if (browser.indexOf("Safari") >= 0) {
        var ver = browser.match(/version\/([\d.]+)/)[1];
        return "Safari" + ver;
    } else{
        return -1;//不是ie浏览器
    }
}
```



