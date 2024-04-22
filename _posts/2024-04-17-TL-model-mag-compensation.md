---
title: 'Tolles-Lawson Model for Aeromagnetic Compensation'
date: 2024-04-17
permalink: /posts/research-journal-2024-04-17-tl-model
tags:
  - research journal
---

Tolles-Lawson Model for Aeromagnetic Compensation.


## Derivation and Extensions of the Tolles-Lawson Model for Aeromagnetic Compensation

link:

[Gnadt, Albert R., Allan B. Wollaber and Aaron Nielsen. “Derivation and Extensions of the Tolles-Lawson Model for Aeromagnetic Compensation.” (2022).](https://arxiv.org/abs/2212.09899)

[A. R. Gnadt, “Advanced Aeromagnetic Compensation Models for Airborne Magnetic Anomaly Navigation,” Massachusetts Institute of Technology, 2022. ](https://dspace.mit.edu/handle/1721.1/145137)

> <u>The basic idea of the Tolles-Lawson model [9] is to use magnetic measurements from a vector magnetometer to calibrate a scalar magnetometer, the latter of which is used for navigation.</u> This model provides a means for removing a corrupting aircraft magnetic field from a scalar total magnetic field measurement, yielding the Earth magnetic field used for navigation.

An airborne vector magnetometer measures the vector sum of two primary magnetic fields,

$$
\vec{B}_t=\vec{B}_e+\vec{B}_a
$$

After some mathematical manipulations, a linear relationship can be obtained.

$$
|\vec{B}_e|\approx|\vec{B}_t|-\vec{B}_t\cdot\hat{B}_t
$$

Note that the scalar form is obtained because the scalar earth magnetic measurements are required during the navigation.

Tolles and Lawson assumed that the aircraft field is comprised of permanent, induced, and eddy current magnetic moments.

$$
\vec{B}_a=\vec{B}_{perm}+\vec{B}_{ind}+\vec{B}_{eddy}
$$

It is further reduced to the simplified form of

$$
\vec{B}_a=\boldsymbol{a}+\boldsymbol{b}\vec{B}_t+\boldsymbol{c}\dot{\vec{B}}_t
$$

![img](http://sunqinxuan.github.io/images/posts-research-journal-2024-04-17-img1.png)

As can be seen in ["Derivation and Extensions of the Tolles-Lawson Model for Aeromagnetic Compensation"](https://arxiv.org/abs/2212.09899), strictly speaking, it is the earth field that induces the induced and eddy terms, rather than the measured field.

An approximation is made here in the derivation of TL-model.

![img](http://sunqinxuan.github.io/images/posts-research-journal-2024-04-17-img2.png)

![img](http://sunqinxuan.github.io/images/posts-research-journal-2024-04-17-img3.png)

The "trick" is to use a bandpass filter, typically a Butterworth infinite impulse response filter is chosen.

The passband frequency range for the bandpass filter is carefully selected in order to remove nearly all of the earth field while keeping much of the aircraft field by setting to the periodicity of the pitch, roll, and yaw maneuvers. 

The calibration flight is performed at a high altitude over a region with a small magnetic gradient to reduce the uncertainty imparted by the earth field.

**However, when solving the TL-model in vector form, the earth field represented in the aircraft coordinate system changes with the maneuvers of the aircraft.**

As a result, the "trick" is not effective anymore.

> When a high-quality map is available for calibration, a bandpass filter is not necessary, and calibration flights should occur at lower altitudes for map-based aeromagnetic calibration and compensation. 

As is claimed in ["Derivation and Extensions of the Tolles-Lawson Model for Aeromagnetic Compensation"](https://arxiv.org/abs/2212.09899), the earth field can be obtained by the magnetic map.

In our project, it can also be measured by the magnetometer that is installed outside of the cabin (i.e., the groundtruth used in the training of the network).

![img](http://sunqinxuan.github.io/images/posts-research-journal-2024-04-17-img4.png)

I've never seen that the vector field can be solved this way...


## Magnetic Navigation on an F-16 Aircraft Using Online Calibration

link:
[A. J. Canciani, "Magnetic Navigation on an F-16 Aircraft Using Online Calibration," in IEEE Transactions on Aerospace and Electronic Systems, vol. 58, no. 1, pp. 420-434.](https://ieeexplore.ieee.org/document/9506809)

> Magnetic anomaly navigation has recently been flight test
proven on an F-16 aircraft. The corrupting magnetic environment on
this operational platform is 2–3 orders of magnitude larger than previous
flight-tests on geo-survey aircraft, while also displaying stochastic
drift. The difficult magnetic environment of the platform necessitated a
<u>combination of batch and online calibration</u> to obtain accurate navigation
solutions.

> When calibrating
a scalar sensor, all magnetic fields in the body-frame
of the aircraft create corrupting fields which are coupled
to aircraft attitude.

> Discussed in this article are two major types of “truthing”
to drive the calibration of the magnetometer measurements:
<u>map-based calibration and map-less calibration</u>.

> The first
method, map-based calibration, is less common in the literature,
but more intuitive. This calibration method flies
through a high-quality magnetic anomaly map (with GPS)
and calibrates the system measurements such that they most
closely match the magnetic anomaly map. This method
requires highly accurate magnetic anomaly maps but can
produce the best results given the navigation system is also
using this map to navigate. <u>This method cannot produce
calibration errors smaller than the errors which exist in the
magnetic map-products being used.</u>

> Map-less calibration is more practical in many situations
and relies on the idea of a “disturbance field.” A
disturbance field takes advantage of the physics of the
<u>slowly varying earth magnetic field and band-pass filters
the scalar magnetic data such that no earth field remains
in the signal</u>. Note this method also eliminates some of the
aircraft fields, however, a mathematical model which fits
the band-pass filtered data can then be expanded to predict
magnetic field effects across the entire frequency spectrum.
This method is more brittle than map-based calibration and
suffers from observability issues but has the obvious benefit
of not requiring a map. 





















