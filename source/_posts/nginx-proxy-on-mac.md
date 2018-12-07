---
title: mac 上配置 nginx 端口转发访问 angular 项目
date: 2018-12-06 21:03:01
tags: [nginx]
toc: true
---

## 背景

在进行前端的移动端开发时，需要在手机上预览电脑上的项目。
以 angular 为例，默认启动项目后，在开发机（以 Mac 为例）的浏览器地址栏输入 http://localhost:4200 即可访问。
> 我们假设前端默认开启的端口号是4200

如果想要在手机上访问，我们先确保手机和 Mac 链接的同一个网络（链接同一个 WIFI 即可），Mac 上输入 

```bash
# 查看本机 ip 地址
ifconfig
```

输出
```bash
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	ether f0:xxxxxxxx
	inet6 xxxxxxxx prefixlen 64 secured scopeid 0xa
	inet 192.168.1.102 netmask 0xffffff00 broadcast 192.168.1.255
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect
	status: active
```

我们看到，我们 ip 为 192.168.1.102。那么我们直接在手机浏览器输入 http://192.168.1.102:4200，发现并不能访问。
理论上，我们可以修改 webpack 或者其他的一些配置来使手机正常访问。这里，我们采用另一种方式来解决，即：Nginx 端口转发。

## 使用 Nginx 进行端口转发

### 1. 安装 Nginx

```bash
brew install nginx
```

### 2. 新建配置文件

```bash
# 进入 nginx server 配置目录
cd /usr/local/etc/nginx/servers

# 新建配置文件
sudo vi fe.conf
```

在打开的 vi 编辑器中进行下述操作
1. 分别输入 `:` `i`，看到编辑器左下角进入 'INSERT' 模式
2. 输入下述内容
```shell
server {
        listen 8888;
        server_name localhost;
        location / {
            proxy_pass http://127.0.0.1:4200;
        }
    }
```
> 其中 8888 表示我们希望手机端访问的端口号，而 4200 表示开发环境中 angular 实际的端口号

### 3. 重启 Nginx

```bash
# 重启 nginx 服务
sudo brew services restart nginx
```

### 4. 验证结果

```bash
# 查看本机已经打开的所有端口
netstat -an | grep -i listen
```

输入的结果类似于
```bash
➜  servers showports
tcp4       0      0  *.8888                 *.*                    LISTEN
tcp4       0      0  *.8080                 *.*                    LISTEN
tcp4       0      0  127.0.0.1.4200         *.*                    LISTEN
tcp6       0      0  *.50306                *.*                    LISTEN
tcp4       0      0  *.50306                *.*                    LISTEN
tcp4       0      0  127.0.0.1.4301         *.*                    LISTEN
tcp4       0      0  127.0.0.1.4300         *.*                    LISTEN
```

我们看到第一行就有 `*.8888`，表示成功了！

### 5 手机端访问

确保手机端和 Mac 在同一局域网后，手机端访问 http://192.168.1.102:8888/ 即可

DONE!
