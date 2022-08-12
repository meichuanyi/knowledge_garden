> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/weixin_43887661/article/details/106645635)

你是否有这样的需求，在 **Windows10 下实现两个 / 多个文件夹的关联 / 同步更新**？

我相信你有，不是现在就是在不久的未来。实现文件夹下子文件夹和文件的同步更新，是个**隐形的诉求**。  
首先，说明一下，为什么强调是在在 Windows10 下，而不是在 WindowsXP、Windows7 等以及 Linux 系统下。

**公文包是什么？**  
首先要说一下，公文包。公文包可以将文件与其对应部分进行同步。在旧版 Windows 版本下，有 Windows 公文包，从 Windows 95 开始引入，到 Windows 8 都可以使用，只不过 Windows 8 里默认隐藏了这个功能。可以说是云同步的祖宗了吧。公文包是长这样的（截图不太清楚，请大家凑合看）：  
![](https://img-blog.csdnimg.cn/20200609165030744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg4NzY2MQ==,size_16,color_FFFFFF,t_70)  
这是 WindowsXP 系统下的公文包的样子  
![](https://img-blog.csdnimg.cn/20200609165131805.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg4NzY2MQ==,size_16,color_FFFFFF,t_70#pic_center)  
后来，随着网络的普及，Windows10“擅自”删除了 “公文包” 功能，改用了需要联网操作同步的 Dropbox、Onedrive 等。微软，自以为网络无处不在了。至此，公文包退出历史舞台。Dropbox、Onedrive 等有缺点，一是需要联网；二是安全问题，你同步到 Dropbox、Onedrive 等上的数据，我们都在监控之下了，微软说你的数据违规，禁止操作、访问，你也没有办法，可能你连备份的机会都没有。公文包，在现实生活还是有需求的，本地计算机可以无网状态进行同步操作。通过查阅资料发现，在 Windows10 系统下，需要使用 **mklink** 命令实现两个文件夹的关联。

mklink 是 windows 系统下创建符号链接和硬链接的命令工具，它是一个很好的解决文件系统问题的工具。使用它需要**管理员权限**。因为 powershell 不支持 mklink 命令，所以要在前面加 [cmd](https://so.csdn.net/so/search?q=cmd&spm=1001.2101.3001.7020) /c 表示用 cmd 来运行该命令，路径**注意引号**。

Linux 系统是自由开放开源的系统，给你系统用户 “无限” 的权限，想怎么搞就怎么搞。不像 Windows 系统，稍微往系统内部操作以下，就说权限不够，要去好几个地方去修改权限。  
执行如下 Windows 命令：

```
C:\Users\bai>mklink /D D:\Foo D:\OneDrive\Foo
你没有足够的权限执行此操作。

```

默认我们的用户账户是 Administrators 组的，会继承它的权限设定。正常情况下，我们使用 mklink 是可以成功执行的。但如果文件系统的设置比较奇怪或者重装过系统，那么可能出现没有权限的错误。  
这时，使用管理员权限启动 cmd 是最简单的做法。不过也可以考虑在 本地安全策略（secpol.msc）\ 本地策略 \ 用户权利分配 中添加当前用户。即：

1.  Win+R—> 输入：secpol.msc，点击 “确定”；
2.  如图所示![](https://img-blog.csdnimg.cn/20200609171243780.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg4NzY2MQ==,size_16,color_FFFFFF,t_70#pic_center)
3.  如图所示。在 2 处填写你的用户名。![](https://img-blog.csdnimg.cn/20200609171309261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg4NzY2MQ==,size_16,color_FFFFFF,t_70#pic_center)

通过 **mklink** 命令可以创建文件或文件夹的链接，而这种链接跟快捷方式是不一样的。**mklink** 可以像创建快捷方式一样建立文件或文件夹的链接，但不同于快捷方式的是，**mklink** 创建的链接绝大多数程序都不会认为那是一个链接，而是一个实实在在的文件或文件夹。**mklink** 可以创建符号链接、硬链接和目录链接。

在 cmd 中输入 **mklink** 即可看到以下这样的帮助信息。  
![](https://img-blog.csdnimg.cn/20200610084648456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg4NzY2MQ==,size_16,color_FFFFFF,t_70#pic_center)

说一下它们之间的大体区别：

<table><thead><tr><th align="center">使用方式</th><th align="center">适用于</th><th align="center">快捷方式小箭头</th></tr></thead><tbody><tr><td align="center">不带参数</td><td align="center">文件</td><td align="center">有</td></tr><tr><td align="center">/D</td><td align="center">文件夹</td><td align="center">有</td></tr><tr><td align="center">/J</td><td align="center">文件夹</td><td align="center">有</td></tr><tr><td align="center">/H</td><td align="center">文件</td><td align="center">无</td></tr></tbody></table>

表格从上到下的行为，从像快捷方式到像两个独立的文件夹。  
注：快捷方式小箭头是,"**<<==>>**"

例如，将 E 盘的 bai 文件夹映射到 D 盘并重命名为 b1

```
mklink /d /j D:\b1 E:\bai

```

这样即为完成了，两个文件夹的关联 / 映射关系，更改任意文件夹内容、以及其文件内容均会进行同步。