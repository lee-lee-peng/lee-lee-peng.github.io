---
date: 2026-10-30
authors:
  - lee
categories:
  - AI
tages:
  - llama.cpp
  - CUDA
  - WSL
---
# WSL环境下，编译llama.cpp+CUDA，在老Nvidia显卡下实现CUDA加速

## 硬件环境

- 硬件情况
  | CPU  | Intel Core i5-8300H @ 2.30G Hz |     |     |     |     |     |     |     |     |
  | ---- | ------------------------------ | --- | --- | --- | --- | --- | --- | --- | --- |
  | GPU  | Nvidia Quadro P600 @ 4G显存    |     |     |     |     |     |     |     |     |
  | 内存 | 16G                            |     |     |     |     |     |     |     |     |
- Host系统：Windows 10 22H2 专业版
- WSL系统：CentOS 10 Stream x86_64
- CUDA版本：12.4

## 软件环境

- WSL中，运行`nvidia-smi`必须能正常显示出显卡信息
- 由于CentOS 10自带的是gcc 14，而CUDA 12.4最高仅支持到gcc 13，因此我从官方网下载载了13.5.0版本的gcc源码手动编译安装到/usr/local/gcc-13，并将/bin/gcc和/bin/g++替换为/usr/local/gcc-13/bin下的软链接，否则cuda 12无法正常安装
- cuda 12.4下载下来是单个很大的run文件，直接运行时，在解压/环境检测的过程中遇到问题导致卡住，实际使用./cuda_12.4.1_550.54.15_linux.run --tar mxvf手动解压缩到一个目录后，运行里面的cuda-installer进行安装

## 编译过程

- 解压好源码后，先不着急编译，先从https://github.com/ggml-org/llama.cpp/releases下载官方预先生成的UI文件，解压到源码目录下的```tools/ui```内，并重命名为dist，否则编译时，由于无法直接从hf下载UI文件会导致无WEB UI
- 编译Rlease版本文件命令如下：

```bash
cmake -B build -DGGML_CUDA=ON -DCMAKE_INSTALL_PREFIX=/usr/local/llama.cpp -DCMAKE_BUILD_TYPE=Release
```

- 经过漫长的等待后，安装好编译后的文件：

```bash
sudo cmake --install build
```

## 运行&验证

- 由于llama.cpp的二进制文件依赖自己的lib，因此需要将安装路径下的lib64目录加入至LD_LIBRARY_PATH（或运行时设置也可以）：

```bash
export LD_LIBRARY_PATH=/usr/local/llama.cpp/lib64
```

- 运行以下命令验证是否取到了可用的CUDA役备：

```bash
LD_LIBRARY_PATH=/usr/local/llama.cpp/lib64 /usr/local/llama.cpp/bin/llama-server --list-devices
```

这里输出，表示取到了可用于CUDA运行的设备：

```
Available devices:
  CUDA0: Quadro P600 (4095 MiB, 3374 MiB free)
```

- 实际运行时，推荐用models-dir的方式运行，并针对模型提供量化参数（不同类型模型不一样，我这里用的QWen系）：

```bash
LD_LIBRARY_PATH=/usr/local/llama.cpp/lib64 /usr/local/llama.cpp/bin/llama-server --models-dir /home/llama/models --host 0.0.0.0 --port 11434 --no-models-autoload -ngl 999 --jinja --api-key 12345678 --ctx-size 32768 -ctk q8_0 -ctv q8_0
```

## Tips

### 关于Vulkan

- 官方编译的版本就能很好的支持Vulkan设备，如果需要自己编译，需安装好对应的依赖：

```bash
sudo dnf install git cmake gcc-c++ vulkan-devel glslc glslang
```

- 安装好后，运行以下命令确认vulkan环境是否达到llama.cpp要求（apiVersion需要大于等于1.2）：

```bash
vulkaninfo|grep apiVersion
```

- 编译时，用`GGML_VULKAN=ON`开启vulkan：

```bash
cmake -B build -DGGML_VULKAN=ON -DCMAKE_INSTALL_PREFIX=/usr/local/llama.cpp
```

### 使用`--no-models-autoload`参数时，是不会自动载入model的，需要手动调一下接口（WEB UI也能操作），写了个小脚本（BAT）

```bash
set "model_name=qwen2.5-coder-1.5b-instruct-q4_k_m"
if not "%~1"=="" set model_name=%~1"

curl -X POST http://127.0.0.1:11434/models/load -H "Content-Type: application/json" -H "Authorization: Bearer 12345678" -d "{\"model\": \"%model_name%\"}"
```
