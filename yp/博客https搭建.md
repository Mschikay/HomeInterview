---
title: 博客https搭建
date: 2018-02-01 09:34:59
tags:
---

# 博客https搭建

## 0x01 简单描述
首先我采用的是免费的https证书[Let’s Encrypt](https://letsencrypt.org/)
自动化脚本地址[certbot](https://certbot.eff.org/)

## 0x01 安装步骤
1. 首先去选择好版本。这里选择nginx+ubuntu16.04
运行以下脚本
```bash
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install python-certbot-nginx
```

2. 停止nginx
```bash
service nginx stop
```

3. 生成证书
```bash
certbot certonly --standalone --email your@email.com -d yourdomain.com
```

4. 在下面的文件夹中生成了证书
```bash
Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/youdomain.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/yourdomain.com/privkey.pem

   /etc/letsencrypt/live/blog.t1m00n.tk/fullchain.pem
   /etc/letsencrypt/live/yourdomain.com/privkey.pem
```

5. 配置nginx
参考一个在线网站，直接复制即可
[nginxconfig](https://nginxconfig.io/#!?https)
其中，80端口跳转增加
```txt
rewrite ^(.*) https://$server_name$1 permanent;
```

6. 重启nginx
