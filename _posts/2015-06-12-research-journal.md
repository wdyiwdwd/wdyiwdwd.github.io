---
title: '【研究工作记录】2015-06-点云预处理'
date: 2015-06-12
permalink: /posts/research-journal-2015-06/
tags:
  - research journal
---

research journal, 2015.06.

## 前期工作

### 基于Kinect的室内环境三维点云地图创建

Overview of RGB-D mapping[^1]

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img1.jpg)

[^1]: RGB-D mapping: Using Kinect-style depth cameras for dense 3D modeling of indoor environments, The International Journal of Robotics Research, 2012.

### RGB-D建图中常用数据预处理方法

- 均匀降采样（常用）

- 直通滤波或低通滤波

- 去除离群点

特点：以降低运算量与避免错误信息干扰为主要目的，通常不考虑原始点云中的结构信息。

## 工作思路

- 有针对性压缩数据量

  - 对原始点云数据的结构信息进行描述
  - 根据结构信息的描述控制局部降采样率

- 加强对环境细节描绘

- 同时在扫描匹配过程中加入对结构信息的考虑

### Kinect点云数据精度对法向量计算的影响

随着距离的增加，深度信息获取精度下降，导致点云生成精度下降。实际的测试中（Kinect1.0）在depth>3m时，基于局部平面拟合生成的点云法向量或曲率已无法较为准确地描述点云结构信息。

与三维物体表面重建不同的是，在用Kinect进行室内环境建图中，相对远距离（>3m）点云数据点较为常见的现象。

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img2.png)

### Grid-based analysis of the point cloud

A 3D uniform grid is constructed and used to partition the point cloud.

For every grid, compute the mean and covariance of 3d coordinates and normal vectors of the points inside. Covariance of 3d coordinates represents whether the points are from the same surface. Covariance of normal vectors represents the consistency of the normal vectors.

Construct a binary tree based on point set partition to form a hierarchical representation. Between every two grids, the global consistency can be defined.

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img3.png)

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img4.png)

### Binary tree construction[^2]

The root node contains all the points in one grid. A parent node in the tree can be split into two child nodes, each representing a certain partition of the points in the parent node. Partition is performed based on the eigenvector corresponding to the largest eigenvalue of the 3d coordinate. For every level of the tree, the partition process is stopped when certain criterion is satisfied.

[^2]: Qingqiong Deng, Xiaopeng Zhang. Grid-based View-Dependent Foliage Simplification.  Journal of Computational Information Systems, 2008

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img5.png)

### 最大特征值阈值法实验结果

场景一：30\\(\times\\)40栅格

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img6.jpg)

场景一：15\\(\times\\)20栅格

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img7.jpg)

场景二：15\\(\times\\)20栅格

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img8.jpg)

### 栅格内平面拟合

15\\(\times\\)20栅格，只加权Kinect深度信息。

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img9.png)

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img10.png)

加权Kinect深度信息以及二叉树节点深度。

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img11.png)

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-06-img12.png)

存在问题：

- 区分度不够大，对局部信息描述的精度不高（通过加权节点深度略有改善）

- 点云结构信息局限于平面&和非平面

- 因数据采集精度等原因，两帧之间的对应关系不易把握









