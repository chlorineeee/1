# 1操作系统上机代码
# 【准备环境】创建工作目录
mkdir -p ~/qwen-ov
cd ~/qwen-ov

# 【准备环境】创建Python虚拟环境并激活
python3 -m venv openvino_env
source openvino_env/bin/activate

# 【准备环境】升级pip
python3 -m pip install --upgrade pip

# 【准备环境】安装基础工具
pip install wheel setuptools

# 【下载 requirements.txt】（从官方github）
wget https://raw.githubusercontent.com/OpenVINO-dev-contest/Qwen2.openvino/main/requirements.txt

# 【安装依赖包】
pip install -r requirements.txt

# 【下载模型】设置huggingface国内镜像
export HF_ENDPOINT=https://hf-mirror.com

# 【下载Qwen1.5-0.5B-Chat模型】（大约1.2G）
huggingface-cli download --resume-download --local-dir-use-symlinks False Qwen/Qwen1.5-0.5B-Chat --local-dir ./Qwen1.5-0.5B-Chat

# 【克隆OpenVINO转换代码】
git clone https://github.com/OpenVINO-dev-contest/Qwen2.openvino.git
cd Qwen2.openvino

# 【执行转换】（这里默认使用fp16格式，比较安全稳定）
python3 convert.py --model_id ../Qwen1.5-0.5B-Chat --precision fp16 --output ../Qwen1.5-0.5B-Chat-ov

# 【回到主目录】
cd ~/qwen-ov

# 【启动聊天测试】
python3 Qwen2.openvino/chat.py --model_path ./Qwen1.5-0.5B-Chat-ov --max_sequence_length 4096 --device CPU
