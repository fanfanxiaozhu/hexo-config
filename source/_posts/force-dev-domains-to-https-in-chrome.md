---
title: 谷歌浏览器强制 dev 域名使用 https
date: 2018-12-10 14:27:47
tags:
---

## 前言

今天在跑本地项目时，一如既往的配置了 nginx，然后在本地修改了 `/etc/hosts`

nginx server 配置
```text
server {
  listen 80;
  server_name sample-app.dev;
  index index.php index.html;
  
  error_log /var/log/nginx/sample-app-error.log;
  access_log /var/log/nginx/sample-app-access.log;

  root /home/rovast/Code/sample-app/public;

  location / {
    try_files $uri $uri/ /index.php$is_args$args;
  }

  location ~ \.php {
      try_files $uri =404;
      fastcgi_split_path_info ^(.+\.php)(/.+)$;
      include fastcgi_params;
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      fastcgi_param SCRIPT_NAME $fastcgi_script_name;
      fastcgi_index index.php;
      fastcgi_pass 127.0.0.1:9000;
      fastcgi_read_timeout 180;
  }

}
```

/etc/hosts配置
```text
127.0.0.1 sample-app.dev
```

然后打开 chrome 输入 `sample-app.dev`。

奇怪的事情发生了，浏览器自动加上了 https，使得我访问的地址变成了 https://sample-app.dev/ 。 然后无法访问了

## 解决办法

还能怎么办呢，谷歌都说了， dev 是人家的，建议我们用其他的域名后缀。我们用 test 吧！

> 其实在 valet 中，默认的后缀就是 test


nginx server 配置
```text
server {
  listen 80;
  server_name sample-app.test;
  index index.php index.html;
  
  error_log /var/log/nginx/sample-app-error.log;
  access_log /var/log/nginx/sample-app-access.log;

  root /home/rovast/Code/sample-app/public;

  location / {
    try_files $uri $uri/ /index.php$is_args$args;
  }

  location ~ \.php {
      try_files $uri =404;
      fastcgi_split_path_info ^(.+\.php)(/.+)$;
      include fastcgi_params;
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      fastcgi_param SCRIPT_NAME $fastcgi_script_name;
      fastcgi_index index.php;
      fastcgi_pass 127.0.0.1:9000;
      fastcgi_read_timeout 180;
  }

}
```

/etc/hosts配置
```text
127.0.0.1 sample-app.test
```


## 参考资料

- [谷歌论坛](https://productforums.google.com/forum/#!topic/chrome/tDpGgzVX3NA)
- [Chrome & Firefox now force .dev domains to HTTPS via preloaded HSTS](https://ma.ttias.be/chrome-force-dev-domains-https-via-preloaded-hsts/)
