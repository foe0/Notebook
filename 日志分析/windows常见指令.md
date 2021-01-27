## 横向移动-WMI

WMI object -- class Win32_Process及其方法Create。Create方法允许用户在本地或远程创建一个进程。需要注意的是，当在远程系统上使用Create方法时，该方法是在一个名为 "Wmiprvse.exe "的主机进程下运行的。

进程WmiprvSE.exe是spawn Create方法的CommandLine参数中定义的进程。因此，远程创建的新进程将以Wmiprvse.exe为父进程。WmiprvSE.exe是一个DCOM服务器，它是在DCOM服务主机svchost.exe下面产生的，参数如下C:\WINDOWS\system32/svchost.exe -k DcomLaunch -p。从登录会话的角度来看，在目标机上，WmiprvSE.exe是由DCOM服务主机在不同的登录会话中生成的。然而，无论WmiprvSE.exe执行的是什么，都会发生在从网络认证的用户创建的新的网络类型（3）登录会话上。

攻击模拟：
(Empire: XXX) > usemodule persistence/elevated/wmi*
(Empire: powershell/persistence/elevated/wmi) > execute
(Empire: XXX) > shell whoami

检测思路：
wmiprvse.exe产生（Spawn）新进程，这些进程是非系统账户会话的一部分。
非系统账户利用WMI通过netwotk执行代码的情况。



event_id = 4688 ，父进程wmiprvse.exe，子进程有cmd，powershell等。

event_id = 4648，进程名称：c:\windows\syswow64\wbem\wmic.exe，网络地址不为-

event_id = 4688 ，父进程%wmiprvse.exe，登陆ID !=0x3e7 



对登陆ID的解释：

系统帐户登录会话的logon ID始终为0x3e7（十进制999），网络服务会话的logon ID始终为0x3e4（996），本地服务的logon ID为0x3e5（997），大多数其他Logon ID是随机生成的。