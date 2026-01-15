# Service

### service 类型

ClusterIP

LoadBalancer







### 关于负载均衡器（LB/Load Balancer）控制器

在云环境云平台已经给你适配了 LB 控制器， 所以你只要指定 service 的 type 为 LoadBalancer，就可以创建出来；但是本地环境如果没有 LB 控制器就无法创建，处于一直 Pending 的状态；有个小技巧可以创建一个伪 LB 类型的 service，那就是通过 externalIPs 字段指定一个集群 node 的 ip，示例如下

```
# get node
NAME   STATUS   ROLES           AGE      VERSION   INTERNAL-IP    
dn0    Ready    control-plane   4y262d   v1.32.9   192.168.150.40
dn1    Ready    <none>          4y262d   v1.32.9   192.168.150.41   

# 伪 lb 类型的 service --> 使用 ClusterIP + externalIPs
# 只有一个service 的 port
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP                                 PORT(S)        AGE
nginx-service-x    ClusterIP      10.43.32.17    192.168.150.41                              82/TCP         16h

# 伪 lb 类型的 service --> 使用 LoadBalancer + externalIPs 
# 会有 service 的 port + nodeport 的 port
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP                                 PORT(S)        AGE
turbo-gateway-lb           LoadBalancer   10.105.17.192    192.168.150.41   58080:30772/TCP      2y21d

# 伪 lb 类型的 service --> 使用 NodePort + externalIPs 
这种没试过...；应该也没问题

# 以上两种用法都可以，下面是错误用法， LoadBalancer 类型的 service(没有指定externalIPs) ；会一直 pending
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP                                 PORT(S)        AGE
turbo-gateway-test         LoadBalancer   10.101.136.194   <pending>        58080:30360/TCP      24s
```

### 原理

externalIPs 是如何工作的？

当你在 Service 中指定了 `externalIPs`（即便类型是 `LoadBalancer` 或 `ClusterIP`），集群内**每一个节点**的 `kube-proxy` 都会执行以下操作：

- 在节点的路由表或防火墙（IPtables 或 IPVS）中增加规则。
- 监听目的地为这些 `externalIPs` 且端口匹配的流量。
- 将这些流量转发到对应的后端 Pod。

当你填入某个 Node IP 时，发往该节点该端口的流量会被 `kube-proxy` 拦截并重定向到 Service 的后端。

- **优点：** 简单，不需要外部组件就能让外部流量进入。
- **缺点/风险：** * **无高可用性：** 如果该节点宕机，即便其他节点还活着，发往这个特定 IP 的流量也会中断。
  - **IP 冲突风险：** 你实际上是在“劫持”发往该 IP 特定端口的流量。

关于 K3s 内置的 ServiceLB (Klipper LB)

当你在 k3s 集群创建 lb 类型的 service 时，会调用 k3s 内置的 LB 控制器，会在所有 node 创建出一个 pod 来代理流量；这样会占用大量端口；所以也可以使用上面的方法，直接创建 ClusterIP 类型的 service + externalIPs，不调用 k3s 的 LB；也可以在安装的时候不安装这个组件，使用参数 --disable servicelb

