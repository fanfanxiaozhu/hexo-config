---
title: 这个 PHP 有点冷
date: 2018-12-10 10:22:12
tags:
toc: true
---


记录一些平时不怎么用到的，有点 cold 的函数或代码片段

## array

### array_walk

> array_walk 也是对数组的一个遍历，可以拿到 key 和 value，并且返回值是 bool 类型

blog
- 博客园 [《【php学习】array_map，array_walk，array_filter的区别》](https://www.cnblogs.com/lhat/p/5882431.html)

example
```php
$a = ["a" => 1, "b" => 2];
$res = array_walk($a, function(&$v, $k) {
  echo $k;
  $v = $v + 1;
});

print_r($res); // true
print_r($a); // ["a" => 2, "b" => 3]
```

### list

使用 list 可实现类似 go 的多值返回
```php
function multi_return() {
  return [200, 'success'];
}

list($code, $msg) = multi_return();

echo $code; // 200
echo $msg; // success
```

## class

### JsonSerializable

> 自定义 json_encode 的实现。

blog
- 鸟哥的博客 [《JsonSerializable接口》](http://www.laruence.com/2011/10/10/2204.html) 

## functions

### register_shutdown_function

> register_shutdown_function — 注册一个会在php中止时执行的函数

example
```php
function shutdown() {
    echo 'php 脚本运行结束';
}

register_shutdown_function('shutdown');

class Controller {
  public function responseJson() {
      echo json_encode(['code' => 200, 'msg' => 'success']);
      exit;
  }
}

$controller = new Controller();
$controller->responseJson();

// {"code":200,"msg":"success"}php 脚本运行结束
```

我们手动触发的 exit 最后会去执行 register_shutdown_function 注册的函数。比如在框架中直接 exit 了之后，进行一些回收动作。

### set_exception_handler

> set_exception_handler — 设置用户自定义的异常处理函数。

设置默认的异常处理程序，用于没有用 try/catch 块来捕获的异常。 在 exception_handler 调用后异常会中止。

example
```php
// 执行完此函数后，运行终止
function exception_handler($exception) {
  echo "自定义异常处理: " , $exception->getMessage(), "\n";
}

set_exception_handler('exception_handler');

throw new Exception('我来抛出一个异常');

echo "异常之后的代码\n"; // 此行代码不会执行
```  
输出 `自定义异常处理: 我来抛出一个异常`。 **在 exception_handler 后调用异常会终止**
