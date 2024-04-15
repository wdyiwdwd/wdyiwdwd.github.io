---
title: '车道线检测相关方案调研'
date: 2022-04-02
permalink: /posts/2022-04-02-research-journal-lane-extract-review
tags:
  - research journal
---

车道线检测相关方案调研.

## 1. Caltech Lane Detection

### 论文

[1411.7113v1\] Real time Detection of Lane Markers in Urban Streets (arxiv.org)](https://arxiv.org/abs/1411.7113v1)

### 开源代码

[Caltech Lane Detection - Mohamed Alaa El-Dien Aly (mohamedaly.info)](http://www.mohamedaly.info/software/caltech-lane-detection)

### 车道线参数化方法

首先采用Hough变换加RANSAC方法进行直线拟合，接着，基于直线拟合的结果，使用Bezier样条曲线来进行曲线拟合。文中采用三阶贝塞尔曲线来替代三次曲线，同样是求解四个参数，三阶贝塞尔曲线通过 ![[公式]](https://www.zhihu.com/equation?tex=P_%7B0%7D)和 ![[公式]](https://www.zhihu.com/equation?tex=P_%7B3%7D) 点固定头尾， ![[公式]](https://www.zhihu.com/equation?tex=P_%7B1%7D) 和 ![[公式]](https://www.zhihu.com/equation?tex=P_%7B2%7D) 控制曲线的形状。


## 2. LaneNet

### 论文

[1802.05591\] Towards End-to-End Lane Detection: an Instance Segmentation Approach (arxiv.org)](https://arxiv.org/abs/1802.05591?context=cs)

### 开源代码

[GitHub - MaybeShewill-CV/lanenet-lane-detection: Unofficial implemention of lanenet model for real time lane detection using deep neural network model https://maybeshewill-cv.github.io/lanenet-lane-detection/](https://github.com/MaybeShewill-CV/lanenet-lane-detection)

### 车道线参数化方法

LaneNet的输出是每条车道线的像素集合，还需要根据这些像素点回归出一条车道线。传统的做法是将图片投影到鸟瞰图中，然后使用二次或三次多项式进行拟合。在这种方法中，投影矩阵H只被计算一次，所有的图片使用的是相同的投影矩阵，这会导致坡度变化下的误差。

为了解决这个问题，论文训练了一个可以预测变换矩阵H的神经网络HNet，网络的输入是图片，输出是投影矩阵H。

![[公式]](https://www.zhihu.com/equation?tex=%5Cmathrm%7BH%7D%3D%5Cleft%5B%5Cbegin%7Barray%7D%7Blll%7D%7Ba%7D+%26+%7Bb%7D+%26+%7Bc%7D+%5C%5C+%7B0%7D+%26+%7Bd%7D+%26+%7Be%7D+%5C%5C+%7B0%7D+%26+%7Bf%7D+%26+%7B1%7D%5Cend%7Barray%7D%5Cright%5D)

使用H-Net网络的输入结果，将图像中车道线上的像素点投影到鸟瞰图中，再通过最小二乘算法，用一个n阶多项式，对鸟瞰图中的车道线进行拟合。

## 3. SCNN

### 论文

[1712.06080\] Spatial As Deep: Spatial CNN for Traffic Scene Understanding (arxiv.org)](https://arxiv.org/abs/1712.06080)

### 开源代码

[github.com](https://github.com/XingangPan/SCNN)

### 车道线参数化方法

提出SCNN方法对车道线进行语义分割，分割结果用三次样条曲线进行拟合（不是论文的重点工作）。

## 4. End2End by LS Fitting

### 论文

[[1902.00293v1\] End-to-end Lane Detection through Differentiable Least-Squares Fitting (arxiv.org)](https://arxiv.org/abs/1902.00293v1)

### 开源代码

[github.com](https://github.com/wvangansbeke/LaneDetection_End2End)

### 车道线参数化方法

传统车道线检测通常采用二段式的流程，即先对属于车道线的像素点进行语义分割，再将分割后的像素点拟合车道线模型参数。论文提出端到端的车道线拟合方法，具体包含产生加权像素坐标的深度网络、可微加权的最小二乘拟合模块和几何损失函数三部分。

由于曲线不同项的拟合参数相差的数量级非常大，因此不能采用L2 loss。因此，作者选择一个具有几何解释的损失函数，它将图像平面上预测曲线与地面真曲线之间的平方面积最小化，通过求积分的方式来实现。

## 5. CNN Regression

### 论文

[(PDF) Reliable Multilane Detection and Classification by Utilizing CNN as a Regression Network: Munich, Germany, September 8-14, 2018, Proceedings, Part V (researchgate.net)](https://www.researchgate.net/publication/330589970_Reliable_Multilane_Detection_and_Classification_by_Utilizing_CNN_as_a_Regression_Network_Munich_Germany_September_8-14_2018_Proceedings_Part_V)

### 车道线参数化方法

论文拓展了一种新思路，用回归坐标点的方法来做。传统的分割方法的在应对车辆遮挡时分割结果会出现碎片化、远离当前车道的车道线容易混淆不同车道线的实例标签。而直接回归坐标点，能够避免这些问题，提升检测精度。

文中每条车道线用15个固定点来表示，预测点在图像外则视为无效。网络结构前半部分只有UNet结构的encoder层，4个全连接层分支分别预测4条线，input为256*480。

