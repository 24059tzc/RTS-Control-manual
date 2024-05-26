# CAN口ros使用教程

## ROS包安装

[ros_canopen - ROS Wiki](http://wiki.ros.org/ros_canopen)

在工作空间下克隆源文件，安装依赖项

```
git clone https://github.com/ros-industrial/ros_canopen.git
sudo apt-get install libmuparser-dev
```

## CAN口配置

```
sudo ip link set can0 type can bitrate 1000000#设置波特率为1M
sudo ip link set up can0#开启串口
```

## 开始使用

编译完成后启动节点

```
rosrun socketcan_bridge socketcan_to_topic_node#can通信到topic转换
rosrun socketcan_bridge topic_to_socketcan_node#topic到can通信转换
```

其中 /received_messages为接收 /sent_messages为发送，消息类型为can_msgs/Frame
