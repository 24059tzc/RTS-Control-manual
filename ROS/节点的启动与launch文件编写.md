# 节点的启动与launch文件编写

## 启动节点

```bash
#在roscore已经启动的情况下
rosrun pkg_name node_name
```

## launch文件编写

### 1 launch文件功能

使用launch文件，可以同时启动多个ros节点，包括master节点。其基本思想是在一个XML格式的文件内将需要同时启动的一组节点罗列出来。

```bash
roslaunch package-name launch-file-name
```

### 2 创建launch文件

具体可参考：[http://wiki.ros.org/roslaunch/XML/]()

#### 2.1 launch文件的节点属性

具体可参考：[http://wiki.ros.org/roslaunch/XML/launch]()

##### 2.1.1 param

param标记定义了要在参数服务器上设置的参数。可以指定文本文件、二进制文件或命令属性来设置参数值。param标记可以放在node标记中，在这种情况下，该参数被视为私有参数。

###### 1 param属性

（1）name=“namespace/name”
参数名称。名称空间可以包含在参数名中，但应避免全局指定的名称。

（2）value=“value”(可选)
定义参数的值。如果省略此属性，则必须指定binfile、textfile或command。

（3）type=“str/int/double/bool/yaml”（可选）
指定参数的类型。如果不指定类型，roslaunch将尝试基于以下规则自动确定类型。
∙ \bullet∙ 带“.”的数字是浮点，否则是整数；
∙ \bullet∙ “true”和“false”是布尔值（不区分大小写）。
∙ \bullet∙ 所有其他值都是字符串

（4）textfile="$(find pkg-name)/path/file.txt"（可选）
文件的内容将被读取并作为字符串存储。

（5）binfile="$(find pkg-name)/path/file"(可选)
文件的内容将被读取并存储为base64编码的XML-RPC二进制对象。

命令的输出将被读取并存储为字符串。

###### 2、例子

```html
<param name="publish_frequency"  type="double"  value="10.0" />
<rosparam command="load"  file="FILENAME" />  加载YAML文件
<param name="robot_description" command="$(arg urdf_file)" />
```

##### 2.1.2 rosparam

rosparam一次性将多个参数加载到参数服务器中. 我们将需要设置的参数放到 .yaml 文件中。它还可以用于删除参数。rosparam标记可以放在node标记中，在这种情况下，该参数被视为私有名称。

###### 1 rosparam属性

（1）command=“load/dump/delete” (可选, default=load)
rosparam命令。

（2）file="$(find pkg-name)/path/foo.yaml" (load or dump commands)
rosparam文件的名称。

（3）param=“param-name”
参数名称。

（4）ns=“namespace” (可选)
将参数范围设置为指定的命名空间。

（5）subst_value=true|false (可选)
允许在yaml文本中使用替换参数。

###### 2 例子

```html
<rosparam command="load" file="$(find rosparam)/example.yaml" />
1
<rosparam command="delete" param="my/param" />
1
<rosparam>
  a: 1
  b: 2
</rosparam>
```

##### 2.1.3 node

node标记指定要启动的ros节点。这是最常见的roslaunch标签，因为它支持最重要的功能：启动和关闭节点。roslaunch并不保证节点的起始顺序。

###### 1 node属性

（1）pkg=“mypackage”
节点的package，相当于 rosrun 命令后面的第一个参数。

（2）type=“nodetype”
rosrun 命令的第二个参数；节点类型。必须有一个同名的对应可执行文件。

（3）name=“nodename”
该节点的名字，相当于代码中 ros::init() 中设置的信息，有了它代码中的名称会被覆盖。注意：名称不能包含命名空间。请改用ns属性。

（4）args=“arg1 arg2 arg3”(optional)
启动参数，将参数传递给节点。

（5）machine=“machine-name”(optional, see )
在指定的计算机上启动节点。

（6）respawn=“true”(optional, default: False)
如果退出，则自动重新启动节点。

（7）respawn_delay=“30” (optional, default 0) New in ROS indigo
如果reshawn为true，则在检测到节点故障后等待reshawn_delay秒，然后再尝试重新启动。

（8）required=“true”(optional)
如果节点终止，终止整个roslaunch。

（9）ns=“foo”(optional)
在“foo”命名空间中启动节点。

（10）clear_params=“true/false”(optional)
在启动之前删除节点私有命名空间中的所有参数。

（12）output=“log/screen”(optional)
如果“screen”，则节点的stdout/stderr将发送到屏幕。
如果是“log”，则stdout/stderr输出将被发送到$ros_home/log中的日志文件，并且stderr将继续发送到屏幕。默认值为“log”。

（13）cwd=“ROS_HOME/node”(optional)
如果是“node”，则该节点的工作目录将设置为与该节点的可执行文件相同的目录。

（14）launch-prefix=“prefix arguments”(optional)
在节点的启动参数前添加命令/参数。这是一个强大的特性，使您能够启用gdb、valgrind、xterm、nice或其他方便的工具。

###### 2 例子

```html
<node name="listener1" pkg="rospy_tutorials" type="listener.py" args="--test" respawn="true" />
```

使用命令行参数test从rospy_tutorials包中的listener.py可执行文件启动“listener1”节点。如果节点终止了，它将自动被重启。

```html
<node name="bar1" pkg="foo_pkg" type="bar" args="$(find baz_pkg)/resources/map.pgm" />
```

从foo_pkg包启动bar节点。本例使用替换参数将可移植引用传递给baz_pkg/resources/map.pgm。

##### 2.1.4 remap

remap标记允许您以比直接设置node的args属性更结构化的方式将名称重新映射参数传递给要启动的ros节点。remap标记应用于其作用域（launch、node或group）中的所有后续声明。

###### 1 remap属性

（1）from=“original-name”
正在重新映射的名称。

（2）to=“new-name”
目标名称。

###### 2例子

```html
<remap from="chatter" to="hello"/>
```

例如，给定一个节点，该节点表示它订阅“chatter”主题，但只有一个节点发布“hello”主题。它们是相同类型的，您希望通过以下方式将“hello”主题导入希望“chatter”的新节点。

##### 2.1.5 machine

machine标记声明一台可以在其上运行ros节点的机器。如果要在本地启动所有节点，则不需要此标记。它主要用于为远程计算机声明ssh和ros环境变量设置，但也可以使用它声明有关本地计算机的信息。

###### 1 machine属性

（1）name=“machine-name”
要分配给计算机的名称。这对应于用于node标记的machine属性的名称。

（2）address=“blah.willowgarage.com”
计算机的网络地址/主机名。

（3）env-loader="/opt/ros/fuerte/env.sh"
指定远程计算机上的环境文件。环境文件必须是一个shell脚本，用于设置所有必需的环境变量，然后对提供的参数运行exec。

（4）default=“true|false|never” (可选)
将此计算机设置为要分配节点的默认值。默认设置仅适用于稍后在同一范围内定义的节点。注意：如果没有默认机器，则使用本地机器。您可以通过设置default=“never”来阻止选择机器，在这种情况下，只能显式分配机器。

（5）user=“username” (可选)
用于登录到计算机的ssh用户名。如果不需要，可以省略。

（6）password=“passwhat”(强烈推荐)
ssh密码

（7）timeout=“10.0” (可选)
此计算机上的roslaunch被视为无法启动之前的秒数。默认情况下，这是10秒。虽然您可以使用此设置来允许较慢的连接，但需要更改此参数通常是您的整个ROS图将出现通信问题的症状。

###### 2 例子

```html
<launch>
  <machine name="foo" address="foo-address" env-loader="/opt/ros/fuerte/env.sh" user="someone"/>
  <node machine="foo" name="footalker" pkg="test_ros" type="talker.py" />
</launch>
```

上面的示例显示如何配置节点“footalker”以运行另一台计算机。它使用fuerte附带的默认env loader文件。

##### 2.1.6 include

include标记允许您将另一个roslaunch xml文件导入当前文件。它将在文档的当前范围内导入，包括group和remap标记。将导入包含文件中的所有内容，但master标记除外：master标记仅在最上层文件中遵守。

###### 1 include属性

（1）file="$(find pkg-name)/path/filename.xml"
要包含的文件名。

（2）ns=“foo” (可选)
导入相对于“foo”命名空间的文件。

（3）clear_params=“true|false” (可选 Default: false)
启动前删除include命名空间中的所有参数。此功能非常危险，应小心使用。必须指定ns。默认值：false。

（4）pass_all_args=“true/false” （可选 Default: false） ( Indigo 和 Jade as of roslaunch version 1.11.17)
如果为true，则当前上下文中设置的所有参数都将添加到为处理包含的文件而创建的子文件上下文中。您可以这样做，而不是显式列出要传递的每个参数。

###### 2 例子

```html
<include file="$(find region_cover_start)/launch/amcl.launch" />
```

##### 2.1.7 env

env标记允许您在启动的节点上设置环境变量。此标记只能在launch、include、node或machine标记的范围内使用。当在launch标记内使用时，env标记仅适用于之后声明的节点。
注意：使用env标记设置的值不会被$(env…)看到，因此env标记不能用于参数化启动文件。

###### 1 env属性

（1）name=“environment-variable-name”
正在设置的环境变量名称。

（2）value=“environment-variable-value”
设置环境变量的值。

##### 2.1.8 test

test标记在语法上与node标记相似。它们都指定要运行的ros节点，但是test标记表示该节点实际上是要运行的测试节点。

##### 1 test属性

test标记共享大多数normal node属性，除了：
没有reshawn属性（测试节点必须终止，因此它们不可重新生成）。
没有输出属性，因为测试使用自己的输出日志记录机制。
忽略计算机属性。

##### 2.1.9 arg

arg标记允许通过指定命令行传递、通过include传递或为更高级别文件声明的值来创建更多可重用和可配置的启动文件。args不是全局的。arg声明特定于单个启动文件，非常像方法中的本地参数。必须显式地将arg值传递给包含的文件，这与在方法调用中一样。

###### 1 arg属性

（1）name=“arg_name”
参数的名称。

（2）default=“default value” (optional)
参数的默认值。不能与值属性组合。

（3）value=“value” (optional)
参数值。不能与默认属性组合。

（4）doc=“description for this arg” (optional) New in Indigo
参数的说明。

###### 2 例子

例1：向包含的文件传递参数

```html
<include file="included.launch">
  <!-- all vars that included.launch requires must be set -->
  <arg name="hoge" value="fuga" />
</include>
```

```html
<launch>
  <!-- declare arg to be passed in -->
  <arg name="hoge" />
<param name="param" value="$(arg hoge)"/>
</launch>
```

<!-- read value of arg -->

例2：通过命令行传递参数
roslaunch使用与ros remapping参数相同的语法来指定arg值。

```bash
roslaunch my_file.launch hoge:=my_value      (.launch file is available at the current dir)
roslaunch %YOUR_ROS_PKG% my_file.launch hoge:=my_value
```

##### 2.1.10 group

group标记使设置更容易应用于一组节点。它有一个ns属性，允许您将节点组推送到一个单独的命名空间中。您还可以使用remap标记在组中应用remap设置。

###### 1 group属性

（1）ns=“namespace” (optional)
将节点组分配给指定的命名空间。名称空间可以是全局名称空间或相对名称空间，但不鼓励使用全局名称空间。

（2）clear_params=“true/false” (optional)
启动前删除组命名空间中的所有参数。此功能非常危险，应小心使用。必须指定ns。

###### 2 例子

```html
<group ns="group_name">
</group>
```

#### 3 launch文件解析

##### 3.1 以urdf问基础的launch文件并启动rviz

```html
<launch>
<!--参数声明，就是要启动的urdf文件路径-->
    <arg name="model" /> 
<!--参数声明及赋值，是否启用关节转动控制面板窗口-->
    <arg name="gui" default="False" />
<!--通过定义全局变量，告知launch文件启动时把全局变量robot_description中存储的模型文件加载到rviz中-->
    <param name="robot_description" textfile="$(find smartcar_description)/urdf/smartcar.urdf" />
<!--设置GUI参数，显示关节插件-->
    <param name="use_gui" value="$(arg gui)"/>
<!--运行joint_state_publisher节点，发布机器人的关节状态（显示关节旋转了多少度等等）-->
    <node name="joint_state_publisher" pkg="joint_state_publisher" type="joint_state_publisher"/ >
<!-- 运行robot_state_publisher节点，发布tf（根据上面的关节状态，创建整个机器人的tf关系，并发布到系统中）-->
    <node name="robot_state_publisher" pkg="robot_state_publisher" type="state_publisher" />
<!-- 运行rviz可视化界面（args的参数作用类似于自定义rviz中的显示设置） -->
    <node name="rviz" pkg="rviz" type="rviz" args="-d $(find urdf_tutorial)/urdf.vcg" />
</launch>
```

##### 3.2 以xacro为基础的launch文件并启动rviz

```html
<launch>
<!--设置参数,是否使用仿真时间-->
    <param name="/use_sim_time" value="false" />
<!-- 加载我们机器人的urdf/xacro模型-->
    <arg name="urdf_file" default="$(find xacro)/xacro.py '$(find smartcar_description)/urdf/smartcar.urdf.xacro'" />
 <!--参数声明及赋值，是否启用关节转动控制面板窗口-->   
    <arg name="gui" default="false" />
<!--通过定义全局变量，告知launch文件启动时把全局变量robot_description中存储的模型文件加载到rviz中-->
    <param name="robot_description" command="$(arg urdf_file)" />
<!-- 设置GUI参数，显示关节控制插件 -->    
    <param name="use_gui" value="$(arg gui)"/>

    <node name="arbotix" pkg="arbotix_python" type="driver.py" output="screen">
        <rosparam file="$(find smartcar_description)/config/smartcar_arbotix.yaml" command="load" />
        <param name="sim" value="true"/>
    </node>

<!-- 运行joint_state_publisher节点，发布机器人的关节状态  -->

    <node name="joint_state_publisher" pkg="joint_state_publisher" type="joint_state_publisher" >
    </node>

<!-- 运行robot_state_publisher节点，发布tf  -->

    <node name="robot_state_publisher" pkg="robot_state_publisher" type="state_publisher">
        <param name="publish_frequency" type="double" value="20.0" />
    </node>

<!-- 对轮子进行静态变换 -->

    <node pkg="tf" type="static_transform_publisher" name="odom_left_wheel_broadcaster" args="0 0 0 0 0 0 /base_link /left_front_link 100" />
    <node pkg="tf" type="static_transform_publisher" name="odom_right_wheel_broadcaster" args="0 0 0 0 0 0 /base_link /right_front_link 100" />

<!-- 运行rviz可视化界面 -->

    <node name="rviz" pkg="rviz" type="rviz" args="-d $(find smartcar_description)/urdf.vcg" />

</launch>
```

原文链接：[https://blog.csdn.net/wwyklnh/article/details/102722698]()
