# Docker-compose

### 安装

直接去 github 仓库 下载对应系统的二进制即可，例如(废弃)

https://github.com/docker/compose/releases/tag/v2.26.1



现在推荐作为 docker 的插件来维护

```
# 如果可以
apt-get update
apt-get install docker-compose-plugin

# 国内配置源安装

# 1. 安装必要的证书和工具
apt-get update
apt-get install -y ca-certificates curl gnupg

# 2. 创建并添加 Docker 官方的 GPG 密钥
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg

# 3. 将 Docker 官方仓库写入你的软件源列表
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. 再次更新并安装插件
apt-get update
apt-get install docker-compose-plugin



# 手动安装
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v5.1.2/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose

# 校验
docker compose version


# 插件目录
# 也可以将插件放在 /usr/libexec/docker/cli-plugins/ 目录下，这是系统级； $HOME/.docker 这是用户级
```







### 概念

network

- 不指定，默认新建 projectName_default
- 指定，新建
- 外部 network，用于多个 docker-compose 网络打通，需要提前创建，然后每个 docker-compose 分别指定，例如

```
# 假如 docker-compose.yaml 在 目录 node 下，即 node/docker-compose.yaml
# 指定
networks:
  hvn:
    name: hvn-n
# network 的名字为 hvn-n

#指定 
networks:
  hvn: {}
# network 的名字为 node-hvn

# 外部 network
networks:
  hvn:
    external: true
    name: hvn-network
```

关于 docker-comepose 各个服务之间的访问

```
# 如果是单个 docker-comepose,且每个 docker-comepose 一个 container,可以使用 service name 或者 container name

# 如果是多个 docker-comepose 共享一个外部 network,且 service name 不冲突，可以使用 service name 或者 container name

# 如果是多个 docker-comepose 共享一个外部 network,且 service name 冲突，必须使用 container name！如果使用 service name 会将 ip 指向相同 service name 的多个容器 IP（类似负载均衡）


## 综上，最好在 docker-compose 中声明 container name，然后使用 container name 进行访问
```



volumes

```


# 只会停止并删除容器和网络
docker compose down

# 数据卷
docker compose down -v

#
docker compose down -v --rmi local

# 
docker compose down -v --rmi all

#
docker volume rm zammad-docker-compose_postgresql-data 
```



env_file

在 Docker Compose 中，**环境变量文件**（通常是 `.env` 文件）允许你将环境变量从文件中加载到 `docker-compose.yml` 配置中

```
## 加载环境变量
# 默认在相同目录下 .env
# 通过命令行指定，可以指定多个
docker-compose --env-file .env --env-file .env.local up
# 环境变量也可以

## 格式
# .env 文件
MYSQL_ROOT_PASSWORD=rootpassword
MYSQL_USER=myuser
MYSQL_PASSWORD=mypassword

## 使用
# 如果没有 WEB_PORT，则使用默认值 8080
version: '3'
services:
  web:
    image: nginx
    container_name: ${NODE_CONTAINER_NAME:-nginx}
    ports:
      - "${WEB_PORT:-8080}:80" 
## 优先级
env_file 先把底层地基打好，而 environment 拥有最终决定权。如果两个地方定义了同一个变量，environment 里的值会覆盖掉 env_file 里的值。

```



profiles

```
version: '3.8'
services:
  # 1. 没有设置 profile，默认就会启动
  web:
    image: my-web-app
    ports:
      - "8080:8080"

  # 2. 设置了 profile: minio，默认不启动
  minio:
    image: minio/minio
    profiles:
      - minio
    ports:
      - "9000:9000"
 #  docker compose -d 默认不启动 minio,如果要启动 minio , docker compose --profile minio up -d 
```



### 常用命令

docker-compose 单独启动某个服务

```
# 启动
docker-compose -f docker-compose-wallet.yml up -d wallet-binancesmart

# 启动
docker-compose  start

# 重启
restart

# 仅停止容器
docker-compose  -f docker-compose-wallet.yml  stop wallet-binancesmart-testnet

# 删除容器，删除网络
docker-compose  down

# 删除容器，删除网络，删除 volumes ,谨慎操作 
docker-compose down -v

# 强制重新创建
up --force-recreate

# 主要用于删除已经停止的容器
rm

# 校验 yml 格式
docker compose config


```



关于 volume 迁移

```
# 旧 docker-compose
volumes:
  langfuse_postgres_data:
    driver: local

# 新 docker-compose
volumes:
  langfuse_postgres_data:
    external: true
    name: langfuse_langfuse_postgres_data

# 核心原理就是通过镜像将老 volume 里的数据打包，然后提前创建新的 volume 再把数据放进去；所以新的 docker-compose.yml 需要使用外部的 volume（不是本 docker-compose 创建，是提前创建的）
```





### 示例

示例 1

```
# docker-compose.yaml
version: '3'

services:
  cschain-tools:
    image: 10.133.38.190/cschain-otcibr/tools-switch-chain:8749454648c9f6347a37741fb86c0a872dd676eb
    container_name: cschain-tools
    volumes:
      - ./config.toml:/cschain-tools/config.toml:ro
    command: /cschain-tools/config.toml sso_login
    entrypoint: cschain-tools

```

示例 2

```
# docker-compose.yaml
version: '3'

services:
  teable:
    image: registry.cn-shenzhen.aliyuncs.com/teable/teable-ee:latest
    restart: always
    ports:
      - '3000:3000'
    volumes:
      - teable-data:/app/.assets:rw
    env_file:
      - .env
    environment:
      - NEXT_ENV_IMAGES_ALL_REMOTE=true
    networks:
      - teable
    depends_on:
      teable-db:
        condition: service_healthy
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:3000/health']
      start_period: 5s
      interval: 5s
      timeout: 3s
      retries: 3

  teable-db:
    image: registry.cn-shenzhen.aliyuncs.com/teable/postgres:15.4
    restart: always
    ports:
      - '5432:5432'
    volumes:
      - teable-db:/var/lib/postgresql/data:rw
    environment:
      - POSTGRES_DB=${POSTGRES_DB}
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
    networks:
      - teable
    healthcheck:
      test: ['CMD-SHELL', "sh -c 'pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}'"]
      interval: 10s
      timeout: 3s
      retries: 3


networks:
  teable:
    name: teable-network

volumes:
  teable-db: {}
  teable-data: {}

```

实例 3

```
# docker-compose.yaml
version: '3'

services:
  hvn-geth: 
    image: ${GETH_IMAGE}
    container_name: ${COMPOSE_PROJECT_NAME:-hvn-node}-geth
    networks:
      - hvn-network
    environment:
      - HTTP_API=${HTTP_API}
      - WS_API=${WS_API}
      - DISABLE_TXPOOL_GOSSIP=${DISABLE_TXPOOL_GOSSIP}
      - GETH_SYNC_MODE=${GETH_SYNC_MODE}
      - GC_MODE=${GC_MODE}
      - GETH_STATICNODES=${GETH_STATICNODES}
    volumes:
      - "${GETH_DATA_DIR}:/data"  # Map user-specified geth data directory to container
      - "./op-geth.sh:/op-geth.sh"
    entrypoint: [ "/bin/sh", "-c" ]
    command:
      - |
        exec /op-geth.sh
  
  hvn-node:
    depends_on:
      - hvn-geth
    image: ${NODE_IMAGE}
    container_name: ${COMPOSE_PROJECT_NAME:-hvn-node}-node
    networks:
      - hvn-network
    ports:
      # node rpc
      - "${NODE_RPC_PORT:-9090}:9090"
      # node p2p 
      - "${NODE_P2P_PORT:-26656}:26656"
      # node metrics
      - "${NODE_METRICS_PORT:-26657}:26657"
    volumes:
      - "${NODE_HOME}:/data/node"  # Map user-specified node directory to container
      - "${GETH_DATA_DIR}:/data/geth"  # Map geth data directory to access jwt.txt
      - "./hvn-node.sh:/hvn-node.sh"  # Map startup script to container
    user: "0:0"  # Run as root to avoid permission issues
    environment:
      - NODE_HOME=/data/node
      - GETH_CONTAINER_NAME=${COMPOSE_PROJECT_NAME:-hvn-node}-geth
    entrypoint: ["/bin/sh", "-c"]
    command:
      - |
        chmod +x /hvn-node.sh
        exec /hvn-node.sh

networks:
  hvn-network:
    external: true
    name: ${HVN_NETWORK_NAME:-hvn-network}

```

示例4：

```
# 重点是环境变量和 depends_on 锚定和复用
services:
  langfuse-worker:
    image: repo.bianjie.ai/langfuse/langfuse-worker:3
    restart: always
    depends_on: &langfuse-depends-on
    ports:
      - 127.0.0.1:3030:3030
    environment: &langfuse-worker-env
      NEXTAUTH_URL: xxx
      SALT: ${SALT:-mysalt}
  langfuse-web:
    image: repo.bianjie.ai/langfuse/langfuse:3
    restart: always
    depends_on: *langfuse-depends-on
    ports:
      - 3000:3000
    environment:
      <<: *langfuse-worker-env
      LANGFUSE_INIT_ORG_ID: ${LANGFUSE_INIT_ORG_ID:-}
      LANGFUSE_INIT_ORG_NAME: ${LANGFUSE_INIT_ORG_NAME:-}
```

参考

https://docs.docker.com/compose/install/standalone/