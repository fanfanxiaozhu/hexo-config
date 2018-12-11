---
title: 在 phpstorm 中使用 xdebug
date: 2018-12-11 14:15:39
tags:
toc: true
---

## 前言

在开发过程中，如果没有 debug，那是无法想象的。目前调试手段大体有如下几种：

1. 代码中直接输出相关信息，可以是 `echo` `var_dump` `var_export` `print_r` `dd`

> dd 这个函数在 laravel 中直接可用。其他框架需要安装拓展。 使用 var_dump 时，最好结合 `<pre>` 标签一起使用

2. 输出调试信息至日志，可以是文件日志、数据库日志等

3、使用 xdebug，单步调试，查看运行时变量等。

以上几点各有各的特点和应用场景，无需分出一个优劣。比如：
- 线上环境，一般不会直接将信息打印出来，更多使用日志调试。例如：微信支付回调 debug
- xdebug 严重损耗性能，一般在本地使用

本文主要讲述如何在 phpstorm 中使用 xdebug 进行调试

## install xdebug extension

1. 进入网址 https://xdebug.org/wizard.php

2. 打开终端， `php -i > info.txt`，将本机 phpinfo 输出到 info.txt 中

3. 拷贝 info.txt 内容到 1 的输入框中，点击框下面的分析按钮

> ubuntu 用户可以使用 gedit 打开，全选复制即可`gedit info.txt`

分析后，得到安装指引：

{% blockquote https://xdebug.org/wizard.php %}
Summary

Xdebug installed: no
Server API: Command Line Interface
Windows: no
Zend Server: no
PHP Version: 7.1.24
Zend API nr: 320160303
PHP API nr: 20160303
Debug Build: no
Thread Safe Build: no
Configuration File Path: /usr/local/lib
Configuration File: /usr/local/lib/php.ini
Extensions directory: /usr/local/lib/php/extensions/no-debug-non-zts-20160303

Instructions

1. Download xdebug-2.6.1.tgz
2. Unpack the downloaded file with `tar -xvzf xdebug-2.6.1.tgz`
3. Run: `cd xdebug-2.6.1`
4. Run: `phpize` (See the FAQ if you don't have phpize.

As part of its output it should show:

Configuring for:
...
Zend Module Api No:      20160303
Zend Extension Api No:   320160303
If it does not, you are using the wrong phpize. Please follow this FAQ entry and skip the next step.

Run: `./configure`
Run: `make`
Run: `cp modules/xdebug.so /usr/local/lib/php/extensions/no-debug-non-zts-20160303`
Edit /usr/local/lib/php.ini and add the line
`zend_extension = /usr/local/lib/php/extensions/no-debug-non-zts-20160303/xdebug.so`
{% endblockquote %}

根据 **Instructions** 的提示安装即可。

```bash
cd xdebug-2.6.1
phpize
make
./configure
make
sudo make install
```

最后得到以下输出信息
```text
Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20160303/

  +----------------------------------------------------------------------+
  |                                                                      |
  |   INSTALLATION INSTRUCTIONS                                          |
  |   =========================                                          |
  |                                                                      |
  |   See http://xdebug.org/install.php#configure-php for instructions   |
  |   on how to enable Xdebug for PHP.                                   |
  |                                                                      |
  |   Documentation is available online as well:                         |
  |   - A list of all settings:  http://xdebug.org/docs-settings.php     |
  |   - A list of all functions: http://xdebug.org/docs-functions.php    |
  |   - Profiling instructions:  http://xdebug.org/docs-profiling2.php   |
  |   - Remote debugging:        http://xdebug.org/docs-debugger.php     |
  |                                                                      |
  |                                                                      |
  |   NOTE: Please disregard the message                                 |
  |       You should add "extension=xdebug.so" to php.ini                |
  |   that is emitted by the PECL installer. This does not work for      |
  |   Xdebug.                                                            |
  |                                                                      |
  +----------------------------------------------------------------------+
```

php.ini 中加入：
```text
zend_extension=xdebug.so
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
xdebug.remote_port = 9001
```

`php -m` 看加载的模块中是否有 xdebug

## config in phpstorm

TODO
1. 解决端口冲突
2. 修改配置后重启 php-fpm
3. 验证配置是否生效
Can't start listening for connections from 'xdebug': Port 9000 is busy
