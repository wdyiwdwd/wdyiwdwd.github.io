---
title: "面向辅助驾驶应用的AVM环视鱼眼相机内外参标定"
collection: projects
type: "Project"
permalink: /projects/2021-09-21-avm-calibration
venue: "CICV"
date: 2021-09-21
location: "Beijing, China"
---

面向辅助驾驶应用的AVM环视鱼眼相机内外参标定

![img](https://sunqinxuan.github.io/images/projects-2021-09-21-img1.jpg)

![img](https://sunqinxuan.github.io/images/projects-2021-09-21-img2.jpg)

## 1.标定意义及目标

相机内参标定以及多传感器外参标定是定位与建图任务的基础，而标定精度也对SLAM系统的性能有着很重要的作用。内参标定包含相机内参矩阵、相机模型参数、以及模型畸变参数的估计，当已知相机内参后，便可以完成相机坐标系下3D点到相机成像平面的投影。而外参标定主要解决多传感器相对位姿计算的问题，是多传感器观测数据之间进行数据关联、对齐以及配准的基础。

## 2.鱼眼相机内参标定

### 2.1 鱼眼相机模型

为了兼容相机-车体外参标定中所使用的AprilTag定位代码，采用[OpenCV fisheye](https://docs.opencv.org/3.4/db/d58/group__calib3d__fisheye.html)模型对鱼眼相机内参进行标定。对于一个在相机坐标系下的3D点\\(X_c=(x,y,z)^T\\)，令

$$
a=\frac{x}{z},\ b=\frac{y}{z}\\
r=\sqrt{a^2+b^2}\\
\theta=\arctan(r)
$$

鱼眼相机图像畸变模型可以表示为

$$
\theta_d=\theta\left(1+k_1\theta^2+k_2\theta^4+k_3\theta^6+k_4\theta^8\right)
$$

经过模型畸变后，3D点在归一化平面的坐标\\((x',y')^T\\)为

$$
x'=\left(\frac{\theta_d}{r}\right)a \\
y'=\left(\frac{\theta_d}{r}\right)b
$$

最后鱼眼图像上的像素坐标\\((u,v)^T\\)为

$$
u=f_x\left(x'+\alpha y'\right)+c_x \\
v=f_y y'+c_y
$$

其中\\(f_x,f_y,c_x,c_y\\)为相机的内参。

### 2.2 鱼眼相机内参标定方案

鱼眼相机内参标定代码可见[avm_calibration](https://github.com/sunqinxuan/avm_calibration)下的opencv_calibration目录。

在进行相机内参标定时，首先对四个鱼眼相机分别进行标定数据的采集，如图2.2.1-图2.2.4所示。在进行数据采集时，应尽量使得标定板在各个维度进行相应的运动，在不离开视野区域的前提下，运动范围尽量遍布相机视野。

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img3.png" alt="1631690453.359431" style="zoom:50%;" />

<div><center>图2.2.1 右视鱼眼相机内参标定图像</center></div>

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img4.png" alt="1631690077.724006" style="zoom:50%;" />

<div><center>图2.2.2 前视鱼眼相机内参标定图像</center></div>

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img5.png" alt="1631690300.334805" style="zoom:50%;" />

<div><center>图2.2.3 左视鱼眼相机内参标定图像</center></div>

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img6.png" alt="1631690343.138452" style="zoom:50%;" />

<div><center>图2.2.4 后视鱼眼相机内参标定图像</center></div>

在标定图像采集完成后，在工程目录下建立`img_${camera}`文件夹存放采集好的标定图像，并生成`img_${camera}.txt`文件，列出所有标定图像的相对路径。以右视相机为例，标定图像存放在`./img_right`目录下，其对应的`./img_right.txt`文件内容为：

```
# ./img_right.txt
img_right/1631690408.524919.png
img_right/1631690409.663292.png
img_right/1631690410.457858.png
img_right/1631690411.546102.png
img_right/1631690412.438392.png
...	
```

按上述方法完成标定图像布署后，在工程目录(`./opencv_calibration`)下运行`run.sh`即可完成四个环视相机的内参标定。

```shell
# -------
# run.sh
# -------

#!/bin/sh

./calibration img_right.txt calib_right.yaml
./calibration img_front.txt calib_front.yaml
./calibration img_left.txt calib_left.yaml
./calibration img_rear.txt calib_rear.yaml

cp ./calib_right.yaml ../data/img/calib_right.yaml
cp ./calib_front.yaml ../data/img/calib_front.yaml
cp ./calib_left.yaml ../data/img/calib_left.yaml
cp ./calib_rear.yaml ../data/img/calib_rear.yaml
```

标定输出`calib_${camera}.yaml`配置文件，其中包含图像分辨率、相机内参矩阵和畸变模型参数，可直接用于相机-车体外参标定。以右视相机为例，其输出配置文件内容为：

```yaml
# ----------------
# calib_right.yaml
# ----------------

image_width: 1280
image_height: 720
camera_name: camera
camera_matrix:
  rows: 3
  cols: 3
  data: [429.74459114051712, 0, 619.22596643438362, 0, 429.83803063011919, 401.92878121320769, 0, 0, 1]
distortion_coefficients:
  rows: 1
  cols: 4
  data: [0.29938336892050299, 0.073557008355643466, -0.069200024479249625, 0.010450303044365006]
```

### 2.3 内参标定结果

相机内参矩阵和畸变参数记为

$$
K=
\begin{bmatrix}
f_x & 0 & c_x \\
0 & f_y & c_y \\
0 & 0 & 1
\end{bmatrix} \\
D=
\begin{bmatrix}
k_1 & k_2 & k_3 & k_4 
\end{bmatrix}
$$

四个环视相机以右下标的形式进行标识，记录内参标定结果如下：

$$
K_{right}=
\begin{bmatrix}
429.74 & 0 & 619.23 \\
0 & 429.84 & 401.93 \\
0 & 0 & 1
\end{bmatrix} \\
D_{right}=
\begin{bmatrix}
0.299 & 0.073 & -0.069 & 0.010 
\end{bmatrix}
$$

$$
K_{front}=
\begin{bmatrix}
433.16 & 0 & 595.30 \\
0 & 432.75 & 386.14 \\
0 & 0 & 1
\end{bmatrix} \\
D_{front}=
\begin{bmatrix}
0.309 & 0.063 & -0.040 & -0.012 
\end{bmatrix}
$$

$$
K_{left}=
\begin{bmatrix}
431.10 & 0 & 614.08 \\
0 & 431.83 & 399.31 \\
0 & 0 & 1
\end{bmatrix} \\
D_{left}=
\begin{bmatrix}
0.304 & 0.116 & -0.136 & 0.043
\end{bmatrix}
$$

$$
K_{rear}=
\begin{bmatrix}
431.71 & 0 & 654.41 \\
0 & 431.32 & 389.04 \\
0 & 0 & 1
\end{bmatrix} \\
D_{rear}=
\begin{bmatrix}
0.299 & 0.085 & -0.090 & 0.022
\end{bmatrix}
$$

### 2.4 内参标定结果评估

通过重投影误差的计算，对内参标定精度进行评估，如下表所示。

|                    | camera_right | camera_front | camera_left | camera_rear |
| ------------------ | ------------ | ------------ | ----------- | ----------- |
| reprojection error | 0.26px       | 0.28px       | 0.20px      | 0.21px      |



## 3.相机-车体外参标定

### 3.1 外参标定方案概述

考虑到四个环视相机固定在车体四周，而IMU安装在车体后轴中心，无法对该视觉-惯导系统进行有效的三轴激励。经过多方调研，最终确定基于AprilTag地面标定布的相机-车体外参标定方案。

在进行相机-车体外参标定时，将车停放在标定布中心空白区域，车的左后轮停于十字交点处，左前轮和右后轮分别位于十字竖线和横线上。

为了进一步保证AprilTag检测以及定位的准确性和鲁棒性，采用[Bundle Calibration](http://wiki.ros.org/apriltag_ros/Tutorials/Bundle%20calibration)的标定模式，对于由若干tag组成的bundle，计算tag码共同的bundle位姿。根据所选用的AprilTag码以及标定布尺寸，录入AprilTag配置文件`./src/apriltag_ros/apriltag_ros/config/tags.yaml `。

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img7.jpg" alt="calib_pattern_cap4" style="zoom:40%;" />

### 3.2 相机-车体外参标定

相机-车体外参标定代码可见[avm_calibration](https://github.com/sunqinxuan/avm_calibration)。

当车体位于标定布上正确位置后，便可以进行外参标定图像的采集，如图3.2.1-图3.2.4所示，并将采集好的标定图像放置于工程数据目录(`./data`)下，四个相机对应的目录组织方式与内参标定中相同。接着，生成文件`img.txt`，列出所有的标定图像文件名。

```
# ./data/img.txt
img1.jpg
img2.jpg
img3.jpg
...
```

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img8.png" alt="1632292117.299239" style="zoom:50%;" />

<div><center>图3.2.1 右视鱼眼相机外参标定图像</center></div>

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img9.png" alt="1632292117.299239" style="zoom:50%;" />

<div><center>图3.2.2 前视鱼眼相机外参标定图像</center></div>

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img10.png" alt="1632292117.299239" style="zoom:50%;" />

<div><center>图3.2.3 左视鱼眼相机外参标定图像</center></div>

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img11.png" alt="1632292117.299239" style="zoom:50%;" />

<div><center>图3.2.4 后视鱼眼相机外参标定图像</center></div>

按上述方式组织好采集的标定图像后，运行`image_to_msg`节点，发布标定图像，发布话题为`/cv_camera_${camera}/image_raw`以及`/cv_camera_${camera}/camera_info`。

```shell
rosrun image_to_msg image_to_msg
```

接着，运行`continuous_detection`节点，完成AprilTag的检测以及Tag码在相机坐标系下的位姿估计。

```shell
roslaunch apriltag_ros continuous_detection.launch 
```

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img12.jpg" alt="right" style="zoom:50%;" />

<div><center>图3.2.5 右视鱼眼相机AprilTag检测结果</center></div>

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img13.jpg" alt="front" style="zoom:50%;" />

<div><center>图3.2.6 前视鱼眼相机AprilTag检测结果</center></div>

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img14.jpg" alt="left" style="zoom:50%;" />

<div><center>图3.2.7 左视鱼眼相机AprilTag检测结果</center></div>

<img src="https://sunqinxuan.github.io/images/projects-2021-09-21-img15.jpg" alt="rear" style="zoom:50%;" />

<div><center>图3.2.8 后视鱼眼相机AprilTag检测结果</center></div>

在确保AprilTag正常检测后，运行`localize`节点，对定位结果进行整合，并通过已知tag bundle坐标系之间的转换关系，最终得到四个环视相机相对于地面标定布十字交点(即车体右后轮中心)的位置和姿态，并保存为`calib_extrinsic.yaml`文件。

```yaml
# --------------------
# calib_extrinsic.yaml
# --------------------

frame_id: bundle_all
pose_wheel_camera_right: [0.85776595366551245, -1.3297249786546665, 1.70759463357486, -0.0037461082991290216, 0.012662183501864755, 0.85994211722375269, -0.51022072753496184]
pose_wheel_camera_front: [3.4728684909554812, -0.73169474983827887, 0.92578770800106336, 0.32955744240001894, -0.6233047217244837, 0.62466249399100093, -0.33567824569915911]
pose_wheel_camera_left: [0.84327463846461403, -0.22055318829518084, 1.7250547956887143, 0.52043734275821096, -0.8538966208432508, 0.0017313909010818068, 0.0015923130339794826]
pose_wheel_camera_rear: [-0.77796316183817105, -0.77092134403012258, 1.2366664981364999, -0.34863975481374709, 0.60202525692481423, 0.62964173407872581, -0.34578490148408281]
```

### 3.3 外参标定结果

假设\\(\boldsymbol t_{wc}\\)和\\(\boldsymbol q_{wc}\\)分别表示相机坐标系相对于标定布十字交叉处（即车体后轮）坐标系的平移和旋转变换，四个环视相机以右下标的形式进行标识。

外参标定结果记录如下：

$$
\boldsymbol t_{wc\_right}=
\begin{bmatrix}
0.857 & -1.329 & 1.707
\end{bmatrix}^T \\
\boldsymbol q_{wc\_right}=
\begin{bmatrix}
-0.003 & 0.012 & 0.859 & -0.510 
\end{bmatrix}^T
$$

$$
\boldsymbol t_{wc\_front}=
\begin{bmatrix}
3.472 & -0.731 & 0.925 
\end{bmatrix}^T \\
\boldsymbol q_{wc\_front}=
\begin{bmatrix}
0.329 & -0.623 & 0.624 & -0.335
\end{bmatrix}^T
$$

$$
\boldsymbol t_{wc\_left}=
\begin{bmatrix}
0.843 & -0.220 & 1.725
\end{bmatrix}^T \\
\boldsymbol q_{wc\_left}=
\begin{bmatrix}
0.520 & -0.853 & 0.001 & 0.001
\end{bmatrix}^T
$$

$$
\boldsymbol t_{wc\_rear}=
\begin{bmatrix}
-0.778 & -0.771 & 1.236
\end{bmatrix}^T \\
\boldsymbol q_{wc\_rear}=
\begin{bmatrix}
-0.348 & 0.602 & 0.629 & -0.345
\end{bmatrix}^T
$$

### 3.4 外参标定结果评估

利用外参标定结果，推算得到地面标定布tag bundle之间的相对位姿，与groudtruth进行对比，对外参标定结果进行简单地评估，如下表所示。

|              | RMSE_position | RMSE_orientation | NEES_position |
| ------------ | ------------- | ---------------- | ------------- |
| camera_right | 0.013m        | 0.25&deg;        | 0.23%         |
| camera_front | 0.010m        | 0.28&deg;        | 0.12%         |
| camera_left  | 0.022m        | 0.50&deg;        | 0.66%         |
| camera_rear  | 0.007m        | 0.17&deg;        | 0.19%         |

## 4.相关链接

代码：
- [avm_calibration](https://github.com/sunqinxuan/avm_calibration)