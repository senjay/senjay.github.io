---
title: Unity使用NetworkServer与NetWorkClient实现服务端与客户端传递消息
date: 2020-02-26 14:44:05
categories:
- Unity
tags:
- Unity
---

## Unity使用NetworkServer与NetWorkClient实现服务端与客户端传递消息
**1.环境**
&emsp;&emsp;Unity2018
**2.前述**
&emsp;&emsp;第一个想到的方法是使用Socket,也实现了功能,但是我想Unity应该有自带的API用来实现,可是查阅了好多资料,发现大多是使用NetWorkManager,鉴于我的项目对网络的要求十分微小,因此我选择了较为低级NetworkServer和NetWorkClient类
**3.实现**
&emsp;&emsp;create一个Emty GameOjbect,挂载以下脚本,注意一下开启客户端方法setClient需要绑定在一个button上,服务端发送信息sendMessage也要绑定在一个button上。
&emsp;&emsp;注意，这是从我项目中直接拷贝过来稍微改了一下，可能并不能直接使用，但是应该可以很好的帮助理解这两个类的大致用法,下面是两个文档，好像需要翻墙
[NetworkServer文档](https://docs.unity3d.com/2018.1/Documentation/ScriptReference/Networking.NetworkServer.html)
[NetworkClient文档](https://docs.unity3d.com/2018.1/Documentation/ScriptReference/Networking.NetworkClient.html)

服务端与客户端的实现
```c#
using System.Net;
using System.Net.Sockets;
using UnityEngine;
using UnityEngine.Networking;
using UnityEngine.UI;

public class MyNetWork : MonoBehaviour
{

    public Text ipText;
    private NetworkClient myClient;

    void Start()
    {
        getIp();      
    }

    void Update()
    {        
    }

    public void getIp()//获取本机IP
    {
        try
        {
            IPHostEntry IpEntry = Dns.GetHostEntry(Dns.GetHostName());
            foreach (IPAddress item in IpEntry.AddressList)
            {
                //AddressFamily.InterNetwork  ipv4
                if (item.AddressFamily == AddressFamily.InterNetwork)
                {
                    ipText.text = "IP:" + item.ToString();
                    MyMessage.ipAddress = item.ToString();
                    //开启服务端
                    if(NetworkServer.active)
                    {
                        NetworkServer.Shutdown();
                    }
                    else
                    {
                        starServer();
                    }
                    return;
                }
            }
            return ;
        }
        catch { return; }
    }

    private void starServer()//开启服务器
    {
        NetworkServer.Listen(MyMessage.ipAddress, MyMessage.serverPort);//参数为IP和Port
        Debug.Log(NetworkServer.active);    
    }
  public void sendMessage()//服务端发送消息
    {
        if(NetworkServer.connections.Count>0)
        NetworkServer.SendToAll(MyMessage.msgTypeSend, new MessageInfo(isWalking, steps));
    }
    public void setClient()//开启客户端
    {
        myClient = new NetworkClient();
        myClient.RegisterHandler(503, receiveMessage);//注册回调函数,参数1为消息类型,数字可以自定义,用于识别回调函数,参数2为回调函数
        myClient.Connect(MyMessage.ipAddress, MyMessage.serverPort);//连接服务端,参数分别为IP和Port
    }

    private void receiveMessage(NetworkMessage netMsg)//客户端接收信息的回调函数的实现
    {
        MessageInfo hostMessage = netMsg.ReadMessage<MessageInfo>();

        bool isWalking= hostMessage.isWalking;
        long steps  = hostMessage.stepCount;

        //Output the Player name and comment
        Debug.Log("isWalking: " + isWalking);
        Debug.Log("steps  : " + steps);
    }
}
```
MessgafeInfo类是封装的用来发送信息的自定义实体类,继承MessageBase
```c#
using UnityEngine.Networking;

public class MessageInfo : MessageBase
{
    public bool isWalking;
    public long stepCount;
    public MessageInfo() { }
    public MessageInfo(bool iswalk,long stepcount)
    {
        this.isWalking = iswalk;
        this.stepCount = stepcount;
    }
}
```

