### iptables 基础

> 通过实验室环境实操学习 iptables 基础知识，练习基本的操作命令并完成一系列基础的网络配置。

* iptables 基本命令
  - iptables 可以简单理解为 Linux 系统内核级防火墙 netfilter 的用户态客户端。
  - Linux 管理员通过调用 iptables 命令，配置 Linux 内核 netfilter 模块规则，对网络数据包的流动进行管理。

* 查看 iptables 表的规则
  - iptables 有表 (tables)的概念，每张表又包含不同链 (chains)，大部分情况下我们仅需要使用 filter 和 nat 两张表的链就可以完成功能。
  - 使用以下命令可以查看 filter 表中的规则。
    - sudo iptables -L -n
    - 参数解析 -L 列出规则， -n 不显示域名，命令默认显示的是 filter 表，可以通过 -t 参数来指定其他表
    - 可以看到目前 filter 表中有 3 条链: INPUT, FORWARD, OUTPUT。 3 条链都没有任何规则存在，因此通过网卡的网络数据包不会受到任何影响。

* 实验
  - 请使用 iptables 命令查看 nat 表的规则，并输出到 /home/ubuntu/iptables_nat
    - sudo iptables -L -n -t nat > /home/ubuntu/iptables_nat

* 场景：禁止访问目的地址
  - 添加一条规则到 Filter 表,在没有任何防火墙规则时，尝试 ping 百度的服务器，ping 是可以正常返回的。假设我们作为 Linux 管理员，不希望有人从本机 ping 百度的服务器，那么就可以用到如下的 iptables 命令：
    - sudo iptables -I OUTPUT -p icmp -d www.baidu.com -j DROP
    - 参数解析 -I 添加规则到链的最前面， -p 匹配协议， -d 匹配目的地址， -j DROP 将匹配的数据包实施丢掉动作
  - 重要:
    - 因为在 iptables 一个链中的规则是从上到下依次执行的，因此一条规则在链中的位置十分重要。
  - 例如一条拒绝全部连接的规则 -j REJECT 一定要放在链的最后，而允许通行的规则要放在这条规则之前。否则所有网络数据包进入链后直接匹配到这条规则，将导致所有连接被拒绝。
  - 现在我们可以用 sudo iptables -L -n 命令看到 filter 表的 OUTPUT 链中新增了两条记录，其中 destination 地址是 DNS 返回的百度服务器 IP 地址。
  
```  
Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
DROP       icmp --  0.0.0.0/0            220.181.112.244
DROP       icmp --  0.0.0.0/0            220.181.111.188
```

* 这时再 ping 百度服务器会发现无法 ping 通了，因为访问百度服务器 IP 地址的数据包已经被防火墙过滤丢掉了


* 场景：导出、编辑、导入规则
  - 在进行一系列复杂的防火墙配置时，大多数时候不建议直接调用 iptables 做规则修改。 因为任何一条错误的配置或者一个 typo （输入错误）都有可能导致严重的网络问题。一个最佳实践是导出现有规则到文本文件，对该文本文件进行编辑，进行检查无误后再将其导入。
导出规则。通过如下命令导出所有规则到文本文件
    - sudo iptables-save > /home/ubuntu/iptables_rules

* 使用命令将编辑过的规则重新导入 iptables
  - sudo iptables-restore /home/ubuntu/iptables_rules
    - 使用 sudo iptables -L -n 查看是否有禁止 ping 114.114.114.114 的新增规则

* 清除全部规则
  - 现在我们清除上一小节中增加的规则，还原到没有任何规则的初始状态。Note：当你因为错误的配置 iptables 导致自己无法远程访问的时候，从 console 执行这条命令可以快速修复状态。
    - sudo iptables -F
    - 查看 iptables 规则，发现前面定义的规则已经被清除。
尝试 ping 百度服务器和 114.114.114.114，都恢复到可以 ping 的状态。iptables 还提供 -X 参数清除自定义链
， -Z 参数重置计数器


* iptables进阶场景：黑/白名单
  - 某公司禁止特定的 PC 设备访问和工作无关的网站，而其他设备则不受限制。
  - 分析：
    - 1.首先明确是对外访问限制，所以应该编辑 iptables OUTPUT 链进行限制
    - 2.其次，由于目的地址可能是数百个网站，针对每一台PC配置数百条规则是不可接受的，因此我们选择使用自建一条链来实现。
    - 3.公司只限制部分 PC 访问，因此对 PC 的匹配最好使用 IP 地址匹配，不匹配的设备则不受限制。
  - Note: 对于进入 INPUT 链的入站规则还可以采用 mac 匹配。

```
实验：
假设本机作为一台网关使用，被限制的目标网站有 114.114.114.114 和 220.181.111.188，被限制访问的 PC 就是本机。
查看本机 IP 地址。
sudo ip a show eth0
在 inet 之后的就是本机网口的 IP 地址，例如 10.135.166.86
编辑 iptables 规则：
//清除已有规则
sudo iptables -F
//清除自建链
sudo iptables -X
//新建一个叫做 BLACKLIST 的链
sudo iptables -N BLACKLIST
//当访问的源 IP 地址是本机的时候进入 BLACKLIST 链
sudo iptables -A OUTPUT -s 10.135.166.86 -j BLACKLIST
//向 BLACKLIST 链添加 2 条规则
sudo iptables -A BLACKLIST -d 114.114.114.114 -j DROP
sudo iptables -A BLACKLIST -d 220.181.111.188 -j DROP
如上配置后，会发现本机已经无法 ping BLACKLIST 中的目的地址了。
如果后续要增加新的禁止访问的目的地址，则只需向 BLACKLIST 链添加新规则。
如果需要限制另一台 PC 通过本网关转发访问外网，则只需添加一条 OUTPUT 链的源 IP 匹配规则并指向 -j BLACKLIST。
```


```
iptables进阶场景：黑/白名单
某公司禁止特定的 PC 设备访问和工作无关的网站，而其他设备则不受限制。
分析：
1.首先明确是对外访问限制，所以应该编辑 iptables OUTPUT 链进行限制
2.其次，由于目的地址可能是数百个网站，针对每一台PC配置数百条规则是不可接受的，因此我们选择使用自建一条链来实现。
3.公司只限制部分 PC 访问，因此对 PC 的匹配最好使用 IP 地址匹配，不匹配的设备则不受限制。
Note: 对于进入 INPUT 链的入站规则还可以采用 mac 匹配。
实验：
实验：
请添加一条规则，限制源 IP 为 123.123.123.123 的 PC 访问 BLACKLIST 中的所有目的地址。
```

```
进阶场景：端口映射
分析： 通常来讲应该首先考虑程序本身能否配置监听多个端口，或者使用负载均衡器作为代理程序。 但在本次实验中我们将使用 iptables 监听 8080 端口并将网络数据包转发给本地80端口
实验：
首先测试 telnet 0 80 和 telnet 0 8080 可以看到 80 和 8080 端口都是不通的。
接下来我们用 netcat 监听 80 端口：
sudo nc -k -l 80 &
这时候 telnet 0 80 发现 80 端口可以工作了。
退出telnet需要按 Ctrl + ] （右方括号） ，再按 q 回车。
下面我们用 iptables 做一个端口映射
sudo iptables -t nat -A OUTPUT -p tcp -d 127.0.0.1 --dport 8080 -j DNAT --to 127.0.0.1:80
这里我们用到了 -t nat 参数，表示我们使用了 netfilter 的 nat 表。在 nat 表的 OUTPUT 链上做了一个 -j DNAT 转发，将访问内网 8080 端口的数据包转向了 80 端口。
这时候 telnet 0 8080 发现 8080 端口也可以工作了。
如果是监听外网的 8080 端口转发到 80 端口，则需要执行以下命令：
sudo iptables -t nat -A PREROUTING -p tcp --dport 8080 -j REDIRECT --to-ports 80
通过使用 PREROUTING 链直接将访问 8080 端口的数据包转发到 80 端口
```



