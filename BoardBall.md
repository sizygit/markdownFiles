---
title: BoardBall
date: 2023-05-28 20:36:54
tags:
---

# 板球系统基本原理

根据拉格朗日广义方程，结合小角度线性化可得到：

$\begin{aligned}&\dot{\mathbf{x}}_x=A_x\mathbf{x}_x+b_xu_x\\ &y_x=[1, 0 ,0 ,0] \mathbf{x}_x\\ &A_\mathrm{x}=\begin{bmatrix}0&1&0&0\\0&0&-7.007&0\\0&0&0&1\\0&0&0&0\end{bmatrix},b_\mathrm{x}=\begin{bmatrix}0\\0\\0\\1\end{bmatrix}\end{aligned}$

式中，$\dot{X}_x= [x,v,\alpha,\dot{\alpha}]^T$

# 基于BP神经网络的PID控制器

## 传统PID控制



# PIDNN（PID神经网络）

