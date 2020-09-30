---
title: Boundary Aligned Smooth 3D Cross-Frame Field
date: 2020-09-16 10:28:34
categories:
- 笔记
tags:
- 标架场
---

### 标架表示方法

&emsp;&emsp;3d标架存在24个方向（坐标轴有6个选择，垂直它的有4种，即6x4=24种），为了表述两个相邻标架的差异，采用基函数来表示**四面体网格面上的标架**，而不是采用以前的使用vector pair来表示（避免了混合整数规划问题）,该文首先提出了采用球谐函数作为基函数来描述标架,从而可以通过积分来描述标架的差异并据此定义能量函数来优化 ，最后使用L-BFGS来光滑标架场，该方法同时支持表面法向量约束对齐。



#### 1.  标架表示

&emsp;&emsp;该文中用$$h(t)=t_x^2t_y^2+t_y^2t_z^2+t_z^2t_x^2$$来表示标架，将分解成球谐基函数表示
$$
f_{[I]}=-\frac{2 \sqrt{\pi}}{15}\left(Y_{4}^{0}+\sqrt{\frac{5}{7}} Y_{4}^{4}+16 \sqrt{\pi} Y_{0}^{0}\right)
$$
考虑比较比较两标架的差异时无需常数项，进而简化成
$$
f_{[I]}=\sqrt{7}Y_{4}^{0}+\sqrt{5} Y_{4}^{4}
$$
又由球谐函数的旋转不变性，则
$$
f_{[R]}(s)=h(R^{\top}s)
$$
其中R是旋转矩阵。进入可用以下目标函数表示两标架之间的差异
$$
\int_{S^{2}}\left(f_{\left[R_{a}\right]}(s)-f_{\left[R_{b}\right]}(s)\right)^{2} d s=\left\|\hat{R}_{a} \hat{h}-\hat{R}_{b} \hat{h}\right\|^{2}
$$

#### 2.约束

&emsp;&emsp;表面约束为标架方向之一与法向量对齐，作者证明了当且仅当$$\hat{f}(4)=\sqrt{7}$$,即参考标架向量的第四个系数为常数，进而约束可分为两步：1.旋转法向量到z轴（需要旋转矩阵）；2.对齐法向量



#### 3.优化目标

&emsp;&emsp;作者为了平滑效果和避免因网格密度造成的不均匀采样，将优化目标转换成了球谐函数的系数的梯度。
$$
\nabla \hat{f}(k)=\sum_{i=1}^{4} \hat{f}_{p_{i}}(k) \nabla w_{i}
$$
以上通过四面体网格面上的标架导出四面体标架系数的梯度，其中$$w_i$$是四面体每个面的重心的梯度（[具体计算方法](https://zhuanlan.zhihu.com/p/162939718)）

&emsp;&emsp;通过体积分计算全局的量，
$$
E_{s}\left(\mathrm{TET}_{j}\right)=E_{s}(p)=\sum_{k=0}^{8}\|\nabla \hat{f}(k)\|^{2}, p \in \mathrm{TET}_{j}
$$
&emsp;&emsp;通过面积分计算表面约束，
$$
\begin{aligned}
E_{a} &=\int_{\partial \Omega}\left\|\left(\hat{R}_{n_{p} \rightarrow z} \hat{f}_{p}\right)(4)-\sqrt{7}\right\|^{2} d p \\
& \approx \sum_{\mathrm{TRI}_{i} \in \partial \Omega} \operatorname{area}\left(\mathrm{TRI}_{i}\right)\left\|\left(\hat{R}_{n_{i} \rightarrow z} \hat{f}_{p_{i}}\right)(4)-\sqrt{7}\right\|^{2}
\end{aligned}
$$
考虑到体积分和面积分造成的权重不一致，则总的能量函数为
$$
E_{f}=\frac{E_{s}}{\operatorname{volume}(\Omega)^{1 / 3}}+w_{a} \frac{E_{a}}{\operatorname{area}(\partial \Omega)}
$$
其中$$w_a$$是对齐约束的惩罚系数。

&emsp;&emsp;至此解出$$\arg \min_{\hat f}{E_f(\hat{f})}$$,但是它是一个嵌入在九维空间的三维流形，需将它投影到三维，生成一个初始旋转$$\Phi_{i}$$,将其映射回九维，并作$$L^2$$范数的优化,得到$$\hat f$$在三维的近似表示
$$
\Phi_{0, i} \leftarrow \arg \min _{\Phi_{i}}\left\|\hat{f}_{0, i}-\hat{R}\left(\Phi_{i}\right) \hat{h}\right\|^{2}
$$
最后使用L-BFGS迭代使	标架场光滑$$\arg \min _{\Phi} E_{f}\left(\hat{f}_{[R(\Phi)]}\right)$$