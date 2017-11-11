
### 基于 CentOS 搭建 FTP 文件服务

> FTP 是一个很实用的文件传输协议，方便在客户端和服务器之间进行文件的传输。本实验带您使用 vsftpd 来搭建一个 FTP 服务，并且创建专有的 FTP 登录账户，保障服务器安全。

* 安装并启动 FTP 服务
  - 安装 VSFTPD 
    - yum install vsftpd -y

* 启动 VSFTPD
  - service vsftpd start
  - 启动后，可以看到系统已经监听了 21 端口
    - netstat -nltp | grep 21
    - 此时，访问 ftp://{ip} 可浏览机器上的 /var/ftp 目录了。

* 配置 FTP 权限
  - 目前 FTP 服务登陆允许匿名登陆，也无法区分用户访问，我们需要配置 FTP 访问权限
  - 了解 VSFTP 配置
    - vsftpd 的配置目录为 /etc/vsftpd，包含下列的配置文件：
    - vsftpd.conf 为主要配置文件
    - ftpusers 配置禁止访问 FTP 服务器的用户列表
    - user_list 配置用户访问控制

* 阻止匿名访问和切换根目录
  - 匿名访问和切换根目录都会给服务器带来安全风险
，我们把这两个功能关闭。编辑 /etc/vsftpd/vsftpd.conf，找到下面两处配置并修改：
    - anonymous_enable=NO  # 禁用匿名用户
    - chroot_local_user=YES  # 禁止切换根目录
    - service vsftpd restart # 重启

* 创建 FTP 用户
  - useradd ftpuser
  - echo "tco_E3Ci" | passwd ftpuser --stdin
* 限制该用户仅能通过 FTP 访问
  - usermod -s /sbin/nologin ftpuser

* 为用户分配主目录
  - mkdir -p /data/ftp/pub    
    - /data/ftp 为主目录, 该目录不可上传文件
    - /data/ftp/pub 文件只能上传到该目录下

* 创建登录欢迎文件 
  - echo "Welcome to use FTP service." > /data/ftp/welcome.txt
* 设置访问权限：
  - chmod a-w /data/ftp && chmod 777 -R /data/ftp/pub
* 设置为用户的主目录：
  - usermod -d /data/ftp ftpuser



