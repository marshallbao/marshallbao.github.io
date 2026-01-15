# 监控 MongoDB

1、新建监控账号

```
use admin
db.createUser({ 
    user: "mongodb_exporter",
    pwd: "exporterPassword",
    roles: [
        { role: "read", db: "local" },
        { role: "clusterMonitor", db: "admin" }
    ]
});
```

2、启动 mongo-exporter

```
version: '2'

services:
  mongodb-exporter:
    command: ["--mongodb.uri=mongodb://mongodb_exporter:exporterPassword@172.16.1.25:27017/?authSource=admin&replicaSet=rs0", "--web.listen-address=:9216", "--collector.replicasetstatus", "--collector.currentopmetrics"]
    image: percona/mongodb_exporter:0.40.0
    ports:
      - '9216:9216'

```

参数详解

| **参数名称**                       | **对应 MongoDB 命令** | **说明与监控价值**                                           |
| ---------------------------------- | --------------------- | ------------------------------------------------------------ |
| **`--collector.diagnosticdata`**   | `getDiagnosticData`   | **核心必选**。采集服务器的状态信息（类似 `serverStatus`）。包括内存使用、连接数、操作计数（Insert/Query等）、断言错误等基础指标。 |
| **`--collector.replicasetstatus`** | `replSetGetStatus`    | **副本集专用**。监控成员状态（Primary/Secondary/Arbiter）、心跳延迟、复制延迟（Replication Lag）以及成员健康状况。 |
| **`--collector.dbstats`**          | `dbStats`             | **数据库级别**。统计每个数据库的对象数量、数据大小、索引大小和存储消耗。*注意：数据库多时会增加扫描耗时。* |
| **`--collector.topmetrics`**       | `top`                 | **性能瓶颈分析**。记录每个集合（Collection）在读、写、锁定上花费的时间。对于定位“哪个表最慢”非常有帮助。 |
| **`--collector.currentopmetrics`** | `currentOp`           | **实时操作监控**。采集当前正在运行的操作（如长查询、等待锁的操作）。能反映集群当前的突发压力。 |
| **`--collector.indexstats`**       | `$indexStats`         | **索引效率**。监控每个索引的使用频率。可以帮你识别哪些索引是“冗余”的（从未使用），从而优化存储。 |
| **`--collector.collstats`**        | `$collStats`          | **集合明细**。提供比 `dbStats` 更细致的单表数据，包括文档总数、物理碎片、Scale 因子等。 |
| **`--collect-all`**                | N/A                   | **全选开关**。相当于把上面所有的参数全部设为 `true`。适合初学者或对监控颗粒度要求极高的场景。 |

简单监控推荐以下 3 个；

​    \- --collector.diagnosticdata

​    \- --collector.replicasetstatus

​    \- --collector.dbstats



3、



参考

https://github.com/percona/mongodb_exporter/