---
title: 常用方法实现
tags:
  - js
categories:
  - es6

date: 2017-08-28 23:36:45
---

  ``` javascript
  // 事件委托、事件封装
  const EventUtil = {
    addHandler: function(element, type, fn) {
      if (element.addEventListener) {
        element.addEventListener(type, fn, false)
      } else if (element.attachEvent) {
        element.attachEvent('on' + type, fn)
      } else {
        element['on' + type] = fn
      }
    },
    removeHandler: function(element, type, fn) {
      if (element.removeEventListener) {
        element.removeEventListener(type, fn, false)
      } else if (element.detachEvent) {
        element.detachEvent('on' + type, fn)
      } else {
        element['on' + type] = null
      }
    },
    getTarget: function(event) {
      return event.target || event.srcElement
    },
    getEvent: function(event) {
      return event ? event : window.event
    },
    preventDefault: function(event) {
      if (event.preventDefault) {
        event.preventDefault()
      } else {
        event.returnValue = false
      }
    },
    stopPropagation: function(event) {
      if (event.stopPropagation) {
        event.stopPropagation()
      } else {
        event.cancelBubble = true
      }
    }
  }
  var ul = document.getElementById('ul')
  EventUtil.addHandle(ul, 'click', function(evt) {
    var event = EventUtil.getEvent(evt),
        targ = EventUtil.getTarget(event);
    switch(targ.id) {
      case "a":
        conosle.log(targ.id)
        break;
      case "b":
        conosle.log(targ.id)
        break;
    }
  })
  
  // CustomEvent 提供自定义事件方法
  var div = document.getElementById('div'),
      event;
  EventUtil.addHandler(div, 'myTese', function(evt){
    console.log(evt.detail)
  })
  if (document.implementation.hasFeature("CustomEvent", "3.0")) {
    event = document.createEvent('myTest')
    // 参数为初始化事件名称，是否应该冒泡，是否可以取消，传递的detail数据
    initCustomEvent('myTest', true, true, 'Hello world')
    div.dispatchEvent(event)
  }
  ```

  ``` javascript
  // document.ready
  function ready(fn) {
    if (window.addEventListener) {
      document.addEventListener('DOMContentLoaded', function () {
        document.removeEventListener('DOMContentLoaded', arguments.callee)
        fn()
      }, false)
    } else if(window.attachEvent) {
      window.attachEvent('onreadystatechange', function() {
        if (document.readyState = 'complete') {
          document.detachEvent('onreadystatechange', arguments.callee)
          fn()
        }
      })
    }
  }
  
  ```

  ``` javascript
  // 节流
  function throttle(fn, wait=500) {
    var timer;
    return function(...args) {
      if(!timer) {
        fn.apply(this, args)
        timer = setTimeout(() => {
          timer = null
        }, wait)
      }
    }
  }
  // 执行一次
  function once(fn) {
    return function(...args) {
      var ret = fn && fn.apply(this, args)
      fn = null
      return ret;
    }
  }
  function add() {
    console.log(222)
  }
  let onceBtn = document.getElementById('once')
  onceBtn.onclick = once(add)
  
  ```

  ``` javascript
  #my-slider{
      position: relative;
      width: 790px;
      height: 340px;
    }

    .slider-list ul{
      list-style-type:none;
      position: relative;
      width: 100%;
      height: 100%;
      padding: 0;
      margin: 0;
    }

    .slider-list__item,
    .slider-list__item--selected{
      position: absolute;
      transition: opacity 1s;
      opacity: 0;
      text-align: center;
    }

    .slider-list__item--selected{
      transition: opacity 1s;
      opacity: 1;
    }

    .slide-list__control{
      position: relative;
      display: table;
      background-color: rgba(255, 255, 255, 0.5);
      padding: 5px;
      border-radius: 12px;
      bottom: 30px;
      margin: auto;
    }

    .slide-list__next,
    .slide-list__previous{
      display: inline-block;
      position: absolute;
      top: 50%;
      margin-top: -25px;
      width: 30px;
      height:50px;
      text-align: center;
      font-size: 24px;
      line-height: 50px;
      overflow: hidden;
      border: none;
      background: transparent;
      color: white;
      background: rgba(0,0,0,0.2);
      cursor: pointer;
      opacity: 0;
      transition: opacity .5s;
    }

    .slide-list__previous {
      left: 0;
    }

    .slide-list__next {
      right: 0;
    }

    #my-slider:hover .slide-list__previous {
      opacity: 1;
    }


    #my-slider:hover .slide-list__next {
      opacity: 1;
    }

    .slide-list__previous:after {
      content: '<';
    }

    .slide-list__next:after {
      content: '>';
    }

    .slide-list__control-buttons,
    .slide-list__control-buttons--selected{
      display: inline-block;
      width: 15px;
      height: 15px;
      border-radius: 50%;
      margin: 0 5px;
      background-color: white;
      cursor: pointer;
    }

    .slide-list__control-buttons--selected {
      background-color: red;
    }
  <div id="my-slider" class="slider-list">
    <ul>
      <li class="slider-list__item--selected">
        <img src="https://p5.ssl.qhimg.com/t0119c74624763dd070.png"/>
      </li>
      <li class="slider-list__item">
        <img src="https://p4.ssl.qhimg.com/t01adbe3351db853eb3.jpg"/>
      </li>
      <li class="slider-list__item">
        <img src="https://p2.ssl.qhimg.com/t01645cd5ba0c3b60cb.jpg"/>
      </li>
      <li class="slider-list__item">
        <img src="https://p4.ssl.qhimg.com/t01331ac159b58f5478.jpg"/>
      </li>
      <a class="slide-list__next"></a>
      <a class="slide-list__previous"></a>
    </ul>
    <div class="slide-list__control">
      <span class="slide-list__control-buttons--selected"></span>
      <span class="slide-list__control-buttons"></span>
      <span class="slide-list__control-buttons"></span>
      <span class="slide-list__control-buttons"></span>
    </div>
  </div>
  // 轮播图
  class Slider {
    constructor(id, cycle = 2000) {
      this.container = document.getElementById(id);
      this.items = this.container.querySelectorAll('.slider-list__item, .slider-list__item--selected');
      this.cycle = cycle;
      this.slideHandlers = [];
    }
    getSelectedItem() {
      let selected = this.container.querySelector('.slider-list__item--selected');
      return selected
    }
    getSelectedItemIndex() {
      return Array.from(this.items).indexOf(this.getSelectedItem());
    }
    slideTo(idx) {
      let selected = this.getSelectedItem();
      if (selected) {
        selected.className = 'slider-list__item';
      }
      let item = this.items[idx];
      if(item){
        item.className = 'slider-list__item--selected';
      }
      this.slideHandlers.forEach(handler => {
        handler(idx);
      })
    }
    slidePrevious() {
      let currentIdx = this.getSelectedItemIndex();
      let previousIdx = (this.items.length + currentIdx - 1) % this.items.length;
      this.slideTo(previousIdx);
    }
    slideNext() {
      let currentIdx = this.getSelectedItemIndex();
      let nextIdx = (currentIdx + 1) % this.items.length;
      this.slideTo(nextIdx);
    }
    addSlideListener(handler){
      this.slideHandlers.push(handler);
    }
    start(){
      this.stop();
      this._timer = setInterval(()=>this.slideNext(), this.cycle);
    }
    stop(){
      clearInterval(this._timer);
    }
    registerPlugin(...plugins) {
      plugins.forEach((plugin) => plugin(this))
    }
  }
  function pluginController(slide) {
    let controller = slide.container.querySelector('.slide-list__control')
    if (controller) {
      let buttons = controller.querySelectorAll('.slide-list__control-buttons, .slide-list__control-buttons--selected')
      controller.addEventListener('mouseover', evt => {
        var idx = Array.from(buttons).indexOf(evt.target)
        if (idx >= 0) {
          slide.slideTo(idx)
          slide.stop()
        }
      })
      controller.addEventListener('mouseout', evt => {
        slide.start()
      })
      slide.addSlideListener(function(idx){
        let selected = controller.querySelector('.slide-list__control-buttons--selected');
        if(selected) selected.className = 'slide-list__control-buttons';
        buttons[idx].className = 'slide-list__control-buttons--selected';
      });
    }
  }
  function pluginPrev(slide) {    
    let previous = slide.container.querySelector('.slide-list__previous');
    if(previous){
      previous.addEventListener('click', evt => {
        slide.stop();
        slide.slidePrevious();
        slide.start();
        evt.preventDefault();
      });
    }
  }
  function pluginNext(slide) {    
    let next = slide.container.querySelector('.slide-list__next');
    if(next){
      next.addEventListener('click', evt => {
        slide.stop();
        slide.slideNext();
        slide.start();
        evt.preventDefault();
      });
    }
  }
  const slider = new Slider('my-slider');
  slider.registerPlugin(pluginController, pluginPrev, pluginNext)
  slider.start();
  
  ```

  ```javascript
  // 数组展开
  var flattened = function(arr) {
    if (arr.length <= 1) {
      return arr
    }
    return arr.reduce((prev, curr) => 
      prev.concat(Array.isArray(curr) ? flattened(curr) : curr)
      , [])
  }
  flattened([[0, 1], [2, 3], [4, 5, [7, 8]]])
  // 深拷贝
  function deepCopy(des, src) {
    for(var key in src) {
      let prop = src[key]
      if (typeof prop === 'object') {
        des[key] = des[key] || {}
        deepCopy(des[key], prop)
      } else {
        des[key] = src[key]
      }
    }
    return des
  }
  ```

  ```javascript
    // 数组展开
    var flattened = function(arr) {
      if (arr.length <= 1) {
        return arr
      }
      return arr.reduce((prev, curr) => 
        prev.concat(Array.isArray(curr) ? flattened(curr) : curr)
        , [])
    }
    flattened([[0, 1], [2, 3], [4, 5, [7, 8]]])
    // 深拷贝
    function deepCopy(des, src) {
      for(var key in src) {
        let prop = src[key]
        if (typeof prop === 'object') {
          des[key] = des[key] || {}
          deepCopy(des[key], prop)
        } else {
          des[key] = src[key]
        }
      }
      return des
    }
  ```

  ``` javascript
    // 找出字符串中出现次数最多的字节
  	function findMaxDuplicateChar(str) {
    if (str.length == 1) {
      return str
    }
    let charObj = {}
    for (let i = 0; i < str.length; i++) {
      if (!charObj[str.charAt(i)]) {
        charObj[str.charAt(i)] = 1
      } else {
        charObj[str.charAt(i)] += 1
      }
    }
    let maxChar = ''
    let maxVal = 1
    for (let k in charObj) {
      if (charObj[k] >= maxVal) {
        maxChar = k
        maxVal = charObj[k]
      }
    }
    return maxChar
  }
  // 冒泡排序
  function bubbleSort(arr) {
    let len = arr.length
    if (len == 1) {
      return arr
    }
    for (let i = 0; i < len - 1;i++) {
      for (let j = i + 1; j < len; j++) {        
        if (arr[i] > arr[j]) {
          let temp = arr[i]
          arr[i] = arr[j]
          arr[j] = temp
        }
      }
    }
  }
  ```