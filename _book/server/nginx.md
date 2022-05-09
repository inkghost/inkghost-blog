# Nginx

![Nginx](img/nginx.jpg)

Nginx (engine x) 是一个高性能的 HTTP 和反向代理 web 服务器，同时也提供了 IMAP/POP3/SMTP 服务。

## 安装 Nginx 服务器

此处使用 `yum` 指令安装 Nginx 服务器。

```shell
yum install -y nginx
```

## 启动 Nginx 服务器

安装后的 Nginx 没有启动，先启动 Nginx 服务器。

```shell
nginx
```

此时，访问 http://<您的域名或 IP> 可以看到 Nginx 的测试页面

> 如果无法访问，请重试用 nginx -s reload 命令重启 Nginx
