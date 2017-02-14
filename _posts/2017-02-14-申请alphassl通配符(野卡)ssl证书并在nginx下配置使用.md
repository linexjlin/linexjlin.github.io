---
layout: post
title: 申请alphassl通配符(野卡)ssl证书并在nginx下配置使用
---
## 流程

1. 生成csr 和 key
```openssl req -new -newkey rsa:2048 -sha256 -nodes -out example_com.csr -keyout example_com.key -subj "/C=CN/ST=Beijing/L=Beijing/O=Example Inc./OU=Web Security/CN=*.example.com" ``` 
得到 example_com.csr example_com.key.
1. 将CSR提交给[alphassl](https://assl.loovit.net/)， 得到 crt证书: example_com.crt
1. 补全crt 证书
由于手机上没有预留alphassl的中级证书， 我们需要将中级证书附加到example_com.crt 后面。 中级证书在邮件里有， 在[这里](https://www.alphassl.com/support/install-root-certificate.html) 也可以找到。

1. 验证crt与key 格式是否一样
```
openssl x509 -noout -modulus -in example_com.crt | openssl md5
openssl rsa -noout -modulus -in example_com.key | openssl md5
```
key and crt md5sum must be the same. These files can be configured in directory.


## nginx 的配置
```
    server {
        listen       443 ssl;
        server_name  eg.example.com;

        ssl_certificate      /etc/nginx/ssl/example_com.pem;
        ssl_certificate_key  /etc/nginx/ssl/example_com.key;


        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
        error_log /var/log/nginx/example.error.log;

        location / {
            autoindex on;
            root /home/d/dir;
        }
    }
```
