---
title: "【Journal paper】RGB-D SLAM in Indoor Environments With STING-Based Plane Feature Extraction"
collection: publications
permalink: /publication/TMech2018
excerpt: 'RGB-D SLAM in Indoor Environments With STING-Based Plane Feature Extraction'
date: 2018-06-12
venue: 'IEEE/ASME Transactions on Mechatronics'
paperurl: 'http://sunqinxuan.github.io/files/publications-2018-06-12-TMech.pdf'
citation: 'Q. Sun, J. Yuan, X. Zhang, F. Sun. RGB-D SLAM in Indoor Environments With STING-Based Plane Feature Extraction. IEEE/ASME Transactions on Mechatronics, 2018, 23(3): 1071-1082.'
---

[RGB-D SLAM in Indoor Environments With STING-Based Plane Feature Extraction](https://ieeexplore.ieee.org/document/8107562)

## Abstract

In this paper, the RGB-D camera-based simultaneous
localization and mapping (SLAM) of indoor environments
is addressed using plane features. The plane parameter
space (PPS) is defined for a compact representation of
planes in the Cartesian space. The statistical information
grid (STING) structure is constructed in the PPS to extract
plane features. The plane association graph is developed to
determine the correspondences between the plane features
from two successive scans. The RGB-D camera pose is directly
calculated using the matched plane features if they
can provide sufficient constraints for the pose estimation.
Otherwise, a novel STING-based scan matching method is
developed in the PPS to achieve a full six degrees of freedom
camera pose estimation. The proposed method uses
only the plane features independent of any other features
to estimate the RGB-D camera poses and can thus be suitable
for some challenging scenes. The experimental results
demonstrate that the proposed plane feature-based RGB-D
SLAM can achieve high accuracy and robustness in both
on-board and hand-held applications.

<video src="http://sunqinxuan.github.io/videos/publications-TMech-2018-06-12-video1.mp4" width="800px" height="600px" controls="controls"></video>

The video shows the online robot SLAM experiment in a real world scene presented in Section V.D of the paper. Specifically, it shows the process of the RGB-D visual odometry and incremental mapping achieved by the proposed plane feature-based RGB-D camera pose estimation (PF-RGBD-CPE) method. 

More in details:

 - Experimental Setup: The size of the laboratory is approximately 12.0m×5.2m. The Kinect is mounted on the Pioneer 3-DX mobile robot 1.14m above the ground, pointing to the right side of the robot. The PF-RGBD-CPE runs on an onboard computer (Intel Pentium Dual T2390 CPU at 1.86GHz, 3G RAM). The length of the trajectory is approximately 46.7m, covering two loops around the laboratory. During the first loop, the Kinect points to the tables in the middle of the room, and during the second loop it points to the surrounding walls. 

 - Video Contents:
   - Text on the top: frame number, number of extracted planes and the constraint case of the current alignment;
   - RGB Image & Depth Image: RGB and depth images collected by the Kinect sensor;
   - Camera 1 & Camera 2: views of the two fisheye cameras fixed on the ceiling (two cameras are used to cover the whole room);
   - Point Cloud Map: the 3D point cloud map as well as the trajectory of the RGB-D sensor estimated by PF-RGBD-CPE;
   - Extracted Planes: the extracted planes in the map shown in different colors.