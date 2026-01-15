# OpenSSL

常用命令

```
# 查看证书内容
openssl x509 -in certificate.crt -text -noout

# 查看证书有效时限
openssl x509 -in ca.crt -dates

# 使用 ca 验证证书有效性
openssl verify -CAfile ca.cert server.crt

# 
openssl crl -in crl.pem -text


```

