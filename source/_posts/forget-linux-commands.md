---
title: linux commands 备忘录
date: 2018-12-14 22:19:01
tags:
toc: true
---

linux 常用指令备忘

## netstat

- 查看端口占用 `netstat -tulpn`
> sudo apt-get install net-tools

## redis-cli

- redis-cli 指定 host 和端口号  `redis-cli -h 127.0.0.1 -p 6379`
> sudo apt-get  install redis-tools

## tc

- 指定网卡网络延迟 100ms `sudo tc qdisc add dev eth0 root netem delay 100ms`
- 指定网卡丢包率 10% `sudo tc qdisc add dev eth0 root netem loss 10%`
- 如果已经设置过，可以用 change `sudo tc qdisc change dev eth0 root netem loss 10%`
- 移除网卡延迟或丢包设置 `sudo tc qdisc del dev eth0 root`
