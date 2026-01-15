### 概念





### 常用命令之 helm chart

```
# repo
helm repo list
helm repo remove bianjie
helm repo add bianjie http://nexus.bianjie.ai/repository/bianjie
# 需要认证的
helm repo add bianjie http://nexus.bianjie.ai/repository/bianjie --username helm_puller --password KXWIuICN

helm repo update

# helm package
helm search hub xxx
helm search repo kong

helm search repo kong --versions
helm search repo  bitnami/grafana -l

# helm search hub 搜索 Artifact Hub，该仓库列出了来自不同仓库的大量chart
# helm search repo 搜索已经(用 helm repo add)加入到本地helm客户端的仓库
# 命令只搜索本地数据，不需要连接网络，所以需要 helm repo update

helm pull bitnami/grafana --version 8.4.6

# helm package
helm lint ./explore/
helm package explore/
helm add repo 
curl -v -u user:password http://nexus.bianjie.ai/repository/rainbow/  --upload-file explorer-0.1.0.tgz

```



### 常用命令之 helm Release

```
helm status api
helm history api
helm rollback api 3


#
helm install loki --namespace=loki loki-stack-2.10.2.tgz --set loki.persistence.enabled=true,loki.persistence.storageClassName=alicloud-disk-essd-pl0,loki.persistence.size=50Gi

helm install rabbitmq --namespace=mw rabbitmq-13.0.3.tgz -f value.yaml

#
helm upgrade api ./kong-2.32.0.tgz -f values-adb.yaml -n kong

#
helm uninstall api -n kong

# 
helm get manifest  ingress-nginx -n x

# 
helm get values api -n kong

```



### nexus 仓库

```
helm 打包上传至 nexus

1、nexus 新建 helm 仓库
avata helm

2、添加仓库库
helm repo add avata http://nexus.bianjie.ai/repository/avata --username admin --password bianjie.ai

3、上传
helm nexus-push avata  console-server-v0.0.1.tgz --username admin --password bianjie.ai

# 注意
添加插件 helm plugin install --version master https://github.com/sonatype-nexus-community/helm-nexus-push.git
```





