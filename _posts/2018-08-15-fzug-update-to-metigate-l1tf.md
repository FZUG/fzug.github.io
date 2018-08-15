---
layout: post
title:  "更新你的 Fedora 系统，防范 L1TF 漏洞"
date:   2018-08-15 11:03:41 +0800
author: Zamir SUN
---

Intel 今天披露了一系列预测执行漏洞，并命名为 "[L1 Terminal Fault (L1TF)](https://www.intel.com/content/www/us/en/architecture-and-technology/l1tf.html)"。在安全研究领域，该漏洞又名 "Foreshadow"，它会影响 Intel 的部分产品。目前已为其指派了三个 CVE 编号：
* 针对[SGX](https://en.wikipedia.org/wiki/Software_Guard_Extensions) 的 [CVE-2018-3615](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3615)。
* 针对 SMM 和操作系统的 [CVE-2018-3620](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3620)。
* 针对虚拟化的 [CVE-2018-3646](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3646)。

L1TF 与 [“熔断”(Meltdown) 漏洞]({% post_url 2018-01-18-protect-fedora-system-meltdown %})类似，攻击者可以利用该漏洞访问任意被缓存在处理器的 [L1 数据缓存](https://lwn.net/Articles/252125/)物理内存。

## 防范 L1TF 漏洞

对于针对 SGX 的 CVE-2018-3615，请与您的硬件厂商联系获取相关微码或者固件更新。

对于 CVE-2018-3620 及 CVE-2018-3646，Fedora 的内核维护人员已经提交了修复该漏洞的[内核更新](https://bugzilla.redhat.com/show_bug.cgi?id=1615998)，新的内核版本是 4.17.14-2 。现在该内核尚在测试中。请及时关注后续动态并尽早更新内核到相关版本。

> 本文内容系 Fedora 中文用户组整理。
> 
> 参考资料:
> 
> https://www.intel.com/content/www/us/en/architecture-and-technology/l1tf.html
> 
> https://access.redhat.com/security/vulnerabilities/L1TF
>
> https://chinagdg.org/2018/08/protecting-against-the-new-l1tf-speculative-vulnerabilities/
