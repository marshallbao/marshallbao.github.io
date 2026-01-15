# Containerd 基础

### 安装

参考

https://github.com/containerd/containerd/blob/main/docs/getting-started.md



### 客户端工具

| Name      | Community             | API    | Target             | Web site                                                     |
| --------- | --------------------- | ------ | ------------------ | ------------------------------------------------------------ |
| `ctr`     | containerd            | Native | For debugging only | (None, see `ctr --help` to learn the usage)                  |
| `nerdctl` | containerd (non-core) | Native | General-purpose    | https://github.com/containerd/nerdctl                        |
| `crictl`  | Kubernetes SIG-node   | CRI    | For debugging only | https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md |

虽然 ctr工 具与 containerd 捆绑在一起，但应该注意的是，ctr 工具仅用于调试 containerd 。nerdctl 工具提供稳定和人性化的用户体验

常用命令

```
# 镜像
# 拉取镜像
ctr images pull docker.io/library/nginx:latest

# 查看镜像
ctr images ls

# 删除镜像
ctr images rm nginx:latest

# tag
ctr images tag nginx:latest my-registry/nginx:v1
```



参考：

https://www.cnblogs.com/Gdavid/p/14913384.html
https://blog.csdn.net/u010157986/article/details/126118897

