---
title: Paper Introduction
date: 2020-09-16 10:06:39
categories:
- 笔记
tags:
- 论文简介
---

### 1. 标架场

#### 《Instant field-aligned meshes》-2015

&emsp;&emsp;采用局部的高斯赛德尔迭代来求解表面网格的标架场和位置场，其在局部直接暴力比较（搜索空间其实很小，以4-Rosy为例，只有16）两个标架的不同来进行优化，并用**多分辨率层次法（Multiresolution hierarchy）**来避免陷入局部解，该方法**速度快**，**鲁棒性好**。[笔记1](https://senjay.github.io/2020/08/22/2020-08-22-Instant-Meshes-%E6%A0%87%E6%9E%B6%E5%9C%BA%E6%96%B9%E6%B3%95/)，[笔记2](https://senjay.github.io/2020/08/28/2020-02-22-Instant-Meshes-%E5%A5%87%E5%BC%82%E7%82%B9%E6%A3%80%E6%B5%8B/)



Wenzel Jakob et al., “Instant Field-Aligned Meshes,” *ACM Transactions on Graphics* 34, no. 6 (November 4, 2015): 1–15, https://doi.org/10.1145/2816795.2818078.



#### 《<i>LoopyCuts</i>: practical feature-preserving block decomposition》-2020

&emsp;&emsp;一阶段首先通过MIQ方法生成的标架场和确定好的特征来生成Field-Coherent Loops（方法采用《Tracing Field-Coherent Quad Layouts》），提出通过维护采样池和采样最远loop队列来对网格进行loop划分；二阶段通过划分好的loop通过中点细分生成体网格。（该方法对标架场质量要求高，速度慢，一些demo我测试没通过）[编译方法](https://senjay.github.io/2020/08/16/LoopyCuts%E7%BC%96%E8%AF%91%E8%BF%90%E8%A1%8C%E6%96%B9%E6%B3%95/)，[笔记](https://senjay.github.io/2020/09/04/2020-09-04-LoopyCuts-Computer-Cutting-Loops/)



Livesu, Marco, Nico Pietroni, Enrico Puppo, Alla Sheffer, and Paolo Cignoni. “*LoopyCuts*: Practical Feature-Preserving Block Decomposition for Strongly Hex-Dominant Meshing.” *ACM Transactions on Graphics* 39, no. 4 (July 8, 2020). https://doi.org/10.1145/3386569.3392472.



#### 《Boundary Aligned Smooth 3D Cross-Frame Field》-2011

&emsp;&emsp;3d标架存在24个方向（坐标轴有6个选择，垂直它的有4种，即6x4=24种），为了表述两个相邻标架的差异，采用基函数来表示**四面体网格面上的标架**，而不是采用以前的使用vector pair来表示,该文首先提出了采用球谐函数作为基函数来描述标架,从而可以通过积分来描述标架的差异并据此定义能量函数来优化 ，最后使用L-BFGS来光滑标架场，该方法同时支持表面法向量约束对齐（有缺陷）。并通过3d标架来引导出流线划分网格并生成hexahedron-dominant mesh。（计算量大）



Huang, Jin, Yiying Tong, Hongyu Wei, and Hujun Bao. “Boundary Aligned Smooth 3D Cross-Frame Field.” In *Proceedings of the 2011 SIGGRAPH Asia Conference on - SA ’11*, 1. Hong Kong, China: ACM Press, 2011. https://doi.org/10.1145/2024156.2024177.



#### 《Practical 3D frame field generation》-2016

&emsp;&emsp;先描述2D形式下使用基函数来描述标架场的可行性，进而拓展到3D采用球谐函数作为基函数来表述**四面体网格顶点上的标架**，该文的优化方法相比于《Boundary Aligned Smooth 3D Cross-Frame Field》性能更好，同时增加了约束限制条件的可行性。该文先以一个简化的方法来给出一个目标能量函数，并用最小二乘来初始化它，最后也已L-BFGS法来平滑。



Nicolas Ray, Dmitry Sokolov, and Bruno Lévy, “Practical 3D Frame Field Generation,” *ACM Transactions on Graphics* 35, no. 6 (November 11, 2016): 1–9, https://doi.org/10.1145/2980179.2982408.



### 2.网格简化

#### 《Surface simplification using quadric error metrics》-1997

&emsp;&emsp;通过Quadratic error Matrix来描述距离误差，进而塌陷边到合适的点来达到简化网格的目的。[笔记](https://senjay.github.io/2020/07/14/2020-07-14-Quadric-error-metric/)



Michael Garland and Paul S. Heckbert, “Surface Simplification Using Quadric Error Metrics,” in *Proceedings of the 24th Annual Conference on Computer Graphics and Interactive Techniques - SIGGRAPH ’97* (the 24th annual conference, Not Known: ACM Press, 1997), 209–16, https://doi.org/10.1145/258734.258849.

