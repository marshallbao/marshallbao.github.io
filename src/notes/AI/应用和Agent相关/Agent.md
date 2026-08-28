# Agent 



OpenCode

```
opencode run "你好"
```



Client



```
curl --no-buffer http://litellm.bianjie.ai/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-xx" \
  -d '{
    "model": "deepseek-v4-flash",
    "messages": [{"role": "user", "content": "写一篇长文解释什么是量子计算"}],
    "stream": true
  }'
```





### 参考

https://opencode.ai/docs/zh-cn