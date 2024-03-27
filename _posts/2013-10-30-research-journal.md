---
title: '【研究工作记录】ICP学习与调研'
date: 2013-10-30
permalink: /posts/research-journal-2013-10/
tags:
  - research journal
---

research journal, 2013.10.

## 2013年10月8日

### 一、课程：

模式识别，8：00，三节。

### 二、工作内容：

1.利用PCL中点云匹配的有关内容跑了几个与ICP相关的简单实验。测试程序中点云收敛前进行了20轮以上的迭代（没有附加限制条件），效果不错，但耗时相对也比较久。不过由于PCL库中太过于模块化，还没有研究具体是用哪种算法实现的。

2.重新跑了一下毕设中用原始方法实现的ICP算法。因为程序中涉及到OpenCV库，重新装了一下，但开始时没有编译生成pdb文件，所以程序一直无法运行，解决这个问题花费了比较长的时间。

## 2013年10月9日

### 一、课程：

政治，14：00，两节；线性系统理论，18：30，三节。

### 二、工作内容：

阅读两篇文献，关于ICP中对应点匹配的加速算法。

[Simon] Fast and Accurate Shape-Based Registration

是本书，主要看了下其中关于用kd-tree加速匹配点对搜索的那部分。

[Blais] Registering Multiview Range Data to Create 3D Computer Objects

利用逆标定的方法将某一点投影到另一个点集上，来加速匹配点的搜索过程。是在激光测距仪的应用背景下提出的，前提条件是要给出初始的变换。

## 2013年10月10日

### 一、课程：

无（智能预测控制停课一次）。

### 二、工作内容：

1.看了些点云融合有关的内容

我想要了解的是有关重合区域检测与匹配点融合的相关内容，而我之前找到的文献并不多，很多侧重点都在之后的表面重建与形状识别上。

[Dorai 98] Registration and Integration of Multiple Object Views for 3D Model Construction

这篇文章在点云匹配之后的融合处理中用到了一种加权平均的算法，而其中的权重是根据点云中某点到邻近边界的距离算出来的，这样做的好处是在匹配不准确时也可以避免表面重建时出现断层的情况。

[Zhou 05] Incremental point-based integration of registered multiple range images

[Zhou 08] Accurate integration of multi-view range images using k-means clustering

同一作者先后发表的两篇文章，利用最近点搜索来进行重合区域的检测，而匹配点融合时也是运用了加权平均的方法，其中权重是根据定义一种点的置信率来计算的。

2.周五汇报工作准备。

主要从点云匹配和点云融合两个方面来进行，结合近期读过的一些文献以及毕业设计和假期所做的相关工作。

## 2013年10月14日

### 一、课程：

机器人学，8：00，三节；机器人仿真技术，18：30，两节。

### 二、工作内容：

通过上周五的汇报，感觉自己之前对SLAM，扫描匹配等概念的认识还是很模糊，所以在做调研或者解决一些碰到的问题的时候都是局限在自己的思路中。

这次根据老师提到的一些问题，以及对之前涉及到概念的一些新的认识，用上个周末和今天下午的时间了解了一下实验室之前在扫描匹配这一领域所做的一些工作（我能找到的，一共两篇，列在参考文献中），也对其中用到的关键性算法进行了简单的了解。

参考文献：

一种基于几何统计特征的全局扫描匹配方法_郭瑞

基于扫描匹配的移动机器人三维环境建图研究_郑杰

## 2013年10月15日

### 一、课程：

模式识别，8：00，三节。

### 二、工作内容：

了解了一下ICP之外另一种局部匹配的方法，正态分布转换法（NDT）。

[Biber 03] The Normal Distributions Transform: A New Approach to Laser Scan Matching

将扫描点云投影到二维平面上（？），栅格化，针对每一栅格用正态分布来建模，用整体分布的特征来进行匹配。

之前对点云数据处理的认识一直比较直观也很局限，觉得可供处理的信息就只有空间点的坐标，不过从这篇文章上来看，可以经过特定的变换将坐标信息转换为其他的信息再进行判别或处理（类似信号处理中时域与频域的变换？）。

## 2013年10月16日

### 一、课程：

政治，14：00，两节；线性系统理论，18：30，三节。

### 二、工作内容：

1.想要运行一下PCL中提供的NDT算法看看效果，但安装好的PCL库缺少一个包含文件，我找了头文件的源码自己添加了一个之后也还没有编译通过…

2.[Tomoto 04] A Scan Matching Method using Euclidean Invariant Signature for Global Localization and Map Building

利用霍夫变换来提取一个扫描点的特征。

以一个扫描点为原点，其切线为x轴建立坐标系，将该点附近一定范围作为一个signature（特征区域？），将signature中所有点变换到该扫描点的坐标系下（使其对平移与旋转具有不变性）。再对signature中所有点进行霍夫变换，在极坐标系下进行表示（如果应用场景中直线比较多，如室内场景，则霍夫变换会将直线对应成一个点，实现数据的压缩）。这样经过霍夫变换后的signature就构成了该扫描点的特征描述。

## 2013年10月18日

### 一、课程：

计算机视觉，8：00，三节；建模与辨识，14：00，三节。

### 二、工作内容：

[1] Using a Depth Camera for Indoor Robot Localization and Navigation

[2] Depth Camera based Localization and Navigation for Indoor Mobile Robots

[3] KinectFusion: Real-Time Dense Surface Mapping and Tracking

[1]和[2]两篇文献都是用Kinect来完成移动机器人的室内定位与导航，不过都是只用到了深度数据做特征抽取没有用到RGB数据。

[3]是微软研究院的一个项目，手持Kinect在一个空间中移动，实时重建三维场景，并可以实现交互。因为用的是GPU所以实时性特别好（每秒30帧甚至更多）。我觉得算法上可能也会有些可借鉴的地方所以仔细看了一下，不过原理性的叙述太多还不是很理解…

## 2013年10月21日

### 一、课程：

机器人学，8：00，三节。

### 二、工作内容：

1.看了些PCL官网中关于点云特征抽取的方法，并利用其中提供的feature类进行了简单的编程实现（估计表面向量）。

2.[Gerhard] Keeping Track of Position and Orientation of Moving Indoor Systems by Correlation of Range-Finder Scans

提出的是用直方图来描述整个扫描的方法。为了同时具有旋转和位移的不变性，所以采用的是角度直方图，用向量来描述整个扫描（不知道是表面法向量还是测距仪与扫描点之间的有向线段当做一个向量），而角度直方图中的每个元素是相邻向量间向量差与一个反向轴的夹角。得到两幅扫描的直方图后，用两个直方图之间的互相关的关系来进行匹配（寻找某一互相关函数的最大值）。

## 2013年10月22日

### 一、课程：

模式识别，8：00，三节。

### 二、工作内容：

1.（接昨天）

[1] Keeping Track of Position and Orientation of Moving Indoor Systems by Correlation of Range-Finder Scans

[2] Histogram Matching and Global Initialization for Laser-only SLAM in Large Unstructured Environments

（[1]是昨天看的那篇，昨天还差一点没看完）

其实[1]的方法是需要构建两种直方图，一种以角度为横轴，通过计算最大的互相关性来确定两个扫描帧之间的旋转变换；另一种是投影到x和y轴上的直方图，用来确定位移变换。

 [1]提到的方法应用是针对室内，因为它需要有对墙面（竖直平面）的检测才可以完成匹配。[2]是基于[1]中的方法做的一些改进，提出一种entropy sequence（熵序列？）的方法，代替角度直方图来进行旋转变换的估计，因为在扫描数据比较杂乱的室外环境中角度直方图也会比较杂乱，不易得到主峰值，会带来很大误差，而熵序列仍可以进行匹配。

2.了解了PCL中关于点特征直方图（PFH）的方法实现。

大体方法是建立某点的局部坐标系，再划定一定范围的邻域，计算邻域内两两点之间的四个参数（三个角度和欧式距离），这些四元组以某种统计的方式放进直方图中，最终构成一个125浮点数元素的特征向量。

## 2013年10月23日

### 一、课程：

线性系统理论，18：30，三节。

### 二、工作内容：

1.Loop closure detection in SLAM by combining visual and spatial appearance

结合视觉特征和空间特征来进行闭环检测，文章前半部分分别介绍了从视觉角度和空间特征来进行特征提取，描述以及构建一个误差函数来检测的方法。

视觉特征描述用的是SIFT描述符（应该会有实时性的问题吧）。空间特征提取是基于一种累积角度函数的方法，先将扫描数据分割段，每一段作为一个节点，段与段之间是节点间的连接线（如下图），然后节点用累积角度函数与函数的熵值来描述，连接用一些关键点来描述。最后定义三种归一化的误差值作为检测的依据。

![img](https://sunqinxuan.github.io/images/posts-research-journal-2013-10-img1.png)

2.在IEEE中找到一些关于Kinect定位和建图的文献。

## 2013年10月24日

### 一、课程：

智能预测控制， 8：00，三节。

### 二、工作内容：

下面是昨天在IEEE上找到的关于Kinect定位建图的文献，括号标注的那些大致都浏览了一遍，几乎所有的应用场景都是室内（others文件夹中有一个室外应用的，不过扫描场景也都比较规则，柱子什么的）。有三篇以上的文献用的都是ICP进行匹配，也都对彩色图像进行了处理（特征提取）来辅助ICP的实现。

![img](https://sunqinxuan.github.io/images/posts-research-journal-2013-10-img2.png)

[1] Simultaneous Localization and Mapping with the Kinect sensor

Hill climbing局部匹配方法：通过当前帧与全局地图的匹配来估计当前位姿，将上次迭代结果按某一固定距离值向8个方向平移，再向两个方向旋转一个特定角度，得到27个位姿估计，对每个位姿分别计算cumulative quality measure（累积质量度量，由当前帧与全局地图之间所有点距离差与颜色差的加权和来确定的），从而选择出一个相对较准确的位姿。

在这个过程中应用到RANSAC来进行点云子集的选取，达到加速的目的。

![img](https://sunqinxuan.github.io/images/posts-research-journal-2013-10-img3.png)
![img](https://sunqinxuan.github.io/images/posts-research-journal-2013-10-img4.png)

以上分别是相对误差以及运行时间（整个点云数据量是30万）。

我感觉虽然运用RANSAC加速大幅的减少了运行时间但还是不太能达到实时性的要求，只是在运行时间与相对误差的度量中取了折中，而且上面那种hill climbing算法看上去是比较耗时的（对于当前帧点云中每一点都要在地图中找到最临近体素，计算距离再求和，再加上颜色差值的计算）。

[2] 3D Environmental Mapping of Mobile Robot Using a Low-cost Depth Camera

用surf对彩色图像进行特征提取，得到一系列特征点，对应三维空间的特征点运行RANSAC来去除一些匹配不好的outliers以及估计出初始变换，在此基础上再对点云运行ICP进行精确匹配。

我之前的思路与这篇文章中的也有点相似，不同的是这里面用到RANSAC对特征点进行处理并为ICP估计初始位姿。但surf算法运行并不算快，而且如果是对整个点云来运行ICP的话就算有初始位姿也是比较慢的，不知道具体是如何保证的实时性（实验结果中也没有提到），还是又进行了额外的处理。

## 2013年10月28日

### 一、课程：

机器人学， 8：00，三节；机器人仿真技术，18：30，两节。

### 二、工作内容：

看了那两篇提到可以实时运行的Kinect建图文献。

[1] 3-D Mapping With an RGB-D Camera

[2] Occupancy Mapping and Surface Reconstruction Using Local Gaussian Processes With Kinect Sensors

其中[1]也是之前看过几篇中的那种思路：提取特征点-初始位姿估计-ICP匹配的流程。但文中所提到的系统中用到了GPU，做到了完全实时（几百帧以内可以达到几十毫秒一帧的处理速度）。

[2]中提出的是一种新的算法（之前看过的文献中都没见过），似乎也能做到实时处理，构建一幅完整地图的时间是10秒以内，但没有明确给出帧数的信息。由于文章中提到的算法我之前没有接触过，看了两遍也没太理解，准备明天再查查资料仔细研究下。

## 2013年10月29日

### 一、课程：

模式识别， 8：00，三节。

### 二、工作内容：

用另外一种特征点检测算法（FAST）来进行图像特征点提取并测试其速度与Surf比较。FAST提取特征点的过程确实比Surf要快很多，FAST基本上可以做到50~60ms/帧（图像大小480*640，特征点数量200~300），而Surf在相同前提下的速率只能达到300ms/帧。

尝试实现利用图像提取到的特征点，用RANSAC估计初始变换，看是否有可能做到实时（还没完成）。

RGB-D mapping: Using Kinect-style depth cameras for dense 3D modeling of indoor environments中首次提出的利用抽取图像特征点再用RANSAC来估计初始位姿，再用ICP精确匹配。注意到之后很多关于Kinect建图的文献中都采用了这样的整体思路，所以想在此基础上再做些尝试。

## 2013年10月30日

### 一、课程：

政治，14：00，两节；线性系统理论，18：30，三节。

### 二、工作内容：

1.用ransac（OpenCV中提供的）实现了变换矩阵的估计，速度倒是很快（筛选后147个点，每帧10ms以下），但还没有判断估计出来的变换准确性如何。因为看了一些资料上面说OpenCV中关于3D数据处理的功能不如平面图像处理那么完善。

2.具体测了一下之前写的ICP各个部分的运行时间，发现实际上最耗时的部分是将变换作用于点云的步骤（我是采用每求一次变换都直接作用于点云，再针对变换后点云来计算下一步的变换，所以每一次迭代都会重复一次这个步骤）。下面我准备想办法优化一下这一步，顺便用最终结果来判断一下ransac的估计结果。
