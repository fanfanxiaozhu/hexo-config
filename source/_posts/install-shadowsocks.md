---
title: 科学上网之 Shadowsocks 安装及优化加速
date: 2018-12-09 20:04:45
tags: [转载]
toc: true
---

本文转载自 [《科学上网之 Shadowsocks 安装及优化加速》](https://yq.aliyun.com/articles/137280?commentId=11711)

原文地址： https://yq.aliyun.com/articles/137280?commentId=11711


> Shadowsocks使用自行设计的协议进行加密通信。加密算法有AES、Blowfish、IDEA、RC4等，除创建TCP连接外无需握手，每次请求只转发一个连接，因此使用起来网速较快，在移动设备上也比较省电。
所有的流量都经过算法加密，允许自行选择算法，所以比较安全。
Shadowsocks通过异步I/O和事件驱动程序运行，响应速度快。
客户端覆盖多个主流操作系统和平台，包括Windows，OS X，Android和iOS系统和路由器（OpenWrt）等 专为移动设备和无线网络优化


## 1. 服务端安装
官方推荐 Ubuntu 14.04 LTS 作为服务器以便使用 TCP Fast Open。服务器端的安装非常简单。

Debian / Ubuntu:
```bash
apt-get install python-pip
pip install shadowsocks
```

CentOS:
```bash
yum install python-setuptools && easy_install pip
pip install shadowsocks
```

然后直接在后台运行：

`ssserver -p 38018 -k password -m rc4-md5 -d start`

当然也可以使用配置文件进行配置，方法创建/etc/shadowsocks.json文件，填入如下内容：

```json
{
    "server":"0.0.0.0",
    "server_port":38018,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"rc4-md5"
}
```

然后使用配置文件在后台运行：

`ssserver -c /etc/shadowsocks.json -d start`

如果要停止运行，将命令中的start改成stop。

> TIPS: 加密方式推荐使用rc4-md5，因为 RC4 比 AES 速度快好几倍，如果用在路由器上会带来显著性能提升。
旧的 RC4 加密之所以不安全是因为 Shadowsocks 在每个连接上重复使用 key，没有使用 IV。现在已经重新正确实现，可以放心使用。更多可以看 issue。

## 2. 多用户配置

网上很多用户vps安装的Linux是debian或ubuntu的，所以可以通过修改/etc/shadowsock.json文件，
通过添加端口号以及密码的方法，进行多用户配置。这种配置方案，网上可以搜出很多。
我这里也粘贴一下具体修改json文件的方法.示例如下

```json
{  
 "server":"0.0.0.0"，  
 "local_address": "127.0.0.1",  
 "local_port":1080,  
  "port_password": {  
     "38011": "passwd",  
     "38012": "passwd",  
     "38013": "passwd",  
     "38014": "passwd"  
 },  
 "timeout":300,  
 "method":"rc4-md5",  
 "fast_open": false  
}
```

## 3. 加速优化

下面介绍几种简单的优化方法，也是比较推荐的几种，能够得到立竿见影的效果。当然还有一些黑科技我没提到，如有大神路过，也可留言指出。

### 3.1 内核参数优化

首先，将 Linux 内核升级到 3.5 或以上。

第一步，增加系统文件描述符的最大限数

编辑文件 limits.conf

`vi /etc/security/limits.conf`
  
增加以下两行

```bash
* soft nofile 51200
* hard nofile 51200
```

启动shadowsocks服务器之前，设置以下参数

`ulimit -n 51200`

第二步，调整内核参数

修改配置文件 /etc/sysctl.conf

```bash
fs.file-max = 51200
net.core.rmem_max = 67108864
net.core.wmem_max = 67108864
net.core.netdev_max_backlog = 250000
net.core.somaxconn = 4096
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 0
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_keepalive_time = 1200
net.ipv4.ip_local_port_range = 10000 65000
net.ipv4.tcp_max_syn_backlog = 8192
net.ipv4.tcp_max_tw_buckets = 5000
net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_rmem = 4096 87380 67108864
net.ipv4.tcp_wmem = 4096 65536 67108864
net.ipv4.tcp_mtu_probing = 1
net.ipv4.tcp_congestion_control = hybla
```

修改后执行 `sysctl -p` 使配置生效

### 3.2 锐速

锐速是一款非常不错的TCP底层加速软件，可以非常方便快速地完成服务器网络的优化，配合 ShadowSocks 效果奇佳。
目前锐速官方也出了永久免费版本，适用带宽20M、3000加速连接，个人使用是足够了。如果需要，先要在锐速官网注册个账户。

然后确定自己的内核是否在锐速的支持列表里，如果不在，请先更换内核，如果不确定，请使用 手动安装。

确定自己的内核版本在支持列表里，就可以使用以下命令快速安装了。

```bash
wget http://my.serverspeeder.com/d/ls/serverSpeederInstaller.tar.gz
tar xzvf serverSpeederInstaller.tar.gz
bash serverSpeederInstaller.sh
```

输入在官网注册的账号密码进行安装，参数设置直接回车默认即可，
最后两项输入 y 开机自动启动锐速，y 立刻启动锐速。
之后可以通过lsmod查看是否有appex模块在运行。到这里还没结束，我们还要修改锐速的3 个参数，

```bash
vi /serverspeeder/etc/config

rsc="1" #RSC网卡驱动模式  
advinacc="1" #流量方向加速  
maxmode="1" #最大传输模式
digitalocean vps的网卡支持rsc和gso高级算法，所以可以开启rsc="1"，gso="1"。
```

重新启动锐速

`service serverSpeeder restart`

### 3.3 net-speeder

net-speeder 原理非常简单粗暴，就是发包翻倍，这会占用大量的国际出口带宽，本质是损人利己，不建议使用。

(1) Ubuntu/Debian 下安装依赖包

```bash
apt-get install libnet1
apt-get install libpcap0.8
apt-get install libnet1-dev
apt-get install libpcap0.8-dev
```

(2) Centos 下安装依赖包

需要配置 epel 第三方源。下载 epel ：http://dl.fedoraproject.org/pub/epe
 l/ 。例如，Centos 7 x64：

```bash
wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.no
arch.rpm
rpm -ivh epel-release-7-5.noarch.rpm
yum repolist
```

然后安装依赖包： `yum install libnet libpcap libnet-devel libpcap-devel`

(3) 下载官方的 tar.gz 压缩包。解压安装运行：

```bash
wget http://net-speeder.googlecode.com/files/net_speeder-v0.1.tar.gz 
tar zxvf net_speeder-v0.1.tar.gz
cd net_speeder
chmod 777 *
sh build.sh -DCOOKED
```

首先你需要知道你的网卡设备名，可以使用 ifconfig 查看。假设是eth0，

那么运行方法是:

`./net_speeder eth0 "ip"`

关闭 net-speeder

`killall net_speeder`

哦，对了，作者已经将 net-speeder 迁移到 GitHub 了，感兴趣的可以关

注、贡献。
以上几种方法是我用过的几种比较有效的加速方法。有任何错误之处还请在下面留言指出。

如果你不想折腾服务端安装和优化，你可以使用虫洞咖啡厅提供的免费 shadowsocks 服务。

一键安装脚本连接地址  https://teddysun.com/486.html

```bash
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```
