
### 搭建基于 HDFS 碎片文件存储服务

> HDFS 是 Hadoop 的分布式文件系统，HDFS 中的文件在物理上是分块（block）存储的，块的大小可以通过配置参数（ dfs.blocksize）来规定。在 Hadoop 2.x 版本中默认大小是 128M，一般将 1M 的文件定义为碎片文件，该类文件如果需要存储到 HDFS 中的话，需要合并成一个大文件存储在 HDFS 中的一个块中，这样可以节约存储空间。本教程从零开始，带您一步步搭建基于 HDFS 的碎片文件存储服务。

##### 准备 Java 环境
* 安装 JDK
  - HDFS 依赖 Java 环境，这里我们使用 yum 安装 JDK 8，在终端中键入如下命令：
    - yum -y install java-1.8.0-openjdk*
  - 使用如下命令查看下 Java 版本，我们可以验证 JDK 是否已成功安装：
    - java -version

* 配置 Java 环境变量
  - 在编辑器中打开文件 /etc/profile，在文件末尾追加如下内容，配置 Java 环境变量：
    - export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk
    - export PATH=$PATH:$JAVA_HOME/bin
    - export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
  - 然后执行如下命令，让环境变量生效：
    - source /etc/profile
  - 通过如下命令，验证 Java 环境变量是否已成功配置并且生效：
    - echo $JAVA_HOME


##### 配置 SSH
  * 先后执行如下两行命令，配置 SSH 以无密码模式登陆：
    - ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
    - cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
  * 接着可以验证下，在不输入密码的情况下，应该能使用 ssh 命令成功连接本机：
    - ssh 119.29.174.175
  * 紧接着在终端中键入如下命令关闭 ssh 连接：
    - exit

##### 安装 Hadoop
* 首先创建 /data/hadoop 目录，然后进入该目录：
  - mkdir -p /data/hadoop && cd $_
* 接着下载 Hadoop 安装包到该目录下：
  - wget http://archive.apache.org/dist/hadoop/core/hadoop-2.7.1/hadoop-2.7.1.tar.gz
* 解压下载好的 Hadoop 安装包到当前目录：
  - tar -zxvf hadoop-2.7.1.tar.gz
  - 然后将解压后的目录重命名为 hadoop，并且将其移至 /usr/local/ 目录下：
  - mv hadoop-2.7.1 hadoop && mv $_ /usr/local/
* 在编辑器中打开 Hadoop 环境配置文件/usr/local/hadoop/etc/hadoop/hadoop-env.sh，使用 Ctrl + F 搜索如下行 
：export JAVA_HOME=${JAVA_HOME}
将其替换为：export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk
* 查看下 Hadoop 的版本：
  - /usr/local/hadoop/bin/hadoop version
  - 可以看到 Hadoop 的版本信息：Hadoop 2.7.1

##### 修改 Hadoop 配置
* 由于我们的实践环境是在单机下进行的，所以此处把 Hadoop 配置为伪分布式模式。
  - 首先新建若干临时文件夹，我们在后续的配置中以及 HDFS 和 Hadoop 的启动过程中会使用到这些文件夹：
    - cd /usr/local/hadoop && mkdir -p tmp dfs/name dfs/data
  - 修改 HDFS 配置文件 core-site.xml，将 configuration 配置修改为如下内容：
  
```
core-site.xml
<configuration>  
    <property>  
        <name>hadoop.tmp.dir</name>  
        <value>/usr/local/hadoop/tmp</value>
    </property>  
    <property>  
        <name>fs.defaultFS</name>  
        <value>hdfs://localhost:9000</value>
    </property>  
</configuration>
```
  * 修改 HDFS 配置文件 hdfs-site.xml，将 configuration 配置修改为如下内容：hdfs-site.xml

```  
<configuration>    
    <property>    
        <name>dfs.replication</name>    
        <value>1</value>    
    </property>    
    <property>    
        <name>dfs.namenode.name.dir</name>    
        <value>file:/usr/local/hadoop/dfs/name</value>    
    </property>    
    <property>    
        <name>dfs.datanode.data.dir</name>    
        <value>file:/usr/local/hadoop/dfs/data</value>    
    </property>    
    <property>
        <name>dfs.permissions</name>    
        <value>false</value>    
    </property>    
</configuration>
```

* 修改 HDFS 配置文件 yarn-site.xml，将 configuration 配置修改为如下内容：yarn-site.xml

```
<configuration>  
    <property>  
        <name>mapreduce.framework.name</name>  
        <value>yarn</value>  
    </property>  

    <property>  
        <name>yarn.nodemanager.aux-services</name>  
        <value>mapreduce_shuffle</value>  
    </property>  
</configuration>
```



##### 启动 Hadoop
  * 首先进入如下目录：
    - cd /usr/local/hadoop/bin/
  * 对 HDFS 文件系统进行格式化：
    - ./hdfs namenode -format
  * 接着进入如下目录：
    - cd /usr/local/hadoop/sbin/
  * 先后执行如下两个脚本启动 Hadoop：
    - ./start-dfs.sh
    - ./start-yarn.sh
  * 接着执行如下命令，验证 Hadoop 是否启动成功：
    - jps
    - 如果输出了类似如下的指令，则说明 Hadoop 启动成功啦 

```
21713 Jps
21089 SecondaryNameNode
21364 NodeManager
20795 NameNode
20923 DataNode
21245 ResourceManager
```
  * 至此，我们的 HDFS 环境就已经搭建好了。在浏览器中访问如下链接，应该能正常访问（注：若出现安全拦截请选择通过）：http://119.29.174.175:50070/explorer.html#/



##### 接下来，我们实践下如何将碎片文件存储到 HDFS 中。
* 准备碎片文件
  - 首先创建目录用于存放碎片文件，进入该目录：
    - mkdir -p /data/file && cd /data/file
  - 然后执行如下 shell 命令，新建一批碎片文件到该目录下 
    - i=1; while [ $i -le 99 ]; do name=\`printf "test%02d.txt"  $i`; touch "$name"; i=$(($i+1)); done 
    - 在终端中执行 ls 命令，可以看到我们已成功创建了一批碎片文件。

* 将碎片文件存储在 HDFS 中
  - 首先在 HDFS 上新建目录：
    - /usr/local/hadoop/bin/hadoop fs -mkdir /dest
  - 接着我们可以上传碎片文件啦！
* 首先在终端中依次执行以下命令 
  - groupadd supergroup
  - usermod -a -G supergroup root
* 然后将之前创建的碎片文件上传到 HDFS 中：
  - cd /data/file && /usr/local/hadoop/bin/hadoop fs -put *.txt /dest
* 这时，再使用如下命令，我们应该能看到碎片文件已成功上传到 HDFS 中：
  - /usr/local/hadoop/bin/hadoop fs -ls /dest

* 部署完成
  - 访问服务
    - 在浏览器是访问如下链接，可以看到 /dest 目录的文件内容：
http://119.29.174.175:50070/explorer.html#/dest