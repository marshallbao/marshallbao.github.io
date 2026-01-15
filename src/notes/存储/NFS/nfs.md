# NFS

### 搭建服务端

1、创建 共享目录
mkdir /opt/dev
2、

```shell
yum install nfs-utils  rpcbind
systemctl enable  rpcbind && systemctl start  rpcbind 
systemctl enable  nfs  && systemctl start nfs
```

3、

```shell
vim /etc/exports
/opt/dev        *(rw,sync,insecure,root_squash,no_subtree_check)
```

4、生效配置
 exportfs -a  #刷新配置
 systemctl reload nfs  #重新加载配置
 exportfs -rv #检查配置

### 客户端配置

 1、安装工具
 yum install nfs-utils 
2、手动挂载
mount -t nfs 192.168.100.184:/opt/dev   /data/xx
3、设置
\# 设置开机自动挂载 
\# 在配置文件中添加下面内容即可： 
vim /etc/fstab  

192.168.100.184:/opt/dev  /data/xxx  nfs defaults,_rnetdev 0 0