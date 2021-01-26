# 1.WINRM和WIMRS

Windows 远程管理

 ## 1.1 WINRM

**Windows远程管理（WinRM）是Windows Server 2003 R2, Windows Vista和Windows Server 2008中一种新式的方便**远程管理**的服务。**

*WinRM是远程管理应用的“服务器”组成部分，并且WinRS（Windows远程Shell）是WinRM的“客户端”，它在远程管理WinRM服务器的计算机上运行。  然而，我们应该注意到两个计算机必须手动安装WinRS，还要使WinRM能够启动并从远程系统传回信息。*

**使用http及SOAP协议完成工作；很容易完成http请求，也很容易被攻击者利用来做远程管理！**

  *WinRM基于Web服务管理（WS-Management）标准。 这么说的意思是：WinRM使用HTTP[协议](http://cisco.chinaitlab.com/List_11.html)（80端口）并且用SOAP来请求它工作。 这样的好处在于HTTP请求能够非常容易的穿过[防火墙](http://security.chinaitlab.com/List_1291.html)进行收发。 因而它的好坏在于：它使通过Internet管理远程Windows PC更为容易，或者它更容易适合Internet上存心不良的远程Windows攻击者。 WinRM使用HTTP的其它好处在于：如果HTTP入站通讯被允许，就没有其它端口必需在服务器和客户端上被开放。*



**使用WMI数据库执行指令；使用80 端口，甚至可和IIS共同使用80端口。**

*WinRM将使用WMI数据库执行你或者你的系统管理包的任务，就像使用VBScript一样使用另一个编程接口的相似任务。与我在前面提及时相同，WinRM的好处是它使用80端口（HTTP）。并且有些特殊的代码甚至允许WinRM与IIS共享80入站端口，它们可能正共同运行在80端口上。*



**可为其添加验证来做安全防范。**

  *WinRM支持多种验证方式，以阻止正连接上的任何人进行任何关于你PC客户端和服务器的管理任务。不过，如同被开放的其他任何端口一样，如果验证和加密被放置在了合适的时机，你就已经采取了全部合理的[安全](http://security.chinaitlab.com/)防范。*

判断是否🔛

  winrm enumerate winrm/config/listener





## 1.2 WinRS
​	WinRS是Windows远程Shell。 

​	使用WinRS你可以查询远程运行WinRM的Windows主机。不过请记住，你的主机也需要运行WinRM来使用WinRS。
​    如你从下面的图解中看到的一样，winrs是一个非常有特色、能够提供丰富帮助信息的全命令行工具。

  

*WinRM和WinRS是Windows系统管理员应该了解的非常强有力的新工具。  我想使用WinRM/WinRS进行远程管理非常有潜力！ 你可以安装程序、更改设置或者Troubleshooting（只要它连在网络上）。 你甚至能更进一步去扩展它并且把WinRS与脚本组合起来在计算机列表中执行那些任务。 并且请记住，无论你是否使用这些个工具，你的系统管理软件在不久的将来全用到它们。*

winrs ver 



## 1.3 winrshost

winrshost ---->cmd.exe





# 2.wce



# 3.vssadmin





# 4.wmic

https://www.freebuf.com/articles/system/182531.html



# PS:Winlogbeat

Winlogbeat是Windows事件日志的轻量级数据发送器。 虽然Elastic群集通常用于实时监视，但是可以对Winlogbeat进行调整，以手动将“冷日志”或旧的非活动Windows事件日志（EVTX）手动发送给Elastic Stack。 该功能使分析人员可以从收集的系统图像中提取EVTX文件，并利用Elastic堆栈的功能进行调查。

使用Winlogbeat将Windows事件日志流传输到Elasticsearch。Winlogbeat 通过标准的 windows API 获取 windows 系统日志，常见的有 Application，Security 、System三个核心日志文件。

https://cloud.tencent.com/developer/article/1539163·



4.schtasks

5.lsass

