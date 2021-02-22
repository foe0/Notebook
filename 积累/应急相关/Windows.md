netstat -ano | findstr LIST

windows操作系统入侵检查流程图如下所示：

![图片](https://mmbiz.qpic.cn/mmbiz_png/oibWJqH5OVmXQogiblfZCHAAVNr3NQa7mSH119iczNPflTHhKicADicB9hmMJeP2RcumZqkgk60b1KNKbTFwh6XN83w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



# 一 现象检查

可通过监测告警、日常巡检等主动机制发现存在的异常事件，如果没有主动发现，则只能在安全事件发生后被动发现。以下说明在被入侵后对可能存在的异常现象进行检查。



现象检查发现的异常程序不可直接删除，应先验证异常进程是否存在自我守护机制，否则安全事件无法得到根除。



## 1.1 已监听端口

已监听端口并非一个独立的对象，而是和进程相关联，进程如果需要对外提供访问接口，则必须通过监听端口的方式对外开放，常用于在内网中部署正向后门程序。

注意点：

1 在操作系统初始化正常运行后，建议记录已监听端口的基线值，供日常巡检使用；

2 受操作系统、关键路径中的网络层访问控制影响。

例如检查已监听端口是否存在异常。则运行cmd命令行，使用**netstat -ano | findstr LIST**命令检查已监听端口。

示例：

点击【开始菜单】，搜索框中输入【cmd】，右键点击【cmd.exe】程序，选择【以管理员身份运行】。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oibWJqH5OVmXQogiblfZCHAAVNr3NQa7mSHP3vz4RJhZxB2bEAaJIWHcsWQrkXf7gyMzJeMibxuNEQjslQkcOG9EA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

使用netstat -ano | findstr LIST命令检查已监听端口。含义如下：

1 左1列，程序协议；

2 左2列，本地监听地址和端口；

3 左3列，外部地址（留空）；

4 左4列，状态为监听；

5 左5列，程序pid。

可根据已知程序不会监听的端口进行判断是否存在异常，并根据该链接的pid进行深入分析。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oibWJqH5OVmXQogiblfZCHAAVNr3NQa7mSwAlhWpmAATL2uuuPJsQSjKjaHibfZYA3ONTy2SIgdVaSoes44iaHasmA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



## 1.2 已建立连接

已建立连接分为入站连接和出站连接，入站意为访问操作系统本地的方向，出站意为操作系统访问外部的方向。

注意点：

1 受操作系统、关键路径中的网络层访问限制影响；

2 服务端如存在主动外联则需要重点检查。

例如检查已建立连接是否存在异常。则使用**netstat -ano | findstr EST**命令检查已建立连接。

示例：

查询结果，可根据非常规连接判断是否存在异常，并根据该链接的pid进行深入分析：

![图片](https://mmbiz.qpic.cn/mmbiz_png/oibWJqH5OVmXQogiblfZCHAAVNr3NQa7mSpxM5YR7pA53aWN8yC0kttV4kXqEUFNJmJYo1ploVcnDBVnw2WPvqnw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



## 1.3 系统进程

cpu资源被占满、异常的已监听端口、异常的已建立连接在深入分析时都会检查系统进程。

注意点：不建议使用任务管理器进行系统进程检查，因为可供分析的维度较少，且容易被进程名欺骗，操作系统允许相同名称但不同执行路径的进程同时存在。

例如检查系统进程是否存在异常，使用以下命令获取系统进程详细信息。

**wmic process get** 

**caption,commandline,creationDate,executablepath,handle,handleCount > c:\yanlian\porcess.txt**

示例：

在cmd命令行中复制以上命令并回车执行。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oibWJqH5OVmXQogiblfZCHAAVNr3NQa7mS5S2YbicialLl01VoibBUJYw1yavhBaCcZ2Ec6UGFEVhsOckWmkdpsGXtA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

打开C:\yanlian\process.txt，可看到6列内容，含义如下所示：

1 caption：进程名；

2 commandline：进程名、程序执行路径、进程执行参数；

3 creationDate：进程启动时间（格式为：年月日时分秒）；

4 executablepath：程序执行路径；

5 handle：进程pid；

6 handleCount：该进程的父进程pid。

可根据进程名、进程执行参数、进程启动时间、程序执行路径判断是否存在异常，并根据异常点进行深入分析。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oibWJqH5OVmXQogiblfZCHAAVNr3NQa7mSSibaSAp0FOg6xDdIG5sZV2ia1YR9FTaa8bJBlWwSraibb9yQcFOoMpHFw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



# 二 持久化检查

如通过现象检查发现异常程序，则可以通过停止运行该进程的方式，判断其是否会重新启动。

## 1.1 任务计划

任务计划可以将任何脚本或程序定时启动。如被黑客利用则会充当恶意程序的守护机制。

注意点：不建议使用图形化任务计划程序进行检查，因为数量、层级较多不方便检查。

检查任务计划是否存在异常的方法：

1 使用schtasks /query /fo LIST /v 

\>c:\yanlian\schtasks.txt命令获取任务计划；

2 使用正则(Folder|TaskName|Status|Author|Task To Run|Scheduled Task State|Start Time|Start Date)(.*)过滤任务计划关键字段；

3 使用正则Start Date(.*)和Start Date$0\n分割不同任务计划。

示例1：

导出任务计划，提示错误。![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

查看当前活动代码页为936，将其修改为437。![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

再次导出任务计划。但导出的任务计划无关信息过多，需要过滤。

示例2：

复制以下正则表达式。

(Folder|TaskName|Status|Author|Task To 

Run|Scheduled Task State|Start Time|Start Date)(.*)

将正则表达式复制到搜索框中，点击【find all】，再【ctrl+c】复制匹配到的内容。![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

同时新建一个文档将复制的内容进行粘贴，但所有任务计划未分割不方便检查，因此还需要对过滤后的任务计划进行分割。

示例3：

复制以下正则表达式。

Start Date(.*)

Start Date$0\n

按【ctrl+h】将以上正则进行粘贴并替换所有。

最后形成如下文档：只记录任务计划名称，运行状态，创建者，程序路径，计划状态，启动时间，以方便对可能存在异常的对象进行检查。

## 1.2 自启动项

自启动项可在系统启动时自动运行相关程序，恶意程序的第二个自启机制。

注意点：不建议使用图形化msconfig工具进行检查，因为名称、路径较长则不方便取证。

使用以下命令将自启动项导出检查。

**reg export** 

**HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run c:\yanlian\autorun.reg**

示例：

输入命令将自启动项配置文件导出。

检查导出的自启动项配置是否存在异常。

## 1.3 环境变量

环境变量用于将系统路径变量化，如被黑客利用则会以最高权限运行恶意程序，例如将环境变量%systemroot%变更为其他路径，同时建立system32文件夹并将恶意程序通过服务启动。

注意点：环境变量%systemroot%修改后需进行恢复，否则系统无法正常重启。

使用set命令将环境变量导出检查。

示例：

输入命令将环境变量配置文件导出。![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

检查导出的环境变量配置是否存在异常。



## 1.4 系统服务

服务可在系统启动时自动运行相关程序或启动后延迟运行相关程序，是恶意程序的第三个自启机制。

注意点：不建议使用图形化services.msc程序进行检查，因为数量、层级较多不方便检查。

1 使用命令将服务配置文件导出检查；

2 过滤包含Description、ImagePath、ServiceDll的字段；

3 过滤包含(.*)(\.dll|\.exe)(.*)的字段；

4 删除Description REG_SZ，Description 

REG_EXPAND_SZ，ImagePath   

REG_EXPAND_SZ，ServiceDll 

REG_EXPAND_SZ无关字符

5 将/Processid(.*)替换为空；

6 将,-(.*)替换为空；

7 将@替换为空；

8 根据环境变量检查结果对%systemroot%，%windir%进行替换；

9 将^[a-z]*\.dll\n替换为空；

10 排序、统一小写和去重后进行服务检查。



示例1：

选择一条命令将服务配置文件导出。

reg query 

"HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services" /s > c:\yanlian\service_001.txt

reg query 

"HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services" /s > c:\yanlian\service_set.txt

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

服务配置文件路径说明：

1 ControlSet001：系统真实的服务配置信息；

2 ControlSet002：最后一次成功启动的服务配置信息；

3 CurrentControlSet：系统运行时的服务配置信息；

4 系统启动时，从ControlSet001复制到CurrentControlSet中；

5 系统运行时，修改的都是CurrentControlSet中的信息；

6 系统重启时，从CurrentControlSet复制到ControlSet001中；

7 系统正常启动时，从ControlSet001、CurrentControlSet复制到ControlSet002；

8 开机选择“最近一次正确配置”时，从ControlSet002复制到CurrentControlSet中。

服务配置说明：

1 ImagePath：服务所启动程序的路径；

2 Parametes\servicedll：程序调用的真实dll文件路径；

3 Start：0/boot，1/system，2/自动，3/手动，4/禁用；

4 DelayedAutostart：1/延迟启动；

5 Type：程序类型。



示例2：

输入以下正则表达式进行内容过滤。

(Description|ImagePath|ServiceDll\ )(.*)

点击【.*】启用正则匹配，输入正则表达式，点击【find all】后复制。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例3：

输入以下正则表达式进行内容过滤。

(.*)(\.dll|\.exe)(.*)

点击【find all】后复制。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例4：

复制以下单行内容并逐个替换。

Description   REG_SZ   

Description   REG_EXPAND_SZ   

ImagePath   REG_EXPAND_SZ   

ServiceDll   REG_EXPAND_SZ   

替换方法为在【find】框中粘贴，【replace】框中内容为空，点击【replace all】进行替换。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例5：

复制以下内容并替换。

/Processid(.*)

替换方法为在【find】框中粘贴，【replace】框中内容为空，点击【replace all】进行替换。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例6：

复制以下内容并替换。

,-(.*)

替换方法为在【find】框中粘贴，【replace】框中内容为空，点击【replace all】进行替换。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例7：

复制以下内容并替换。

@

替换方法为在【find】框中粘贴，【replace】框中内容为空，点击【replace all】进行替换。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例8：

复制以下内容并替换。

%systemroot% c:\windows

%windir% c:\windows

替换方法为在【find】框中粘贴，【replace】框中内容为空，点击【replace all】进行替换。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例9：

复制以下内容并替换。

^[a-z]*\.dll\n

替换方法为在【find】框中粘贴，【replace】框中内容为空，点击【replace all】进行替换。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例10：

对过滤后的程序路径进行排序。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

全选所有内容将所有大写字母转换为小写。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

对过滤后的程序路径进行去重。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

根据去重后的结果可直观的对服务所调用的程序进行检查。例如异常的程序路径，程序名称。同时工作量也会大大减少，因为原先需要分析9062行，现在只需要分析145行即可，如在145行中发现异常则可查看导出的服务配置文件进行深入分析。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



## 1.5 用户登录

用户登录可在系统启动登录、注销登录时自动运行相关程序，是恶意程序的第四个自启机制。

注意点：操作系统中有两处配置文件可用于在登录时启动相关程序。

使用命令将用户登录配置文件导出检查。

示例1：

复制以下命令：

reg query HKEY_CURRENT_USER\Environment 

/v UserInitMprLogonScript > 

c:\yanlian\userlogin.txt

reg query 

"HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Userinit >> c:\yanlian\userlogin.txt

在命令行中粘贴将用户登录配置文件导出检查。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

根据导出的登录配置进行检查。



## 1.6 svchost及dll劫持

svchost.exe主要作用是将动态链接库(后缀为.dll的文件)以服务的方式运行。svchost.exe对系统的正常运行非常重要，是不能被结束的。通过服务、dll、com均可劫持注入到程序中启动。



注意点：

1 在入侵检查方向，更倾向于将svchost.exe作为一个单独的持久化检查项目，而非系统服务，因为检查方法完全不同；

2 同时svchost.exe不作为常规检查项，一般根据【现象检查】的结果寻找恶意程序。



存在异常监听端口，pid指向svchost.exe程序，需检查是否存在异常。通过第三方工具ProcessExplorer（简称pe）检查svchost.exe程序。



示例：

通过检查已监听端口，发现“异常”监听，pid指向800。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

打开任务管理器，发现pid 800是svchost.exe程序，由于该程序的特性，需使用第三方工具pe进行检查。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

通过pe工具可发现pid 800是由服务启动，根据启动路径可发现该程序是由正常路径启动，但程序是否被替换未知，启动的服务是netsvcs（小白杀手，当初虐了我n久），该服务下挂12个子服务。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

在pe中双击该程序，点击【tcp/ip】，可发现49154端口是服务schedule监听。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

点击【services】，可发现schedule服务启动的动态链接库绝对路径是

c:\windows\systemc32\schedsvc.dll，从而发现49154端口是哪个程序正在监听。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

通过点击【view handles】可显示该程序所调用的clsid。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

如存在启动、点击某个程序后恶意进程重新启动的情况，则可以对com劫持进行检查。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

通过点击【view dlls】可显示该程序所调用的dll文件。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

通过排序可直观的看到是否存在dll劫持。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



## 1.7 帐号检查

操作系统的账户和密码主要用于本地或远程登录。属于黑客持久化后门的一种方式。



注意点：

1 恶意账号可能已隐藏，或通过克隆的方式复制管理员账户，需要通过注册表进行检查；

2 作为主管单位对下辖单位进行攻击溯源时，可通过mimikatz工具检查是否存在弱口令，所有口述证据均不可信。



检查账户是否存在异常：方法如下所示：

1 调整注册表权限将账户导出检查；

2 使用net命令将账户导出检查。



示例1：

在cmd命令行中输入regedit打开注册表。

在注册表【HKEY_LOCAL_MACHINE\SAM\SAM】处右键选择权限，点击【administrators】，勾选【读取】，并点击确定。![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

复制以下命令。

reg export 

HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users c:\yanlian\user.reg

在命令行中粘贴将账户配置文件导出检查。

根据导出的账户信息进行检查。

记录该路径下的账户名称16进制数。

HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users\Names

例如YunWei$是3ee。可在以下注册表中检查其F值和V值

HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users\000003EE

其中F为权限值，如将administrator的F值复制给guest，并启用免密码登录，则会形成克隆账户的隐藏现象，V为密码值。



示例2：

复制以下命令。

net user > c:\yanlian\user.txt

net localgroup administrators >> c:\yanlian\user.txt

在cmd命令行中粘贴将账户导出，注意>意为覆盖写入，>>意为追加写入。



根据导出的账户信息进行检查。



# 三 痕迹检查

## 1.1 日志

操作系统日志中记录着攻击成功前和攻击后的相关痕迹。

注意点：日志默认记录20Mb，达到最大值时优先覆盖旧事件，同时网络安全法要求日志至少保存6个月以上。

将安全日志和系统日志导出检查。

示例：

操作系统安全日志和系统日志默认存储路径：

%SystemRoot%\System32\Winevt\Logs\System.evtx

%SystemRoot%\System32\Winevt\Logs\Security.evtx

使用以下命令将日志导出。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

安全日志典型分析方法：大量事件id 4625后第一次4624，则为操作系统密码暴力破解成功。其中源ip可能为空，则需要软硬件资产管理系统支持，定位源工作站的名称，即主机名，特定情况下windows可通过ping 主机名携带-4参数返回ipv4地址。或通过不对互联网映射操作系统远程桌面管理端口以及使用堡垒机进行管理可规避操作系统密码暴力破解攻击。

系统日志典型分析方法：id为7036是服务启动/关闭的系统事件，在异常时间启动的服务需要重点检查，例如wmi服务。



## 1.2 文件落地

恶意程序保存在硬盘的文件系统中，例如后门程序，后门程序属于非授权的远程管理通道，黑客可通过该通道未授权管理被入侵的主机，以及随后门文件同时生成的其他恶意程序。



注意点：文件落地不单独进行手工检查，一般根据【现象检查】或【持久化检查】的结果在硬盘中寻找恶意程序，否则工作量会过大及质量较差，常规恶意程序检查建议通过操作系统杀毒软件进行。



例如在1999年01月01日发现异常程序对其相关处置后，检查当天是否生成其他异常内容。



1 在%temp%目录下创建并修改test.txt文件的创建时间为1999年01月01日00点00分00秒，修改时间为1999年01月02日00点00分00秒、访问时间为1999年01月03日00点00分00秒；

2 通过everything工具进行检查。



示例1： 

修改文件时间bat脚本内容如下所示：

ChangeDate.bat

@echo off

powershell.exe -command "ls '%temp%\test.txt' | foreach-object {$_.CreationTime = '01/01/1999 00:00:00'; $_.LastWriteTime = '01/02/1999 00:00:00'; $_.LastAccessTime = '01/03/1999 00:00:00'}"

pause

\#CreationTime 创建时间

\#LastWriteTime 修改时间

\#LastAccessTime 访问时间

修改后各项时间如下所示：

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例2： 

dc:后跟日期，检查创建时间为1999年01月01日，后缀为.txt的文件。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

dm:后跟日期，检查修改时间为1999年01月02日，名称包含te和.的文件。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

da:后跟时间，检查访问时间为1999年01月03日的文件。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

通过以上案例可知，在入侵取证时windows文件的所有时间完全不可信（linux只是两项时间不可信），因此文件时间仅作为参考依据。



## 1.3 无文件落地

wmi全称windows管理规范，其提供大量api接口供程序调用；同时也是恶意程序无文件落地的关键所在，黑客将恶意载荷存储于wmi中，并通过持久化机制在内存中通过powershell直接调用存储于wmi中的恶意载荷，从而实现无文件落地。



注意点：

1 可以将Windows Management Instrumentation服务关闭，并将其作为一个基线对象定期检查是否被恶意开启。因为wbemtest.exe或powershell均是通过该服务调用【wmiclass】【对象】【属性】中的【值】；

2 当服务关闭时，wbemtest.exe可打开wmi测试器，但无法连接命名空间，powershell可正常运行，但不会执行wmiclass中的载荷；

3 无文件落地不单独进行手工检查，一般根据【现象检查】或【持久化检查】的结果在wmiclass中寻找恶意载荷，否则工作量会过大及质量较差，常规恶意程序检查建议通过操作系统杀毒软件进行。



检查wmiclass是否存在异常：

1 搭建无文件落地场景；

2 对无文件落地场景进行检查。



示例1：

对以下字符串进行base64编码：

start powershell "ping 114.114.114.114 -t"

不能使用网页版base64编码工具进行编码，因为编码方式不同会导致powershell无法识别，上述命令通过网页版base64工具编码后再通过powershell识别，其结果如下所示：

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

因此可通过以下powershell工具将其base64编码。将命令复制到【$string】中，当存在【$】【"】符号时需要使用【`】进行转义，多行内容可直接回车。

ps_string_base64.ps1

$string = "start powershell `"ping 114.114.114.114 -t`""

$bytes = [System.Text.Encoding]::Unicode.GetBytes($string)

$encoded = [Convert]::ToBase64String($bytes)

echo $encoded

当前目录下打开cmd执行powershell -executionpolicy bypass -file ps_string_base64.ps1即可获得base64后的字符串。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

打开poershell输入以下命令（可以将以下内容base64编码化，从cc主控端拉取执行，执行完毕即从内存中释放，这可能也是在被入侵主机中没有发现此类样本的原因），以下命令通过powershell调用.net的方法将base64字符串写入wmiclass同时执行。



\#要连接的wmi命名空间及类对象

$SaveClass 

= [System.Management.ManagementClass]

('root\default:StdRegProv')



\#添加对象属性(名称，类型，非数组)

$SaveClass.Properties.Add('ping',[System.Management.CimType]::String,$False)



\#修改ping属性的值为base64字符串

$SaveClass.SetPropertyValue('ping','cwB0AGEAcgB0ACAAcABvAHcAZQByAHMAaABlAGwAbAAgACIAcABpAG4AZwAgADEAMQA0AC4AMQAxADQALgAxADEANAAuADEAMQA0ACAALQB0ACIA')



\#保存

$SaveClass.Put()



\#查询ping属性的值

$SaveClass.GetPropertyValue('ping')



\#取载荷执行后退出

set ping 

([WmiClass]'root\default:StdRegProv').Properties['ping'].Value;powershell -E $ping;exit

.net帮助文档参考链接如下所示：

https://docs.microsoft.com/zh-cn/dotnet/api/system.management.managementbaseobject.setpropertyvalue?view=dotnet-plat-ext-5.0#System_Management_ManagementBaseObject_SetPropertyValue_System_String_System_Object_



通过此类方法可发现存储于wmiclass中的载荷已被执行，但却不见其父进程，严重影响到溯源的逻辑性，即无法根据痕迹溯源该程序如何启动，只能通过经验判断可能的自我守护机制。



如持久化机制未被发现，则无法根除，因此在【现象检查】伊始即强调，在未确认是否存在自我守护机制前，不可先行删除异常程序。逆向思维考虑：如结束进程后频繁自启动，则一定存在自我守护机制。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

以下命令可在前台运行时将载荷带入后台运行，诸如任务计划运行的程序默认在后台运行：

start powershell -NoP -NonI -W Hidden "ping 114.114.114.114 -t"



示例2：

根据pe检查结果无法判断该进程的父进程，即无法确认该进程如何启动，仔细检查任务计划时发现异常：

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

在解码时不建议使用网页版base64解码工具，因为大部分网页工具默认会按多字节解码，导致每个字符后会再补一个字节内容，如下所示：

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

因此可通过以下powershell工具将其base64解码。将base64字符串复制到【$string】中，GetString()方法建议使用Unicode，如改成utf8，则会在每个字符后面补一个空格。



ps_base64_string.ps1

$string = 

"cwBlAHQAIABwAGkAbgBnACAAKABbAFcAbQBpAEMAbABhAHMAcwBdACcAcgBvAG8AdABcAGQAZQBmAGEAdQBsAHQAOgBzAHQAZAByAGUAZwBwAHIAbwB2ACcAKQAuAFAAcgBvAHAAZQByAHQAaQBlAHMAWwAnAHAAaQBuAGcAJwBdAC4AVgBhAGwAdQBlADsAcABvAHcAZQByAHMAaABlAGwAbAAgAC0ARQAgACQAcABpAG4AZwA7AGUAeABpAHQA"



$bytes = 

[System.Convert]: :FromBase64String($string);

$decoded = 

[System.Text.Encoding]: :Unicode.GetString($bytes);



echo $decoded

当前目录下打开cmd执行powershell -executionpolicy bypass -file ps_base64_string.ps1即可发现解码后的内容。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

检查wmi服务，发现其已被未授权启动（默认为自动启动，建议关闭后建立基线值作为日常自动化巡检的对象）。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

需判断root\default命名空间的stdregprov类对象的ping值是否存在异常。可打开powershell通过以下命令进行检查。

$SaveClass 

= [System.Management.ManagementClass]

('root\default:StdRegProv')

$SaveClass.GetPropertyValue('ping')'

检查结果如下所示：

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

也可以打开wbemtest.exe工具进行检查。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

点击【连接】。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

根据残留痕迹，输入【root\default】并点击【连接】。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

点击【枚举类】。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

勾选【递归】并【确定】。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

双击【stdregprov】类对象。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

在【属性】中找到ping属性，双击编辑属性

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

最终对其进行解码，发现其功能和异常现象一致。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



## 1.4 操作系统防火墙

windows操作防火墙配置着允许/拒绝通行的入站/出站规则。可根据防火墙配置判断该主机在同网段内的可攻击面积。



注意点：检查配置前应先检查防火墙是否已启用。



检查操作系统防火墙是否存在异常：

1 通过netsh命令将防火墙状态导出检查；

2 通过netsh命令将防火墙所有入站、出站规则导出检查；



示例1：

复制以下命令在cmd中执行。

netsh firewall show state > c:\yanlian\firewall-state.txt

netsh advfirewall firewall show rule name=all dir=in > c:\yanlian\firewall-in.txt

netsh advfirewall firewall show rule name=all dir=out > c:\yanlian\firewall-out.txt

【操作模式】为【禁用】意为防火墙已关闭，即已监听的端口同网段所有主机均可访问。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

【操作模式】的启用和禁用随网卡的网络位置变更而变更。



例如网卡的网络位置是【家庭网络】或【工作网络】，则匹配家庭或工作(专用)网络位置防火墙的状态。或网卡的网络位置是【公用网络】，则匹配公用网络位置防火墙的状态。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



示例2：

复制以下正则进行搜索和结果复制：

(已启用|本地端口|操作)(.*)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

复制以下正则进行结果替换。

操作(.*)

操作$1\n

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

即可对入站、出站规则进行检查分析，且工作量也会大大减少。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

windows终端入站规则应根据实际工作需要进行网络层访问控制，windows服务端出站规则应根据实际工作需要进行网络层访问控制。



## 1.5 操作系统杀毒软件

杀毒软件可用于防护恶意程序的存储、运行等恶意行为，作为主管单位对下辖单位进行攻击溯源时需进行检查。

注意点：

1 应检查杀毒软件的安装时间；

2 应检查杀毒软件的补丁更新情况；

3 应检查杀毒软件的特征库是否最新；

4 应检查杀毒软件最近的杀毒记录；

应检查杀毒软件的白名单。



