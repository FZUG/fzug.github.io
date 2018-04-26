---
layout: post
title: Fedora 忘记 root 密码怎么办？
date: 2018-04-26 13:16:14 +0800
---

> 本文翻译自 Curt Warfield 的文章 [How to reset a root password on Fedora](https://fedoramagazine.org/reset-root-password-fedora/)。

系统管理员可以轻松重置忘记密码的用户密码。但是，如果系统管理员忘记了 root 密码该如何处理呢？本指南教你如何重置 root 密码。请注意，要重置 root 密码，需要能直接物理方式访问到计算机，以便重启并访问 GRUB 设置。此外，如果系统有加密，你还需要知道 LUKS 密码。

## 编辑 GRUB 设置

首先是要中断启动过程。因此，如果已通电，则需要开机或重启。第一步需要「眼疾手快」，因为 GRUB 菜单在屏幕上会很快闪现。

看到 GRUB 菜单时，按 `e` 键：

![grub](/assets/2018/04/26/grub.png)

按 `e` 之后会显示类似这样：

![grub2](/assets/2018/04/26/grub2.png)

用方向键将光标移动到 `linux16` 的那一行：

![grub3](/assets/2018/04/26/grub3.png)

> 译者注：使用 UEFI 方式启动的用户需要找到 `linuxefi` 这一行，类似下图：
>
> ![grub3-1](/assets/2018/04/26/grub3-1.png)

按 `Del` 或 `Backspace` 键删掉 `rhgb quiet` 并改写成以下内容：

```
rd.break enforcing=0
```

![grub4](/assets/2018/04/26/grub4.png)

> 译者注：使用 UEFI 的用户改完后类似下图：
>
> ![grub4-1](/assets/2018/04/26/grub4-1.png)

编辑完成后，按 `Ctrl+x` 快捷键开始启动系统。如果系统做了加密，会有提示输入 `LUKS` 密码。

**注：**之所以设置 `enforcing=0` 是为了避免 SELinux 造成干扰。等到系统重启后，需要恢复 `/etc/shadow` 文件的 SELinux 上下文 (context) 标签（下文中会解释）。

## 挂载文件系统

*系统现在会进入紧急模式 (emergency mode) 。*现在需要重新挂载硬盘允许读写：

```
# mount -o remount,rw /sysroot
```

## 修改密码

执行 `chroot` 访问系统：

```
# chroot /sysroot
```

然后修改密码：

```
# passwd
```

按照提示输入两遍新密码。成功之后你可以看到 `all authentication tokens updated successfully` 的提示。

输入 `exit` 退出，需要退出两遍。重启系统。

使用 root 身份登录并恢复 `/etc/shadow` 文件的 SELinux 标签：

```
# restorecon -v /etc/shadow
```

重新将 SELinux 设置成启用模式 (enforcing mode)：

```
# setenforce 1
```
