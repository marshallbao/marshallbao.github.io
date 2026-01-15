# IP  命令

### 关于路由表和路由策略

路由表是用来决定如何根据目标地址将数据包发送到合适的下一跳，而路由策略则提供了一种基于不同条件（如源地址、目标地址、接口等）选择路由表的方式。

### 1. 路由表（Routing Table）

路由表是一个包含多个路由条目的数据结构，每个条目指定如何到达某个网络（或目标地址）。通常，每台 Linux 设备都会有至少一个路由表，默认是 `main` 路由表。

```
# 路由表字段解释
ip route show
10.4.1.0/24 dev tun0_master scope link
# 字段解释
10.4.1.0/24 ：目标网段
dev tun0_master：要从 tun0_master 设备出去
scope link：链路范围。表示目标就在这条线上，不需要经过路由器转发，直接 ARP 广播就能找到对方

192.168.150.0/24 dev ens3 proto kernel scope link src 192.168.150.38
# 字段解释
192.168.150.0/24：目标网段
dev ens3：网络设备
proto kernel ：内核自动生成标识
scope link：链路范围
src 192.168.150.38： 设置源i p 为 192.168.150.38

```



路由决策优先级

| **优先级** | **判定标准**         | **说明**                                                |
| ---------- | -------------------- | ------------------------------------------------------- |
| **1**      | **路由策略 (Rules)** | 通过 `ip rule` 查看。决定去查哪张表（main, local 等）。 |
| **2**      | **最长前缀 (LPM)**   | 在选定的表里，找子网掩码最长的条目。                    |
| **3**      | **度量值 (Metric)**  | 前缀长度相同时，选 Metric 最小的（开销最低）。          |

Linux 系统中默认有几个路由表：

- **main**：主路由表，通常包含所有的路由规则。
- **local**：本地路由表，主要处理本机的 IP 地址（例如回环地址）。
- **default**：默认路由表，通常用于没有具体路由匹配时的默认路由。

除了这些预定义的路由表，Linux 还允许你创建自定义的路由表，通常放在 `/etc/iproute2/rt_tables` 文件中。

### 2. 路由策略（Policy Routing）

路由策略是对传统路由决策的一种扩展，它允许根据更复杂的条件来选择使用哪个路由表。通常，我们通过 `ip rule` 来设置路由                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       策略。

```
[root@dev01 ~]# ip rule ls 
0:	from all lookup local 
32766:	from all lookup main 
32767:	from all lookup default 
# 字段解释
左边的数字越小，优先级越高（0 是最高）
它会按照优先级从上往下匹配。匹配到了，就去对应的表
from all 是指所有数据
lookup local/main 是指使用哪个路由表来找

1999:	not from all fwmark 0xca6c lookup 1999
# 字段解释
not from all fwmark 0xca6c： 所有没有 0xca6c 标记的流量
lookup 1999： 都找 1999 路由表
```



#### 路由策略的流程：

1. 当数据包进入 Linux 系统时，**路由策略规则**首先决定它应使用哪个路由表。
2. 根据路由策略规则，系统会查找符合条件的路由表，并使用该路由表来决定如何转发数据包。
3. 如果没有匹配的路由策略规则，系统会默认使用 `main` 路由表。



常用模块

```
# addr

# route
# 以下 3 个命令都是查看 main 路由表的
ip route list
ip route show
ip route show talbe main

# 查看所有表的路由条目
ip route show table 0

# 指定路由表
ip route show table 100

# rule
# 查看路由策略
ip rule ls
ip rule list

```







常用命令

```
临时添加 eth0 的 ip 地址
# ip addr add 192.168.2.199/24 dev eth0

删除 eth0 的 ip 地址
# ip addr del 192.168.2.199/24 dev eth0

```

