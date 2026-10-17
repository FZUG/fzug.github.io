---
layout: post
title: 安装系统时使用手动分区
author: 暮光的白杨
date: 2026-10-17 12:00:00 +0800
---

## 说明

- 本文描述：在安装系统时使用手动分区；
- 本文只使用默认的 btrfs 文件系统；
- 本文不描述：完整的安装流程及其他。

## 分区表

一般地，要获得一个可正常工作的 Fedora Linux，至少需要两个磁盘分区：[ESP] 和根分区。

[ESP]: https://wiki.archlinux.org/title/EFI_system_partition

Fedora Linux 单系统的 ESP 分区大小设定为 100MiB 即可（Fedora 实际使用了大约 20MiB，见下文实例）。

```bash
poplar@Greysia:~$ sudo du -h /boot/efi
2.0M    /boot/efi/EFI/BOOT
19M     /boot/efi/EFI/fedora
21M     /boot/efi/EFI
4.0K    /boot/efi/System/Library/CoreServices
6.0K    /boot/efi/System/Library
8.0K    /boot/efi/System
21M     /boot/efi
```

当涉及 Windows-Linux 双系统时，如果不确定安装顺序，则建议 ESP 大小为 256MiB 或更大。

**特别注意：不要让 Windows 和 Linux 共用同一个 ESP 分区。**

根据文档[^ref1]，根分区大小建议为 15GiB 及更大。

[^ref1]: https://docs.fedoraproject.org/en-US/fedora/latest/release-notes/hardware_overview/

## 使用存储编辑器设置

在启动 Fedora Installer，进入第二步后，点击右上角的按钮，进入存储编辑器。

如果此时选择的硬盘是完全空白的，则需要先创建分区表。如果系统使用的是 [UEFI]，则默认用 [GPT] 分区表，如果系统使用的是 [BIOS] 则使用 [MBR] 或者 GPT 分区表。

[UEFI]: https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface
[GPT]: https://wiki.archlinux.org/title/Partitioning#GUID_Partition_Table
[MBR]: https://wiki.archlinux.org/title/Partitioning#Master_Boot_Record
[BIOS]: https://wiki.archlinux.org/title/GRUB#GUID_Partition_Table_(GPT)_specific_instructions
[biosboot]: https://www.gnu.org/software/grub/manual/grub/html_node/BIOS-installation.html#BIOS-installation

![](/assets/2026/10/ref1.png)

这主要对后续创建 ESP 时有影响，BIOS 设备需要使用一种叫 [biosboot]，大小至少为 1MiB 的空白分区（不需要创建文件系统，保留未格式化状态即可），建议磁盘上第一个分区创建为 `biosboot`。具体详见[文档][biosboot]。

然后，在空闲区域创建新的分区，主要有两个：

- ESP 分区：挂载点为 `/boot/efi`，文件系统格式为 `EFI system partition`，大小为 100MiB。
- 根分区：挂载点为 `/`，文件系统格式为 `btrfs`，大小至少为 15GiB。

例如：

![](/assets/2026/10/ref2.png)

然后在新建的 btrfs 分区中，新建以下三个子卷：

- ROOT：系统安装位置；
- HOME：用于存储用户文件的子卷；
    - 如果只是临时使用，不需要独立的 `/home` 子卷；
- boot：用于将 `/dev/nvme0n1p1` 挂载至 `/boot`。

例如：

![](/assets/2026/10/ref3.png)

这个用于安装系统的子卷的挂载点可以自定义，我的习惯是把它放在 `/sys_fs`，它是 Fedora Installer 默认要求但未明确声明的子卷。系统安装完成后并不会存在一个叫 `sys_fs` 的文件夹：

```bash
poplar@Greysia:~$ ls /
afs  boot  dev  home  lib64  mnt  proc  run   srv  tmp  var
bin  bt    etc  lib   media  opt  root  sbin  sys  usr
```

此外，Fedora 默认使用 [ZRAM]，所以不需要额外创建一个单独的 [swap] 分区。当然，如果你需要，可以参考 [RHEL 文档] 给出的推荐配置。

[ZRAM]: https://wiki.archlinux.org/title/Zram
[swap]: https://wiki.archlinux.org/title/Swap
[RHEL 文档]: https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/managing_storage_devices/getting-started-with-swap_managing-storage-devices#recommended-system-swap-space_getting-started-with-swap

## 重新分配挂载点

完成分区后，最好检查一下是否存在问题，installer 自身似乎并不能正确地识别分区结构是否正确：

![](/assets/2026/10/ref4.png)

进入下一步，此时安装方案需要选择 **挂载点配置**，以便手动重新指定正确的挂载点和子卷。

例如：

![](/assets/2026/10/ref5.png)

如上文，用于容纳系统文件的 `/` 需要一个挂载点，但 installer 并没有在配置分区时提及这一点。

值得一提的是，如果有独立的 `/home` 分区，重装系统时，如果不选择 **重新格式化**，这样 installer 就会保存 `/home` 子卷的数据。

需要注意的是，重装系统时，如果使用了相同的用户名，建议：

1. 使用 live 环境将 `/home/$USER` 重命名为其他名称；
2. 重命名完成后，需要卸载已挂载的分区，防止安装系统失败。

另外，重装系统时，可以选择直接挂载已有的子卷，然后选择性格式化子卷即可。

如上，其他内容请参考万能的 archlinux wiki 和各处零散的 fedora 文档。