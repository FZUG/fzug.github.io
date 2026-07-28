---
layout: page
title: 验证安装镜像
permalink: /verify/
---

下载 Fedora 安装镜像后，请务必校验文件完整性。以下是在各系统中验证 SHA256 校验和的方法。

## 准备工作

1. [下载 Fedora ISO 镜像](/download-fedora/)
2. 下载对应的 **CHECKSUM** 文件（下载页面的「校验」区域提供链接）

## Linux / macOS

```bash
# 进入下载目录，校验 ISO 文件
sha256sum -c Fedora-Workstation-*-CHECKSUM --ignore-missing

# 或手动比对
sha256sum Fedora-Workstation-Live-x86_64-*.iso
```

## Windows

```powershell
# PowerShell 中执行
Get-FileHash -Algorithm SHA256 .\Fedora-Workstation-Live-x86_64-*.iso
```

将命令输出的哈希值与 CHECKSUM 文件中的值比对，两者一致即为完整下载。

## 更多信息

- [Fedora 官方安全页面](https://fedoraproject.org/security/)
