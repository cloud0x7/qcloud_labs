
### Maven 安装与入门

> Apache Maven，是一个软件项目管理及自动构建工具，由 Apache 软件基金会所提供。基于项目对象模型（缩写：POM）概念，Maven 利用一小段描述信息能管理一个项目的构建、报告和文档等步骤。

* Java 安装
  - yum -y install java-1.8.0-openjdk-devel
  
* Maven 下载
  - 我们可以从官网下载页获取最新的下载链接（Binary tar.gz archive）。
  - cd /home
  - wget http://mirror.bit.edu.cn/apache/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz
  - 解压压缩包：
    - tar xzvf apache-maven-3.5.0-bin.tar.gz
    - mv apache-maven-3.5.0  /usr/local/apache-maven

* 配置环境变量
  - 编辑 /etc/profile，在最下方添加并执行source /etc/profile

```
MAVEN_HOME=/usr/local/apache-maven
export MAVEN_HOME
export PATH=${PATH}:${MAVEN_HOME}/bin
```

* 检查 Maven 是否成功安装：
  - mvn -version

* Maven 简单使用
  - 我们可以通过 archetype:generate 命令快速构建出项目骨架。
Hello World
  - mvn archetype:generate -DgroupId=helloworld -DartifactId=helloworld
 mvn 指令首次执行时，会从远程“中央仓库”下载一些必需的文件，请耐心等待。

* 项目结构
  - /pom.xml 为项目对象模型（Maven 项目配置）
  - /src/main/java 用于存放源代码
  - /src/test/java 用于存放单元测试代码
  - /src/target 用于存放编译、打包后的输出文件
  - 
  
* 编译
  - cd /home/helloworld
  - mvn compile
  - 重新开启 helloworld 项目文件夹，可以看到生成 target 目录。

* 运行
  - 你可以使用 mvn 指明 mainClass 来运行项目：
    - mvn exec:java -Dexec.mainClass="helloworld.App"
  - 完成后可看到终端输出了：Hello World!

* 测试
  - mvn test

* 打包
  - mvn package
  - 重新开启 target 目录，可看到生成了 .jar 文件。
  - 从输出的日志可以看到，执行 package 前，会先执行 compile 及 test，最后执行了打包。