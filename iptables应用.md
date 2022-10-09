# iptables应用

iptables和netfilter的关系

iptables是linux的一个管理工具，用户使用iptables去管理真正的netfilter的安全框架，netfilter位于内核空间中，所以说是在linux内核中实现过滤的

## 四表五链

四表（按顺序）

raw表：关闭nat表上启用的连接追踪机制（output、prerouting）

mangle表：拆解报文，做出修改，并重新封装的功能（五个链都有）

nat表：network address translation，网络地址转换功能（prerouting、postrouting、output）

filter表：负责过滤功能，防火墙（input、output、forward）

五链

prerouting：对数据包作路由选择前用此链中的规则

input：进来的数据包应用此链中的规则

forward：转发数据包是应用此链中的规则

output：外出的数据包应此链中的规则

postrouting：对数据包作路由选择后应的规则

![img](https://img-blog.csdnimg.cn/img_convert/176a08b75cbd765fe0bdf5d2e4ef0f31.png)

常用的nat表、filter表，默认filter

命令：

```

iptables [-t table] COMMAND [chain] CONDITION -j ACTION
 
-t table   是指'操作的表',filter、nat、mangle或raw,'默认使用filter'
 
COMMAND    '子命令',定义'对规则的管理'（-A末尾添加 -D 删除
-I插入-R修改 -F清空 -n数字显示结果）
 
chain      指明'链路'
 
CONDITION  匹配的'条件或标准'
 -p指定协议一般的tcp、udp、icmp
 -s源地址
 -d目标地址
 -i 网卡
 -sport来源的端口
 -dport目标的端口
ACTION     匹配后'操作动作'
-j
  accept：经数据包放行，跳往下一个规则链
  drop：丢弃不在处理，丢完之后不在进行规则匹配
  reject：阻拦该数据包，但是会通知对方，中断过滤规则
  log：记录信息之后，将会继续对比其他规则
```

eg：

1.拒绝进入防火墙的所有ICMP协议数据包

iptables -I INPUT -p icmp -j REJECT

2.允许防火墙转发除ICMP协议以外的所有数据包

iptables -A FORWARD -p ! icmp -j ACCEPT

3.拒绝转发来自192.168.1.10主机的数据，允许转发来自192.168.0.0/24网段的数据

iptables -A FORWARD -s 192.168.1.11 -j REJECT 
iptables -A FORWARD -s 192.168.0.0/24 -j ACCEPT

4.封堵网段（192.168.1.0/24），两小时后解封。

iptables -I INPUT -s 10.20.30.0/24 -j DROP 
iptables -I FORWARD -s 10.20.30.0/24 -j DROP 
at now 2 hours at> iptables -D INPUT 1 at> iptables -D FORWARD 1

5.只允许管理员从202.13.0.0/16网段使用SSH远程登录防火墙主机。

iptables -A INPUT -p tcp --dport 22 -s 202.13.0.0/16 -j ACCEPT 
iptables -A INPUT -p tcp --dport 22 -j DROP

6.允许本机开放从TCP端口20-1024提供的应用服务。

iptables -A INPUT -p tcp --dport 20:1024 -j ACCEPT 
iptables -A OUTPUT -p tcp --sport 20:1024 -j ACCEPT

7.禁止转发来自MAC地址为00：0C：29：27：55：3F的和主机的数据包

iptables -A FORWARD -m mac --mac-source 00:0c:29:27:55:3F -j DROP

8.禁止转发与正常TCP连接无关的非—syn请求数据包。

iptables -A FORWARD -m state --state NEW -p tcp ! --syn -j DROP