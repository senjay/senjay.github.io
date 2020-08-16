---
title: Gauss、Stokes、Green公式
date: 2020-08-13 19:35:33
categories:
- 笔记
tags:
- math
---

&emsp;&emsp;以下$$F$$为向量场,$$\mathbf{n}$$为法向量，$$\mathbf{\tau}$$为切向量。

#### 1. Gauss

$$
\int_{V}\nabla\cdot Fdv=\int_{S}F\cdot d\mathbf{A}=\int_{S}F\cdot \mathbf{n}dA
$$



#### 2. Stokes

$$
\int_{S}\nabla\times F\cdot d\mathbf{A}=\int_{S}\nabla\times F\cdot \mathbf{n}dA=\oint_{\partial S}F\cdot d\mathbf{l}=\oint_{\partial S}F\cdot \mathbf{\tau}dl
$$



#### 3. Green

##### 3.1 散度形式

$$
\int_{S}\nabla\cdot F dA=\oint_{\partial S}F\cdot d\mathbf{l}=\int_{\partial S}F\cdot\mathbf{n}dl
$$

##### 3.2 旋度形式

$$
\int_{S}\nabla\times F\cdot d\mathbf{A}=\oint_{\partial S}F\cdot d\mathbf{l}=\oint_{\partial S}F\cdot \mathbf{\tau}dl
$$



#### 4. 说明

&emsp;&emsp;可理解为内部区域的性质通过边界来反映，即：

1. Gauss:内部体积元的散度积分等于表面通量积分
2. Stokes:表面面积元矢量的旋度积分等于边界的环量积分
3. Green:两个形式分别为以上的退化形式

