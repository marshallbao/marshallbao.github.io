# Nginx Header

关于 Header

![image-20230323185954257](header.assets/image-20230323185954257.png)



header 分为两种，请求头 header 和响应头 header

proxy_set_header 是 nginx 设置请求头给上游服务器,也就是配置请求头 header，需要注意的是 proxy_set_header 设置的内容是不可见的，因为是代理服务器添加了 header 给上游服务器，不是客户端配置的，我们看到的 header 都是客户端的请求头 header 或者服务端的响应头 header

add_header 是 nginx 设置响应头信息给浏览器，也就是配置响应头 header

