# D435i环境配置

 参考链接：  https://zhuanlan.zhihu.com/p/371410573

[Ubuntu18.04+ROS Realsense的安装与使用_Twilight89的博客-CSDN博客](https://blog.csdn.net/a17381562089/article/details/115345082)

## 配置yolov5环境

参考学长写的文档 **nx—yolov5环境安装** 对nx配置yolov5环境

## 下载d435i运行文件

[]: https://gitcode.net/mirrors/Thinkin99/yolov5_d435i_detection

## 安装d435i驱动

先进入下载好的文件内：

```
cd yolov5_d435i_detection
```

运行[YOLOv5](https://so.csdn.net/so/search?q=YOLOv5&spm=1001.2101.3001.7020)的python环境：

```
pip install -r requirements.txt
```

[realsense](https://so.csdn.net/so/search?q=realsense&spm=1001.2101.3001.7020)相机和pyrealsense2库，注意该过程中不能接入摄像头：

```
cd 
git clone https://github.com/jetsonhacks/installRealSenseSDK.git
cd installRealSenseSDK
./buildLibrealsense.sh 
```

若出现cmake python路径相关的报错就修改./buildLibrealsense.sh脚本，找到/usr/bin/cmake ../这一行，修改为

/usr/bin/cmake ../ -DBUILD_EXAMPLES=true -DFORCE_LIBUVC=ON -DBUILD_WITH_CUDA="$USE_CUDA" -DCMAKE_BUILD_TYPE=release -DBUILD_PYTHON_BINDINGS=bool:true -DFORCE_RSUSB_BACKEND=ON -DPYTHON_EXECUTABLE=/usr/bin/python3
若无报错则开始等待大约一小时，安装完成后连接摄像头，检测能否正常使用

```
realsense-viewer
```

参看pyrealsense2是否安装正确

```
python3
import pyrealsense2
```

如果出现nomodule的报错则需要找到~/librealsense/build/wrappers/python该路径下的六个gnu文件，复制到 /usr/local/lib/python3.8中

该步骤结束后再编辑bashrc修改环境变量

```
export PYTHONPATH=$PYTHONPATH:/usr/local/lib/python3.8/pyrealsense2
添加这一行至bashrc中，然后
source ~/.bashrc
再次输入
import pyrealsense2
查看是否安装正确
```

## 运行realsense d435i

直接进入下载好的文件夹，双击打开rsrest.py

找到第69行和201行

```
69   def __init__(self, yolov5_yaml_path='config/yolov5s.yaml'):
201  model = YoloV5(yolov5_yaml_path='config/yolov5s.yaml')
```

将config/yolov5s.yaml全改为自己下载好的文件路径/home/amov/yolov5_d435i_detection/config/yolov5s.yaml

进入文件夹config打开yolov5s.yaml文件，将第3行的weights/yolov5s.pt改为/home/amov/yolov5_d435i_detection/weights/yolov5s.pt

保存后退出

在终端打开之前下载好的文件夹

```
cd yolov5_d435i_detection
```

运行

```
python rstest.py
```

如果出现报错

```
AttributeError:'Upsample' object has no attribute 'recompute_sale_factor'
```

进入报错文件，找到报错的位置

```
return F.interpolate(input, self.size, self.scale_factor, self.mode, self.align_corners,
                                       recompute_sale_factor=self.recompute_sale_factor)
```

将上面代码改为

```
return F.interpolate(input, self.size, self.scale_factor, self.mode, self.align_corners)
                                      # recompute_sale_factor=self.recompute_sale_factor)
```

保存后退出

再次运行

```
python rstest.py
```

运行成功
