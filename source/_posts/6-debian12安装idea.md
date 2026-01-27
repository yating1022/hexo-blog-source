---
title: 6.debian12安装idea
date: 2026-01-27 21:57:02
tags:
---
linux基础使用：安装软件
<!-- more -->
使用的系统为debian12，本文演示安装IDEA
## 一、下载
![Pasted image 20260127222132.png](https://image.bjyt.de/storage/uploads/202601/27/Pasted%20image%2020260127222132.png)
在linux下默认也会直接选择linux的版本，下载下来后是一个压缩包
## 二、解压运行
```bash
root@fruits:~/sofe/idea-IU-253.30387.90/bin# pwd
/root/sofe/idea-IU-253.30387.90/bin
root@fruits:~/sofe/idea-IU-253.30387.90/bin# 
```
解压之后进入bin目录
在此处直接运行脚本也能够运行
```bash
root@fruits:~/sofe/idea-IU-253.30387.90/bin# ./idea.sh

```
但是总不能每次运行都要去运行这个脚本
## 三、创建桌面快捷方式运行
#### 步骤 1：创建并编辑 desktop 文件

首先，使用文本编辑器创建 IDEA 的桌面配置文件，执行以下命令：
```bash
	nano /usr/share/applications/idea.desktop
```
#### 步骤 2：粘贴以下配置内容

将下面的内容复制到编辑器中，**注意修改`Exec`和`Icon`的路径为你自己的 IDEA 解压路径**：
```ini
[Desktop Entry] 
Type=Application
Name=IntelliJ IDEA (Native Launcher)
Exec=/root/sofe/idea-IU-253.30387.90/bin/idea
# 这里替换为你实际的idea.png路径 
Icon=/root/sofe/idea-IU-253.30387.90/bin/idea.png
Terminal=false
Categories=Development;IDE;Java;
StartupWMClass=jetbrains-idea
Keywords=IntelliJ;IDEA;Java;IDE;
```
#### 步骤 3：保存并退出

在 nano 编辑器中，按`Ctrl+O`保存，按`Enter`确认文件名，再按`Ctrl+X`退出。

#### 步骤 4：赋予执行权限

```bash
chmod +x /usr/share/applications/idea.desktop
```

以上是对于一些支持比较不全的软件，但是比如idea比较友好
可以直接通过idea内的工具直接创建


1. 先从终端启动

```bash
root@fruits:~/sofe/idea-IU-253.30387.90/bin# ./idea
```
2. 启动完毕后在idea内生成桌面图标

![Pasted image 20260127224733.png](https://image.bjyt.de/storage/uploads/202601/27/Pasted%20image%2020260127224733.png)
点击创建桌面条目即可

四、效果图
![Pasted image 20260127223157.png](https://image.bjyt.de/storage/uploads/202601/27/Pasted%20image%2020260127223157.png)
