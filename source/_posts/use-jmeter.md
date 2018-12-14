---
title: jmeter 使用入门
date: 2018-12-14 18:30:10
tags:
toc: true
---


## 引言

进行压测时，使用 ab 是个不错的选择，奈何 ab 的报表纬度有时候不能满足需要。除了 ab，我们还可以用 jmeter 呀！

## 安装

**1. 下载压缩包 https://jmeter.apache.org/download_jmeter.cgi**

**2. 安装 jre**

Ubuntu `sudo apt-get install default-jre`

**3. 运行**

我的包解压目录 `/home/rovast/Applications/`，运行比较简单，直接运行 `/home/rovast/Applications/apache-jmeter-4.0/bin/jmeter.sh` 即可

**4. 设定 desktop 文件**

除了直接运行脚本，我们也可以新建 desktop 文件，这样就可以在应用程序列表中直接找到运行

新建文件 `/usr/local/share/applications/jmeter.desktop`

```text
[Desktop Entry]
Encoding=UTF-8
Name=jmeter
Comment=Apache Jmeter Tool
Exec=/bin/sh "/home/rovast/Applications/apache-jmeter-4.0/bin/jmeter.sh"
Categories=Application;Development;
Version=1.0
Type=Application
Terminal=0
```

## 使用

运行软件

> 请确定已经安装 java 环境了

### 新建测试计划

添加测试组，请注意层级关系！不然不能正常工作。

```text
测试计划
  线程组
    HTTP请求
      HTTP信息头管理器
      聚合报告
      图形结果
      查看结果树
```

> HTTP 信息头管理器可以管理 http headers，对于一些把认证放到 header 里的测试很有用

{% asset_img 1.png 新建测试计划 %}

### 线程组设置

一般情况下进行压测，线程数 50, 循环数 10000。什么意思呢？就是位有 50 个线程请求 http，每一个请求 1w 次，总计也就是 50w 次。

### 开始运行

点击工具栏的绿色运行按钮（一般运行前会点击扫帚按钮，清除之前记录），运行完指定循环次数后，就结束了。

### 查看报告

聚合报告。主要关注吞吐量、错误率

{% asset_img 2.png 新建测试计划 %}

图形报告。可以查看响应是否稳定

{% asset_img 3.png 新建测试计划 %}

查看图形树。这里存放了每一个请求，可以看到响应数据

{% asset_img 4.png 新建测试计划 %}