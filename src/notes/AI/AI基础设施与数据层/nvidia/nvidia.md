# GPU

### Docker 环境配置 GPU

1、nvidia 驱动的安装（配置服务器显卡环境）

```
# 查看显卡型号
lspci |grep -i nvidia

# 登录 nvidia 官网，根据显卡型号下载显卡驱动
# 或者是通过 
ubuntu-drivers devices|grep recommended 
# 查看安装哪个驱动合适
# 安装驱动并重启服务器，要提前配置源
apt install -y nvidia-driver-580-open

reboot

# 测试
nvidia-smi
# 以上步骤完成，说明服务器的显卡运行环境已经 ok 了，可以使用 gpu 来进行计算了；如果容器想调用服务器的显卡来计算，还需要进行以下操作
```

2、配置 docker 调用显卡

```
# 配置国内源
# 1.加key
curl -fsSL https://mirrors.ustc.edu.cn/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg

# 2.生成list
curl -s -L https://mirrors.ustc.edu.cn/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
  sed 's#deb https://nvidia.github.io#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://mirrors.ustc.edu.cn#g' | \
  sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
  
# 3.更新
apt-get update

# 安装配置工具
apt install -y nvidia-container-toolkit
nvidia-ctk runtime configure --runtime=docker
systemctl restart docker

# 查看
docker info | grep -i runtime
```

3、测试

```
docker run -d \
  --gpus all \
  sophiacloud/bge-m3-service:cuda
 
# --gpus，这个参数能识别说明没问题了
```



### nvidia-smi 使用

```
root@iZ0jl1uil793vuw9pxvsgmZ:~# nvidia-smi
Wed Mar 11 10:43:18 2026       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 580.126.09             Driver Version: 580.126.09     CUDA Version: 13.0     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA A10                     Off |   00000000:00:03.0 Off |                    0 |
|  0%   38C    P0             56W /  150W |   14261MiB /  23028MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A            8050      C   /opt/venv/bin/python3                  1622MiB |
|    0   N/A  N/A           13609      C   /usr/bin/python3                       2348MiB |
|    0   N/A  N/A           13661      C   VLLM::EngineCore                      10272MiB |
+-----------------------------------------------------------------------------------------+

# 实时监控
watch -n 1 nvidia-smi

# 查询完整信息
nvidia-smi -q

# 指定查询字段
nvidia-smi --query-gpu=index,name,memory.total,memory.used --format=csv
```





### 主机安装 cuda 10.2

如果在主机上直接搭建 cuda 平台，可直接下载 cuda rpm 包，进行安装，因为 cuda 安装包内含 nvidia 驱动，可提前将机器上的 nvidia 驱动删除；

```
rpm -i cuda-repo-rhel7-10-2-local-10.2.89-440.33.01-1.0-1.x86_64.rpm
yum clean all
rpm -i vulkan-filesystem-1.1.73.0-1.el7.noarch.rpm
rpm -i dkms-2.3-5.8.noarch.rpm
yum install nvidia-driver-latest-dkms cuda
```

即安装完成，安装完成后要 reboot 节点，防止内核驱动和系统驱动不一致导致运行 nvidia-smi 命令错误；（也可能是之前遗留的驱动）


```
cat /proc/driver/nvidia/version   #查看驱动使用的内核版本

rpm -qa |grep nvidia  #查看刚安装的驱动

# 如遇到以下报错（也可以直接重启，也可以按照下面步骤操作）

$ nvidia-smi 
Failed to initialize NVML: Driver/library version mismatch

# 执行
$ rmmod nvidia
rmmod: ERROR: Module nvidia is in use by: nvidia_modeset

$ rmmod nvidia_modeset
rmmod: ERROR: Module nvidia_modeset is in use by: nvidia_drm

$ rmmod nvidia_drm
$ rmmod nvidia
$ nvidia-smi 
```



