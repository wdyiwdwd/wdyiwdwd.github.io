---
title: "无人机仿真平台搭建过程记录"
collection: projects
type: "Project"
permalink: /projects/2023-02-23-uav-simulation
venue: "ZJLab"
date: 2023-02-23
location: "Beijing, China"
---

无人机仿真平台搭建过程记录

<iframe width="1255" height="675" src="https://www.youtube.com/embed/mDv9pnbBqMI" title="uav sim" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

# 仿真环境安装与配置

## 依赖安装

```shell
sudo apt install ninja-build exiftool ninja-build protobuf-compiler libeigen3-dev genromfs xmlstarlet libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev python-pip python3-pip gawk
pip2 install pandas jinja2 pyserial cerberus pyulog==0.7.0 numpy toml pyquaternion empy pyyaml 
pip3 install packaging numpy empy toml pyyaml jinja2 pyargparse
```

## ROS安装

详见ROS官网[Installation/Ubuntu - ROS Wiki](http://wiki.ros.org/Installation/Ubuntu)。

创建工作空间：

```shell
mkdir -p ~/catkin_ws/src
mkdir -p ~/catkin_ws/scripts
cd catkin_ws/src && catkin_init_workspace
cd .. && catkin_make 
```

## Gazebo安装

先卸载原有Gazebo（以下若无特殊说明，均以ubuntu 20.04(ros-noetic)为例）：

```shell
sudo apt-get remove gazebo* 
sudo apt-get remove libgazebo*
sudo apt-get remove ros-noetic-gazebo* 
```

Gazebo安装详见官网[Gazebo : Tutorial : Ubuntu (gazebosim.org)](https://classic.gazebosim.org/tutorials?tut=install_ubuntu&cat=install)。

注意：若选用Alternative installation: step-by-step的安装方式，安装gazebo9，不要安装gazebo11。按步骤装完Gazebo后，升级所有的包 sudo apt upgrade，这样能保证gazebo所有依赖版本一致。

安装相关依赖：

```shell
sudo apt-get install ros-melodic-moveit-msgs ros-melodic-object-recognition-msgs ros-melodic-octomap-msgs ros-melodic-camera-info-manager  ros-melodic-control-toolbox ros-melodic-polled-camera ros-melodic-controller-manager ros-melodic-transmission-interface ros-melodic-joint-limits-interface
```

下载XTDrone源码：

```shell
git clone https://gitee.com/robin_shaun/XTDrone.git
cd XTDrone
git submodule update --init --recursive
```

然后进行编译：

```shell
cd ~/catkin_ws
cp -r ~/XTDrone/sitl_config/gazebo_ros_pkgs src/
catkin_make
```

下载[models.zip](https://www.yuque.com/attachments/yuque/0/2021/zip/985678/1614564736994-3ac600e4-75fc-44ef-9cf5-3f1c3f0c8679.zip)，解压缩后放在`~/.gazebo`文件夹中。

## MAVROS安装

```shell
sudo apt install ros-noetic-mavros ros-noetic-mavros-extras
wget https://gitee.com/robin_shaun/XTDrone/raw/master/sitl_config/mavros/install_geographiclib_datasets.sh
sudo chmod a+x ./install_geographiclib_datasets.sh
sudo ./install_geographiclib_datasets.sh
```

## PX4安装配置

### 从github直接安装

若github下载速度可以接受，可以直接使用以下命令行进行安装：

```shell
git clone https://github.com/PX4/PX4-Autopilot.git
mv PX4-Autopilot PX4_Firmware
cd PX4_Firmware
git checkout -b xtdrone/dev v1.11.0-beta1
git submodule update --init --recursive
make px4_sitl_default gazebo
```

### 通过gitee安装

若github太慢，也可以通过gitee进行下载：

```shell
git clone https://gitee.com/robin_shaun/PX4_Firmware
cd PX4_Firmware
git checkout -b xtdrone/dev v1.11.0-beta1
```

下面需要逐一修改各个`submodule`，每修改一个`.gitmodules`文件，都需要通过命令行`git submodule upodate --init`进行下载：

(1) `~/PX4_Firmware/.gitmodules`

```shell
[submodule "mavlink/include/mavlink/v2.0"]
	path = mavlink/include/mavlink/v2.0
	url = https://gitee.com/robin_shaun/c_library_v2.git
	branch = master
[submodule "src/drivers/uavcan/libuavcan"]
	path = src/drivers/uavcan/libuavcan
	url = https://gitee.com/robin_shaun/uavcan.git
	branch = px4
[submodule "Tools/jMAVSim"]
	path = Tools/jMAVSim
	url = https://gitee.com/robin_shaun/jMAVSim.git
	branch = master
[submodule "Tools/sitl_gazebo"]
	path = Tools/sitl_gazebo
	url = https://gitee.com/robin_shaun/sitl_gazebo.git
	branch = master
[submodule "src/lib/matrix"]
	path = src/lib/matrix
	url = https://gitee.com/robin_shaun/Matrix.git
	branch = master
[submodule "src/lib/ecl"]
	path = src/lib/ecl
	url = https://gitee.com/robin_shaun/ecl.git
	branch = master
[submodule "boards/atlflight/cmake_hexagon"]
	path = boards/atlflight/cmake_hexagon
	url = https://gitee.com/robin_shaun/cmake_hexagon.git
	branch = px4
[submodule "src/drivers/gps/devices"]
	path = src/drivers/gps/devices
	url = https://gitee.com/robin_shaun/GpsDrivers.git
	branch = master
[submodule "src/modules/micrortps_bridge/micro-CDR"]
	path = src/modules/micrortps_bridge/micro-CDR
	url = https://gitee.com/robin_shaun/micro-CDR.git
	branch = px4
[submodule "platforms/nuttx/NuttX/nuttx"]
	path = platforms/nuttx/NuttX/nuttx
	url = https://gitee.com/robin_shaun/NuttX.git
	branch = px4_firmware_nuttx-9.1.0+
[submodule "platforms/nuttx/NuttX/apps"]
	path = platforms/nuttx/NuttX/apps
	url = https://gitee.com/robin_shaun/NuttX-apps.git
	branch = px4_firmware_nuttx-9.1.0+
[submodule "platforms/qurt/dspal"]
	path = platforms/qurt/dspal
	url = https://gitee.com/robin_shaun/dspal.git
[submodule "Tools/flightgear_bridge"]
	path = Tools/flightgear_bridge
	url = https://gitee.com/robin_shaun/PX4-FlightGear-Bridge.git
	branch = master 
[submodule "Tools/jsbsim_bridge"]
	path = Tools/jsbsim_bridge
	url = https://gitee.com/robin_shaun/px4-jsbsim-bridge.git
[submodule "src/examples/gyro_fft/CMSIS_5"]
	path = src/examples/gyro_fft/CMSIS_5
	url = https://gitee.com/mirrors/CMSIS_5
```

(2) `~/PX4_Firmware/src/drivers/uavcan/libuavcan/.gitmodules`

```shell
[submodule "dsdl"]
	path = dsdl
	url = https://gitee.com/robin_shaun/dsdl
	branch = legacy-v0
[submodule "libuavcan/dsdl_compiler/pyuavcan"]
	path = libuavcan/dsdl_compiler/pyuavcan
	url = https://gitee.com/robin_shaun/pyuavcan
[submodule "libuavcan_drivers/kinetis"]
	path = libuavcan_drivers/kinetis
	url = https://gitee.com/robin_shaun/libuavcan_kinetis.git
```

(3) `~/PX4_Firmware/src/drivers/uavcan/libuavcan/libuavcan/dsdl_compiler/pyuavcan/.gitmodules`

```shell
[submodule "dsdl"]
	path = dsdl
	url = https://gitee.com/robin_shaun/dsdl
```

(4) `~/PX4_Firmware/Tools/jMAVSim/.gitmodules`

```shell
submodule "jMAVlib"]
	path = jMAVlib
	url = https://gitee.com/robin_shaun/jMAVlib
	branch = master
```

(5) `~/PX4_Firmware/Tools/sitl_gazebo/.gitmodules`

```shell
[submodule "external/OpticalFlow"]
	path = external/OpticalFlow
	url = https://gitee.com/robin_shaun/OpticalFlow
```

(6) `~/PX4_Firmware/platforms/qurt/dspal/.gitmodules`

```shell
[submodule "cmake_hexagon"]
	path = cmake_hexagon
	url = https://gitee.com/robin_shaun/cmake_hexagon
```

(7) `~/PX4_Firmware/Tools/sitl_gazebo/external/OpticalFlow/gitmodules`

```shell
[submodule "external/klt_feature_tracker"]
	path = external/klt_feature_tracker
	url = https://gitee.com/robin_shaun/klt_feature_tracker
```

全部`submodule`都更新完成后，安装必要的依赖：

```shell
cd ~/PX4_Firmware
bash ./Tools/setup/ubuntu.sh --no-nuttx --no-sim-tools
```

编译：

```shell
make px4_sitl_default gazebo
```

此时可能出现报错信息：

```shell
ERROR [px4] is_server_running: failed to create lock file: /tmp/px4_lock-0, reason=Permission denied
INFO  [px4] Creating symlink /home/sun/PX4_Firmware/ROMFS/px4fmu_common -> /home/sun/PX4_Firmware/build/px4_sitl_default/tmp/rootfs/etc
ERROR [px4_daemon] error binding socket /tmp/px4-sock-0, error = Address already in use
INFO  [px4] Calling startup script: /bin/sh etc/init.d-posix/rcS 0
ERROR [px4] is_server_running: failed to create lock file: /tmp/px4_lock-0, reason=Permission denied
ERROR [px4] Failed to communicate with daemon: Permission denied
ERROR [px4] is_server_running: failed to create lock file: /tmp/px4_lock-0, reason=Permission denied
ERROR [px4] Failed to communicate with daemon: Permission denied
```

若出现以上报错信息，运行：

```shell
cd /tmp
sudo chmod 777 px4_lock-0
sudo chmod 777 px4-sock-0
sudo chown username px4_lock-0
sudo chown username px4-sock-0
```

编译完成后，会弹出Gazebo界面，将其关闭即可。

修改`~/.bashrc`文件，加入以下语句：

```sh
source ~/catkin_ws/devel/setup.bash
source ~/PX4_Firmware/Tools/setup_gazebo.bash ~/PX4_Firmware/ ~/PX4_Firmware/build/px4_sitl_default
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/PX4_Firmware
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/PX4_Firmware/Tools/sitl_gazebo
```

再运行：

```
source ~/.bashrc
```

之后可以运行如下命令启动Gazebo：

```shell
cd ~/PX4_Firmware
roslaunch px4 mavros_posix_sitl.launch
```

## XTDrone配置

之前在Gazebo安装中已经下载过XTDrone源码，下面需要再进行相关配置：

```sh
cd ~/XTDrone
cp sensing/gimbal/gazebo_gimbal_controller_plugin.cpp ~/PX4_Firmware/Tools/sitl_gazebo/src/
cp sitl_config/init.d-posix/rcS ~/PX4_Firmware/ROMFS/px4fmu_common/init.d-posix/
cp sitl_config/worlds/* ~/PX4_Firmware/Tools/sitl_gazebo/worlds/
cp -r sitl_config/models/* ~/PX4_Firmware/Tools/sitl_gazebo/models/ 
cp -r sitl_config/launch/* ~/PX4_Firmware/launch/
cd ~/.gazebo/models/
rm -r stereo_camera/ 3d_lidar/ 3d_gpu_lidar/ hokuyo_lidar/
```

再编译一次PX4：

```sh
cd ~/PX4_Firmware
make px4_sitl_default gazebo
```

## 用键盘控制无人机飞行

在一个终端运行

```bash
cd ~/PX4_Firmware
roslaunch px4 indoor1.launch
```

- 注意，用ctrl+c关闭仿真进程，有可能没有把Gazebo的相关进程关干净，这样再启动仿真时可能会报错。如果出现这种情况，可以用killall -9 gzclient，killall -9 gzserver 这两个命令强行关闭gazebo所有进程。

Gazebo启动后，在另一个终端运行（注意要等Gazebo完全启动完成，或者可能脚本会报错）

```bash
cd ~/XTDrone/communication/
python multirotor_communication.py iris 0
```

与0号iris建立通信后，在另一个终端运行

```bash
cd ~/XTDrone/control/keyboard
python multirotor_keyboard_control.py iris 1 vel
```

便可以通过键盘控制1架iris的解锁/上锁(arm/disarm)，修改飞行模式，飞机速度等。使用v起飞利用的是takeoff飞行模式，相关参数（起飞速度、高度）要在rcS中设置。一般可以使用offboard模式起飞，这时起飞速度要大于0.3m/s才能起飞(即：upward velocity 需要大于0.3)。注意，飞机要先解锁才能起飞！飞到一定高度后可以切换为‘hover’模式悬停，再运行自己的飞行脚本，或利用键盘控制飞机。

推荐起飞流程，按i把向上速度加到0.3以上，再按b切offboard模式，最后按t解锁。

# 无人机导航算法仿真

## 视觉惯性里程计（VINS-Fusion）

### VINS-Fusion编译

```sh
cp -r ~/XTDrone/sensing/slam/vio/VINS-Fusion ~/catkin_ws/src/
mkdir ~/catkin_ws/scripts/
cp ~/XTDrone/sensing/slam/vio/xtdrone_run_vio.sh ~/catkin_ws/scripts/
cd ~/catkin_ws
catkin_make
```

针对ubuntu20.04的编译错误修改：

1. 报错信息：`error: ‘CV_GRAY2BGR’/‘CV_BGR2GRAY’/‘CV_AA’ was not declared in this scope`

   解决方案：

   报错文件中添加以下头文件：

   ```c++
   #include <opencv2/imgproc/types_c.h>
   #include <opencv2/imgproc/imgproc_c.h>
   ```

2. 报错信息：` error: ‘CV_CALIB_CB_ADAPTIVE_THRESH’/ ‘CV_CALIB_CB_NORMALIZE_IMAGE’/ ‘CV_CALIB_CB_FILTER_QUADS’/ 'CV_CALIB_CB_FAST_CHECK' was not declared in this scope`

   解决方案：

   `‘CV_CALIB_CB_ADAPTIVE_THRESH’/ ‘CV_CALIB_CB_NORMALIZE_IMAGE’/ ‘CV_CALIB_CB_FILTER_QUADS’/ 'CV_CALIB_CB_FAST_CHECK'`

   修改为`‘cv::CALIB_CB_ADAPTIVE_THRESH’/ ‘cv::CALIB_CB_NORMALIZE_IMAGE’/ ‘cv::CALIB_CB_FILTER_QUADS’/ 'cv::CALIB_CB_FAST_CHECK'`。

3. 报错信息：`error: ‘CV_FONT_HERSHEY_SIMPLEX’ was not declared in this scope`

   解决方案：

   `‘CV_FONT_HERSHEY_SIMPLEX’`修改为`‘cv::FONT_HERSHEY_SIMPLEX’`。

4. 报错信息：`error: ‘CV_LOAD_IMAGE_GRAYSCALE’ was not declared in this scope`

   解决方案：

   `‘CV_LOAD_IMAGE_GRAYSCALE’`修改为`‘cv::IMREAD_GRAYSCALE’`。

### PX4飞控配置

PX4默认使用的EKF配置为融合GPS的水平位置与气压计高度。如果想使用视觉定位，需要修改配置文件，使得EKF融合来自`mavros/vision_pose/pose`的数据。

修改rcS文件：

```shell
vim ~/PX4_Firmware/ROMFS/px4fmu_common/init.d-posix/rcS
```

```shell
	# GPS used
	#param set EKF2_AID_MASK 1
	# Vision used and GPS denied
	param set EKF2_AID_MASK 24

	# Barometer used for hight measurement
	#param set EKF2_HGT_MODE 0
	# Barometer denied and vision used for hight measurement
	param set EKF2_HGT_MODE 3
```

重启仿真前，需要删除上一次记录在虚拟eeprom中的参数文件，否则仿真程序会读取该参数文件，导致本次rcS的修改不能生效。

```sh
rm ~/.ros/eeprom/parameters*
rm -rf ~/.ros/sitl*
```

### VINS-Fusion仿真

在配置文件中，设置需要订阅的话题。

```sh
vim ~/catkin_ws/src/VINS-Fusion/config/xtdrone_sitl/px4_sitl_stereo_imu_config.yaml
```

```sh
imu_topic: "/iris_0/imu_gazebo"
image0_topic: "/iris_0/stereo_camera/left/image_raw"
image1_topic: "/iris_0/stereo_camera/right/image_raw"
output_path: "/home/robin/catkin_ws/vins_output"
...
pose_graph_save_path: "/home/robin/catkin_ws/vins_output/pose_graph/"  # save and load path
```

启动仿真程序（launch文件中使用的是iris_stereo_camera.sdf）。

```sh
roslaunch px4 indoor1.launch
```

启动VINS-Fusion。

```sh
cd ~/catkin_ws
bash scripts/xtdrone_run_vio.sh
```

将VINS-Fusion发布的`Odometry`类型的话题转换为PX4所需的话题类型，并通过查看`/iris_0/mavros/local_position/pose`，确定是否正确接收用于飞控的数据。

```sh
cd ~/XTDrone/sensing/slam/vio
python vins_transfer.py iris 0
```

然后建立通信，键盘控制起飞。

```sh
cd ~/XTDrone/communication
python multirotor_communication.py iris 0 
```

```sh
cd ~/XTDrone/control/keyboard
python multirotor_keyboard_control.py iris 1 vel
```

### 单目VINS系统的初始化

VINS-Fusion的双目+IMU可以在静止条件下完成初始化，因此不用额外考虑。但单目+IMU以及ORBSLAM3的单目/双目+IMU均需要在运动中初始化，在实物系统中，我们可以用手晃一晃飞机实现初始化，而仿真中必须起飞才能让无人机运动，但PX4的offboard模式如果没有定位数据，又无法起飞，这就造成了矛盾。因此仿真里只能用Gazebo真值给PX4定位，然后在飞行过程中初始化VIO，VIO的输出只用于测试精度，并不实际给无人机定位用。

Gazebo提供了无人机位姿姿态的真实值，作用类似于实物系统中的动作捕捉系统。位姿真值主要有两个作用，一是对于集群编队等任务，提供了可靠准确的全局定位；二是用于分析SLAM算法的精度。下面简述使用方法。

首先要启动通信脚本，该脚本会中转Gazebo的真值位姿话题

```bash
cd  ~/XTDrone/communication
python multirotor_communication.py iris 0
```

然后启动获取位姿真值的脚本，脚本命令行参数的“1”表示1架无人机

```bash
cd ~/XTDrone/sensing/pose_ground_truth/
python get_local_pose.py iris 1 
```

值得注意的是，该脚本默认发布的是`/mavros/vision_pose/pose`话题，如果是用于分析SLAM的精度，该话题和SLAM程序发布的话题名不能冲突，也就是SLAM程序和该程序只能有一个发布`/mavros/vision_pose/pose`。

## 运动规划（Fast-Planner）

### Fast-Planner编译

#### 安装Armadillo库

```sh
sudo apt-get install libarmadillo-dev
```

#### 从源码编译和安装nlopt

```sh
sudo apt purge ros-noetic-nlopt # 如果已安装ros-noetic中的nlopt库
git clone https://github.com/stevengj/nlopt.git
cd nlopt
mkdir build
cd build
cmake ..
make
sudo make install
```

#### 下载Fast-Planner

```sh
cd ~/catkin_ws/src
git clone https://github.com/HKUST-Aerial-Robotics/Fast-Planner.git
```

#### 针对ubuntu20.04与ros noetic的修改：

修改`/Fast-Planner/uav_simulator/Utils/odom_visualization/src/odom_visualization.cpp`文件：全文搜索`/world`，并全部替换为`world`。

修改`/Fast-Planner/fast_planner/path_searching/src/kinodynamic_astar.cpp`文件：搜索`KinodynamicAstar::timeToIndex`函数，添加函数返回值。

```c++
int KinodynamicAstar::timeToIndex(double time)
{
    int idx = floor((time - time_origin_) * inv_time_resolution_);
    return idx;
}
```

修改`Fast-Planner/fast_planner/plan_env/include/edt_environment.h`第69和71行，把返回值类型修改成void，再去对应cpp文件中84到118行做同样修改。

```c++
// Fast-Planner/fast_planner/plan_env/include/edt_environment.h

void interpolateTrilinear(double values[2][2][2], const Eigen::Vector3d& diff,
                          double& value, Eigen::Vector3d& grad);
void evaluateEDTWithGrad(const Eigen::Vector3d& pos, double time,
                         double& dist, Eigen::Vector3d& grad);
```

```c++
// Fast-Planner/fast_planner/plan_env/src/edt_environment.cpp

void EDTEnvironment::interpolateTrilinear(double values[2][2][2],
                                           const Eigen::Vector3d& diff,
                                           double& value,
                                           Eigen::Vector3d& grad) {
  // trilinear interpolation
  double v00 = (1 - diff(0)) * values[0][0][0] + diff(0) * values[1][0][0];
  double v01 = (1 - diff(0)) * values[0][0][1] + diff(0) * values[1][0][1];
  double v10 = (1 - diff(0)) * values[0][1][0] + diff(0) * values[1][1][0];
  double v11 = (1 - diff(0)) * values[0][1][1] + diff(0) * values[1][1][1];
  double v0 = (1 - diff(1)) * v00 + diff(1) * v10;
  double v1 = (1 - diff(1)) * v01 + diff(1) * v11;

  value = (1 - diff(2)) * v0 + diff(2) * v1;

  grad[2] = (v1 - v0) * resolution_inv_;
  grad[1] = ((1 - diff[2]) * (v10 - v00) + diff[2] * (v11 - v01)) * resolution_inv_;
  grad[0] = (1 - diff[2]) * (1 - diff[1]) * (values[1][0][0] - values[0][0][0]);
  grad[0] += (1 - diff[2]) * diff[1] * (values[1][1][0] - values[0][1][0]);
  grad[0] += diff[2] * (1 - diff[1]) * (values[1][0][1] - values[0][0][1]);
  grad[0] += diff[2] * diff[1] * (values[1][1][1] - values[0][1][1]);
  grad[0] *= resolution_inv_;
}

void EDTEnvironment::evaluateEDTWithGrad(const Eigen::Vector3d& pos,
                                          double time, double& dist,
                                          Eigen::Vector3d& grad) {
  Eigen::Vector3d diff;
  Eigen::Vector3d sur_pts[2][2][2];
  sdf_map_->getSurroundPts(pos, sur_pts, diff);

  double dists[2][2][2];
  getSurroundDistance(sur_pts, dists);

  interpolateTrilinear(dists, diff, dist, grad);
}
```

修改`Fast-Planner/fast_planner/bspline_opt/CMakeLists.txt`文件。

```cmake
cmake_minimum_required(VERSION 2.8.3)
project(bspline_opt)

find_package(Eigen3 REQUIRED)
find_package(PCL 1.7 REQUIRED)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
  visualization_msgs
  cv_bridge
  plan_env
)

catkin_package(
 INCLUDE_DIRS include
 LIBRARIES bspline_opt
 CATKIN_DEPENDS plan_env
#  DEPENDS system_lib
)

include_directories( 
    SYSTEM 
    include 
    ${catkin_INCLUDE_DIRS}
    ${Eigen3_INCLUDE_DIRS} 
    ${PCL_INCLUDE_DIRS}
)

set(CMAKE_CXX_FLAGS "-std=c++11 ${CMAKE_CXX_FLAGS} -O3 -Wall")

add_library( bspline_opt 
    src/bspline_optimizer.cpp 
    )
target_link_libraries( bspline_opt
    ${catkin_LIBRARIES} 
    nlopt
    )  
```

#### 编译Fast-Planner

```sh
catkin_make -DCMAKE_CXX_STANDARD=14
```

### Fast-Planner测试

在两个终端分别运行：

```sh
source devel/setup.bash && roslaunch plan_manage rviz.launch
```

```sh
source devel/setup.bash && roslaunch plan_manage kino_replan.launch
```

### 运动规划仿真

ego_planner需要输入深度图+相机位姿或是点云，这里以深度图+相机位姿的组合为例进行仿真，深度图来源于realsense_camera，相机位姿由VINS-Fusion计算得到。

将`indoor1.launch`中的sdf文件设置为`iris_realsense_camera`，将飞机用键盘控制起飞后悬停，关闭键盘控制。

```sh
# 启动仿真环境
roslaunch px4 indoor1.launch

# 启动VINS-Fusion
cd ~/catkin_ws
bash scripts/xtdrone_run_vio.sh

# 转换Odometry话题类型
cd ~/XTDrone/sensing/slam/vio
python vins_transfer.py iris 0

# 启动通信
cd ~/XTDrone/communication
python multirotor_communication.py iris 0 

# 启动键盘控制
cd ~/XTDrone/control/keyboard
python multirotor_keyboard_control.py iris 1 vel

# 启动Rviz界面，选择目标点
# 待优化
roslaunch plan_manage rviz_xt.launch

# 启动Fast-Planner
roslaunch plan_manage kino_replan_xt.launch
```






