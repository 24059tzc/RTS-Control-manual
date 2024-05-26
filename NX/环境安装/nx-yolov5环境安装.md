# NX-yolov5环境安装

## 安装cuda

~~~
sudo apt-get install cuda-11.4
~~~

## 安装cudnn

~~~
sudo apt-get install libcudnn8
~~~

## 安装pytorch

### nvidia官网下载pytorch安装包，然后安装

nvidia官网下载地址：https://forums.developer.nvidia.com/t/pytorch-for-jetson/72048

选择选用Jetpack5.0.2的版本(推荐1.12.0)

### 安装依赖库

~~~
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev libopenblas-base libopenmpi-dev
sudo apt install python3-pip
~~~

### 安装pytorch

~~~
sudo pip3 install  torch-1.12.0a0+2c916ef.nv22.3-cp38-cp38-linux_aarch64.whl
~~~

## 安装torchvision

选择对应版本

PyTorch v1.0 - torchvision v0.2.2

PyTorch v1.1 - torchvision v0.3.0

PyTorch v1.2 - torchvision v0.4.0

PyTorch v1.3 - torchvision v0.4.2

PyTorch v1.4 - torchvision v0.5.0

PyTorch v1.5 - torchvision v0.6.0

PyTorch v1.6 - torchvision v0.7.0

PyTorch v1.7 - torchvision v0.8.1

PyTorch v1.8 - torchvision v0.9.0

PyTorch v1.9 - torchvision v0.10.0

PyTorch v1.10 - torchvision v0.11.1

PyTorch v1.11 - torchvision v0.12.0

PyTorch v1.12 - torchvision v0.13.0

~~~
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install libopenblas-dev 
git clone --branch <version> https://github.com/pytorch/vision torchvision   # 根据pytorch版本选择对应的torchvision版本,推荐v0.13.0
cd torchvision
export BUILD_VERSION=0.x.0  # where 0.x.0 is the torchvision version 
sudo python3 setup.py install
~~~

## 验证

在python3环境下运行以下代码
~~~
import torch
print(torch.__version__)
print('CUDA available: ' + str(torch.cuda.is_available()))
print('cuDNN version: ' + str(torch.backends.cudnn.version()))
~~~

## 安装yolov5

~~~
git clone https://github.com/ultralytics/yolov5.git
cd yolov5
~~~
打开requirements.txt文件，将torchvision torch注释掉，然后运行以下命令
~~~
pip3 install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple some-package #时间较长
~~~

## 运行

~~~
python3 detect.py 
~~~
