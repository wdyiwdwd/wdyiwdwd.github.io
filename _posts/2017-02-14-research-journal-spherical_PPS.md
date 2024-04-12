---
title: 'ideas about the spherical pps'
date: 2017-02-14
permalink: /posts/research-journal-2017-02-14/
tags:
  - research journal
---

ideas about the spherical pps

## spherical PPS

### some basics

transform between \\((\theta,\varphi,r)\\) and \\(({\bf{n}},d)\\)

$$
\left\{
  \begin{aligned}
  &\theta=\arccos(n_z)\\
  &\varphi=\arctan(n_y,n_x)\\
  &r=d
  \end{aligned}
\right.
$$

transform between different frames through \\((\bf{R,t})\\)

$$
\left\{
  \begin{aligned}
  &{\bf{n}}_r={\bf{R}}\cdot{\bf{n}}_c\\
  &d_r=d_c+{\bf{n}}_r^T{\bf{t}}
  \end{aligned}
\right.
$$

[wiki - spherical coordinate system](https://en.wikipedia.org/wiki/Spherical_coordinate_system)

### problems

when \\(\theta\\) is near 0 or \\(\pi\\) (around the polar point), a small variation in normal \\({\bf n}\\) will cause large variation in \\(\varphi\\). also \\(\theta\\) and \\(\phi\\) both wrap around.

so the Euclidean distance is not appropriate to measure the distance between points in the PPS.

its effects include: 

- the discretization problems in STING
- the error metric in scan matching and map optimizing (g2o)

### an example on spherical-SM

["Spherical Terrain Matching for SLAM in Planet Exploration" ](http://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=6359407) 

laser scanner points are describe in spherical coordinate.

aligns the current scan with respect to the reference scan so that the sum of square range residuals is minimized. 

It is assumed that an initial pose is given.

The ICP alignment is to find the optimized configuration pair \\((\theta_{ik},\phi_{ik})\\) of the laser scanner that minimizes \\(\sum{\lambda_i(r_{ik}-r_{ik}')^2}\\), where \\(\lambda_i\\) is a weight used to reduce weighting of bad matches.

### spherical PPS

![img](https://sunqinxuan.github.io/images/posts-research-journal-2017-02-14-img1.svg)

the coordinate (location) in spherical PPS represents parameters of a plane.
\\((\theta,\varphi)\\) represents the orientation and \\(r (d)\\) the vertical distance from origin.

so the distance between points in spherical PPS should measure the degree of the plane's variation.

what about decoupling?

the normals $\bf n_i$ is only affected by the rotation transfrom of the camera. 
so the rotation can be aligned first.


## hierarchical scan matching in STING

the STING structure is build by discretizing the spherical PPS using geodesic domes.

how to implement geodesic dome?

the geodesic dome is constructed on a unit sphere, while the pps also involves a $d$ dimension. 

## something else

the distance metric between two planes.

if decoupling the orientation and the intercept of the local planes, 
the rotation is only relevant to the orientation, so the orientations is used first to determine the rotation. 
then use the intercept to determine the translation. 

- discretizing problems: use the Geodesic dome to discretize \\(\theta\\) and \\(\varphi\\), then figure out the method to control the resolutions. and also how to discretize \\(d\\)?

- hierarchical scan matching: it's relevant to the area of a single facet on a Geodesic dome. to count some scores on the dome.

- normal estimation: consider the depth uncertainty. and also the uncertainty caused by the object edge. it's significant how much information could be held by the local plane parameters.

- the parameter propagation in STING: a cell in the higher level in STING should be made up by several cells in the lower level. how to achieve that in Geodesic dome way?
