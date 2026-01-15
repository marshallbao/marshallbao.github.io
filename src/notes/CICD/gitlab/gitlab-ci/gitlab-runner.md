# gitlab-runner



### gitlab-runner 分类

| **类型**               | **颗粒度**  | **管理权限** | **优点**                       |
| ---------------------- | ----------- | ------------ | ------------------------------ |
| **Instance (Shared)**  | 全局级别    | 管理员       | 资源利用率最高，维护成本最低   |
| **Group**              | 部门/组级别 | 组管理员     | 灵活性适中，能满足团队私有需求 |
| **Project (Specific)** | 项目级别    | 项目维护者   | 隔离性最强，环境最稳定         |



添加不同类型的 runner

全局 runner 在 admin 界面进行添加；group 级别 runner 在 group 页面进行添加； project 亦然；





### gitlab-runner 安装

1、下载对应 gitlab 版本的 gitlab-runner 二进制文件 放置 /usr/local/bin/ 下

```
# 下载二进制
# 下载页面 https://gitlab-runner-downloads.s3.amazonaws.com/v15.11.0/index.html
wget https://gitlab-runner-downloads.s3.amazonaws.com/v12.4.1/binaries/gitlab-runner-linux-amd64
chmod +x gitlab-runner

# 新建用户及用户组下
groupadd -g 1005 gitlab-runner
useradd -m -d /home/gitlab-runner -u 1005 -g gitlab-runner gitlab-runner
```

2、去 gitlab 生成 gitlab-runner 认证 token

不同的入口对应不同类型/权限的 runner

3、进行注册

```
gitlab-runner register --non-interactive --url "https://gitlab.bianjie.ai/" --registration-token "xxxx" --executor "docker" --docker-image alpine:latest --description "shared-runner-nj-120"
```

3、注册之后会生成 /etc/gitlab-runner/config.toml 配置文件

```
root@LAPTOP-8P8KN0QD:/etc/gitlab-runner# cat config.toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "shared-runner2"
  url = "[https://gitlab.bianjie.ai/"]
  token = "xxxxxx"
  executor = "docker"
  [runners.custom_build_dir]
  [runners.docker]
    tls_verify = false
    image = "alpine:latest"
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
    allowed_pull_policies = ["always", "if-not-present"]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
```

4、配置

```
# 新建service文件
[root@dev01 ~]# cat /lib/systemd/system/gitlab-runner.service
[Unit]
Description=GitLab Runner
After=syslog.target network.target
ConditionFileIsExecutable=/usr/local/bin/gitlab-runner

[Service]
StartLimitInterval=5
StartLimitBurst=10
ExecStart=/usr/local/bin/gitlab-runner "run" "--working-directory" "/home/gitlab-runner" "--config" "/etc/gitlab-runner/config.toml" "--service" "gitlab-runner" "--syslog" "--user" "gitlab-runner"

Restart=always
RestartSec=120

[Install]
WantedBy=multi-user.target
```

4、启动

```
systemctl daemon-reload && systemctl enable gitlab-runner && systemctl start gitlab-runner
```



### 参考

http://www.coder55.com/article/4966