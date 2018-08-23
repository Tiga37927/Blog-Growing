---
title: 工作中问题记录180322
id: 20180322
tags:
  - 浏览器版本
  - vmware全屏
categories:
  - 其他
  - 前端问题
  - vmware

date: 2018-03-022 03:36:45
---

### 获取浏览器版本信息

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

### vmware相关

```
注册码：
vmware12 5A02H-AU243-TZJ49-GTC7K-3C61N

vmware14CG54H-D8D0H-H8DHY-C6X7X-N2KG6

全屏显示问题：
1.安装VMware Tools
2.复制 VMwareTools-10.0.10-4301679.tar.gz（版本根据自己的实际情况） 到/home/lance/目录下
3.tar -vxzf XXX.tar.gz
4.sudo ./vmware-install.pl
5.一路回车
6.直到你看到—the vmware team 后 reboot 重启即可
7.若还没有全屏显示，则将虚拟机的【查看】->【自动调整大小】->【自适应客户机】,都选上。即可实现全屏
```

### 修改Ubuntu的aptget源为阿里源的方法

```
1、复制原文件备份

sudo cp /etc/apt/source.list /etc/apt/source.list.bak

2、编辑源列表文件

sudo vim /etc/apt/source.list

3、将原来的列表删除，添加如下内容

deb http://mirrors.aliyun.com/ubuntu/ vivid main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ vivid-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ vivid-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ vivid-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ vivid-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ vivid main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ vivid-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ vivid-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ vivid-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ vivid-backports main restricted universe multiverse

4、运行sudo apt-get update

5、运行sudo apt-get upgrade
```

### 安装node

```
sudo apt-get install nodejs
sudo apt install nodejs-legacy
sudo apt install npm
sudo npm config set registry https://registry.npm.taobao.org
sudo npm config list
sudo npm install n -g
sudo n stable
```

