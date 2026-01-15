kong

ingress

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: kong
    konghq.com/plugins: xxx
  name: prometheus-server
  namespace: monitor
spec:
  rules:
  - host: xxxxx
    http:
      paths:
      - backend:
          service:
            name: xxxx
            port:
              number: 80
        path: /
        pathType: Prefix

```



### plugin

rate-limiting

```
apiVersion: configuration.konghq.com/v1
kind: KongPlugin
metadata:
  name: rate-limiting-prometheus
  namespace: monitor
config: 
  minute: 60 # 或者是second: 1
  redis_port: 6379
  redis_timeout: 2000
  redis_ssl: false
  redis_ssl_verify: false
  redis_database: 8
  redis_password: xxx
  redis_username: 
  redis_host: redis-master.db
  policy: redis
  limit_by: ip
  fault_tolerant: true
  hide_client_headers: false
plugin: rate-limiting
```

ip-restriction

```
apiVersion: configuration.konghq.com/v1
config:
  deny:
  - 110.244.117.68
  - 115.54.255.178
  allow:
  - 110.244.117.68
  - 115.54.255.178
  - 117.11.127.243
  - 115.54.225.137
kind: KongPlugin
metadata:
  name: ip-restriction
  namespace: spark
plugin: ip-restriction
```

cors

```
apiVersion: configuration.konghq.com/v1
config:
  credentials: false
  exposed_headers:
  - X-Auth-Token
  - Content-Disposition
  methods:
  - GET
  - POST
  - HEAD
  - DELETE
  - OPTIONS
  - CONNECT
  - TRACE
  - PUT
  origins:
  - '*'
kind: KongPlugin
metadata:
  name: cors
  namespace: spark
plugin: cors
```

hmac

```
apiVersion: configuration.konghq.com/v1
config:
  algorithms:
  - hmac-sha1
  - hmac-sha256
  - hmac-sha384
  - hmac-sha512
  clock_skew: 60
  enforce_headers:
  - X-Date
  - Digest
  hide_credentials: false
  validate_request_body: true
kind: KongPlugin
metadata:
  name: hmac-auth
  namespace: spark
plugin: hmac-auth
---
apiVersion: configuration.konghq.com/v1
credentials:
- wallet
kind: KongConsumer
metadata:
  annotations:
    kubernetes.io/ingress.class: kong
  name: wallet
  namespace: spark
username: wallet
---
apiVersion: v1
data:
  kongCredType: aG1hYy1hdXRo
  secret: U1MwMHZZcEs=
  username: d2FsbGV0
kind: Secret
metadata:
  name: wallet
  namespace: spark
type: Opaque

```

jwt

```
apiVersion: configuration.konghq.com/v1
config:
  run_on_preflight: true
  secret_is_base64: true
kind: KongPlugin
metadata:
  name: w3box-jwt
  namespace: w3box
plugin: jwt
```





http 跳转 https

```
      konghq.com/protocols: https
      konghq.com/https-redirect-status-code: "301"
```

