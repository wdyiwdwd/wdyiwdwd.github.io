---
layout: archive
title: "CV"
permalink: /cv/
author_profile: true
redirect_from:
  - /resume
---

{% include base_path %}

The Chinese version of my CV can be <u><a href="http://sunqinxuan.github.io/files/cv_sqx.pdf">download it here.</a></u>

Education
======
* Ph.D in Control Science and Engineering, Nankai University, 2021
* M.S. in Control Science and Engineering, Nankai University, 2016
* B.S. in Electronics and Information Engineering, Beihang University, 2013

Work experience
======
* 2022.9-present: Research Assistant
  * Zhejiang Lab
  * Duties included: 

* 2021.8-2022.8: Algorithm Engineer
  * China Intelligent and Connected Vehicles (Beijing) Research Institute Co.,Ltd.
  * Duties included: 


Projects
======
* **CICV Research Project on Semantic SLAM Algorithm for AVP**
  * Intrinsic and extrinsic calibration of surrounding fish-eye cameras.
  * Development of image stitching algorithm for Around View Monitor on the vehicle.
  * Development of the front-end (including Semantic-GICP-based scan matching, detection of the degenerate cases, region-grow-based line segment extraction, etc.) and back-end (a Ceres-based pose graph optimization framework) modules of the semantic SLAM system.

* **Environmental Perception and Mapping for Tightly-Coupled Air-Ground Cooperation using Multi-source and Heterogeneous Data** (Natural Science Foundation of China)
  * Development of the joint association and optimization of multiple geometric features (planes and lines) based on the interpretation tree structure (published on TIM).
  * Development of the SLAM system based on the multi-feature fusion.
* **Environmental Perception via Tightly-Coupled Multi-Sensor and Heterogeneous Multi-Robot Cooperation** (Tianjin Science Fund for Distinguished Young Scholars)
  * Quantitative analysis of how multiple geometric features constraining the sensor pose estimation.
  * Development of the seamless fusion framework of (plane-line-point) multimodal features (published on T-ASE).
  * Development of the probabilistic geometric feature fitting algorithm. By exploiting the error model of the depth sensor, the proposed probabilistic fitting is adaptive to various measurement noises corresponding to different depth measurements.

* **RGB-D Image Sequence-based Place Recognition and Map Construction for Mobile Robots** (Natural Science Foundation of China)
  * Development of the sensor pose estimation algorithm based on the adaptive fusion of the plane and line features.
  * Analysis of the correspondences between the degenerate cases in the pose estimation and the spatial configurations of features (published on TMech).

* **Research on Theory and Technology of Man-Robot-Environment Integration for Health-Care Service Robot** (Major Basic Research Projects of Natural Science Foundation of Shandong Province)
  * Development of the RGB-D camera-based 3D environmental map building system using RANSAC and ICP scan matching.
  * Development of the AprilTag detection and localization.

Publications
======

{% for post in site.publications reversed %} {% include archive-single-cv.html %} {% endfor %}

Awards
======

{% for post in site.talks reversed %} {% include archive-single-talk-cv.html %} {% endfor %}

- 2010.10, Second Prize in Physics Contest for College Students in Beihang University.
- 2011.05, Third Prize in 2011 National English Contest for College Students.
- 2011.05, Third Prize in the 21st Fengru Cup Competition of Beihang University.
- 2011.06, Third Price in the 7th Fujitsu Cup Competition of Beihang University.
- 2012.05, Third Prize in the 22nd Fengru Cup Competition of Beihang University.
- 2012.05, Second Prize in the 8th Electronics Competition of Beihang University.
- 2012.06, Third Prize in 2012 Beijing College-Student Electronics Design Contest.
- 2012.12, Second Prize Scholarship of Collective Duties for the 2011-2012 Academic Year of Beihang University.
- 2012.12, Third Prize Scholarship of Science and Technology Competition for the 2011-2012 Academic Year of Beihang University.





