# 前言
提供两种方式提供情感分析服务：
1. 通用化服务：通过api方式提供服务，情感分类仅支持 正向 负向 双分类，且由于训练数据集的领域化，可能针对特殊场景，效果不理想。
2. 定制化服务：使用谷歌提出的bert-base-chinese作为预训练模型，针对自己的特殊场景，对模型定制化训练，可以实现情感多分类，且效果良好。

## 环境安装
### 安装
请确保你已经安装了**conda**
```shell
# 清华大学镜像站
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/

# 中科大镜像站
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/

# 北京外国语大学镜像站
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/free/

```
1. 下载项目
```shell
git clone https://github.com/zpskt/sentiment.git
cd sentiment
```
2. 创建环境
```shell
conda create -n sentiment --override-channels -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/ python=3.12.11
```
3. 安装依赖
```shell
conda activate sentiment
pip install -r requirements.txt
#pip install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple/
```
4. 使用cuda
查看cuda是否支持
```shell
python -c "import torch; print(torch.mps.is_available())"
```
去[官网](https://pytorch.org/get-started/locally/)获取cuda版本安装 （注意需要看你的cuda版本，原则向下兼容）
```shell
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```

## 通用化服务
使用[魔塔社区](https://www.modelscope.cn/models/iic/nlp_structbert_nli_chinese-large)模型，使用modelscope提供的模型进行推理

### 使用
运行Api服务
```shell
uvicorn app/sentiment_analysis_api:app --reload
```
api调用示例
```shell
python app/test_sentiment_analysis_api.py
```
## 定制化服务
预训练模型通过huggingface下载
1. 准备训练数据
将训练数据放置于data/train.csv
2. 下载预训练模型
```shell
wget -P model/bert-base-chinese https://hf-mirror.com/google-bert/bert-base-chinese/resolve/main/pytorch_model.bin
```
或者配置huggingface镜像
```shell
export HF_ENDPOINT=https://hf-mirror.com
```

2.开始训练
```shell
python train.py 
```
3. 查看训练结果 
训练结果放置于results文件夹下 
模型检查点目录结构

| 文件名              | 说明                                                         |
|---------------------|--------------------------------------------------------------|
| [config.json](file://D:\zpskt\sentiment\model\bert-base-chinese\config.json)       | 模型配置文件，保存模型的超参数和架构配置信息   |              |
| `model.safetensors` | 模型权重文件，使用 safetensors 格式存储模型参数              |
| `optimizer.pt`      | 优化器状态文件，保存优化器的参数和状态，用于恢复训练         |
| `rng_state.pth`     | 随机数生成器状态文件，确保训练过程的可重现性                 |
| `scheduler.pt`      | 学习率调度器状态文件，保存学习率调整策略的状态               |
| `trainer_state.json`| 训练器状态文件，记录训练过程中的各种状态信息                 |
| `training_args.bin` | 训练参数文件，保存训练时使用的命令行参数配置                 |

该目录保存了训练过程中的模型检查点，包含模型权重、配置和训练状态等文件
用于模型的恢复训练或推理部署
当使用时，加载模型选择某个文件夹模型即可，要保证结构与我的一致
### 使用
运行Api服务
```shell
```
api调用示例
```shell
```

# 常用命令
## 备份环境
```shell
pip install pip-chill
pip-chill > requirements.txt
conda list --explicit > requirements.txt
```
## 查看是否支持cuda
```shell
python -c "import torch; print(torch.cuda.is_available())" #linux
python -c "import torch; print(torch.mps.is_available())" #macos
```
## 查看系统GPU占用
```shell
  nvidia-smi
```
