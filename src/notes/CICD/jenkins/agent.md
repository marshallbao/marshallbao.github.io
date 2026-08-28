# Agent

jenkins agent 有两种方式和 jenkins master 连接



| **连接模式**                               | **启动与连接方向**                                           | **适用场景**                                                 | **优缺点**                                                   |
| ------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **SSH 模式** (Launch agent via SSH)        | **Master $\rightarrow$ Agent** Master 通过 SSH 凭证，主动登录到 Agent 机器上并启动代理。 | 运行在固定内网、Linux 环境的专属服务器。                     | 优点：配置集中在 Master，安全且便于管理。 缺点：Master 必须能直接访问 Agent 的 IP。 |
| **Inbound 模式 (JNLP)** (你目前使用的方式) | **Agent $\rightarrow$ Master** Agent 运行 Java 命令，通过网络主动向 Master 报到并建立长连接。 | Agent 在动态环境（如 K8s）、跨机房、公网、或在防火墙/NAT 后面的场景。 | 优点：Agent 不需要公网 IP，能出网就能连。 缺点：网络链路变长，容易被中间防火墙切断。 |



### JNLP 模式

##### 1、配置 agent

节点管理 --> 新建节点 --> 输入节点名称 --> 固定节点，填写主要信息

1. 名字  后期会通过此名称进行 label选择 调度
2. 远程工作目录  jenkins agent 的工作空间 cache 拉取的代码等
3. 启动 方式 使用 java web 方式启动

点击保存即可

##### 2、配置 agent 启动环境

下载安装 jdk17，配置环境变量

```
wget https://download.oracle.com/java/17/archive/jdk-17.0.5_linux-x64_bin.tar.gz
# 
export PATH=$PATH:/usr/local/jdk-17.0.5/bin
export JAVA_HOME=/usr/local/jdk-17.0.5
```

##### 3、启动 agent

点击 agent 看到如下界面 使用里面的命令进行启动

```
# 下载 jar 包
curl -sO http://jenkins.bianjie.ai:8080/jnlpJars/agent.jar

# 启动
java -jar agent.jar -jnlpUrl http://jenkins.bianjie.ai:8080/computer/sh%2Ddeploy%2D02%2D83/jenkins-agent.jnlp -secret 6976ec4f19ab772022226208dfac88e742d5321211101a5ec56aa2b3caa4b4fc -workDir "/data/jenkins" &
```

##### 4、启动成功

也可以做成 service

```
[root@sn3 ~]# cat /usr/lib/systemd/system/jenkins-agent.service
[Unit]
Description=jenkins-agent
[Service]
Type=simple
User=root
ExecStart=/usr/local/jdk-17.0.5/bin/java -jar /data/jenkins/agent.jar -url http://jenkins.bianjie.ai/ -secret xx -name "nj-deploy-0-120" -webSocket -workDir "/data/jenkins/"
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
[Install]
WantedBy=multi-user.target
```

配置

```
systemctl daemon-reload && systemctl start jenkins-agent && systemctl enable jenkins-agent && systemctl status jenkins-agent
```

另外关于JNLP 模式的两个参数

 -websokcet 

最新的版本支持 -websokcet 参数，agent 可以直接通过 80/443 (websokcet)来连接 jenkins master，不用通过 50000 端口了，这样更稳定；

-tunnel

如果在较为复杂的网络可以使用 -tunnel 参数来指定 jenkins 的 50000 端口和可以访问的 ip

```
-tunnel 域名:端口` 或 `-tunnel IP:端口
```



参考

https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html