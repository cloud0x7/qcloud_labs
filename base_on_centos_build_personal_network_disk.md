
### 基于CentOS搭建个人网盘

> Seafile 是一款开源的企业云盘，注重可靠性和性能。支持 Windows, Mac, Linux, iOS, Android 平台。支持文件同步或者直接挂载到本地访问。

* 安装依赖环境
  - yum install python python-setuptools python-imaging python-ldap python-memcached MySQL-python mariadb mariadb-server
  - 启动 MariaDB 服务：
    - sudo systemctl start mariadb.service
    - sudo systemctl enable mariadb.service
  - 配置 MySQL：
    - /usr/bin/mysql_secure_installation
    - 配置过程输入参数如截图所示，其中 New password 和 Re-enter new password 字段都设置为 RG9Smaok，其他字段一路回车使用


* 安装 Seafile
  - 下载 Seafile 安装包：
    - wget https://mc.qcloudimg.com/static/archive/3d8addbe52be88df4f6139ec7e35b453/seafile-server_5.1.4_x86-64.tar.gz
  - 解压 Seafile 安装包：
    - tar -zxvf seafile-server_5.1.4_x86-64.tar.gz
  - 安装 Seafile 安装包：
    - sudo mkdir -p /opt/seafile/installed
    - sudo mv seafile-server_5.1.4_x86-64.tar.gz /opt/seafile/installed
    - sudo mv seafile-server-5.1.4/ /opt/seafile
    - cd /opt/seafile/seafile-server-5.1.4
    - sudo ./setup-seafile-mysql.sh
  - 执行过程输入参数如截图所示，[server name] 字段输入 Seafile，[ This server's ip or domain ] 字段输入教程第一步申请的域名，[ 1 or 2 ] 字段选择 1，mysql 的 [ root password ] 字段输入 RG9Smaok，其他字段一路回车使用默认值：

* 启动 Seafile 及修改防火墙规则
  - 启动 Seafile
    - sudo ./seafile.sh start
    - sudo ./seahub.sh start
  - 执行过程输入参数如截图所示，其中 [ admin email ] 设置为您登录网盘的帐号，如 admin@qcloudlab.wang，[ admin password ] 和 [ admin password again ] 设置为登录网盘的密码，如 admin_RG9Smaok：

* 修改防火墙规则
  - sudo firewall-cmd --zone=public --permanent --add-port=8082/tcp
  - sudo firewall-cmd --zone=public --permanent --add-port=8000/tcp
  - sudo firewall-cmd --reload

