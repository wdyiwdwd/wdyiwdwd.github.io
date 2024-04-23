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

## 测试数据1

### 传感器信息

[WTGAHRS2产品资料](https://wit-motion.yuque.com/wumwnr/docs/rlp3gu)

### 测试场景

手持传感器。

北京市海淀区知春路，中国卫星通信大厦楼顶。

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

### Mag测量值

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

### 传感器信息

[WTGAHRS2产品资料](https://wit-motion.yuque.com/wumwnr/docs/rlp3gu)

### 测试场景

车载传感器，沿北京六环线一圈。

传感器安装（右-前-上，车头朝向图片左侧）：

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img7.png)

### IMU测量值 

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img8.png)

### Mag测量值

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img9.png)

### GNSS数据

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img10.png)

全程停留在 经度116.1deg，纬度39.8deg 附近，
并且，[16:02:53.683 - 16:03:07.057]时间区别内输出零值。

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img11.png)

### 惯性导航结果

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img12.png)

### AHRS与惯导结果比较

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img13.png)


## 测试数据3

### 传感器信息

[3DM-GX5-GNSS/INS产品资料](https://www.microstrain.com/sites/default/files/3dm-gx5-45_datasheet_8400-0091_rev_o.pdf)

### 测试场景

车载传感器，沿北京六环线一圈。

传感器安装（前-右-下，车头朝向图片左侧）：

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img14.jpg)

### IMU测量

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img15.png)

### Mag测量

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img16.png)

### GNSS

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img17.png)

### 惯性导航结果

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img18.png)

### AHRS与惯导结果比较

![img](http://sunqinxuan.github.io/images/projects-2023-06-07-img19.png)

- 姿态角误差<8.5deg
- 航向角误差
	- 0~1h: <10deg
	- 1~2h: <17deg
	- 2~4h: <50deg


## 相关链接

代码：
- [psins](https://github.com/sunqinxuan/psins)