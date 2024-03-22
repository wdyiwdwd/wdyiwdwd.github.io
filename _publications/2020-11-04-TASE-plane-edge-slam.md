---
title: "【Journal paper】Plane-Edge-SLAM: Seamless Fusion of Planes and Edges for SLAM in Indoor Environments"
collection: publications
permalink: /publication/TASE2020
excerpt: 'Plane-Edge-SLAM: Seamless Fusion of Planes and Edges for SLAM in Indoor Environments'
date: 2020-11-04
venue: 'IEEE Transactions on Automation Science and Engineering'
paperurl: 'http://sunqinxuan.github.io/files/publications-2020-11-04-TASE.pdf'
citation: 'Q. Sun, J. Yuan, X. Zhang, F. Duan. Plane-Edge-SLAM: Seamless Fusion of Planes and Edges for SLAM in Indoor Environments. IEEE Transactions on Automation Science and Engineering. 18(4): 2061-2075.'
---

[Plane-Edge-SLAM: Seamless Fusion of Planes and Edges for SLAM in Indoor Environments](https://ieeexplore.ieee.org/document/9248035)

## Abstract

Planes and edges are attractive features for
simultaneous localization and mapping (SLAM) in indoor environments
because they can be reliably extracted and are robust
to illumination changes. However, it remains a challenging
problem to seamlessly fuse two different kinds of features to
avoid degeneracy and accurately estimate the camera motion.
In this article, a plane-edge-SLAM system using an RGB-D
sensor is developed to address the seamless fusion of planes
and edges. Constraint analysis is first performed to obtain a
quantitative measure of how the planes constrain the camera
motion estimation. Then, using the results of the constraint
analysis, an adaptive weighting algorithm is elaborately designed
to achieve seamless fusion. Through the fusion of planes and
edges, the solution to motion estimation is fully constrained, and
the problem remains well-posed in all circumstances. In addition,
a probabilistic plane fitting algorithm is proposed to fit a plane
model to the noisy 3-D points. By exploiting the error model
of the depth sensor, the proposed plane fitting is adaptive to
various measurement noises corresponding to different depth
measurements. As a result, the estimated plane parameters are
more accurate and robust to the points with large uncertainties.
Compared with the existing plane fitting methods, the proposed
method definitely benefits the performance of motion estimation.
The results of extensive experiments on public data sets and in
real-world indoor scenes demonstrate that the plane-edge-SLAM
system can achieve high accuracy and robustness.

<iframe width="1255" height="706" src="https://www.youtube.com/embed/w3abLO_PDNo" title="publications TASE 2020 11 04 video real world" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

The video shows the online experiments to test the proposed plane-edge-VO in two real world scenes, i.e., a laboratory and a large-scale corridor, respectively, as is presented in Section VI.F of the paper. 

<iframe width="1255" height="706" src="https://www.youtube.com/embed/bsTNRcUvqGs" title="publications TASE 2020 11 04 video low light" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

The video includes two online experiments conducted in scenes under varying and low illumination conditions, respectively, as is presented in Section VI.F of the paper. 

<iframe width="1255" height="706" src="https://www.youtube.com/embed/WWyD0M0gcRc" title="publications TASE 2020 11 04 video map quality" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

The video includes three experiments for evaluation of the map quality, in which our system is compared with other three plane-based SLAM systems: SA-SHAGO, STING-SLAM and pont-plane-SLAM. The experimental results are presented in Section VI.E of the paper.
