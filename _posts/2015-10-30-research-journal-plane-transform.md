---
title: '【研究工作记录】2015-10-30-基于平面的扫描匹配方案初步'
date: 2015-10-30
permalink: /posts/research-journal-2015-10-30/
tags:
  - research journal
---

基于平面的扫描匹配方案的初步想法

- 将每一个点看作一个局部平面（这个在计算点云法向量时都已得到），并将其法向量转换到球坐标系下。

- 用Parzen窗口拟合其分布，训练数据可以用匹配平面做一下选择，比如选平面附近一定距离内的点。

- 匹配时

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-10-30-img1.jpg)

- 需要解决的问题

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-10-30-img2.jpg)

![img](https://sunqinxuan.github.io/images/posts-research-journal-2015-10-30-img3.jpg)


