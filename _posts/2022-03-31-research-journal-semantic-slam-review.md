---
title: 'Semantic SLAM 相关方案调研'
date: 2022-03-31
permalink: /posts/research-journal-2022-03-31-semantic-slam/
tags:
  - research journal
---

Semantic SLAM 相关方案调研.


## 高精地图语义定位

[[1]	Huayou Wang, Changliang Xue, Yanxing Zhou, Feng Wen and Hongbo Zhang. Visual Semantic Localization based on HD Map for Autonomous Vehicles in Urban Scenarios. 2021 IEEE International Conference on Robotics and Automation (ICRA 2021).](https://ieeexplore.ieee.org/document/9561459)

论文提出基于高精地图和语义特征的视觉主义定位算法。在城市道路环境中，语义特征易于提取，并且对光照、天气和视角等变化有着一定的鲁棒性。针对语义特征关联问题，提出了鲁棒关联算法，充分考虑了局部结构一致性、全局模式一致性以及时间序列一致性。另外，提出了基于滑动窗口的因子图优化框架，对特征关联结果以及里程计信息进行融合与优化。

### 方法流程

![img](http://sunqinxuan.github.io/images/posts-research-journal-2022-03-31-img1.png)

视觉图像语义特征提取：

- 这使用卷积神经网络方法YOLOV3，提取道路标识、电线杆、交通灯和指示牌作为语义特征。提取出的语义特征描述子为\\(s_t=(s_t^l,s_t^c,s_t^b)\\)，其中\\(s_t^l\\)表示语义类别，\\(s_t^c\\)表示置信度，\\(s_t^b\\)表示特征的几何描述信息。

与高精地图的语义特征关联：

- 在先验位姿附近采样生成候选位姿，对于每一个候选位姿，进行地图特征到当时图像帧的投影。

- 基于局部结构一致性的粗关联过程，根据特征在横向位置的分布情况，去除明显的误关联结果。

- 对粗关联结果进行进一步优化，融合匹配数据、匹配相似度和局部结构相似性等信息，得到最优的全局一致关联结果。

- 在时间相邻的图像序列上对特征进行跟踪。

- 通过时间域平滑过程，得到时间一致性的特征关联结果。

位姿图优化：

- 对于滑动窗口内的帧，使用非线性优化方法对位姿估计结果进行优化。具体误差项包括：里程计误差、观测重投影误差和地图特征匹配误差。

### 关键技术

-	算法提供基于高精地图和语义特征的定位方案，在进行局部地图构建的同时，完成局部地图特征与第三方高精地图特征的关联和匹配，为语义SLAM建图结果适配第三方高精地图的需求提供了一种可选的解决方案。

-	算法提出了具有时空一致性的语义特征关联方法，针对不同类型的语义特征，如果交通信号灯、指示牌等，采用不同的高层特征描述方法。

-	通过往图优化算法中加入提取特征与地图特征的匹配情况，完成基于高精地图的定位。

### 性能分析

-	算法在30km范围的城市道路中，可以达到0.43m的纵向定位精度，0.12m的横向定位精度，以及0.11deg的俯仰角精度。

-	算法主要依赖于低成本传感器，满足传感器成本的商业化需求。

-	论文未涉及实时性评估，并提到在未来工作中希望扩展框架来实现实时定位，因此推测目前算法不具备实时性。






## Compensation of aircraft magnetic fields

link:
[Compensation of aircraft magnetic fields (US2692970A, Walter E Tolles)](https://patents.google.com/patent/US2692970A)

![img](http://sunqinxuan.github.io/images/posts-research-journal-2024-04-03-img2.png)

> This invention relates to compensation of 
magnetic fields, and more particularly to the 
compensation of the effect in a given direction of the
magnetic field of an aircraft due to all sources.

> Considering the nature of the total magnetic
field of an aircraft, it will be seen that this 
magnetic field may be due to contributions from
several sources which produce magnetic field
components of different types. <u>Ferromagnetic
elements in the aircraft structure having high
retentivities become permanently magnetized and
each of then produces a magnetic field.</u> The sum
of all of these permanent magnetic field com
ponents is known as the **"perm" magnetic field** of
the aircraft and is <u>constant in magnitude and in
direction in respect to the aircraft</u>.

> <u>"Soft" ferromagnetic elements in the aircraft
structure are magnetized through induction by
the earth's magnetic field and each set up an
induced magnetic field which varies in magnitude
and direction with the geographical location of
the aircraft and its attitude in space.</u> The total
**induced imagnetic field** of the aircraft is the sum
of the induced fields of all "soft" ferromagnetic
structural elements, and its contribution to the
total magnetic field of the aircraft is accordingly
complex in nature.

> <u>Additional contributions to the total magnetic
field of the aircraft are produced by eddy 
currents which are induced in sheet conductors, as
for example in the metal skin forming portions
of the aircraft, or in metallic loops, such as 
control cable assenblies, whenever the attitude of the
aircraft changes.</u> The total **eddy-current 
magnetic field** contribution will thus be understood to
depend in a complex way upon the geographical
location of the aircraft, upon its attitude in space,
and upon the rate of change of the attitude of
the aircraft.

> When the measuring instrument installed in
the aircraft is a portable magnetometer of the
type including a magnetometer element and 
<u>arranged to be continuously oriented in the 
direction of the earth's magnetic field and to measure
magnetic field components in that direction only</u>,
special problems are encountered. Since the
magnetornetter element is so oriented as to 
maintain a substantially constant attitude in space,
the portion of the total magnetic field of the 
aircraft affecting the output of the magnetometer
will also vary with the attitude of the aircraft
in space.

Note that the original Tolles-lawson is not applied on a strapdown system.

> It is an object of the present invention to 
provide means whereby the various magnetic fields
contributing to the total magnetic field of an 
aircraft may, each in the presence of the other, be
compensated for their effect <u>on a magnetometer
arranged to measure magnetic fields in the 
direction of the earth's magnetic field</u>.

> Accordingly, there is provided a method of
compensating the magnetic field of an aircraft to
facilitate operation of a magnetometer mounted
therein for measuring magnetic field components
in a chosen direction which includes <u>choosing a
set of reference axes in respect to the aircraft;
resolving the perm, induced, and eddy-current
imagnetic fields of the aircraft into components
along these axes;</u> choosing a series of maneuvers
such that the output of the magnetometer is in
each case due to identifiable components of the
perm and induced magnetic fields, and to at
least one eddy-current magnetic field component; 
performing each maneuver of the series in
turn, temporarily eliminating the effect of the
perm and induced magnetic field components on
the magnetometer for each maneuver, and 
compensating the eddy-current magnetic field 
conponent thus identified; thereafter performing a
second set of maneuvers such that the output of
the magnetometer is due in the case of each
maneuver to identifiable components of the perm
and induced magnetic fields; and compensating
each of these components as identified.

> Referring to Fig. 1, a set of orthogonal 
reference axes x, y and z is chosen in respect to the
aircraft. <u>Conveniently, the reference system 
including these axes has its origin at the location
of the magnetometer element and is so chosen
that the x, y and z axes are parallel respectively
to the transverse, longitudinal and vertical axes
of the aircraft.</u> Angles X, Y and Z are direction
angles indicating respectively the orientation of
the x, y and z axes in respect to the earth's 
magnetic Vector designated in the drawing by the
arrow marked H.

> The total magnetic field due to all permanently
magnetized structures in the aircraft may be 
resolved into three components T, L and V, parallel
respectively to the x, y and z axes.

$$
\vec{H}_p=T\vec{i}+L\vec{j}+V\vec{k}
$$

> Since the magnetometer is arranged to measure
only magnetic field components in the direction
of the earth's magnetic field, the total perm 
magnetic field \\(\vec{H}_p\\) may be resolved in the direction
of the earth's magnetic vector H. The effective
perm magnetic field \\(H_{pd}\\) is then

$$
H_{pd}=T\cos X+L\cos Y+V\cos Z
$$

> The induced magnetic field of an aircraft 
effective in the direction of the earth's magnetic field
is given by the following expression.

$$
\begin{aligned}
H_{ip}=&H[TT\cos^2X+(LT+TL)\cos X\cos Y \\
&+LL\cos^2Y+(VT+TV)\cos X\cos Z \\
&+VV\cos^2Z+(LV+VL)\cos Y\cos Z]
\end{aligned}
\tag{3}
$$




