
### 搭建svn服务器

> Subversion 是一个版本控制系统，相对于的 RCS 、 CVS ，采用了分支管理系统，它的设计目标就是取代 CVS 。

* 安装 Subversion
  - yum install -y subversion
* 创建项目版本库
  - mkdir -p /data/svn/myproject
  - svnadmin create /data/svn/myproject

* 配置 SVN 信息
  - 配置文件简介, 版本库中的配置目录 conf 有三个文件:
    - authz 是权限控制文件
    - passwd 是帐号密码文件
    - svnserve.conf 是SVN服务综合配置文件
    
* 配置权限配置文件 authz
  - [groups] admin = admin,root,test 
  - [/] @admin = rw         
  - *=r #用户组权限

* 配置账号密码文件 passwd
  - [users] root = 123456

* 配置 SVN 服务综合配置文件 svnserve.conf
  - anon-access = none#未鉴定的用户无权限访问该仓库
  - auth-access = write#鉴定后的可读写仓库
  - password-db = passwd #使用的密码文件是同级路径的passwd文件
  - authz-db = authz#使用的权限控制文件是同级路径的authz文件
  - realm = myproject #realm 指定版本库的仓库名

* 启动 SVN 服务
  - svnserve -d -r /data/svn  ## -d:指放在后台运行 -r：指定

* checkout SVN项目
  - mkdir -p /data/workspace/myproject
  - svn co svn://127.0.0.1/myproject /data/workspace/myproject --username root --password 123456 --force --no-auth-cache


