---
title: 2.ubuntu设置root用户登录
date: 2025-11-09 17:54:50
tags: 
---

ubuntu默认不支持root账号登录，通过设置让root账号登录

<!-- more -->

## 一、启用 root 密码登录（简单直接）

### 步骤 1：设置 root 密码

首先给 `root` 用户设置密码（默认 `root` 无密码，无法登录）：

```bash
# 普通用户执行，输入当前普通用户密码验证
sudo passwd root
```

执行后按提示输入两次 `root` 密码（密码不可见，输入正确即可），显示 `passwd: password updated successfully` 说明设置成功。

### 步骤 2：允许 root 密码登录（修改 SSH 配置）

Ubuntu 默认 SSH 禁止 `root` 密码登录，需修改配置文件：

```bash
# 用 nano 编辑 SSH 配置（若没安装，先执行 sudo apt install nano -y）
sudo nano /etc/ssh/sshd_config
```

### 步骤 3：修改配置项（关键）

在文件中找到以下配置，按如下修改：

找到 `PermitRootLogin`：

- 若显示 `PermitRootLogin prohibit-password`（默认值，禁止密码登录），改为 `PermitRootLogin yes`；
- 若显示 `PermitRootLogin no`，直接改为 `PermitRootLogin yes`；
- 若文件中没有该配置，直接在文件末尾添加 `PermitRootLogin yes`。

确保 `PasswordAuthentication` 为 `yes`（允许密码登录，默认开启，若被注释则取消注释）：

1. ```bash
   PasswordAuthentication yes
   ```

### 步骤 4：保存配置并重启 SSH 服务

- 按 `Ctrl+O` → 回车保存 → `Ctrl+X` 退出 nano；
- 重启 SSH 服务使配置生效：

- ```bash
  sudo systemctl restart sshd
  ```

### 步骤 5：测试 root 密码登录

本地或远程执行以下命令，输入 `root` 密码即可登录：

```bash
# 本地登录
su root  # 输入 root 密码
# 远程 SSH 登录（若服务器端口是 12310，加 -p 12310）
ssh root@服务器IP -p 12310
```

## 二、启用 root 密钥登录（更安全，推荐）

如果想让 `root` 用密钥登录（无需密码，比密码登录安全），按以下步骤操作：

### 步骤 1：先完成普通用户的密钥配置（已配置可跳过）

```bash
ssh-keygen -t rsa -b 4096  # 按 3 次回车，生成 ~/.ssh/id_rsa 和 id_rsa.pub
```

### 步骤 2：将本地公钥上传到 root 的授权列表

#### 方式 1：通过普通用户中转（推荐，无需 root 密码）

1. 先通过普通用户 

   ```
   fruits
   ```

    登录服务器（已配置密钥可免密）：

```bash
ssh fruits@服务器IP
```

在服务器上，将 `fruits` 的公钥复制到 `root` 的 `authorized_keys`：

1. ```bash
   # 切换到 root（输入普通用户密码提权）
   sudo -i
   # 创建 root 的 .ssh 目录（若不存在）
   mkdir -p /root/.ssh && chmod 700 /root/.ssh
   # 复制 fruits 的公钥到 root 的授权文件（若 fruits 已上传公钥）
   cat /home/fruits/.ssh/authorized_keys >> /root/.ssh/authorized_keys
   # 若本地公钥未上传到 fruits，先在本地执行（上传到 root）：
   # ssh-copy-id -i ~/.ssh/id_rsa.pub root@服务器IP -p 12310
   ```

#### 方式 2：本地直接上传（需 root 密码）

```bash
# 本地执行，将公钥上传到 root 的授权列表（输入 root 密码）
ssh-copy-id -i ~/.ssh/id_rsa.pub root@服务器IP -p 12310
```

### 步骤 3：修复 root 密钥目录的权限（关键）

```bash
# 切换到 root
sudo -i
# 设置正确权限（否则 SSH 会拒绝密钥登录）
chmod 600 /root/.ssh/authorized_keys
chmod 700 /root/.ssh
```

### 步骤 4：修改 SSH 配置（允许 root 密钥登录）

```bash
sudo nano /etc/ssh/sshd_config
```

确保以下配置（无需 `PermitRootLogin yes`，密钥登录默认支持）：

```bash
PermitRootLogin prohibit-password  # 允许密钥登录，禁止密码登录（更安全）
PubkeyAuthentication yes  # 开启公钥认证（默认开启）
```

保存后重启 SSH 服务：



```bash
sudo systemctl restart sshd
```

### 步骤 5：测试 root 密钥登录

本地执行以下命令，无需密码即可登录 `root`：

```bash
ssh root@服务器IP
```

## 三、禁用 root 登录（如需恢复默认安全配置）

若后续想禁用 `root` 登录，只需修改 SSH 配置：

```bash
sudo nano /etc/ssh/sshd_config
# 改为禁止 root 登录
PermitRootLogin no
# 重启 SSH 服务
sudo systemctl restart sshd
```

## 常见问题排查

1. **root 登录提示 Permission denied**：
   - 检查 `sshd_config` 中 `PermitRootLogin` 是否为 `yes`（密码登录）或 `prohibit-password`（密钥登录）；
   - 密钥登录需确保 `/root/.ssh` 权限 700，`authorized_keys` 权限 600。
2. **SSH 重启失败**：
   - 执行 `sudo systemctl status sshd` 查看错误日志，大概率是 `sshd_config` 语法错误（比如缩进、拼写错误）。
3. **su root 提示 Authentication failure**：
   - 未设置 `root` 密码，执行 `sudo passwd root` 重新设置。
