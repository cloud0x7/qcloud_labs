
### 搭建LNMP环境

> LNMP是建立web应用的平台，是Linux、Nginx，MySQL（有时也指MariaDB，数据库软件） 和PHP（有时也是指Perl或Python） 的简称。

* 搭建 Nginx 静态服务器
  - 安装 Nginx 
    - yum install nginx -y
  - 修改 /etc/nginx/conf.d/default.conf，去除对 IPv6 地址的监听，可参考下面的代码示例：default.conf

```
server {
    listen       80 default_server;
    # listen       [::]:80 default_server;
    server_name  _;
    root         /usr/share/nginx/html;

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
```

* 启动 Nginx：
  - nginx
  - 此时，可访问实验机器外网 HTTP 服务（http://{ip}）来确认是否已经安装成功。
  - 将 Nginx 设置为开机自动启动：
    - chkconfig nginx on


* 安装 MySQL
  - yum install mysql-server -y
  - 安装完成后，启动 MySQL 服务：
    - service mysqld restart
  - 设置 MySQL 账户 root 密码：
    - /usr/bin/mysqladmin -u root password 'RYemCIQS'
  - 将 MySQL 设置为开机自动启动：
    - chkconfig mysqld on


* 安装 PHP
  - yum install php php-fpm php-mysql -y
  -  安装之后，启动 PHP-FPM 进程：
    - service php-fpm start
  - 启动之后，可以使用下面的命令查看 PHP-FPM 进程监听哪个端口 
    - netstat -nlpt | grep php-fpm
  - 把 PHP-FPM 也设置成开机自动启动：
    - chkconfig php-fpm on
  - 配置 Nginx
    -在 /etc/nginx/conf.d 目录中新建一个名为 php.conf 的文件，并配置 Nginx 端口 ，配置示例如下：
  - 修改配置完成后，重启 nginx 服务
     - service nginx restart
  - 这时候，我们就可以在/usr/share/php 目录下新建一个 info.php 文件来检查 php 是否安装成功了.此时，访问 http://{ip}:8000/info.php 可浏览到我们刚刚创建的 info.php 页面了


```
## php.conf
server {
    listen 8000;
    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    location ~ .php$ {
        root           /usr/share/php;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```



