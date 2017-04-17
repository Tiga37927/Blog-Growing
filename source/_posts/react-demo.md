---
title: react-demo
tags:
  - react
  - react组件
categories:
  - react
  - reactjs

date: 2017-04-17 03:36:45
---
### react学习记录
* react组件只能有一个顶层元素
* jsx实际是js对象，html标签可以抽象成js对象：标签、属性、子元素
* state的主要作用是用于组件保存、控制、修改自己的可变状态。state在组件内部初始化，可以被组件自身修改，而外部不能访问也不能修改。你可以认为state是一个局部的、只能被组件自身控制的数据源。state中状态可以通过`this.setState`方法进行更新，setState会导致组建的重新渲染。
* props的主要作用是让使用该组件的父组件可以传入参数来配置该组件。它是外部传进来的配置参数，组件内部无法控制也无法修改。除非外部组件主动传入，否则组建的props永远保持不变。
* 状态提升：当某个状态被多个组件依赖或者影响的时候，就应该把状态提升到这些组件的最近公共父组件中去管理，用props传递数据或者函数来管理这种依赖或者影响的行为。
* 最新版本，`PropTypes`不在react里，需要`npm i prop-types --save-dev`,在使用时使用import
* 高阶函数：接受或返回一个函数的函数