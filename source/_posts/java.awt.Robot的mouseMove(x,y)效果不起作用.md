---
title: java.awt.Robot的mouseMove(x,y)效果不起作用
date: 2019-03-29 18:12:49
categories:
- Java
tags:
- Bug
---

`mouseMove(x,y)`，此函数是使鼠标移动到`（x,y）`位置
测试发现它是一个逐渐逼近的过程，所以外面套个循环，就能移动到想要的位置了
原因未知，不知是不是win10的锅

```java
import java.awt.Robot;
Robot robot=new Robot();
int k=10;
int x=666;
int y=666;
while((--k)>0)
{
	robot.mouseMove(x,y);
}
```