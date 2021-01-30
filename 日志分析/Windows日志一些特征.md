eventvwr.msc打开事件查看器

# 1.创建服务 event_id = 7045 

```cmd
sc create monitor binpath = cmd displayname = name start = auto


sc create services binpath= "C:\Users\Test\Desktop\PSTools\PsExec.exe" start= demand displayname= "PsExec"

sc create services binpath= "‪C:\Users\Test\Desktop\PSTools\PsExec.exe" type= own start= demand displayname= "PsExec" depend= DHCP Client
```

![image-20201231155023973](E:\2.图片\images\实验\image-20201231155023973.png)

注意：等号后一定要有空格！！！

创建服务的日志如下：

![image-20201231155131589](E:\2.图片\images\实验\image-20201231155131589.png)

![image-20201231155235189](E:\2.图片\images\实验\image-20201231155235189.png)



2.event_id = 4698