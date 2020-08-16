---
title: LoopyCuts编译运行方法
date: 2020-08-16 01:01:23
categories:
- 教程
tags:
- 教程
---

这绝对是目前我编译过的最心酸的代码:），第一部分的pro配置和部分源码头文件是有误的，第二部分的pro、源码有误，更是几乎翻遍了依赖库cinolib的issue和commit:(

#### 一.loop_distributor部分

##### 1. windos qtcreater下：

如果使用windows且使用64位,按以下步骤：

1. glwidget.h文件中在`#define GLWIDGET_H`后面添加以下代码（一定得按此顺序添加头文件）

   ```c++
   #define GLEW_STATIC
   #include <GL/glew.h>
   ```

2. 将编译好的一份glew库放在loop_distributor文件夹下，同时我在里面lib下的glew库的64位lib库里添加了一份win下的`OpenGL32`和`GlU32`静态库

3. 修改loop_distributor.pro文件，将以下修改

   ```cmake
   win32{
     DEFINES += NOMINMAX
     #LIBS +=$$ANTDIR/lib/AntTweakBar.lib
     LIBS +=-L$$ANTDIR/lib/ -lAntTweakBar64#需添加64位，并用该写法否则无法链接dll
   }
   ```

   同时在最后添加glew库的依赖

   ```cmake
   
   #windos下还需以下依赖，同时取消glwidget.h 中#include <GL/glew.h>的注释，并在其前面添加#define GLEW_STATIC
   win32: LIBS += -L$$PWD/../lib/glew-2.1.0/lib/Release/x64/ -lglew32s
   INCLUDEPATH += $$PWD/../lib/glew-2.1.0/include
   DEPENDPATH += $$PWD/../lib/glew-2.1.0/include
   win32: LIBS += -lOpenGL32
   win32: LIBS += -lGlU32
   win32:QMAKE_CXXFLAGS += /bigobj#debug模式下编译需要加该项
   ```

   编译完成运行时选择`test_data`文件夹下将里面的`.sharp`和`.rosy`改成和obj模型同名文件，同时将该模型路径以参数方式运行程序（项目->run->command line arguments），否则无法运行。

   这样调试还不行，而且qtcreater调试体验也不好，不过按照以上方法设置后会为vs中的设置大大减轻压力，因为vs能识别一部分pro文件。

##### 2.vs

&emsp;&emsp;导入pro文件后运行，debug模式下提示缺少`AntTweakBar64.dll`,在调试->属性->配置属性->调试->环境中添加以下路径`PATH=%PATH%;G:\test\LoopyCuts\lib\AntTweakBar1.16\lib;E:\QT\5.12.9\msvc2015_64\bin`

第二个是缺少dll的路径，第三个是qt中的dll路径（不添加会继续提示缺少）。缺少dll的解决方法也可以用其他的，我一直用这个，比其他的体验真的好。。

&emsp;&emsp;release下上述方法失效，解决方法就是拷贝缺少的dll到包含有exe的同文件夹下。

&emsp;&emsp;vs中添加命令行参数的方法为调试->属性->配置属性->调试->命令参数中设置



#### 二.volumetric_cutter部分

##### 1. tetgen

&emsp;&emsp;正常cmake,记得用msvc编译，选择x64,不然默认出linux下的.a静态库

##### 2.程序部分

&emsp;&emsp;提前说明以下，这个程序依赖的一个库cinolib无法用msvc编译，巨坑，[issue链接](https://github.com/mlivesu/cinolib/issues/12)！首先在pro文件中配置好tetgen的依赖，然后如果有以下报错依次解决:

1. `octree.cpp`661行报错，改成以下代码,[相关commit链接](https://github.com/mlivesu/cinolib/commit/4849cb3beeb2ea1a26526bc6f7fc265da4ed2c4f)：

   ```c++
   AABB s_box(s[0],s[1]);
   ```

2. `hexmesh.cpp`405行和415行报错，[相关commit链接](https://github.com/mlivesu/cinolib/commit/df7cf8f4dfa6dbbebf1e6b04c26262185861335d)，依次修改为

   ```c++
   this->update_p_quality(pid);//405
   this->update_quality();//415
   ```

3. `draw_lines_tris.cpp`160行`glGenerateMimap(GL_TEXTURE_2D)`报错，未找到相关解决方法，注释掉后能在linux中运行,有个commit也是注释掉该方法，可是在下一个commit又取消了该注释,[链接](https://github.com/mlivesu/cinolib/commit/2d9f95ccc8f63a0bc0a0a17989cdfec5649e41c0#diff-3fd1c919d023c8ba52b7932fc71fa5c9)，具体效果未知

4. 以上步骤能在linux下编译，如果使用windows,还需以下步骤：

   - 在lib\cinolib\include\cinolib\gl文件夹下的draw_xxx.h中头文件中的win下的条件编译宏改成以下代码

     ```c++
     #include<windows.h>
     #include <GL/gl.h>
     #include <GL/glu.h>
     ```

   - 如果在win下使用mingw编译的话不仅速度慢而且因为obj文件太大无法编译，**不推荐**

   - 使用msvc编译，msvc不支持变长数组，但源码中有些地方使用变长数组，所以在源码中做如下修改，使用动态申请，但勿忘delete

     laplacian.cpp54行: 

     &emsp;&emsp;`uint base[n]`改用`uint *base=new uint[n]()`,勿忘delete！

     ambient_occlusion.cpp195行：

     &emsp;&emsp;`float depth_buffer[buffer_size*buffer_size]`改为`float *depth_buffer=new float[buffer_size*buffer_size]();`勿忘delete！

5. 关于这个的pro改动地方有点多，未作记录，就不列出

#### 说明

1. 第一部分在**windos下编译运行成功**，linux下的话应该可以添加glew库运行，我ubuntu机子上没有该库就没尝试。
2. 第二部分第三个错误未完美解决，翻到两个相关commit（前一个注释，后一个取消注释？？），注释掉后在**linux编译成功，windos下修改后能在msvc编译下编译成功**，mingw编译特别慢，而且还出错。
3. 以上两部分使用方法和运行效果还未测试。

