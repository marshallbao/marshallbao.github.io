# chart

包目录结构

```
my-chart/
├── Chart.yaml             # 1. 核心元数据 (必需)
├── values.yaml            # 2. 默认配置值 (必需)
├── templates/             # 3. Kubernetes 资源模板 (必需)
│   ├── NOTES.txt          #    安装成功后的提示信息 (推荐)
│   ├── _helpers.tpl       #    可重用的模板片段/宏 (强烈推荐)
│   ├── deployment.yaml    #    部署应用 Pod 的模板
│   ├── service.yaml       #    暴露应用的 Service 模板
│   └── ingress.yaml       #    可选：外部访问的 Ingress 模板
├── charts/                # 4. 子 Chart 或依赖 (仅限伞形 Chart)
├── README.md              # 5. 用户文档 (强烈推荐)
├── LICENSE                # 6. 许可证信息 (推荐)
└── values.schema.json     # 7. 配置值校验模式 (可选)
```



### Helm 包常用结构

单组件结构：只有一个服务

伞型结构：一个主服务，其他是可选的子 chart,例如 Prometheus 包；

扁平化结构：多个服务都放在一个 Helm Chart 中，只用一个 values.yaml 来管理配置

子chart 结构：每个服务作为独立的子 chart，可以单独管理和维护



### Chart.yaml 

Chart.yaml 文件内容，参考 prometheus chart 包

```
apiVersion: v2
appVersion: v2.55.1
dependencies:
- condition: alertmanager.enabled
  name: alertmanager
  repository: https://prometheus-community.github.io/helm-charts
  version: 1.13.*
description: Prometheus is a monitoring system and time series database.
home: https://prometheus.io/
icon: https://raw.githubusercontent.com/prometheus/prometheus.github.io/master/assets/prometheus_logo-cb55bb5c346.png
keywords:
- monitoring
- prometheus
kubeVersion: '>=1.19.0-0'
maintainers:
- email: gianrubio@gmail.com
  name: gianrubio
name: prometheus
sources:
- https://github.com/prometheus/alertmanager
- https://github.com/prometheus/prometheus
type: application
version: 25.30.2

```



### 子 chart



