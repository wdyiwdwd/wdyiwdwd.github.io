---
title: "基于Tolles-Lawson模型的航磁补偿系统"
collection: projects
type: "Project"s
permalink: /projects/2023-09-21-mag-compensation
venue: "ZJLab"
date: 2023-09-21
location: "Beijing, China"
---

基于Tolles-Lawson模型的航磁补偿系统

## 背景

详情可参阅[Signal Enhancement for Magnetic Navigation Challenge Problem](https://magnav.mit.edu/)。

> The best submission to date was submitted by: Ling-Wei Kong, Cheng-Zhen Wang, and Ying-Cheng Lai from Arizona State University ([submission](https://github.com/lw-kong/MagNav)).

### dataset版本问题

dataset有两个版本：版本一和版本二。

两个版本所采集数据是相同的，但是文件组织不同，包括：数据命名、数据存储顺序等。

使用时须注意。

[版本一](https://zenodo.org/record/6327685)

![img](http://sunqinxuan.github.io/images/projects-2023-09-21-img01.png)

[版本二](https://zenodo.org/record/4271804#.YnWQuIdBxD8)

![img](http://sunqinxuan.github.io/images/projects-2023-09-21-img02.png)

## 方案

<img src="https://sunqinxuan.github.io/images/projects-2023-09-21-img1.jpg" alt="architecture" />

## 实验

ensemble rmse on 1003.020000 = 9.241940

<table><tr>
<td><img src=https://sunqinxuan.github.io/images/projects-2023-09-21-img1.jpg border=0></td>
<td><img src=https://sunqinxuan.github.io/images/projects-2023-09-21-img2.jpg border=0></td>
</tr></table>

## 相关链接

专利：
- [基于矢量数据的地磁导航方法、装置以及电子设备](https://sunqinxuan.github.io/publication/CN117213472A)

代码：
- [a modified version with c++ implementation of the TL-model](https://github.com/sunqinxuan/magnav)










