
### 搭建 ShowDoc 文档工具

> 程序员都很希望别人能写文档，而自己却不愿意写文档。文档的编写和管理影响了团队沟通协作的效率，ShowDoc 是一个非常适合 IT 团队的在线文档分享工具，为提升团队之间的沟通协作效率而生。本实验带您在 centos 系统上搭建基于 Nginx + PHP 的 ShowDoc 文档工具。 

##### 准备 Nginx + PHP 环境
* 安装 Nginx
  - yum install nginx
  - 修改 /etc/nginx/nginx.conf 文件为如下内容：
    
```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80;
        server_name  127.0.0.1;
        root         /var/www/html;
        index index.php index.html
        error_page  404              /404.html;
        location = /40x.html {
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
        }
        location ~ .php$ {
            root           /var/www/html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
        location ~ /.ht {
            deny  all;
        }
    }
}
```

* 启动 Nginx 并设置为开机启动：
  - service nginx start
  - chkconfig nginx on

* 安装 PHP
  - yum install php php-gd php-fpm php-mcrypt php-mbstring php-mysql php-pdo
  - 启动 php-fpm 并设置为开机启动：
    - service php-fpm start
    - chkconfig php-fpm on

* 创建项目
  - 下载安装 Composer。Composer 是 PHP 的一个依赖管理工具，推荐使用 Composer 创建 ShowDoc 项目。
    - curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer

* 设置 Composer 使用国内镜像
  - composer config -g repo.packagist composer https://packagist.phpcomposer.com

* 使用 Composer 创建项目
  - cd /var/www/html/ && composer create-project  showdoc/showdoc

*  设置 showdoc 目录写权限
  - 执行命令赋予 showdoc 下部分目录的写权限
  
```  
chmod a+w showdoc/install
chmod a+w showdoc/Sqlite
chmod a+w showdoc/Sqlite/showdoc.db.php
chmod a+w showdoc/Public/Uploads/
chmod a+w showdoc/Application/Runtime
chmod a+w showdoc/server/Application/Runtime
chmod a+w showdoc/Application/Common/Conf/config.php
chmod a+w showdoc/Application/Home/Conf/config.php
```

* 创建完毕，您现在可以通过浏览器访问 http://{IP}/showdoc/install/ ，进行语言的选择以后即可通过 http://{IP}/showdoc 查看站点效果。