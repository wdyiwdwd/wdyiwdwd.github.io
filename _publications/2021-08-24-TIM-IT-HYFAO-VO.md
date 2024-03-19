---
title: "【Journal paper】IT-HYFAO-VO: Interpretation Tree-Based VO With Hybrid Feature Association and Optimization"
collection: publications
permalink: /publication/TIM2021
excerpt: 'This paper is about IT-HYFAO-VO.'
date: 2021-08-24
venue: 'IEEE Transactions on Instrumentation & Measurement'
paperurl: 'http://sunqinxuan.github.io/files/publications-2021-08-24-TIM.pdf'
citation: 'Q. Sun, J. Yuan, X. Zhang. IT-HYFAO-VO: Interpretation Tree-Based VO With Hybrid Feature Association and Optimization. IEEE Transactions on Instrumentation & Measurement, 2021, 70: 1-18.'
---

## Abstract

For the visual odometry (VO) and simultaneous
localization and mapping (SLAM) in indoor environments, highlevel
geometric features have attracted more and more attention
in recent years. Unlike the generally used point features,
the geometric features, such as planes and lines, encode more
higher-level semantic information of the scene which is beneficial
for various tasks of mobile robots. In this article, an RGB-D
VO system with hybrid high-level geometric features is developed.
An interpretation tree (IT)-based hybrid feature association
framework is proposed, which turns the feature association into a
multiple-hypothesis decision problem. The IT expansion method
is elaborately designed. Specifically, an internode consistency is
proposed for generation of hypotheses and a consistent transformation
model (CTM) for each hypothesis is explicitly expressed
and incrementally updated. When the IT is constructed, a closedform
solution to the feature association and the camera transformation
can be obtained. Then, a hybrid feature joint optimization
method is introduced to further refine the pose estimate and
parameters of geometric features. During optimization, the planes
and lines are appropriately parameterized and the uncertainties
arising from feature extraction are derived and used to balance
the contributions of two types of features in the cost function.
Extensive experiments are executed on public datasets and the
results demonstrate that the proposed method can achieve higher
accuracy and stronger robustness.
