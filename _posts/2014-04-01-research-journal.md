---
title: '【研究工作记录】点云配准'
date: 2014-04-01
permalink: /posts/research-journal-2014-04/
tags:
  - research journal
---

research journal, 2014.4.

## 前期工作

### 特征点的提取与匹配

1.角点检测：FAST（Features from Accelerated Segment Test）使用直径为3、4个像素的圆周作为检测模板；如果圆周上存在n个连续像素，它们的亮度均高于候选像素亮度Ip和阈值t之和，或，它们的亮度均低于Ip- t，则模板中心对应像素为角点。

2.特征描述符：BRIEF（Binary Robust Independent Elementary Features），选择n个二值测试的像素对，BRIEF描述符是n位的比特串。

3.处理时间统计（特征点数量：~1500/帧）

|                 | 处理时间 |
| :-------------: | :------: |
|  Fast角点检测   |   10ms   |
| Brief描述符生成 |   10ms   |
|   特征点匹配    |   60ms   |

### 粗匹配

将特征点投影到三维空间，用Ransac算法完成粗匹配，匹配时间约240ms。

1. 在匹配好的点对中随机选出三个点对，确定一个变换RT。
2. 将RT作用于所有匹配点对，筛选出符合RT变换的点对（内点），记内点数count。
3. 循环执行1~2，直到内点数count到达一个阈值，或者迭代次数超过一个阈值，停止迭代。
4. 记此时的变换RT为粗匹配的结果。

| **第n次匹配** | **ICP时间（ms）** |
| :-----------: | :---------------: |
|       1       |        294        |
|       2       |        291        |
|       3       |        246        |
|       4       |        251        |
|       5       |        215        |
|       6       |        230        |
|       7       |        252        |
|       8       |        259        |
|       9       |        246        |

![img](https://sunqinxuan.github.io/images/posts-research-journal-2014-04-img1.png)
![img](https://sunqinxuan.github.io/images/posts-research-journal-2014-04-img2.png)

## 后期工作思路

- 提高匹配过程的鲁棒性

- 在用特征点粗匹配失效时提供备选策略

  - 在粗匹配的实现中考虑图像中的其它信息，如颜色、纹理等。

  - 加入学习机制，建立地图的高层描述。

