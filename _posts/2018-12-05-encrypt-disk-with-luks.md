---
layout: post
title: "保护数据，用 LUKS 给磁盘全盘加密"
categories: tutorial
date: 2018-12-05 00:05:27
author: Nova Kwok
---


> LUKS is the standard for Linux hard disk encryption. By providing a standard on-disk-format, it does not only facilitate compatibility among distributions, but also provides secure management of multiple user passwords. In contrast to existing solution, LUKS stores all setup necessary setup information in the partition header, enabling the user to transport or migrate his data seamlessly.

便于理解起见，整个流程为：
1. 准备硬盘（插上电脑）
2. 直接把整块硬盘创建为一个加密的块设备 
3. 解密创建好的块设备并且 map 到系统上（此时把 map 出来的设备作为一个物理设备看待）
4. 给 map 出来的设备创建文件系统 
5. 挂载那个文件系统到系统某一个目录

## 准备设备并创建加密卷

插上硬盘后读取为 `/dev/sda`（电脑上硬盘是 NVMe 的），首先创建加密块设备：

```bash
# cryptsetup luksFormat /dev/sda
WARNING!
========
This will overwrite data on /dev/sda irrevocably.

Are you sure? (Type uppercase yes): YES
Enter passphrase for /dev/sda: 
Verify passphrase: 
```

验证设备情况：

```bash
# cryptsetup luksDump /dev/sda 

LUKS header information for /dev/sda

Version:        1
Cipher name:    aes
Cipher mode:    xts-plain64
Hash spec:      sha256
Payload offset: 4096
MK bits:        256
MK digest:      76 e3 6a ec 43 87 27 77 25 dc 84 3c 06 7f 81 7e 29 a7 f0 85
MK salt:        92 e8 a4 1d aa ab 7b 85 21 bb 5f 55 0a 27 ed 03
                ec 7d 47 8f 78 67 7a c2 77 25 61 20 22 07 d5 36
MK iterations:  183317
UUID:           601a22a0-0888-4917-9047-4c8f15364f8b

Key Slot 0: ENABLED
        Iterations:             2876750
        Salt:                   d0 2a 56 0d 30 5d 24 d3 29 2a ec 19 fc 63 90 f8 
                                dd c1 6e 02 38 c6 e1 85 5f 20 b6 b7 93 ab f1 08 
        Key material offset:    8
        AF stripes:             4000
Key Slot 1: DISABLED
Key Slot 2: DISABLED
Key Slot 3: DISABLED
Key Slot 4: DISABLED
Key Slot 5: DISABLED
Key Slot 6: DISABLED
Key Slot 7: DISABLED
```

注意到底下有 Slot 的概念，对于加密卷的设备并不只有一种解密的方式，比如一个 Slot 可以留给 Yubikey 用，然后就有了 [YubiKey Full Disk Encryption](https://github.com/agherzan/yubikey-full-disk-encryption) 的操作.

## 修改加密卷密码

LUKS 加密设备没法直接修改密码，而是先创建一个新的密码，然后再删除掉旧的密码，新建一个密码的方式如下：

```bash
# cryptsetup luksAddKey /dev/sda 
Enter any existing passphrase:  # 此时输入任意一个密码
Enter new passphrase for key slot: 
Verify passphrase: 
```

再次执行 `cryptsetup luksDump /dev/sda` 可以看到：
```
Key Slot 0: ENABLED
        Iterations:             2876750
        Salt:                   d0 2a 56 0d 30 5d 24 d3 29 2a ec 19 fc 63 90 f8 
                                dd c1 6e 02 38 c6 e1 85 5f 20 b6 b7 93 ab f1 08 
        Key material offset:    8
        AF stripes:             4000
Key Slot 1: ENABLED
        Iterations:             2830164
        Salt:                   c2 ce e5 b0 cb 85 b4 dc c0 06 89 53 81 27 35 e5 
                                ca 28 12 41 4b 16 93 57 ce 9c d3 5b 84 a0 69 4a 
        Key material offset:    264
        AF stripes:             4000
```

这个时候两个密码都可以用来解开这个加密设备，此时我们删除掉第一次创建的密码，现在通过以上的 Key Slot 我们并不能判断出哪一个 Slot，所以我们可以先尝试用某一个密码解开一下，然后让 LUKS 告诉我们用的是哪一个 Slot，指令如下：

```bash
# cryptsetup -v open /dev/sda dot
Enter passphrase for /dev/sda: 
Key slot 0 unlocked.
Command successful.
```

好的，我知道旧密码是 Slot 0，抹了它！

> 注意，如果手滑的话是可以直接把一個卷的所有 Slot 给抹完导致完全没有办法访问到那个设备的，所以，谨慎操作！

```bash
# cryptsetup luksRemoveKey /dev/sda 
Enter passphrase to be deleted: 
```

这里直接输入你需要抹除的 Slot 对应的密码，如果有两个 Slot 密码一样的话，会抹除第一个，如果需要抹除两个的话，执行这个指令两遍就好了.

## 初始化加密卷

此时我们解密这个块设备并且 map 到 `/dev/mapper/dot` 上，为后续创建文件系统和挂载做准备：
```bash
# cryptsetup open /dev/sda dot
Enter passphrase for /dev/sda: 
```

这个时候，我们的加密设备已经解开并且 map 出来了，我们给 map 出来的块设备创建文件系统.

此时 `fdisk -l` 可以看到出现了两个设备，这个时候请自动忽略掉 `/dev/sda`，并且假设 `/dev/mapper/dot` 才是我们真正的设备（硬盘）.

```
Disk /dev/sda: xxx GiB, xxx bytes, xxx sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/dot: xxx GiB, xxx bytes, xxx sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

```

给块设备格式化为 `ext4` 文件系统：

```bash
# mkfs -t ext4 /dev/mapper/dot 
mke2fs 1.44.2 (14-May-2018)
Creating filesystem with xxx 4k blocks and xxx inodes
Filesystem UUID: c4b26ca1-d017-4996-937b-9fa3c61d3050
...
Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (xxx blocks): done
Writing superblocks and filesystem accounting information: done   
```

## 挂载加密卷

挂载设备并检查挂载情况：
```bash
# mount /dev/mapper/dot /media/dot/
# df -h
Filesystem       Size  Used Avail Use% Mounted on
...省略无关内容...
/dev/mapper/dot   xxxG   45M   xxxG   1% /media/dot
```

## 关闭加密卷

关闭整个设备分为以下两步：

1. 解除挂载
2. 关闭（锁上）加密卷设备

对于如上操作来说，解除挂载只需要如下指令：
```bash
# umount /media/dot
```
关闭（锁上）加密卷设备：
```bash
# cryptsetup close dot
```

## 一点题外话

如果你的设备和你在同一个区域/国家的话，加密你的设备**只能防止因为设备失窃带来的信息泄漏或者让你知道你的设备正在被第三方尝试获取（比如在你睡着的时候偷走设备进行取证）**。

此外，加密不等于备份，对于一个非备份用途的全盘加密磁盘来说，增量备份会变得更加困难，所以，建议此类使用场景的用户加入备份的元素，此外，给加密卷的 Header 和 Key-Slots 加密一下吧，方法见第二个参考链接.

## References

1. [Disk encryption - ArchWiki](https://wiki.archlinux.org/index.php/Disk_encryption)
2. [Frequentlyaskedquestions · Wiki · cryptsetup / cryptsetup · GitLab](https://gitlab.com/cryptsetup/cryptsetup/wikis/FrequentlyAskedQuestions#6-backup-and-data-recovery)
