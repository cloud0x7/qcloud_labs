
### 基于 CentOS 7 搭建 GitLab

> GitLab 是一款基于 Web 的 Git 仓库管理软件，提供代码管理、Wiki、Issue 跟踪等功能。GitLab 相当于可以安装在自有服务器上的 GitHub，非常适合团队开发者使用。

##### 准备环境
* 在正式开始安装之前，先更新软件包并打开相关服务的权限。
    - yum update -y
* 安装 sshd
  - yum install -y curl policycoreutils-python openssh-server
* 启用并启动 sshd：
  - systemctl enable sshd
  - systemctl start sshd

* 配置防火墙
  - 打开 /etc/sysctl.conf 文件，在文件最后添加新的一行并保存：
    - net.ipv4.ip_forward = 1
  - 启用并启动防火墙：
    - systemctl enable firewalld
    - systemctl start firewalld
  - 放通 HTTP：
    - firewall-cmd --permanent --add-service=http
  - 重启防火墙：
    - systemctl reload firewalld
  - 在实际使用中，可以使用 systemctl status firewalld 命令查看防火墙的状态。

* 安装 postfix
  - GitLab 需要使用 postfix 来发送邮件。当然，也可以使用 SMTP 服务器，具体步骤请参考 官方教程。
    - yum install -y postfix
  - 打开 /etc/postfix/main.cf 文件，找到 inet_protocols = all，将 all 改为 ipv4 并保存：
  - 启用并启动 postfix：
    - systemctl enable postfix 
    - systemctl start postfix

* 配置 swap 交换分区
  - 由于 GitLab 较为消耗资源，我们需要先创建交换分区，以降低物理内存的压力。在实际生产环境中，如果服务器配置够高，则不必配置交换分区。
  - 新建 2 GB 大小的交换分区：
    - dd if=/dev/zero of=/root/swapfile bs=1M count=2048
  - 格式化为交换分区文件并启用：
    - mkswap /root/swapfile
    - swapon /root/swapfile
  - 添加自启用。打开 /etc/fstab 文件，在文件最后添加新的一行并保存：
    - /root/swapfile swap swap defaults 0 0

* 安装 GitLab
  - 将软件源修改为国内源
由于网络环境的原因，将 repo 源修改为清华大学
。在 /etc/yum.repos.d 目录下新建 gitlab-ce.repo 文件并保存。内容如下：

```
gitlab-ce.repo
12345
[gitlab-ce]name=Gitlab CE Repositorybaseurl=https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el$releasever/gpgcheck=0enabled=1
```

* 刚才修改过了 yum 源，因此先重新生成缓存：
（此步骤执行时间较长，一般需要 3~5 分钟左右，请耐心等待）
  - yum makecache
* 安装 GitLab：
（此步骤执行时间较长，一般需要 3~5 分钟左右，请耐心等待）
  - yum install -y gitlab-ce

* 配置 GitLab 的域名（非必需）
  - 打开 /etc/gitlab/gitlab.rb 文件，在第 13 行附近找到 external_url 'http://gitlab.example.com'，将单引号中的内容改为自己的域名（带上协议头，末尾无斜杠），并保存。
例如：external_url 'http://work.myteam.com'记得将域名通过 A 记录解析到IP 哦。

* 初始化 GitLab
  - 使用如下命令初始化 GitLab：（此步骤执行时间较长，一般需要 5~10 分钟左右，请耐心等待）
    - sudo gitlab-ctl reconfigure
    