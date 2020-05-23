---
title: Unity下计步器Pedometer算法的实现
date: 2020-05-15 19:28:42
categories:
- Unity
- 算法
tags:
- 算法
- Unity
---

## Unity下计步器Pedometer的实现
**1.环境**
unity2018,Android(小米5)
**2.原理**
[加速度传感器的计步算法Pedometer](https://blog.csdn.net/qq_36490364/article/details/104522980)
**3.实现**
注释写的很清楚了应该
```c#
using UnityEngine;
using UnityEngine.Networking;
using UnityEngine.UI;
#pragma warning disable CS0618

public class pedometer : MonoBehaviour
{

    public Text statusText,stepsText;
    public float lowLimit = 0.005f; //平缓
    public float highLimit = 0.1f; // 走路时的波峰波谷
    public float vertHighLimit = 0.25f;//跳跃时的波峰波谷
    private bool isHigh = false; // 状态
    private float filterCurrent = 10.0f; // 滤波参数 得到拟合值
    private float filterAverage = 0.1f; //   滤波参数  得到均值
    private float accelerationCurrent = 0f; //拟合值
    private float accelerationAverage = 0f;//均值
    private int steps = 0; // 步数
    private int oldSteps;
    private float deltaTime = 0f;//计时器
    private int jumpCount = 0;//跳跃数
    private int oldjumpCount = 0;
    
    private bool startTimer = false;//开始计时
    private bool isWalking = false;
    private bool isJumping = false;

    void Awake()
    {
        accelerationAverage = Input.acceleration.magnitude; 
        oldSteps = steps;
        oldjumpCount =jumpCount ;
    }

    void Update()
    {
        checkWalkingAndJumping(); // 检测是否行走
        if (isWalking)
        {
            statusText.text = ("状态:行走");
            
        }
        else if (!isWalking)
        {
            statusText.text = ("状态:不动");
        }

        if (isJumping)
        {
            statusText.text = ("状态:跳跃");
        }
    }

    void FixedUpdate()
    {

        //通过Lerp对Input.acceleration.magnitude(加速度标量和)滤波
        //这里使用线性插值的公式正好为EMA一次指数滤波 y[i]=y[i-1]+(x[i]-y[i])*k=(1-k)*y[i]+kx[i]
        accelerationCurrent = Mathf.Lerp(accelerationCurrent, Input.acceleration.magnitude, Time.deltaTime * filterCurrent);
        accelerationAverage = Mathf.Lerp(accelerationAverage, Input.acceleration.magnitude, Time.deltaTime * filterAverage);
        float delta = accelerationCurrent - accelerationAverage; // 获取差值，即坡度

        if (!isHigh)
        {   
            if (delta > highLimit)//往高
            {
                isHigh = true;
                steps++;
                stepsText.text = "步数: " + steps+"\n跳跃数："+jumpCount;
            }
            if(delta>vertHighLimit)
            {
                isHigh = true;
                jumpCount++;
                stepsText.text = "步数: " + steps + "\n跳跃数：" + jumpCount;
            }
        }
        else
        {
            if (delta < lowLimit)//往低
            {                
                isHigh = false;
            }
        }
    }

   
    private void checkWalkingAndJumping()
    {
        if ((steps != oldSteps)||(oldjumpCount != jumpCount))
        {
            startTimer = true;
            deltaTime = 0f;
        }
        if (startTimer)//计时器，让更新UI的慢一点，因为你不可能走路只用一帧的时间QAQ
        {
            deltaTime += Time.deltaTime;
            if (deltaTime != 0)
           {
                if (oldjumpCount != jumpCount)//检测是否是跳跃
                    isJumping = true;
                else
                    isWalking = true;           
           }
            if (deltaTime > 2)
           {
                deltaTime = 0F;
                startTimer = false;
           }
        }
        else if (!startTimer)
        {
            isWalking = false;
            isJumping = false;
        }
        oldSteps = steps;
        oldjumpCount = jumpCount;
    }
}

```

