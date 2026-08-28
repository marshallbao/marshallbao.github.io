# swap

swap 分区，即交换区    

Swap 空间的作用可简单描述为：当系统的物理内存不够用的时候，就需要将物理内存中的一部分空间释放出来，

以供当前运行的程序使用，那些被释放的空间可能来自一些很长时间没有什么操作的程序，这些被释放的空间被临

时保存到 Swap 空间中，等到那些程序要运行时，再从Swap中恢复保存的数据到内存中。这样，系统总是在物理

内存不够时，才进行Swap交换

通常情况下，Swap 空间应大于或等于物理内存的大小，最小不应小于64M，通常Swap空间的大小应是物理内存

的2-2.5倍，Swap 的调整对Linux服务器，特别是Web服务器的性能至关重要，通过调整Swap，有时可以越过系

统性能瓶颈，节省系统升级费用

### 创建并开启swap

```
# 创建一个空文件，具体大小的话对于小内存机器建议为内存的两倍
sudo mkdir -v /var/cache/swap
cd /var/cache/swap
sudo dd if=/dev/zero of=swapfile bs=1024M count=16
sudo chmod 600 swapfile

# 将新建的文件转换为 swap 文件.
sudo mkswap swapfile

# 启用 swap.
sudo swapon swapfile

# 将该分区设置成开机加载
echo "/var/cache/swap/swapfile none swap defaults 0 0" | sudo tee -a /etc/fstab

# 在线关闭
swapoff -a

# 禁用 swap 
sudo swapoff swapfile

# 去掉开机加载
修改 /etc/fstab 文件

# 查看 swap
swapon -s

# 查看/配置 swap 使用倾向,默认值为 60，可以配置为 30
cat /proc/sys/vm/swappiness
vim /etc/sysctl.conf
vm.swappiness=10
```



参考：

https://www.cnblogs.com/zhongguiyao/p/13963998.html