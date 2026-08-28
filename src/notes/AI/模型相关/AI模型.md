# AI 模型



### 模型文件类型及格式

GGUF

GGUF (GPT-Generated Unified Format)*是一种专门为大语言模型（LLM）设计的文件存储格式。它是由 llama.cpp 团队（主要是 Georgi Gerganov）开发的，目的是为了让大模型在消费级硬件（比如你的笔记本电脑、带有集成显卡的 PC）上运行得更顺畅。

模型类型

模型大小





### 常用模型市场

#### huggingface_hub

```
# 安装模型下载工具
pip install -U huggingface_hub

# 模型下载
export HF_ENDPOINT=https://hf-mirror.com
hf download BAAI/bge-m3 \
  --local-dir /data/huggingface/bge-m3 \
  --resume-download
  
# --resume-download 断点续传 

# 国内配置镜像
export HF_ENDPOINT=https://hf-mirror.com

# 模型目录结构
/data/huggingface/bge-m3
 ├── config.json
 ├── pytorch_model.bin
 ├── tokenizer.json
 ├── tokenizer_config.json
 ├── sentence_bert_config.json
 └── modules.json
 
# Hugging Face Hub 的标准缓存结构
# 路径为
~/.cache/huggingface/hub/models--BAAI--bge-m3/

# 结构为
models--BAAI--bge-m3
 ├─ blobs
 ├─ refs
 └─ snapshots
# 也就是说你可以使用两种方式启动模型
# 1. 启动的时候去 hub 上下载，这时服务会将模型缓存到 ~/.cache/huggingface 下；
# 2. 启动之前先手动下载好模型，然后启动的时候指定模型的目录进行启动，比如 M3_MODEL_ID=/data/huggingface/bge-m3

# 示例1
docker run -d \
  --gpus all \
  --env HF_ENDPOINT=https://hf-mirror.com \
  -v /data/huggingface-cache:/root/.cache/huggingface
  sophiacloud/bge-m3-service:cuda


# 示例 2
docker run -d \
  --gpus all \
  -v /data/huggingface/bge-m3:/models/bge-m3\
  -e M3_MODEL_ID=/models/bge-m3 \
  sophiacloud/bge-m3-service:cuda
```

