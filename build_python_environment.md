
### 搭建 Python 开发环境

> Python 是一门优雅的面向对象的编程语言, 在人工智能, Web开发, 量化交易等方面有广泛的应用。

* Python是一种解释型、面向对象、动态数据类型的高级程序设计语言。首先我们来看看系统中是否已经存在 Python ，并安装一些开发工具包:
安装前准备查看当前系统中的 Python 版本，可以看到实验室的这台服务器已经安装了 Python 2.6.6
    - python --version
  - 检查 CentOS 版本，我们可以看到这台服务器的 CentOS的版本是CentOS release 6.8
    - cat /etc/redhat-release
  - 为了避免后续安装出错，我们先来安装开发工具包先安装 Development Tools
    - yum groupinstall -y "Development tools"
  - 然后安装其它的工具包
    - yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel

* 下载、编译和安装 Python 2.7.13
  - yum 源中没有新版 Python ，我们到官网中下载 Python 2.7.13
    - wget https://mc.qcloudimg.com/static/archive/b577469e4ed03782eb1f62e8fd6125a5/Python-2.7.13.tar.gz
  - 下载完成后，解压这个安装包
    - tar zxvf Python-2.7.13.tar.gz
  - 进入文件夹 Python-2.7.13
    - cd Python-2.7.13
  - 执行 configure 文件预编译
    - ./configure
  - 编译和安装
    - make && make install

* 配置 Python
  - 更新系统默认 Python 版本，先把系统默认的旧版 Python 重命名
    - mv /usr/bin/python /usr/bin/python.old
  - 再删除系统默认的 python-config 软链接
    - rm -f /usr/bin/python-config
  - 最后创建新版本的 Python 软链接
    - ln -s /usr/local/bin/python /usr/bin/python
    - ln -s /usr/local/bin/python-config /usr/bin/python-config
    - ln -s /usr/local/include/python2.7/ /usr/include/python2.7
  - 编辑 /usr/bin/yum 文件，把代码第一行的 python 改为指向老的 python2.6 版本
    - yum
  - 再查看 Python 版本，现在我们看到的已经是最新版了
    - python --version


* 为新版 Python 安装一些工具
  - 为新版 Python 安装 pip
    - curl https://bootstrap.pypa.io/get-pip.py | python

* 使用 pip 安装第三方库 requests
  pip install requests