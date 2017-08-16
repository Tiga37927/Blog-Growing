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


