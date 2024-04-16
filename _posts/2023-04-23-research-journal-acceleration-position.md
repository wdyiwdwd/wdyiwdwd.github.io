---
title: 'VINS中的加速度-位移公式'
date: 2023-04-23
permalink: /posts/research-journal-2023-04-23-acceleration-position
tags:
  - research journal
---

VINS中的加速度-位移公式


$$
p(t_2)=p(t_1)+\int^{t_2}_{t_1}v(\tau)d\tau 
$$

$$
v(\tau)=v(t_1)+\int^{\tau}_{t_1}a(\zeta)d\zeta 
$$

$$
\begin{aligned}
p(t_2)&=p(t_1)+\int^{t_2}_{t_1}v(\tau)d\tau \\
&=p(t_1)+\int^{t_2}_{t_1}\left(v(t_1)+\int^{\tau}_{t_1}a(\zeta)d\zeta\right)d\tau \\
&=p(t_1)+v(t_1)\Delta t+\int^{t_2}_{t_1}\int^{\tau}_{t_1}a(\zeta)d\zeta d\tau
\end{aligned}
$$

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-04-23-img1.jpg)

