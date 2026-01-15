# 监控 Mysql

1. 监控账号

```mysql
> CREATE USER 'exporter'@'%' IDENTIFIED BY 'exporterPassword' WITH MAX_USER_CONNECTIONS 3;
> GRANT PROCESS, REPLICATION CLIENT ON *.* TO 'exporter'@'%';

# 可选
> GRANT SELECT ON performance_schema.* TO 'exporter'@'%' WITH MAX_USER_CONNECTIONS 3;
```

2. 部署 mysqld-exporter







3. prometheus job 配置

```
    - job_name: mysql
      metrics_path: /probe
      params:
        auth_module: [client]
      static_configs:
        - targets:
          - 192.168.0.193:3306
          - 192.168.0.194:3306
      relabel_configs:
        - source_labels: [__address__]
          target_label: __param_target
        - source_labels: [__param_target]
          target_label: instance
        - target_label: __address__
          replacement: mysqld-exporter:9104
```

