---
title: Ubuntu18.04安装深度截图
date: 2018-12-03 21:04:15
categories: 杂项
tags: [ubuntu, 截图]
---

Ubuntu默认的截图可以使用 `shift + printScreen` 组合键来截取区域截图，保存在图片文件夹中。**但是，** 我觉得还是深度截图好用。

### 1. 下载deb包
访问 http://packages.linuxdeepin.com/deepin/pool/main/d/deepin-scrot/ 下载 deepin-scrot_2.0-0deepin_all.deb 

或者直接 `wget http://packages.linuxdeepin.com/deepin/pool/main/d/deepin-scrot/deepin-scrot_2.0-0deepin_all.deb `

### 2. 安装

```bash
sudo dpkg -i deepin-scrot_2.0-0deepin_all.deb

# 出现依赖问题
sudo apt-get -f install
```

### 3. 安装其他一些依赖

```bash
 sudo apt-get install qt5-qmake qttools5-dev qt5-default \
 qtbase5-dev python-gtk2 python-gtk2-dbg python-gtk2-dev \
 python-gtk2-doc libcanberra-gtk-dev
```

### 4. 使用

命令行运行 `deepin-scrot` 即可

也可以自定义快捷键 （我的是 Ctrl Shift A）

{% asset_img 20181203124439388.png 设置截图 %}
