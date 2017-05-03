---
title: react-native问题记录
tags:
  - js
  - react
  - react-native
categories:
  - react-native

date: 2017-04-28 23:36:45
---

## 1、环境配置相关问题

+ [android ndk](https://facebook.github.io/react-native/docs/android-building-from-source.html)
  > 选择平台对应版本
+ 虚拟设备：[genymotion](http://pan.baidu.com/s/1geNQZ5p)
+ [cygwin](http://pan.baidu.com/s/1jHNKSa6)
  > 安装过程可能会出现下载很慢，可选择离线安装，将[离线包](http://pan.baidu.com/s/1mhF9oQ8)导入
+ 运行官方demo可能会下载缓慢，将[此包放到react-native\ReactAndroid\build\downloads](http://pan.baidu.com/s/1bLtrci)下
+ 组件绑定props传入的事件需要bind(this)或者onPress={() => this.props.onPress()}
