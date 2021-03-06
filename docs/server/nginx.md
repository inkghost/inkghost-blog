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

![nginx_index](img/nginx_index.png)

> 如果无法访问，请重试用 nginx -s reload 命令重启 Nginx

## 配置静态服务器访问路径

外网用户访问服务器的 Web 服务由 Nginx 提供，Nginx 需要配置静态资源的路径信息才能通过 url 正确访问到服务器上的静态资源。

打开 Nginx 的默认配置文件 `/etc/nginx/nginx.conf` ，修改 Nginx 配置。

> 若使用 vscode ssh-remote 之类的插件，可以直接按目录进行访问。

```shell
vi /etc/nginx/nginx.conf
```

将默认的 `/usr/share/nginx/html` 更改为自己所需的路径即可。最后再重启 Nginx 让新的配置生效：

```shell
nginx -s reload
```

## Nginx 配置文件基本信息

```shell
main # 全局设置
events { # Nginx工作模式
    ....
}
http { # http设置
    ....

    upstream myproject { # 负载均衡服务器设置
        .....
    }

    server  { # 主机设置
        listen      80; # 用于指定虚拟主机的服务端口。
        server_name localhost www.example.com; # 用来指定IP地址或者域名，多个域名之间用空格分开。
        root        /www/wwwroot/www.example.com; # 全局定义，表示在该server下web的根目录，注意要和locate {}下面定义的区分开来。
        index       index.php index.html index.htm; # 全局定义访问的默认首页地址。
        charset     utf-8; # 设置网页的默认编码格式。
        access_log  logs/host.access.log  main; # 用来指定此虚拟主机的访问日志存放路径，输出格式为main。
        error_log   logs/host.error.log  error; # 错误日志存放路径，输出格式为error。
        error_page  404  /404.html; # 状态码为404时的时候的网页地址,还可定义500,502之类的
        ....

        location / {
            # 简单例子，匹配所有请求
            root   /home/www/html;
            index  index.php index.html index.htm;

            # vue-router、react-router等路由框架要开启history模式可以选择的nginx配置的例子
            try_files $uri $uri/ /index.html;
        }

        location /api {
            # 请求代理
            proxy_pass
        }
    }

    # Settings for a TLS enabled server.
    server {
        # 服务器端口使用443，开启ssl
        listen       443 ssl http2 default_server;
        listen       [::]:443 ssl http2 default_server;

        # 输入你的域名
        server_name  nsuedu.cn;

        # 修改静态文件的路径
        root         /data/www;

        # ssl证书配置

        # 修改证书路径一
        ssl_certificate ".pem";
        # 修改证书路径二
        ssl_certificate_key ".key";

        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  10m;  #缓存有效期
        ssl_ciphers HIGH:!aNULL:!MD5;  #加密算法
        ssl_prefer_server_ciphers on;  #使用服务器端的首选算法

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}
```

负载均衡配置：

weight 轮询(默认)。每个请求按时间顺序逐一分配到不同的后端服务器，如果后端某台服务器宕机，故障系统被自动剔除，使用户访问不受影响，weight 可以指定轮询权值，weight 越大，分配到的访问机率越高，主要用于后端每个服务器性能不均的情况下。

ip_hash，每个请求按访问 IP 的 hash 结果分配，这样来自同一个 IP 的访客固定访问一个后端服务器，有效解决了动态网页存在的 session 共享问题。

fair(第三方)，比上面两个更加智能的负载均衡算法。此种算法可以依据页面大小和加载时间长短智能地进行负载均衡，也就是根据后端服务器的响应时间来分配请求，响应时间短的优先分配。Nginx 本身是不支持 fair 的，如果需要使用这种调度算法，必须下载 Nginx 的 upstream_fair 模块。

url_hash(第三方)。按访问 url 的 hash 结果来分配请求，使每个 url 定向到同一个后端服务器，可以进一步提高后端缓存服务器的效率。Nginx 本身是不支持 url_hash 的，如果需要使用这种调度算法，必须安装 Nginx 的 hash 软件包
