---
title: 工作中问题记录170814
id: 20170814
tags:
  - js
  - 前端
  - 记录
  - Dom优化
categories:
  - 其他
  - 前端问题

date: 2017-05-14 03:36:45
---

* html5自带pattern校验

  ``` javascript
  <input class="row_body" type="text" required v-model="user.birthday" placeholder="1989-01-01" pattern="[1-9]{4}-[0-9]{2}-[0-9]{2}"  title="这里是提示的内容部分" />
  ```

* 网站顶部加载的进度条

  ``` html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>test</title>
      <style>
          .cont {
              width: 100%;
              height: 8000px;
          }
          #loading {
              position: fixed;
              top: 0;
              left: 0;
              height: 4px;
              background-color: ;
          }
      </style>
  </head>
  <body>
      <div class="cont">
          <div id="loading"></div>
      </div>
      <script>
          let loading = document.getElementById('loading');
          // 整个dom的高度 $(document).height()
          let docH = document.documentElement.scrollHeight;
          // 偏移量，即滚动条占的高度 $(this).height()
          let scrollH = document.documentElement.clientHeight;
          // 总宽度
          let winW = window.screen.availWidth;
          // 滚动位置
          let scrollTop = null;
          document.addEventListener("scroll", function() {
              // 滚动的高度 $(doucment).scrollTop()
              scrollTop = window.scrollY || document.body.scrollTop;
              loading.style.width = ((scrollTop + scrollH) / docH) * 100 + '%';
          }, false);
      </script>
  </body>
  </html>
  ```


* let声明只在所声明的代码块内有效

  ``` javascript
  // var 声明的变量属于全局，因此当for循环结束后，i = 10，最终也会导致console.log（i） // 10
  var a = [];
  for (var i = 0; i < 10; i++) {
    a[i] = function () {
      console.log(i);
    };
  }
  a[6](); // 10

  var a = [];
  for (let i = 0; i < 10; i++) {
    a[i] = function () {
      console.log(i);
    };
  }
  a[6](); // 6
  // let 声明的变量属于代码块中，不存在变量提升，因此for每一次循环都会将i放在一个代码块中，i不是全局变量
  // 等同于
  { //进入第二次循环
       let i=1; //注意：因为let i=1; 和 上面的let i=0;出在不同的作用域中，所以两者不会相互影响。
       a[1]=function(){
           console.log(i);
       }; //同样，这个a[i]也是一个闭包
  }
  ```



* 解析HTML，并生成一棵DOM tree

  解析各种样式并结合DOM tree生成一棵Render tree(渲染树)

  对Render tree的各个节点计算布局信息，比如box的位置与尺寸

  根据Render tree并利用浏览器的UI层进行绘制

  `注意`：DOM tree和Render tree上的节点并非一一对应，比如一个"display:none"的节点就只会存在于DOM tree上，而不会出现在Render tree上，因为这个节点不需要被绘制。

* call/apply/bind

  ``` javascript
  call和apply方法都用于改变this的指向，区别在于call接受个数明确的参数，并全部写在参数位置；apply接受的参数是一个数组；
  bind方法也用于改变this指向，区别在于bind绑定之后不会立即执行，而是返回一个函数引用，call/apply绑定的方法都会立即执行；

  bind实现：
  if (!Function.prototype.bind) {
  		Function.prototype.bind = function() {
  			if (typeof this !== 'function') {
  				// 绑定的不是一个函数
  				throw new TypeError("Function.prototype.bind - what is trying to be bound is not callable");
  			}

  			var args = Array.prototype.slice.call(arguments, 1), // 获取传入的参数
  				fNOP = function () {},
  				fToBind = this, // this在这里指向的是目标函数
  				fBound = function () {
  					return fToBind.apply(this, 
  					//将通过bind传递的参数和调用时传递的参数进行合并，并作为最终的参数传递
                 		aArgs.concat(Array.prototype.slice.call(arguments)));
  				};
  			//将目标函数的原型对象拷贝到新函数中，因为目标函数有可能被当作构造函数使用
  			fNOP.prototype = this.prototype;
              //返回fBond的引用，由外部按需调用
  			fBound.prototype = new fNOP();

  			return fBound;
  		}
  	}
  ```




* Dom优化

  浏览器呈现过程：

  解析html，并生成一颗DOM tree

  解析各种样式并结合DOM tree生成一颗Render tree（渲染树）

  对Render tree的各个节点计算布局信息，比如box的位置与尺寸

  根据Render tree并利用浏览器的UI层进行绘制

  **注意**：DOM tree和Render tree上的节点并非一一对应，比如一个"display:none"的节点就只会存在于DOM tree上，而不会出现在Render tree上，因为这个节点不需要被绘制。

  ​	paint是一个耗时的过程，然而layout是一个更耗时的过程，我们无法确定layout一定是自上而下或是自下而上进行的，甚至一次layout会牵涉到整个文档布局的重新计算。

  ​	layout是无法避免的，所以我们主要是最小化layout的次数

  ​	一般情况下，浏览器的layout是lazy的，也就是说：在js脚本执行时，是不会去更新DOM的，任何对DOM的修改都会被暂存在一个队列中，在当前js的执行上下文完成执行后，会根据这个队列中的修改，进行一次layout。[浏览器的layout为lazy模式，也就是说，并非每次我们对DOM进行修改时都会layout，而是将这些修改存储在一个队列中，在一定的情况下统一提交队列，进而实现layout操作。

  　　然而有时希望在js代码中立刻获取最新的DOM节点信息，浏览器就不得不提前执行layout，这是导致DOM性能问题的主因。

  ​	通过js获取需要计算的DOM属性[计算才能得到的属性（clientHeight。。。）]

  ​	添加或删除DOM元素

  ​	resize浏览器窗口大小

  ​	改变字体

  ​	css伪类的激活，如:hover

  ​	通过js修改DOM元素样式且该样式设计到尺寸的改变

  所以要尽量尖山上面layout的次数

  ``` javascript
  1.批量读写
  当我们需要获取某一属性，这一属性需要计算才能得到，并且队列中存在尚未提交的DOM修改操作，则此时，DOM修改操作的队列将会被提交。为了提高效率，减少更新render tree的次数，可以先统一读取属性，然后统一修改DOM，这样，就可以减少更新render tree的次数
  var h1 = element1.clientHeight;
  element1.style.height = (h1 * 2) + 'px';
  var h2 = element2.clientHeight;
  element2.style.height = (h2 * 2) + 'px';
  var h3 = element3.clientHeight;
  element3.style.height = (h3 * 2) + 'px';
  clientHeight，这个属性是需要计算得到的，于是就会触发一次layout
  优化：
  var h1 = element1.clientHeight;  
  var h2 = element2.clientHeight;  
  var h3 = element3.clientHeight;

  element1.style.height = (h1 * 2) + 'px';  
  element2.style.height = (h2 * 2) + 'px';  
  element3.style.height = (h3 * 2) + 'px';

  2.虚拟节点
  当我们需要对DOM做出大量修改时，可以先创建一个虚拟结点，将所有修改附加在该节点，最后将该虚拟节点一次性提交给在render tree上存在的结点，则相当于只提交了一次修改操作。
  var fragment = document.createDocumentFragment();  
  for (var i=0; i < items.length; i++){  
    var item = document.createElement("li");
    item.appendChild(document.createTextNode("Option " + i);
    fragment.appendChild(item);
  }
  list.appendChild(fragment);  

  3.动画的每一帧都会导致layout，这是无法避免的，但是为了减少动画带来的layout的性能损失，可以将动画元素绝对定位，这样动画元素脱离文本流，layout的计算量会减少很多。
  ```

  ​

