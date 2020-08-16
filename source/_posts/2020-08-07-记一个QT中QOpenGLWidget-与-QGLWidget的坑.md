---
title: 记一个QT中QOpenGLWidget 与 QGLWidget的坑
date: 2020-08-07 14:46:45
categories:
- bug
tags:
- bug
---

&emsp;&emsp;因为QGLWidget在文档中显示过时，就采用了QOpenGLWidget,因为要用到多重采样来抗锯齿

```c++
QSurfaceFormat format;
format.setSamples(4);
QSurfaceFormat::setDefaultFormat(format);
```

结果造成像素深度`winz`无法读取

```c++
glReadPixels((int)winx, (int)winy, 1, 1, GL_DEPTH_COMPONENT, GL_FLOAT,&winz); 
```

`winz`一直不会改变。

&emsp;&emsp;这个bug找了两天多，最终才确定是QOpenGLWidget的多重采样造成的。在QT文档中有

```c++
	QOpenGLWidget always renders offscreen, using framebuffer objects. QGLWidget on the other hand uses a native window and surface.
```

同时关于Multisampling的记录是

```C++
Multisampling
	To enable multisampling, set the number of requested samples on the QSurfaceFormat that is passed to setFormat(). On systems that do not support it the request may get ignored.
	Multisampling support requires support for multisampled renderbuffers and framebuffer blits. On OpenGL ES 2.0 implementations it is likely that these will not be present. This means that multisampling will not be available. With modern OpenGL versions and OpenGL ES 3.0 and up this is usually not a problem anymore. 
```

&emsp;&emsp;可以看到它是**离屏渲染（offscreen）**的，要求使用`multisampled renderbuffers`和`framebuffer`,这是一个很大的不同，所以如果坚持要使用它并且要用到读取深度之类的可行思路是重新绑定FBO后去读取。而在QGLWidget中只需要在构造函数的初始化列表中用

```c++
:QGLWidget(QGLFormat(QGL::SampleBuffers | QGL::AlphaChannel), parent)
```

