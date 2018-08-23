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

+ Android studio 弹出unable to access android sdk add-on list原因是你电脑没有SDK而且你下载的android studio又是不带SDK的； 解决方法：在自己安装的目录下找到：bin\idea.properties打开这个文件末尾添加一行disable.android.first.run=true

+ 环境变量：`ANDROID_HOME`指向：Sdk,`path`中添加`%ANDROID_HOME%\tools`


+ [cygwin](http://pan.baidu.com/s/1jHNKSa6)
  > 安装过程可能会出现下载很慢，可选择离线安装，将[离线包](http://pan.baidu.com/s/1mhF9oQ8)导入

+ 运行官方demo可能会下载缓慢，将[此包放到react-native\ReactAndroid\build\downloads](http://pan.baidu.com/s/1bLtrci)下

+ 组件绑定props传入的事件需要bind(this)或者onPress={() => this.props.onPress()}

+ 完成图书、电影模块 [demo地址](https://github.com/youngauto/Cake)

## 2、学习记录点

+ 使用字体图标，将下载的字体图标文件*.ttf* 放到\android\app\src\main\assets\fonts\iconfont.ttf

+ 在使用的地方设置fontFamily: 'iconfont'，Text标签内部填入对应的图片code：&#xe600;

+ 官方已经不再维护navigator组件，目前使[react-navigation](https://reactnavigation.org/docs/intro/)组件

+ 数据使用[mockapi](http://www.mockapi.io/)

+ 修改app logo和名称: ![TIM截图20170519102015](C:/Users/yx/Desktop/TIM截图20170519102015.png)

  修改values下面的string.xml的* <string name="app_name">一场旅行</string> *，完成后在android目录下执行* * ./gradlew clean * 然后在执行运行命令即可

+ android支持图片格式问题`android/app/build.gradle`：

  ```javascript
  dependencies {
    // 如果你需要支持Android4.0(API level 14)之前的版本
    compile 'com.facebook.fresco:animated-base-support:0.11.0'

    // 如果你需要支持GIF动图
    compile 'com.facebook.fresco:animated-gif:0.11.0'

    // 如果你需要支持WebP格式，包括WebP动图
    compile 'com.facebook.fresco:animated-webp:0.11.0'
    compile 'com.facebook.fresco:webpsupport:0.11.0'

    // 如果只需要支持WebP格式而不需要动图
    compile 'com.facebook.fresco:webpsupport:0.11.0'
  }
  ```

  ​

