---
title: "导航相机与TOF相机配准与外参优化"
collection: projects
type: "Project"
permalink: /projects/2023-01-17-extrinsic-calibration
venue: "ZJLab"
date: 2023-01-17
location: "Beijing, China"
---

导航相机与TOF相机配准与外参优化

<img src="https://sunqinxuan.github.io/images/projects-2023-01-17-img0.png" alt="architecture" />

## 实验结果——第1组（来自third_round.bag）

### 初始标定外参的Canny边缘提取结果

导航左相机图像的Canny边缘提取结果，以绿色显示；

TOF相机图像的Canny边缘提取结果，通过初始标定外参与内参，投影到导航左相机的图像像素坐标系下，以红色显示。

初始外参为离线标定的外参。

<img src="https://sunqinxuan.github.io/images/projects-2023-01-17-img1.png" alt="4_edge_original" style="zoom: 50%;" />

### 经过外参优化再投影的Canny边缘结果

通过最近邻边缘点关联与导航相机-TOF相机外参优化，使用优化后的外参再投影后的结果。

<img src="https://sunqinxuan.github.io/images/projects-2023-01-17-img2.png" alt="4_edge_calib" style="zoom: 50%;" />


## 实验结果——第2组（来自third_round.bag）

### 初始标定外参的Canny边缘提取结果

<img src="https://sunqinxuan.github.io/images/projects-2023-01-17-img3.png" alt="edges_original" style="zoom:50%;" />

### 经过外参优化再投影的Canny边缘结果

<img src="https://sunqinxuan.github.io/images/projects-2023-01-17-img4.png" alt="edges_calib" style="zoom:50%;" />

## 实验结果——第3组（来自run1.bag）

### 初始标定外参的Canny边缘提取结果

<img src="https://sunqinxuan.github.io/images/projects-2023-01-17-img5.png" alt="1_edge_org" style="zoom:57%;" />

### 经过外参优化再投影的Canny边缘结果

<img src="https://sunqinxuan.github.io/images/projects-2023-01-17-img6.png" alt="1_edge_calib" style="zoom:57%;" />

## 初步实验结果——第4组（来自run1.bag）

### 初始标定外参的Canny边缘提取结果

<img src="https://sunqinxuan.github.io/images/projects-2023-01-17-img7.png" alt="3_edge_org" style="zoom:57%;" />

### 经过外参优化再投影的Canny边缘结果

<img src="https://sunqinxuan.github.io/images/projects-2023-01-17-img8.png" alt="3_edge_calib" style="zoom:57%;" />


## 相关链接

论文：
- [Online Extrinsic Calibration of RGB and ToF Cameras for Extraterrestrial Exploration](https://sunqinxuan.github.io/publication/CCC2023)

代码：
- [tof_rgb_calibration](https://github.com/sunqinxuan/tof_rgb_calibration)










