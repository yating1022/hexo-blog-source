---
title: 7.配置vscode远程开发
date: 2026-02-05 08:34:02
tags:
---
通过连接远程的ubuntu系统，来简化开发，不需要每到一处就配置环境，转移代码
<!-- more -->
## 1.配置网络环境
我的ubuntu没有开启公网，通过异地组网的方式进行连接是最快最安全的
前往 https://github.com/EasyTier/easytier-manager/releases
下载windows客户端，安装完毕配置组网信息
组网信息微信找我要

我的ubuntu在组网中的ip为  10.126.126.3
使用终端测试连通性，能ping通就表示通了
![Pasted image 20260205084100.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205084100.png)


## 2.连接远程桌面
我已经为你创建了相对应的用户mdlldz，密码是123456
当组网通了之后，就可以通过windows自带的远程桌面进行连接
打开windows自带的远程桌面，输入ip
输入账号密码
进入远程桌面之后，不要修改桌面类型，再次输入账号和密码，即可显示ubuntu桌面
![Pasted image 20260205084329.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205084329.png)


![Pasted image 20260205084524.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205084524.png)

此时就能像操作本机电脑一样操作这台ubuntu，这个桌面用的相对较少，应该会很少打开，远程开发只需要通过ssh连接即可
## 3.配置ssh连接
### 1.密码连接
默认直接就可以通过终端连接
如果在win10和win11直接使用cmd就能连接，也可以直接使用gitbash进行连接
输入命令
```bash
ssh mdlldz@10.126.126.3
```
![Pasted image 20260205084724.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205084724.png)
在这个界面时输入密码
![Pasted image 20260205084755.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205084755.png)
这种方式就是通过密码连接
但是后续为了配置vscode的远程开发，密码连接会出现各种问题
于是我们需要配置通过ssh进行秘钥连接
### 2.秘钥连接
参考之前的博客生成秘钥，也可以直接复用秘钥，我的建议是复用
#### 1.服务端
输入命令，进入ssh的配置文件夹
```bash
cd ~/.ssh
```
创建一个文件
```bash
nano authorized_keys
```
把公钥输入进去
公钥公钥，就是公共的，放在我们要连接的那一端，比如配置github的ssh，也是把公钥配置在github上，公钥私钥怎么分辨具体进行百度即可
然后 curl + o -> 写入 ，回车-> 确认写入，curl + x -> 退出
#### 2.windows端

在自己的windows上配置私钥，你如果复用的是之前的公私钥，然后之前本机电脑也配置了，就直接跳到下一步
此处我说明一下没复用的情况
打开 C盘，用户，自己的用户名，.ssh，创建一个文件，比如id_ed25519（可以自定义），不要叫id_rsa，否则会把你原来github的私钥替换掉，你github就连不上了，这也就是为什么要你复用公私钥的原因
打开创建的私钥文件，将私钥输入进去保存
在同个文件夹下创建一个名为config的文件（不要有后缀名）
#### 3.配置连接
打开输入如下配置配置
```yaml
Host ubuntu
    HostName 10.126.126.3
    User fruits
    IdentityFile ~/.ssh/id_ed25519
    PreferredAuthentications publickey
    ServerAliveInterval 60
    Compression yes
```
参数说明
- Host:备注，写一个自己能识别的就行，英文最佳
- HostName:服务器的ip，此处为组网的ip，固定10.126.126.3
- User:你的用户名，我是因为我用户名是fruits，你自己的话要写成mdlldz
- IdentityFile:秘钥文件路径，~/.ssh 表示当前用户的.ssh文件夹，id_ed25519替换成你上面输入的那个秘钥名称
其余跟我一样即可
#### 4.连接测试
打开gitbash或者cmd，输入命令
```bash
ssh ubuntu
```
此处的ubuntu替换为你上面配置的Host，也就是备注
![Pasted image 20260205090517.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205090517.png)
![Pasted image 20260205090528.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205090528.png)
出现这样子的界面就是连接成功了
## 4.vscode远程开发
接下来就是本文的最终目标，利用vscode进行远程开发
安装vscode，这个自行去下载安装
![Pasted image 20260205090740.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205090740.png)
点击拓展

![Pasted image 20260205090802.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205090802.png)
搜索安装这个拓展
安装完毕后，通过 curl + shift + P调出命令
输入remote就可跳出对应菜单
![Pasted image 20260205090917.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205090917.png)
选择connect to host
如果配置没有问题就有对应的远程主机了
![Pasted image 20260205090946.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205090946.png)
连接上之后，你就可以把当前的vscode当做运行在linux主机上的了，打开文件夹，打开文件，都是远程主机
![Pasted image 20260205091055.png](https://image.bjyt.de/storage/uploads/202602/05/Pasted%20image%2020260205091055.png)
终端也是远程主机的终端
像这里就是我写的一个go的demo项目，我即使在公司，只要我连接上远程主机，我就不需要重新配置环境，或者迁移代码
linux环境的配置非常简单，像mysql能够直接通过docker容器进行安装
