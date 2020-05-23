---
title: Unity fog雾的三种模式
date:  2020-02-22 15:35:12
categories:
- Unity
tags:
- Unity
---

## unity fog雾的三种模式

**1.Linear**
线性模式，有fogStartDistance和fogEndDistance两个参数，雾从Start开始到End越来越浓，End之后也为最大浓度，计算方法为$\frac{end-|z|}{end-start}$，z为相机距离
**2.Exponential**
指数模式，有参数fogDensity（取值范围0-1），表示雾的浓度，浓度越大雾越大，计算方法为$e^{-d|z|}$,其中d为浓度，z为相机距离
**3.Exponential Squared**
指数平方，有参数fogDensity，越大表示雾越浓，计算方法为$e^{-(dz)^2}$

ps:fogDensity参数只适用于2，3，fogStartDistance和fogEndDistance只适用于1

下面是使用代码开启雾气的效果

```c#
RenderSettings.fog = true;//开启
RenderSettings.fogMode = FogMode.ExponentialSquared;//模式
RenderSettings.fogColor = Color.red;//颜色
RenderSettings.fogDensity = 0.04f;//浓度
```
[关于打包apk后雾效果消失的解决方法](https://blog.csdn.net/qq_36490364/article/details/104443965)