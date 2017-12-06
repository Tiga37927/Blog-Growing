---
title: JavaScript读书笔记
id: 20171010
tags:
  - js
categories:
  - js
  - 笔记
date: 2017-08-28 03:36:45
---

### JavaScript实现

- 文档对象模型（DOM）

  DOM是针对xml但经过扩展用于html的应用程序编程接口，dom把整个页面映射为一个多层节点结构

- DOM级别

  DOM1级1998.10由w3c推荐，有两个模块组成：DOM核心和DOM HTML。DOM核心规定如何映射基于xml的文档结构，以便简化对文档中任意部分的访问和操作。DOM HTML模块则在DOM核心的基础上加以扩展，添加了针对html的对象和方法

  DOM2在原来的DOM基础上又扩充了鼠标和用户界面事件、范围、遍历等细分模块，而且通过对象接口增加了对css的支持

  DOM3进一步扩展了DOM，引入了以统一方式加载和保存文档的方法

- 其他DOM标准

  以下语言都是基于xml的，每种语言的DOM标准都添加了与特定语言相关的新方法和接口：

  SVG

  MathML

  SMIL

- 浏览器对象模型（BOM）

  BOM只处理浏览器窗口和框架，提供与浏览器交互的方法和接口，如window、navigator


### html中使用JavaScript

* <script>元素

  用于向html页面插入JavaScript，有如下属性

  + async：可选，表示应该立即下载脚本，但不妨碍页面中的其他操作，不保证执行时间
  + defer：可选，表示脚本立即下载，但延迟执行，现实中，多个延迟脚本并不会按顺序执行，因此最好只包含一个延迟脚本

* 在XHTML中的用法

  * xhtml是基于xml的应用而重新定义的一个标准，编写XHTML比html严格得多，直接影响能否在嵌入javascript代码时使用<script />标签，比如a < b，XHTML会将< 解析为标签，这里需要使用'& l t;'代替


###文档模式

+ doctype是通过使用文档类型切换实现的，最初的两种文档模式是：混杂模式和标准模式。混杂模式会让IE的行为与IE5相同，而标准模式则让IE的行为更接近标准行为。这两种模式主要影响Css内容的呈现，某些情况下也会影响到js的解释执行

### typeof操作符

+ "undefined"---未定义

  + 声明变量但未对其加以初始化，但对未声明的变量也会返回undefined

+ "boolean"---布尔值

  + 只有true和false两个值，但是其他类型有等价的值

    | 数据类型      | 转换为true的值 | 转换为false的值 |
    | --------- | --------- | ---------- |
    | Boolean   | true      | false      |
    | String    | 任何非空字符串   | ""（空字符串）   |
    | Number    | 任何非零的数字   | 0和NaN      |
    | Object    | 任何对象      | null       |
    | Undefined |           | undefined  |
    |           |           |            |

+ "string"---字符串

  + ``````
    不可变
    var lang = "Java";
    lang = lang + "Script";
    首先创建一个能容纳是个字符的新字符串，然后在这个字符串中填充"Java"和"Script",最后一步是消回原来的字符串"Java"和"Script"，因此字符串的拼接很慢
    ``````

  + 转换为字符串

    第一种使用每个值的toString方法（null，undefined没有）；第二种，不知道类型，使用String()
+ "number"---数字

  + 浮点数值

    浮点数值中必须包含一个小数点，小数点后面必须至少有一位数字，最高精度17位

  + 数值范围

    5e-324~1.7976931348623157e+308，超出的会被转换成-Infinity或者Infinity

  + NaN

    任何数除以0会返回NaN，isNaN()用于判断是否"不是数值"

  + 数值转换

    Number()用于任何数据类型，parseInt()、parseFloat()转换字符串

+ "object"---对象或者null

  + null只有一个值，就是null，表示空指针对象，undefined == null // true

+ "function"---函数


### 操作符

+ 一元操作符

  + 只能操作一个值的操作符

  + 当执行前置递增和递减操作时，变量的值都是在语句被求值以前改变的

    ```
    var age = 29;
    var anotherAge = --age + 2;
    alert(age); // 28
    alert(anotherAge); //30
    ```

  + 后置递增和递减操作，变量的值都是在语句被求值以后改变

+ 一元加和减操作符

  + 用于基本算术运算









































