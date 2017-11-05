### CentOS搭建Git服务器

> Git 是一款免费、开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。
此实验以 CentOS 7.2 x64 的系统为环境，搭建 git 服务器。

* 安装依赖库和编译工具
  - yum install curl-devel expat-devel [gettext-devel](http://www.gnu.org/software/gettext/) openssl-devel zlib-devel
* 安装编译工具
  - yum install gcc perl-ExtUtils-MakeMaker
* 下载 git
  - cd /usr/local/src
  - wget https://www.kernel.org/pub/software/scm/git/git-2.10.0.tar.gz
* 解压和编译
  - tar -zvxf git-2.10.0.tar.gz
  - cd git-2.10.0
  - make all prefix=/usr/local/git
* 安装
  - make install prefix=/usr/local/git

* 配置环境变量
  - 将 git 目录加入 PATH
将原来的 PATH 指向目录修改为现在的目录
    - echo 'export PATH=$PATH:/usr/local/git/bin' >> /etc/bashrc
  - 生效环境变量
    - source /etc/bashrc
  - 此时我们能查看 git 版本号，说明我们已经安装成功了
    - git --version

* 创建 git 账号密码
  - useradd -m gituser
  - passwd gituser

* 创建 git 仓库并初始化
  - mkdir -p /data/repositories
  - cd /data/repositories/ && git init --bare test.git

* 配置用户权限
  - 给 git 仓库目录设置用户和用户组并设置权限
    - chown -R gituser:gituser /data/repositories
    - chmod 755 /data/repositories
    - 查找 git-shell 所在目录
 , 编辑 /etc/passwd 文件，将关于 gituser 的登录 shell 配置改为 git-shell 的目录，例如：gituser:x:500:500::/home/gituser:/usr/local/git/bin/git-shell

* 使用搭建好的 Git 服务
  - git clone gituser@{IP}:/data/repositories/test.git

### 补充资料
* expat-devel: The expat-devel package contains the libraries, include files and documentation to develop XML applications with expat.
* perl-ExtUtils-MakeMaker: This utility is designed to write a Makefile for an extension module from a Makefile.PL. It is based on the Makefile.


### 相关资料链接
* [在这里查rpm信息](http://rpmfind.net/linux/rpm2html/search.php)

