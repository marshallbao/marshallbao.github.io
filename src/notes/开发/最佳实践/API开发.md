# API  开发

API 路径格式

```
/api/v1/<resource>

例如
GET /api/v1/users
GET /api/v1/projects/123
POST /api/v1/deployments
```

`api` 是功能类别，`v1` 是该功能的版本。语义顺序应是：**功能 → 版本 → 资源**。



| URL 示例                | 说明             |
| ----------------------- | ---------------- |
| `/api/v1/users`         | 标准 REST API    |
| `/admin/v1/users`       | 管理后台 API     |
| `/auth/v1/login`        | 登录接口         |
| `/internal/v1/serviceA` | 内部服务 A 接口  |
| `/public/v1/projects`   | 外部开放查询接口 |
| `/webhook/v1/payment`   | 支付回调接口     |



1. **区分内部 vs 外部接口**
   - `/api` 一般面向外部（客户端、第三方）
   - `/internal` 面向微服务调用
   - `/admin` 面向运维/管理
2. **区分版本号**
   - 一般放在功能类别后 `/api/v1/...`
   - 内部接口也可以加版本 `/internal/v1/...`
3. **健康检查和监控接口不要混入业务接口**
   - `/health` `/metrics` 独立，方便 Kubernetes 或 Prometheus 直接访问
4. **静态资源单独划分**
   - `/static`、`/assets`、`/uploads`
   - 可以通过 Nginx 或 CDN 提供服务，避免通过 API 网关转发