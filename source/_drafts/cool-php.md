---
title: 这个 PHP 有点冷
tags:
---

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

### JsonSerializable

> 自定义 json_encode 的实现。

blog
- 鸟哥的博客 [《JsonSerializable接口》](http://www.laruence.com/2011/10/10/2204.html) 

