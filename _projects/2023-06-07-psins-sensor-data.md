---
title: "基于PSINS的惯性导航系统"
collection: projects
type: "Project"
permalink: /projects/2023-06-07-ins
venue: "ZJLab"
date: 2023-06-07
location: "Beijing, China"
---

基于PSINS的惯性导航系统

本文所使用的是由西北工业大学[严恭敏](https://teacher.nwpu.edu.cn/yangongmin.html)老师开源的[PSINS](http://www.psins.org.cn/sy)算法（c++版本）。



## 测试数据1

### 传感器信息

[WTGAHRS2产品资料](https://wit-motion.yuque.com/wumwnr/docs/rlp3gu)

### 测试场景

北京市海淀区知春路，中国卫星通信大厦楼顶。

手持传感器。

### 结果统计

| 时间(s)         | 0-850 (14min) | 850-1080 (18min) | 1080-1560 (26min) | 1560-1760 (29min) | 1760-1800 (30min) |
| ------------- | ------------- | ---------------- | ----------------- | ----------------- | ----------------- |
| 运动模式          | 静止            | 静止            | 静止            | 移动               | 划八字               |
| 姿态误差-俯仰角(deg) | <4            | <10              | <19               | <50               | <50               |
| 姿态误差-横滚角(deg) | <0.25         | <0.25            | <0.25             | <50               | <50               |
| 航向误差(deg)     | <3            | <3               | <3                | <46               | <50               |

* 参考姿态/航向：AHRS，标称精度：姿态0.2deg，航向1deg。

### IMU测量值

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img1.png)

### 磁场测量值

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img2.png)

### GPS测量值

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img3.png)

### 惯性导航结果

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img4.png)

### AHRS & GPS

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img5.png)

### AHRS与惯导结果比较

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img6.png)




## 测试数据2












## 相关链接

代码：
- [psins](https://github.com/sunqinxuan/psins)