## 微信安装
https://zhuanlan.zhihu.com/p/339286211

ubuntu20.04 安装微信 解决中文显示问题
当下，ubuntu桌面系统越来越受到开发者的喜爱，此外微信、QQ等聊天软件更是大家日常频繁使用的，本文针对ubuntu20.04，安装微信，并解决微信默认安装后显示乱码的问题。

1. 安装deepin-wine
~~~
wget -O- https://deepin-wine.i-m.dev/setup.sh 
sh ./setup.sh
~~~

2. 安装微信
~~~
sudo apt-get install deepin.com.wechat
~~~
如果安装后，在应用程序中找不到，可以重启电脑

此方法的缺点：截图发送时是以附件形式发送的，其他功能正常

常用应用及对应软件包名

|应用|包名|
|---|---|
|TIM|deepin.com.qq.office|
|QQ|deepin.com.qq.im|
|QQ轻聊版|deepin.com.qq.im.light|
|微信|deepin.com.wechat|
|百度网盘|deepin.com.baidu.pan|
|迅雷极速版|deepin.com.thunderspeed|
|WinRAR|deepin.cn.com.winrar|