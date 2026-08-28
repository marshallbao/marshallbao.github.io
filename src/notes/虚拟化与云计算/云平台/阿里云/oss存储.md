# OSS

#### ossutil 

客户端配置

version: 2.2.2

```
# .ossutilconfig
[profile default]
language=CH
accessKeySecret=xx
accessKeyID=xxx
endpoint=oss-cn-wulanchabu.aliyuncs.com
region = cn-shanghai

[profile bianjieai-shanghai-resources]
language=CH
accessKeySecret=xx
accessKeyID=xx
endpoint=oss-cn-shanghai.aliyuncs.com
region = cn-shanghai
```

常用命令

```
# 指定配置文件
ossutil64 cp /Ethereum_qr.png oss://asia-resource/ --config-file=/root/.ossutilconfig-bhk 

# 指定某个 profile，也可以组合使用
ossutil ls oss://bianjieai-shanghai-resources --profile bj-resource
```

