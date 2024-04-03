---
title: '【Research journal】Two Patents relating Tolles-Lawson Model'
date: 2019-07-08
permalink: /posts/research-journal-2019-07-08-calibration-kinect2-qualisys/
tags:
  - research journal
---

Two Patents relating Tolles-Lawson Model.


## Compensation of induced magnetic fields

link:
[Compensation of induced magnetic fields (US2834939A, Walter E Tolles)](https://patents.google.com/patent/US2834939)

![img](http://sunqinxuan.github.io/images/posts-research-journal-2024-04-03-img1.png)

I read this patent for the derivation of the eq.(3) in Patent US2692970A.

> This invention relates to compensation systems, and
more particularly to methods of and means for com
pensating the induced magnetic fields of aircraft.

> The total induced magnetic field due to all soft magnetic 
parts of an aircraft structure may be reproduced by
<u>a system of three suitable virtual bars oriented in chosen
directions in respect to the aircraft</u>. Referring to Fig. 1,
<u>the aircraft is shown at the origin of a coordinate system
in which the x, y and z axes are parallel respectively to
the transverse, longitudinal and vertical axes of the 
aircraft</u>. The three virtual bars mentioned above may be
chosen in such fashion that one of them is parallel to
each of these reference axes, the bar parallel to the x axis.
being designated the transverse bar, that parallel to they
axis being designated the longitudinal bar, and that par
allel to the z axis being designated the vertical bar.

> Accordingly, <u>a double-letter system is used in which the first letter 
indicates the orientation of the bar causing the field 
component and the second letter indicates the direction of
the component caused thereby</u>. Thus the transverse bar
which produces components in the transverse, 
longitudinal and vertical directions may result in components
designated as TT, TL and TV. Similarly, there will be
other components designated LL, LV, LT, VV, VL and
VT. The components acting along each of the three
reference axes have been indicated in Fig. 1.

For better understanding, I rewrite the eq.(1) in the patent as

$$
\begin{aligned}
\vec{H}_I&=\vec{i}(TT\cdot H\cos X+LT\cdot H\cos Y+VT\cdot H\cos Z) \\
&+\vec{j}(TL\cdot H\cos X+LL\cdot H\cos Y+VL\cdot H\cos Z) \\
&+\vec{k}(TV\cdot H\cos X+LV\cdot H\cos Y+VV\cdot H\cos Z)
\end{aligned}
$$

where \\(H\cos Y\\) represents the projection of the earth magnetic field onto the x axis (transverse virtual bar), and \\(TL\\) is the coefficient of the induced magnetic field caused by the transverse component (1st letter) on the longitudinal direction (2nd letter).

> Of the total induced field, only the component in the
direction of the earth's magnetic field affects the 
operation of a magnetometer arranged to measure components
in the direction of the earth's magnetic field. <u>Resolving
the field of Equation 1 in the direction of the earth's
magnetic field</u> results in an expression of the following
form:

$$
\begin{aligned}
H_{ID}=
\end{aligned}
$$


## Compensation of aircraft magnetic fields

link:
[Compensation of aircraft magnetic fields (US2692970A, Walter E Tolles)](https://patents.google.com/patent/US2692970A)

![img](http://sunqinxuan.github.io/images/posts-research-journal-2024-04-03-img2.png)