---
title: 工作中问题记录
id: 20170814
tags:
  - js
  - 前端
  - 记录
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

  ​