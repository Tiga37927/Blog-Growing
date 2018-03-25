---
title: spring boot高程读书笔记
id: 20170930
tags:
  - java
  - spring boot
  - jpa
categories:
  - 后端
  - java

date: 2017-10-20 03:36:45
---
### spring-boot学习记录###

* 使用idea自带工具生成spring-boot项目
* SpringBoot启动报错：Cannot determine embedded database driver class for database type NONE

  ``` java
  // 使用了jpa，spring-boot启动时会自动注入数据源和配置，给配置里传入对应的class
  @SpringBootApplication(exclude = {DataSourceAutoConfiguration.class, HibernateJpaAutoConfiguration.class})
  ```

  ​

