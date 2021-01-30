# 一 初始化配置：

生成密钥：
ssh-keygen -t rsa -C "stelen97@163.com"
cat c:\Users\Foe0\.ssh\id_rsa.pub

验证ssh设置
ssh -T git@github.com

配置gitbash的用户名和邮箱：
λ git config --global user.name "name"
λ git config --global user.email "email"

使用git init初始化文件夹
git remote add origin git地址
git pull 你的git地址

如果这里没有报错，恭喜你，没有啥问题了，该pullpush去。如果有问题，呵，请继续看啊啊啊啊啊啊啊啊~

------

参考链接：https://www.cnblogs.com/lyh233/p/12748737.html

![涉及到分支问题，也许是创建项目时弄的吗，其实我不太明白](\image\image-20210130180033294.png)

# 二 切换分支

有创建分支的意思：

git checkout -b local origin/local

![image-20210130180634457](./image\image-20210130180634457.png)

查看当前分支：

git checkout -b

## pull操作
1.将远程指定分支 拉取到 本地指定分支上：

git pull origin <远程分支名>:<本地分支名>

2.将远程指定分支 拉取到 本地当前分支上：

git pull origin <远程分支名>

3.将与本地当前分支同名的远程分支 拉取到 本地当前分支上(需先关联远程分支，方法见文章末尾)
git pull
在克隆远程项目的时候，本地分支会自动与远程仓库建立追踪关系，可以使用默认的origin来替代远程仓库名，
所以，我常用的命令就是 git pull origin <远程分支名>，操作简单，安全可控。

## push操作

1.将本地当前分支 推送到 远程指定分支上（**注意：pull是远程在前本地在后，push相反）：**
git push origin <本地分支名>:<远程分支名>

2.将本地当前分支 推送到 与本地当前分支同名的远程分支上（注意：pull是远程在前本地在后，push相反）：
git push origin <本地分支名>

3.将本地当前分支 推送到 与本地当前分支同名的远程分支上(需先关联远程分支，方法见文章末尾)
git push
同样的，推荐使用第2种方式，git push origin <远程同名分支名>

 

附：
// 将本地分支与远程同名分支相关联

git push --set-upstream origin <本地分支名>


# 操作方法：

第一步：git init 初始化项目文件夹

第二步：git add . 键所有文件添加到暂存区

第三步：git commit -m "first commit"  提交到本地仓库

第四步：git remote add origin XXX（XXX就是你github或者码云等远程仓库的地址，git branch这个命令可以看到你所在的分支，删除某个仓库地址使用git remote rm origin）

第五步：git pull 拉取远程分支信息，首次拉取合并信息

第六步：git push -u -f origin master 提交到远程仓库，这个命令中的 -f 是强制推送，因为远程仓库只有初始化的文件，所以强制推送上去就行了，不加-f 会报当前分支没有远程分支，强制推送可以覆盖master，这样就完成了第一次提交的步骤)

  

平时：git add filename 

git commit -m ’‘

git push origin master
