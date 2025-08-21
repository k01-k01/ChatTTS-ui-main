# ChatTTS WebUI & API



一个基于 ChatTTS 的本地网页界面和 API 服务，支持中英文文本转语音，并提供丰富的音色控制选项。

原 [ChatTTS](https://github.com/2noise/ChatTTS) 项目。



## 界面预览

![image](https://github.com/jianchang512/ChatTTS-ui/assets/3378335/669876cf-5061-4d7d-86c5-3333d0882ee8)


## 特性

- 🌐 简洁易用的 Web 界面
- 🎵 支持多种音色和语音控制
- 🔤 支持中英文混合文本
- 📡 提供 RESTful API 接口
- 🚀 支持 CPU/GPU 加速
- 🐳 支持 Docker 部署
- 🌍 多平台支持 (Windows/Linux/macOS)

## 技术栈与实现原理

### 核心技术栈

- [ChatTTS](https://github.com/2noise/ChatTTS) - 主要的文本到语音模型
- [Flask](https://flask.palletsprojects.com/) - Web 框架
- [PyTorch](https://pytorch.org/) - 深度学习框架
- [FFmpeg](https://ffmpeg.org/) - 音频处理工具
- [ModelScope](https://modelscope.cn/) - 模型下载平台
- [Vocos](https://github.com/gnu4cn/vocos) - 音频声码器
- [Transformers](https://huggingface.co/docs/transformers/) - 预训练模型工具库

### 工作原理

ChatTTS-UI 是基于 ChatTTS 的文本转语音系统，其工作流程如下：

1. 用户通过 Web 界面或 API 提供文本输入
2. 文本预处理模块对输入进行处理，包括：
   - 文本规范化（数字、符号转换等）
   - 中英文分词处理
   - 控制符解析（如 [laugh]、[speed] 等）
3. ChatTTS 模型处理：
   - 使用预训练的语言模型对文本进行编码
   - 通过扩散模型生成声学特征
   - 利用 Vocos 声码器将声学特征转换为波形
4. 音频后处理：
   - 使用 FFmpeg 合并多个音频片段
   - 生成最终的 WAV 音频文件
5. 返回结果给用户

### 模型架构

ChatTTS 模型主要包含以下组件：

1. **文本编码器**：基于 Llama 架构的语言模型，用于理解文本语义
2. **声学模型**：扩散模型，用于生成声学特征
3. **声码器**：Vocos 模型，用于将声学特征转换为音频波形
4. **音色编码器**：用于控制语音音色的嵌入向量

### 音色系统

ChatTTS 支持灵活的音色控制：
- 预设音色：通过数字 ID 选择（如 2222, 7869 等）
- 自定义音色：通过种子值生成特定音色
- 音色保存：可将生成的音色保存为 .pt 文件供后续使用

## 快速开始

### Windows 预编译版本

1. 下载压缩包
2. 解压后双击 `app.exe` 即可使用
3. 某些安全软件可能报毒，请退出或使用源码部署
4. 英伟达显卡且显存大于 4G 并安装了 CUDA 11.8+ 后，将自动启用 GPU 加速

### 手动下载模型

首次运行时会从 huggingface.co 或 github 下载模型到 asset 目录下，如果网络不稳定可能下载失败。



下载解压后，将所有 .pt 文件复制到 asset 目录下，然后重启软件。

## 部署方式

### Docker 部署 (Linux)

#### 安装

```bash
# 克隆项目
git clone 
cd chat-tts-ui

# 启动容器 (选择其一)
# GPU 版本
docker compose -f docker-compose.gpu.yaml up -d 

# CPU 版本    
docker compose -f docker-compose.cpu.yaml up -d

# 查看日志
docker compose logs -f --no-log-prefix
```

访问 `http://IP:3000` 使用服务 (本机: http://127.0.0.1:3000)

#### 更新

```bash
# 获取最新代码
git checkout main
git pull origin main

# 更新镜像
docker compose down

# GPU 版本
docker compose -f docker-compose.gpu.yaml up -d --build

# CPU 版本
docker compose -f docker-compose.cpu.yaml up -d --build

# 查看日志
docker compose logs -f --no-log-prefix
```

### 源码部署

#### 系统要求

- Python 3.9-3.11
- ffmpeg 4.0+
- 至少 4GB 内存

#### Linux 部署

```bash
# 1. 安装依赖
# Ubuntu/Debian: apt-get install ffmpeg
# CentOS/RHEL: yum install ffmpeg

# 2. 克隆项目
mkdir /data/chattts
cd /data/chattts
git clone https://github.com/jianchang512/chatTTS-ui .

# 3. 创建虚拟环境
python3 -m venv venv
source ./venv/bin/activate

# 4. 安装依赖
pip3 install -r requirements.txt

# 5. 安装 PyTorch
# CPU 版本
pip3 install torch==2.2.0 torchaudio==2.2.0

# GPU 版本 (需要 CUDA 11.8+)
pip install torch==2.2.0 torchaudio==2.2.0 --index-url https://download.pytorch.org/whl/cu118
pip install nvidia-cublas-cu11 nvidia-cudnn-cu11

# 6. 启动服务
python3 app.py
```

#### macOS 部署

```bash
# 1. 安装依赖
brew install libsndfile git python@3.10 ffmpeg

# 设置环境变量
export PATH="/usr/local/opt/python@3.10/bin:$PATH"
source ~/.bash_profile 
source ~/.zshrc

# 2. 克隆项目
mkdir /data/chattts
cd /data/chattts
git clone https://github.com/jianchang512/chatTTS-ui .

# 3. 创建虚拟环境
python3 -m venv venv
source ./venv/bin/activate

# 4. 安装依赖
pip3 install -r requirements.txt

# 5. 安装 PyTorch
pip3 install torch==2.2.0 torchaudio==2.2.0

# 6. 启动服务
python3 app.py
```

#### Windows 部署

1. 下载安装 Python 3.9-3.11，安装时注意选中 `Add Python to environment variables`
2. 下载 [ffmpeg](https://ffmpeg.org/download.html) 并将 `ffmpeg.exe` 放在项目目录下的 `ffmpeg` 文件夹内
3. 下载安装 [Git](https://git-scm.com/downloads)
4. 克隆项目:
   ```bash
   mkdir D:/chattts
   cd D:/chattts
   git clone https://github.com/jianchang512/chatTTS-ui .
   ```
5. 创建并激活虚拟环境:
   ```bash
   python -m venv venv
   .\venv\scripts\activate
   ```
6. 安装依赖:
   ```bash
   pip install -r requirements.txt
   ```
7. 安装 PyTorch:
   ```bash
   # CPU 版本
   pip install torch==2.2.0 torchaudio==2.2.0
   
   # GPU 版本 (需要 CUDA 11.8+)
   pip install torch==2.2.0 torchaudio==2.2.0 --index-url https://download.pytorch.org/whl/cu118
   ```
8. 启动服务:
   ```bash
   python app.py
   ```

## 配置说明

### 环境变量

创建 `.env` 文件来自定义配置:

```
WEB_ADDRESS=127.0.0.1:9966    # Web 服务地址
device=default                # 设备选择: default/cuda/cpu/mps
lang=zh                       # 语言: zh/en
compile=true                  # 是否启用编译优化
```

### GPU 支持说明

1. 如果 GPU 显存低于 4G，将强制使用 CPU
2. Windows 或 Linux 下如果显存大于 4G 并且是英伟达显卡，但源码部署后仍使用 CPU，可尝试:
   ```bash
   # 卸载
   pip uninstall -y torch torchaudio
   
   # 重新安装 CUDA 版本
   pip install torch==2.2.0 torchaudio==2.2.0 --index-url https://download.pytorch.org/whl/cu118
   ```
   注意: 必须已安装 CUDA 11.8+

## 音色管理

0.96 版本后因 ChatTTS 内核升级，无法直接使用旧版 pt 文件。

使用 `cover-pt.py` 脚本转换音色文件:
```bash
python cover-pt.py
```

该脚本会将 `speaker` 目录下以 `seed_` 开头、以 `_emb.pt` 结尾的文件转换为可用格式。

## API 使用

### 请求方式

**方法**: POST  
**地址**: `http://127.0.0.1:3000/tts`

### 请求参数

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| text | string | 是 | - | 要合成语音的文字 |
| voice | int | 否 | 2222 | 音色编号: 2222/7869/6653/4099/5099 |
| prompt | string | 否 | "" | 控制笑声、停顿等 |
| temperature | float | 否 | 0.3 | 温度参数 |
| top_p | float | 否 | 0.7 | top_p 采样 |
| top_k | int | 否 | 20 | top_k 采样 |
| skip_refine | int | 否 | 0 | 1=跳过 refine text，0=不跳过 |
| custom_voice | int | 否 | 0 | 自定义音色种子值 |

### 返回格式

成功:
```json
{
  "code": 0,
  "msg": "ok",
  "audio_files": [
    {
      "filename": "文件绝对路径",
      "url": "可下载的网址"
    }
  ]
}
```

失败:
```json
{
  "code": 1,
  "msg": "错误原因"
}
```

### 调用示例

```python
import requests

res = requests.post('http://127.0.0.1:9966/tts', data={
  "text": "你好，世界！",
  "voice": "2222",
  "temperature": 0.3
})

print(res.json())
```




