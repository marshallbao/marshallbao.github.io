# Ingress 配置

annotations 自定义块配置

```
# configuration-snippet
用于 插入到请求处理的配置部分，通常用于定制请求头、响应头、负载均衡等细粒度的配置。

# server-snippet
用于 插入到虚拟主机级别的配置，通常用于修改虚拟主机的 listen、server_name 等配置。

```



限流

```
  annotations:
    nginx.ingress.kubernetes.io/configuration-snippet: |
      limit_req_status 429;
    nginx.ingress.kubernetes.io/limit-burst-multiplier: "5"
    nginx.ingress.kubernetes.io/limit-rps: "20"
    nginx.ingress.kubernetes.io/limit-whitelist: ip
```

Location

```
  annotations:    
    nginx.ingress.kubernetes.io/server-snippet: |
      location = / {
        return 301 https://docs.avata.bianjie.ai;
      }
#
  annotations:
    nginx.ingress.kubernetes.io/server-snippet: |
      location = / {
        proxy_set_header X-Apifox-Project-ID 2471586;
        proxy_pass http://2471586.n3.apifox.cn;
      }
      location /api {
        proxy_set_header X-Apifox-Project-ID 2471586;
        proxy_pass http://2471586.n3.apifox.cn;
      }

```

重写

```
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: https://wenchang.bianjie.ai
```

