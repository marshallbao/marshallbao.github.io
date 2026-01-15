# Traefik

K3s 中 Traefik 定位是默认集成且预配置的轻量级 Ingress Controller，它扮演着边缘路由器和流量入口的角色，负责将外部流量路由到 K3s 集群内部的微服务，提供自动发现、HTTPS、指标监控等功能，极大简化了边缘计算环境下的服务暴露和管理，让用户只需关注应用部署



访问 dashboard  访问

```
# 方式1：使用 ingressroute
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: dashboard
  namespace: kube-system
spec:
  entryPoints:
    - web
  routes:
    - match: Host(`web.traefik.example`) && (PathPrefix(`/dashboard`) || PathPrefix(`/api`))
      kind: Rule
      services:
        - name: api@internal
          kind: TraefikService
# 添加启动参数，使用 svc 直接访问
--api.insecure=true

# 路由，一定要带后面的 /
http://IP:8080/dashboard/
```

middleware 功能

```
# 限流
# Traefik 第一步：定义限流中间件
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: test-ratelimit
spec:
  rateLimit:
    average: 10
    burst: 20
---
# Traefik 第二步：在 Ingress 中引用（通过注解或 CRD）
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app
  annotations:
    traefik.ingress.kubernetes.io/router.middlewares: default-test-ratelimit@kubernetescrd

# 跨域
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: test-cors
spec:
  headers:
    accessControlAllowMethods:
      - GET
      - POST
      - OPTIONS
    accessControlAllowOriginList:
      - "https://example.com"
    accessControlMaxAge: 100
```

其他

整体理解起来，其实 traefik 和 kong 差不多，都是通过 crd 资源来进行一些配置

参考

https://traefik.io/