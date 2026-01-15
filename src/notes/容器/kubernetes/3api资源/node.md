# Node

### 标签

```
# 给主机打标签
kubectl label nodes node01 nodetype=bcnode

# 修改标签
kubectl label nodes node-1 nodetype=bcnode --overwrite

# 删除标签
kubectl label nodes node-1 nodetype-
```



### 污点和容忍污点



