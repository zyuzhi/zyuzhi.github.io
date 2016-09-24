---
layout: post
title: 旋转矩阵、欧拉角、四元数和角轴
categories: [blog ]
tags: [数学, ]
description: 旋转矩阵、欧拉角、四元数和角轴的关系及推导
---

最近对三维空间的旋转表达方式做了整理。
三维空间中常用的表征旋转的方式有：
**[旋转矩阵(rotation matrix)](https://en.wikipedia.org/wiki/Rotation_matrix)**、
**[欧拉角(euler angles)](https://en.wikipedia.org/wiki/Euler_angles)**、
**[四元数(quaternion)](https://en.wikipedia.org/wiki/Quaternions_and_spatial_rotation)**和
**[角轴(axis angle)](https://en.wikipedia.org/wiki/Axis%E2%80%93angle_representation)**。  

自己的一些理解：
1. 欧拉角中的 **(x, y, z)**，**(roll, pitch, yaw)**，**(heading, elevation(attitude), bank)**是一回事，名称不同
1. 四元数和角轴近似
1. 角轴（**Axis Angle**）和**exponential twist**、罗德里格斯（**Rodrigues**）旋转向量是一回事，叫法不同，公式上有微小不同  
1. 欧拉角、四元数和角轴是更符合人类思维的表达方式，这三种旋转方式都可以转换为旋转矩阵，旋转矩阵更利于计算机计算——将空间旋转（spatial rotation）变为矩阵运算。  

后文分享了欧拉角、四元数、角轴与旋转矩阵相互转换公式。数学原理和代码实现将会在后续的blog中Po出。

## 相互转换

旋转矩阵：
\$R=\begin{bmatrix}x_{11} & x_{12} & x_{13} \\\\ x_{21} & x_{22} & x_{23} \\\\ x_{31} & x_{32} & x_{33}\end{bmatrix}\$，
欧拉角：
\$\begin{bmatrix}\theta_x \\\\ \theta_y \\\\ \theta_z \end{bmatrix}\$，
四元数：
\$q=\begin{bmatrix}x \\\\ y \\\\ z \\\\ \omega \end{bmatrix}\$，
角轴：
\$r=\begin{pmatrix}\begin{bmatrix}k_x \\\\ k_y \\\\ k_z \end{bmatrix},& \theta\end{pmatrix}\$

### 欧拉角\$\longleftrightarrow\$旋转矩阵
1. 欧拉角\$\longrightarrow\$旋转矩阵  
绕\$x\$(*roll*)、\$y\$(*pitch*)、\$z\$(*yaw*)轴分别旋转\$\theta_x\$、\$\theta_y\$、\$\theta_z\$的各旋转矩阵\$R_x\$、\$R_y\$、\$R_z\$
\$$\left\lbrace\begin{aligned}R_x &=& \begin{bmatrix}1 & 0 & 0 \\\\ 0 & \cos\theta_x & -\sin\theta_x \\\\ 0 & \sin\theta_x & \cos\theta_x \end{bmatrix} \\\\ R_y &=& \begin{bmatrix} \cos\theta_y & 0 & \sin\theta_y \\\\ 0 & 1 & 0 \\\\ -\sin\theta_y & 0 & \cos\theta_y \end{bmatrix} \\\\ R_z &=& \begin{bmatrix} \cos\theta_z & -\sin\theta_z & 0 \\\\ \sin\theta_z & \cos\theta_z & 0 \\\\ 0 & 0 & 1 \end{bmatrix}\end{aligned}\right.\$$
则
\$$\left\lbrace\begin{array}{cc}R=R_x \cdot R_y \cdot R_z & or \\\\ R=R_x \cdot R_z \cdot R_y & or \\\\ R=R_y \cdot R_x \cdot R_z & or \\\\ \vdots & \\\\ R=R_z \cdot R_y \cdot R_x & \end{array}\right.\$$
通常是按照*roll*\$\rightarrow\$*pitch*$\rightarrow\$*yaw*的顺序进行旋转的，即
\$$\begin{aligned}
& R &=& R_z \cdot R_y \cdot R_x \\\\ \\\\ & &=&\begin{bmatrix}
\cos\theta_z\cos\theta_y & \cos\theta_z\sin\theta_y\sin\theta_x-\sin\theta_z\cos\theta_x & \cos\theta_z\sin\theta_y\cos\theta_x+\sin\theta_z\sin\theta_x \\\\ 
\sin\theta_z\cos\theta_y & \sin\theta_z\sin\theta_y\sin\theta_x-\sin\theta_z\cos\theta_x & \sin\theta_z\sin\theta_y\cos\theta_x+\cos\theta_z\sin\theta_x \\\\ 
-\sin\theta_y & \cos\theta_y\sin\theta_x & \cos\theta_y\cos\theta_x 
\end{bmatrix}\end{aligned}\$$

1. 旋转矩阵\$\longrightarrow\$欧拉角  

### 角轴\$\longleftrightarrow\$旋转矩阵
1. 角轴\$\longrightarrow\$旋转矩阵  
绕转轴
\$\left\lbrace\begin{aligned}&\vec{k}&=&\begin{bmatrix} k_x \\\\ k_y \\\\ k_z \end{bmatrix} \\\\ &\||\vec{k}\|| &=& 1\end{aligned}\right.\$
按照右手定则旋转
\$\theta\$  
有
\$$K=\begin{bmatrix}0 & -k_z & k_y \\\\ k_z & 0 & -k_x \\\\ -k_y & k_x & 0 \end{bmatrix}\$$
则旋转矩阵\$R\$为
\$$\begin{aligned}& R &=& I+(\sin\theta)K+(1-\cos\theta)K^2 \\\\ & &=& \exp(\theta K) \end{aligned}\$$
注：罗德里格斯旋转向量\$\vec{r}=\theta\cdot\vec{k}\$

1. 旋转矩阵\$\longrightarrow\$角轴  
旋转矩阵
\$$R=\begin{bmatrix}x_{11} & x_{12} & x_{13} \\\\ x_{21} & x_{22} & x_{23} \\\\ x_{31} & x_{32} & x_{33}\end{bmatrix}\$$
 则角轴
\$$\left\lbrace
\begin{aligned}
     &\theta &=& \cos^{-1}(\frac{x_{11}+x_{22}+x_{33}-1}{2})
\\\\ &\vec{k} &=& \begin{bmatrix}\frac{x_{32}-x_{23}}{m} \\\\\frac{x_{13}-x_{31}}{m} \\\\\frac{x_{21}-x_{12}}{m} \end{bmatrix}
\end{aligned}
\right.\$$
其中
\$$m=\sqrt{(x_{32}-x_{23})^2+(x_{13}-x_{31})^2+(x_{21}-x_{12})^2}\$$

### 四元数\$\longleftrightarrow\$旋转矩阵
1. 四元数\$\longrightarrow\$旋转矩阵  
四元数\$$q=\begin{bmatrix}x \\\\ y \\\\ z \\\\ \omega \end{bmatrix}\$$
则旋转矩阵
\$$\begin{aligned}
& R &=& I+2\cdot\begin{bmatrix}-y^2-z^2 & x\cdot y & x \cdot z \\\\ x \cdot y & -x^2-z^2 & y \cdot z \\\\ x \cdot z & y \cdot z & -x^2-y^2\end{bmatrix}+2\cdot\omega\cdot\begin{bmatrix}0 & -z & y \\\\ z & 0 & -x \\\\ -y & x & 0 \end{bmatrix} \\\\ \\\\ & &=& \begin{bmatrix}1-2y^2-2z^2 & 2xy-2z\omega & 2xz+2y\omega \\\\ 2xy+2z\omega & 1-2x^2-2z^2 & 2yz-2x\omega \\\\ 2xz-2y\omega & 2yz+2x\omega & 1-2x^2-2y^2\end{bmatrix}
\end{aligned}$$
1. 旋转矩阵\$\longrightarrow\$四元数  
旋转矩阵
\$$R=\begin{bmatrix}x_{11} & x_{12} & x_{13} \\\\ x_{21} & x_{22} & x_{23} \\\\ x_{31} & x_{32} & x_{33}\end{bmatrix}\$$
则四元数为
\$$\left\lbrace\begin{aligned}&\omega &=& \frac{\sqrt{1+x_{11}+x_{22}+x_{33}}}{2} \\\\ &x &=& \frac{x_{32}-x_{23}}{4\omega} \\\\ \\\\ &y &=& \frac{x_{13}-x_{31}}{4\omega} \\\\ \\\\ &z &=& \frac{x_{21}-x_{12}}{4\omega}\end{aligned}\right.\$$

