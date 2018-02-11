
### 体验无服务器云函数

> 无服务器云函数（Serverless Cloud Function）是腾讯云提供的无服务器（serverless）执行环境，您无需购买和管理服务器，而只需使用平台支持的语言编写核心代码并设置代码运行的条件，代码即可在腾讯云基础设施上弹性、安全地运行。本实验带你使用 SCF，轻松生成缩略图。 


* 准备腾讯云 API 调用工具
  - 我们使用 API 命令行工具来管理并运行无服务器云函数（SCF），下面先来安装配置该工具。
  - 安装 Python 和 PIP:Python 环境是腾讯云命令行工具运行时的必要环境。腾讯云的 CentOS 镜像已经包含 Python 的发行版本，可以用下面的命令查看：
    - python --version
    - yum install python-pip -y

* 安装腾讯云 API 命令行工具，命令行工具已经发布到 PIP 中，可以直接用 PIP 进行安装：
  - pip install qcloudcli
  - 命令行工具同时提供了一个自动补全的功能，使用下面的命令进行启用 
    - complete -C "$(which qcloud_completer)" qcloudcli

* 配置 API 密钥
  - 配置命令行工具使用的密钥：
    - qcloudcli configure
  - 工具会要求您提供：
    - Qcloud API SecretId: 粘贴您的 SecretId 
    - Qcloud API SecretKey: 粘贴您的 SecretKey 
    - Region Id: 输入 gz 
    - Output Format：输入 json
  - 配置完成后，测试下使用 CLI 拉取云主机列表：
    - qcloudcli cvm DescribeInstances

* 与 SCF 的初体验
  - 我们使用云 API 来创建一个无服务器云函数（后文统一称为 SCF），并且调用它， SCF 公测期间免费。
  - 创建函数目录
    - 我们第一个 SCF 叫做 hello-scf，下面我们创建一个目录来存放它：
    - mkdir -p /data/hello
    - vim hello.py
```
print('Start Hello World function')
def main_handler(event, context):
    print("value1 = " + event['key1'])
    print("value2 = " + event['key2'])
    return event['key1']  #return the value of key "key1"
```


* 部署 Hello SCF
  - 使用 CreateFunction API
 来创建并部署一个 SCF：
```
qcloudcli scf CreateFunction \
--functionName "hello" \
--code "@$(cd /data/hello && zip -r - * | base64)" \
--handler "hello.main_handler" \
--description "My first scf"
```
  - 部署成功后，会有 Success 的返回，我们也可以使用 ListFunctions
 来查询自己账号下面有哪些 SCF：
qcloudcli scf ListFunctions


* 运行 Hello SCF
  - 现在，可以到控制台查看您的 SCF 函数。在控制台点击名为 hello 的 SCF，然后点击界面右上角的 测试 按钮来测试该函数。

