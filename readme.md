# 阿克曼小车模型

将文件夹拷贝到工作空间 src 目录下，catkin_make，如果报错应该是没有安装依赖包，按编译提示安装即可。

可能缺少的功能包

```
sudo apt-get install ros-noetic-ackermann-msgs
sudo apt-get install ros-noetic-teb-local-planner
sudo apt-get install ros-noetic-ros-control
sudo apt-get install ros-noetic-ros-controllers
```

在终端输入 `roslaunch akmbot_gazebo teb_navigation.launch` 即可开始导航测试。  
第一次打开 rviz 可能会卡住，关了重新打开一次就行。

参考这个作者的博客，《阿克曼小车仿真》专栏  
[阿克曼移动机器人](https://blog.csdn.net/qq_48427527/article/details/124589888)

## 1. akmbot_description

存放阿克曼小车模型 urdf 文件  
车身：`<box size="0.70 0.4 0.1"/> `  
车轮：`<cylinder radius="0.09" length="0.045" />`  
前轮转向角度：`<limit lower="-0.3576" upper="0.3576" effort="5.0" velocity="1000.0"/>`  
轴距：0.62m  
转弯半径：1.65m  
最大转向角：20.48°（0.3576）

传感器参数在 akmbot_description\urdf\akmbot.gazebo 里设置  
雷达测距同时在 akmbot_description\urdf\akmbot.gazebo，akmbot_gazebo/launch/gmapping.launch，akmbot_gazebo/launch/amcl.launch 中出现

## 2. akmbot_control

负责控制小车的运动  
在 script 文件夹里：  
**servo_commands.py** 负责控制关节的速度和转向角  
**keyboard_teleop.py** 实现用键盘控制小车  
**transform.py** 将控制小车运动的消息类型 ackermann_msgs/AckermannDriveStamped 转化为 cmd_vel 话题的 geometry_msgs.msg/Twist 消息类型，这里直接将 cmd_vel 中**z 方向的角速度直接作为旋转角**输出，并限制了加速度等参数。

## 3. akmbot_gazebo

实现建图和导航等功能  
主要看 launch 文件夹下的 teb_base.launch 文件和 teb_navigation.launch 文件

### akmbot_launch

启动 gazebo 仿真环境，设置小车模型的起点位置，**更换 world**在这里修改

### teb_base.launch

启动 move_base 节点，里面设置了 teb 局部规划器，**全局规划器**没有设置，可以改成你需要的

`<param name="base_global_planner" value="RAstarPlannerROS"/>`

**teb 参数**在 akmbot\akmbot_gazebo\config\teb\teb_local_planner_params.yaml, **目前参数没有调整**

### teb_navigation.launch

导航功能  
配置**地图**在这里修改`<arg name="map" default="room_mini.yaml" />`  
地图与 akmbot_launch 中的 world 文件对应

### gmapping.launch

使用 gmapping 建图，配合键盘控制完成

### slam_navi.launch

导航+建图
