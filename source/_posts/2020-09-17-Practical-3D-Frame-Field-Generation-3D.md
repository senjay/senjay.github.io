---
title: Practical 3D Frame Field Generation-3D
date: 2020-09-17 20:27:22
categories:
- 笔记
tags:
- 标架场
---

&emsp;&emsp;作者在前文的2d形式下引入的基函数其实是傅里叶基底，而在3d下，根据《Boundary aligned smooth 3D cross-frame field》的启发，使用了球谐函数作为基函数。不同的是该文使用了不同的多项式$$x^4+y^4+z^4$$来在球上展开。（而前文是$$x^2y^2+y^2z^2+z^2x^2$$，其实优化后式子是一样的）

### 二、3D

#### 1. 标架表示

&emsp;&emsp;如果$$\tilde{F}$$为参考标架函数，那么它为：
$$
\begin{aligned}
\tilde{F}&=\sqrt{\frac{7}{12}}Y_{4}^{0}+\sqrt{\frac{5}{12}}Y_{4}^{4}\\&=B\tilde{a}\\&=\left(Y_{4}^{-4},Y_{4}^{-3},Y_{4}^{-2},Y_{4}^{-1}, Y_{4}^{0}, Y_{4}^{1},Y_{4}^{2},Y_{4}^{3}, Y_{4}^{4}\right)\left(0,0,0,0, \sqrt{\frac{7}{12}}, 0,0,0, \sqrt{\frac{5}{12}}\right)^{\top}
\end{aligned}
$$
其中$$Y_{l}^{m}$$是实球谐基函数，其中$$l$$称为degree/band,$$m$$称为order，$$m\in[-l,l]$$。

（球谐函数在图形学中先是被用于光照渲染《*Spherical harmonic lighting: The gritty details*》，[一个通俗的介绍球谐函数的系列文章](https://zhuanlan.zhihu.com/p/51179426)）

ps:为什么选择球谐函数？因为它具有以下性质：

- 标准正交性，它是一组正交基

- 旋转不变性，比如用矩阵$$R$$旋转$$f(x)$$后变为$$g(x)$$,即$$g(x)=Rf(x)$$,根据旋转不变性有$$g(x)=f(Rx)$$

- **函数乘积的积分等于其球谐系数向量的点积**，$$\int_{S} L(s) t(s) d s=\sum_{i=0}^{\infty} L_{i} t_{i}$$

由旋转不变性，任意标架可表示为$$F=BR\tilde{a}$$，其中$$R$$是$$9\times 9$$旋转矩阵，可由Wigner D-matrices  给出。



#### 2. 能量函数

$$
E=\sum_{i j} \int_{S^{2}}\left(F^{j}(\alpha)-F^{i}(\alpha)\right)^{2} d \alpha=\sum_{i j}\left\|a^{j}-a^{i}\right\|^{2}
$$

相比于2d,3d中的能量函数其实是类似的，不同的是$$a$$，不再相同，因为引入了球谐函数作为基函数。可以注意到$$a$$是一个九维向量，而我们要得到的是一个三维向量（即$$a$$是一个嵌入到九维空间的三维流形），所以后面还需要用其他方法求解其投影在三维中对应的向量，文章中赋予了一个随机初值并使用梯度下降来求解（We do not have a formal proof, but we conjecture that there is a single minimum of the L2 norm ）



#### 3. 约束

&emsp;&emsp;一些顶点上的标架需要满足约束条件，如下

#####  3.1法向量对齐z轴

&emsp;&emsp;根据$$a=R_z\tilde{a}$$可得到
$$
a=\left(\sqrt{\frac{5}{12}} \sin 4 \theta, 0,0,0, \sqrt{\frac{7}{12}}, 0,0,0, \sqrt{\frac{5}{12}} \cos 4 \theta\right)^{\top}
$$
其中$$R_z$$是绕z轴的旋转矩阵。引入向量$$c=(c_0,c_1)$$,可写成以下形式
$$
\begin{aligned}
a &=\sqrt{\frac{7}{12}}(0,0,0,0,1,0,0,0,0)^{\top} \\
&+c_{0}(0,0,0,0,0,0,0,0,1)^{\top} \\
&+c_{1}(1,0,0,0,0,0,0,0,0)^{\top}
\end{aligned}
$$
其中$$cc^{\top}=\frac{5}{12}$$

##### 3.2法向量不与z轴对齐

&emsp;&emsp;策略是先将z轴转到法向量方向，即乘一个旋转矩阵$$R$$，则$$a$$变成以下形式
$$
\begin{aligned}
a &=\sqrt{\frac{7}{12}}R(0,0,0,0,1,0,0,0,0)^{\top} \\
& +c_{0}R(0,0,0,0,0,0,0,0,1)^{\top} \\
& +c_{1}R(1,0,0,0,0,0,0,0,0)^{\top}\\
\end{aligned}
$$

##### 3.3尖锐、角点

&emsp;&emsp;该类顶点上的标架需要符合两个法向量，选择两个大致正交的法向量作为它的特征，然后通过旋转让它们正交，然后将z轴转到它们的叉积方向，方法同3.2

##### 3.4 旋转矩阵

&emsp;&emsp;旋转矩阵为9x9矩阵，为Wigner D-matrices 
$$
\begin{aligned}
&R_{B}^{z}(\gamma)=\left[\begin{array}{ccccccccc}
\cos (4 \gamma) & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \sin (4 \gamma) \\
0 & \cos (3 \gamma) & 0 & 0 & 0 & 0 & 0 & \sin (3 \gamma) & 0 \\
0 & 0 & \cos (2 \gamma) & 0 & 0 & 0 & \sin (2 \gamma) & 0 & 0 \\
0 & 0 & 0 & \cos (\gamma) & 0 & \sin (\gamma) & 0 & 0 & 0 \\
0 & 0 & 0 &0 & 1 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 &  -\sin (\gamma) & 0 & \cos ( \gamma) & 0 & 0 & 0 & 0 \\
0 & 0 & -\sin (2 \gamma) & 0 & 0 & 0 & \cos (2 \gamma) & 0 & 0 & 0 \\
0 & -\sin (3 \gamma) & 0 & 0 & 0 & 0 & 0 & \cos (3 \gamma) & 0 \\
-\sin (4 \gamma) & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \cos (4 \gamma)
\end{array}\right]
\end{aligned}
$$


$$
\begin{aligned}
&R_{B}^{x}(\pi / 2)=\left[\begin{array}{cccccccc}
0 & 0 & 0 & 0 & 0 & \sqrt{14} / 4 & 0 & -\sqrt{2} / 4 & 0 \\
0 & -3 / 4 & 0 & \sqrt{7} / 4 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & \sqrt{2} / 4 & 0 & \sqrt{14} / 4 & 0 \\
0 & \sqrt{7} / 4 & 0 & 3 / 4 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 3 / 8 & 0 & \sqrt{5} / 4 & 0 & \sqrt{35} / 8 \\
-\sqrt{14} / 4 & 0 & -\sqrt{2} / 4 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & \sqrt{5} / 4 & 0 & 1 / 2 & 0 & -\sqrt{7} / 4 \\
\sqrt{2} / 4 & 0 & -\sqrt{14} / 4 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & \sqrt{35} / 8 & 0 & -\sqrt{7} / 4 & 0 & 1 / 8
\end{array}\right]
\end{aligned}
$$


$$
\begin{array}{l}
R_{B}^{y}(\beta)=R_{B}^{x}(\pi / 2) \times R_{B}^{z}(\beta) \times R_{B}^{x}(\pi / 2)^{\top} \\
R_{B}^{x}(\alpha)=R_{B}^{y}(\pi / 2)^{\top} \times R_{B}^{z}(\alpha) \times R_{B}^{y}(\pi / 2)
\end{array}
$$


则最终旋转矩阵为
$$
R_{B}(\alpha, \beta, \gamma)=R_{B}^{x}(\alpha) \times R_{B}^{y}(\beta) \times R_{B}^{z}(\gamma)
$$


#### 4.矩阵化

&emsp;&emsp;求解采用最小二乘$$||AX-b||$$，其中标架$$a^i$$和约束$$c^i$$存储于$$X$$向量（$$(9n_v+2n_l)$$维，$$n_v$$为不带约束的顶点，$$n_l$$是带约束的顶点）
$$
\begin{aligned}
X[9i+d]=a_d^i ,\quad \quad i \in[0,n_v],d \in [0,8]\\ 
X[9n_v+2i+d]=c_{d}^{i},\quad \quad i \in [0,n_l],d \in [0,1]\\
\end{aligned}
$$
&emsp;&emsp;系数矩阵$$A_{(9\mathcal{E}+9n_l)\times(9n_v+2n_l)}$$（$$\mathcal{E}$$为边数）下标是行，上标是列，设置如下：
$$
A_{9\mathcal{E}\times (9n_v+2n_l)}=\left\{
\begin{aligned}&a_{\mathcal{E}_{ij}+d}^{9i+d}=1,a_{\mathcal{E}_{ij}+d}^{9j+d}=-1 &&edge(v_i,v_j)\in \mathcal{E}, d \in[0,8]\\
&0  &&other\end{aligned}\right.
$$
注意到上面的$$A$$还不完整，还需在下面加入一个描述约束的block($$9n_l\times(9n_v+2n_l)$$)

&emsp;&emsp;对于有约束点，计算得到其将z轴旋转到法向方向的9x9旋转矩阵$$R_B$$,并定义如下值
$$
\begin{aligned}
h_{0} & = R_{B} (1,0,0,0,0,0,0,0,0)^{\top} \\
h_{4} & = R_{B} (0,0,0,0,1,0,0,0,0)^{\top} \\
h_{8} &= R_{B} (0,0,0,0,0,0,0,0,1)^{\top}\\
\lambda& =100
\end{aligned}
$$
其中$$\lambda$$是惩罚系数，则矩阵A剩余部分为
$$
A_{9n_l\times(9n_v+2n_l)}=\left\{
\begin{aligned}&a_{i+d}^{9i+d}=\lambda,\quad a_{i+d}^{9 n_v+2i}=\lambda h_0[d],\quad a_{i+d}^{9n_v+2i+1}=\lambda h_8[d] \ \ \ &&i\in \mathcal{n_l}, d \in[0,8]\\&0  &&other
\end{aligned}
\right.
$$


&emsp;&emsp;$$b$$为$$(9\mathcal{E}+9n_l)$$维向量，其前$$9\mathcal{E}$$维为$$0$$,后$$9n_l$$由约束决定，如下
$$
b_{9\mathcal{E}+9n_l}=\left\{
\begin{aligned}&b_i=0  \quad && i \in[0,9\mathcal{E})\\
&b_i=\lambda \sqrt{\frac{7}{12}}h_4[d]  &&i \in[9\mathcal{E},9\mathcal{E}+9n_l]
\end{aligned}
\right.
$$
注意到$$b$$中的非零项中的$$h_4$$和$$A$$中的约束项一一对应的，即矩阵$$A$$和$$b$$都是动态生成的，分成了两个block:表面约束点和自由点。另据作者的说法，在这两个block中做一个Hilbert sort会提升大约30%的效率。

&emsp;&emsp;至此，可通过求解$$A^{\top}AX=A^{\top}X$$得到位于顶点的嵌入到九维空间的标架表示（不再像2D中那样只需标准化就行了）。
