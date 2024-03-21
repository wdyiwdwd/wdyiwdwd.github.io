---
title: '【研究工作记录】2015-12-22-平面配准相关方案与实验记录'
date: 2015-12-22
permalink: /posts/research-journal-2015-12-22-plane-matching/
tags:
  - research journal
---

平面配准相关方案与实验记录

## 2015-10-30

基于平面的扫描匹配方案的初步想法

- 将每一个点看作一个局部平面（这个在计算点云法向量时都已得到），并将其法向量转换到球坐标系下。

- 用Parzen窗口拟合其分布，训练数据可以用匹配平面做一下选择，比如选平面附近一定距离内的点。

- 匹配时

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-10-30-img1.jpg)

- 需要解决的问题

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-10-30-img2.jpg)

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-10-30-img3.jpg)

## 2015-11-08

<a href="http://sunqinxuan.github.io/files/research-journal-2015-11-08-plane-matching.pdf">基于平面的扫描匹配方案</a>

## 2015-11-14

<a href="http://sunqinxuan.github.io/files/research-journal-2015-11-14-pcl-debug.pdf">PCL VoxelGrid 调试记录</a>

## 2015-11-27

<a href="http://sunqinxuan.github.io/files/research-journal-2015-11-27-2pln-ndt.pdf">两对匹配平面实验结果</a>

## 2015-12-02

<a href="http://sunqinxuan.github.io/files/research-journal-2015-12-02-2pln-opt.pdf">平面匹配加局部优化实验结果</a>

## 2015-12-13

<a href="http://sunqinxuan.github.io/files/research-journal-2015-12-13-expr1.pdf">实验结果1</a>

## 2015-12-17

<a href="http://sunqinxuan.github.io/files/research-journal-2015-12-17-expr2.pdf">实验结果2</a>

## 2015-12-22

<a href="http://sunqinxuan.github.io/files/research-journal-2015-12-22-expr3.pdf">实验结果3</a>