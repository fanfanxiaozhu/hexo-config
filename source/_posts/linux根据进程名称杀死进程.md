---
title: linux根据进程名称杀死进程
date: 2018-12-04 17:17:21
categories: Linux
tags: [linux, kill]
toc: true
---

我们知道，通过 `kill -9 PID` 命令可以指定 pid 的进程，如果我们只知道进程的名称怎么杀死呢？

> 通过 pkill 也能根据名称杀死，感兴趣的读者可以自行搜索下

### 思路

1. 根据名称找出需要杀死的进程列表 `ps -aux | grep`
2. 列出 pid 列表 `awk`
3. 调用 kill 杀死进程

### 示例

杀死包含名称为 phpstorm 的所有进程（其实就是强制关闭 phpstorm，在 phpstorm 假死时很有用）

1. 先找出名为 phpstorm 的进程列表
```bash
ps -aux | grep phpstorm
```

{% asset_img ps.png ps -aux 结果 %}

2. 打印出 pid 列表
```bash
ps -aux | grep phpstorm | awk '{print $2}'
```

{% asset_img awk.png awk 结果 %}

3. 杀死
```bash
sudo kill -9 $(ps -aux | grep phpstorm | awk '{print $2}')
```

其实就是把结果的列表用 $ 包裹起来作为一个变量，然后 kill -9 会把那些结果都用起来。awk 中的 $2 表示结果的第二列，也就是 id 列，试着改成 $1，就会打印出第一列，即 rovast

### 总结

我们可以加一个函数，便于直接调用，在 `~/.bashrc` 或者是 `~/.zshrc` 中加入下述函数

```bash
function killByName() {
 sudo kill -9 $(ps -aux | grep $1 | awk '{print $2}')
}
```

这样，杀死 phpstorm 可以简化为 `killByName phpstorm`

> 函数中的 $1 表示拿到命令行 killByName 后的第一个参数，即 phpstorm，之后会执行 sudo kill -9 $(ps -aux | grep phpstorm | awk '{print $2}')

这里主要需要注意下述指令

- ps
- grep
- awk

在删除已退出 docker 容器时，也用到了此类方法

```bash
docker rm $(docker ps -a | grep Exit | awk '{print $1}')
```

其实就是把 `docker ps -a | grep Exit` 的第一行结果（就是hashid列表）提取出来，用 `docker rm` 删掉这些结果。 

我们同样可以写一个函数，这里就不写了，需要的童鞋自己根据 killByName 应该能写出来。
