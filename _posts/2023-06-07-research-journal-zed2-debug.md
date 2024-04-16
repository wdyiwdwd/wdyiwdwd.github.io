---
title: 'ZED2深度相机调试记录'
date: 2023-06-07
permalink: /posts/research-journal-2023-06-07-zed2-debug
tags:
  - research journal
---

ZED2深度相机调试记录


## ZED SDK安装

[How to Install ZED SDK on NVIDIA® Jetson](https://www.stereolabs.com/docs/installation/jetson/)

### 查看cuda版本

```shell
agilex@xavier:/usr/local/2ed/toolsS nvcc--version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2019 NIDIA Corporation
Built on Wed_Oct_23_21:14:42_PDT_2019
Cuda compilation tools, release 10.2，V10.2.89
```

### 选择对应版本的SDK

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-06-07-img1.png)

### 下载并安装

```shell
$ chmod +x ZED_SDK_JP4.3_v3.0.run
$ ./ZED_SDK_JP4.3_v3.0.run
```

## 测试

### 图像采集测试

```shell
$ /usr/local/zed/tools/ZED_Explorer
```

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-06-07-img2.png)

### 深度恢复测试

```shell
$ /usr/local/zed/tools/ZED_Depth_Viewer
```

报错：

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-06-07-img3.png)

### 运行诊断程序

```shell
$ /usr/local/zed/tools/ZED_Diagnostic
```

```
    "Devices": {
        "CorruptedFirmware": false,
        "GMSLList": [
        ],
        "MCUDetected": true,
        "USBList": [
            {
                "USBMode": 3,
                "USB_path": "/3/2",
                "bDescriptorType": 1,
                "bDeviceProtocol": 1,
                "bLength": 18,
                "bMaxPacketSize0": 9,
                "bNumConfigurations": 1,
                "bcdDevice": "1.0",
                "bcdUSB": "3.0",
                "bcdUSBClass": 239,
                "bcdUSBSubClass": 2,
                "busNumber": 2,
                "device": "ZED2",
                "iManufacturer": 1,
                "iProduct": 2,
                "iSerial": 0,
                "idProduct": "0xf780",
                "idVendor": "0x2b03"
            },
            {
                "USB_path": "/3/1",
                "idProduct": "0x0813",
                "idVendor": "0x2109"
            },
            {
                "USB_path": "/3",
                "idProduct": "0x0813",
                "idVendor": "0x2109"
            },
            {
                "USBMode": 2,
                "USB_path": "/3/2/2",
                "bDescriptorType": 1,
                "bDeviceProtocol": 0,
                "bLength": 18,
                "bMaxPacketSize0": 64,
                "bNumConfigurations": 1,
                "bcdDevice": "3.8",
                "bcdUSB": "2.0",
                "bcdUSBClass": 0,
                "bcdUSBSubClass": 0,
                "busNumber": 1,
                "device": "ZED2 MCU",
                "iManufacturer": 1,
                "iProduct": 2,
                "iSerial": 3,
                "idProduct": "0xf781",
                "idVendor": "0x2b03"
            },
            {
                "USB_path": "/3/2",
                "idProduct": "0x2512",
                "idVendor": "0x0424"
            },
            {
                "USB_path": "/3/1/2",
                "idProduct": "0x606f",
                "idVendor": "0x1d50"
            },
            {
                "USB_path": "/3/1",
                "idProduct": "0x2813",
                "idVendor": "0x2109"
            },
            {
                "USB_path": "/3",
                "idProduct": "0x2813",
                "idVendor": "0x2109"
            }
        ],
        "USBMode": 3,
        "ZED Camera Module Detected": 63360,
        "ZED MCU Module Detected": 63361,
        "ZEDDetected": true,
        "error": [
            "<b>Low USB bandwidth</b> <br/> Read our FAQ to troubleshoot your USB connection issues. <a href='https://support.stereolabs.com/hc/en-us/articles/207635225' style='color: #19b5ec;' >Learn more</a><br/> <b>Frame drops:</b> 131/500<br/> <b>Frame tearing:</b> 4/500"
        ]
    },
```

## 问题解决

### 查看具体版本信息

```shell
sudo -H pip install jetson-stats
sudo jtop
```

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-06-07-img4.png)

### 重新选择SDK版本进行安装

[ZED SDK Downloads](https://www.stereolabs.com/developers/release/3.8/)

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-06-07-img5.png)

```shell
$ chmod +x ZED_SDK_Tegra_L4T32.4_v3.8.2.zstd.run 
$ ./ZED_SDK_Tegra_L4T32.4_v3.8.2.zstd.run
```

### 深度恢复测试

```shell
$ /usr/local/zed/tools/ZED_Depth_Viewer
```

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-06-07-img6.png)

