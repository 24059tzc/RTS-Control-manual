# 校准方法

## 条件

Action校准时需保证静止不动，温度从25度到27度之间。

## 上位机校准

连接上位机，按“开始校准”后静置约十五分钟，等待提示校准完成。部分情况下可能无效，使用串口校准方法。

## 串口校准

在供电正常工作的情况下使用串口向该模块发送字符串“ACTR”，校准开始；首先会返回类似“start2653”的数据，表示现在是从26度53开始校准的，建议从25度到27度之间开始校准，继续保持供电，大概十五分钟后，串口会返回“check”表示校准结束。校准过程中请保持该模块处于绝对静止状态中。

若“start”后的温度大于27度，将action置于冰箱中约十分钟后取出重新校准。
