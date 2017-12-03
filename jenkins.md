
### Jenkins 环境搭建

> Jenkins 是一个开源软件项目，是基于Java开发的一种持续集成工具，用于监控持续重复的工作，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能。

* Java 安装
  - yum -y install java-1.8.0-openjdk-devel

* enkins 安装
  - sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
  - sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
  - 如果您以前从 Jenkins 导入过 key，那么 rpm --import 将失败，因为您已经有一个 key。请忽略，继续下面步骤。
安装
  - yum -y install jenkins

* 启动 Jenkins
  - systemctl start jenkins.service
  - chkconfig jenkins on

* 测试访问
  - Jenkins 默认运行在 8080端口。
稍等片刻，打开 http://{IP}}

* 进入 Jenkins
  - 管理员密码，登入 Jenkins 需要输入管理员密码，按照提示，我们使用以下命令查看初始密码：
cat /var/lib/jenkins/secrets/initialAdminPassword
复制密码，填入，进入 Jenkins。

* 定制 Jenkins
  - 我们选择默认的 install suggested plugins 来安装插件。
* 创建用户
  - 请填入相应信息创建用户，然后即可登入 Jenkins 的世界。