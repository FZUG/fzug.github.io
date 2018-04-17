---
layout: post
title:  "在 Fedora 中使用 Sangfor 公司提供的 SSL VPN 解决方案"
date:   2018-04-16 19:09:19 +0800
categories: vpn
---

> 有一家名叫深信服(Sangfor)的公司，貌似很多公司都是用他们家的 SSL VPN，尤其是让 Linux 用户头疼，因为他的连接方式是通过浏览器，而且浏览器必须得支持 Java 虚拟机，这年头，还有哪家浏览器继续支持着 Java 虚拟机。

如果你不能改变你所使用的 VPN，那就改变自己的使用方式。虽然很让人抓狂，但是 VPN 总还是要用的。下面介绍一下在 Fedora 上使用 Sangfor VPN 的解决方法。

来吧！

### 安装 Firefox 52 ESR

Firefox 限制了 NPAPI 的支持。从 2017 年 3 月发布的 Firefox 52 开始，插件支持仅限于 Adobe Flash，去掉了 NPAPI 的支持，这直接影响了 Java、Silverlight 和其他基于 NPAPI 的插件。

Mozilla 提供 Firefox 的 ESR (Extended Support Release) 版本给需要扩展支持的用户。目前只有最新的 32 位的 Firefox 52 ESR 还在继续支持 Java 运行插件。

详情参考 [Java 针对不支持 Firefox 帮助说明的内容](https://java.com/en/download/help/firefox_java.xml)

访问 [Firefox ESR 下载](https://www.mozilla.org/en-US/firefox/organizations/all/) 页面下载 [32 位 Firefox 52 ESR](https://download.mozilla.org/?product=firefox-esr-latest-ssl&os=linux&lang=zh-CN)

下载完之后解压，放到指定路径下，执行 `firefox` 程序即可运行

```
tar jxvf firefox-52.7.3esr.tar.bz2
cd firefox
./firefox
```

![Firefox](/assets/2018/04/16/firefox.png "Firefox")

### 安装 Java 1.6 虚拟机

下载 Sangfor 提供的 jre-for-linux.bin

下载完执行以下命令即可安装完成

```
sudo chmod 777 jre-for-linux.bin
sudo ./jre-for-linux.bin
```

安装完成后将 jre 安装路径下的 `libnpjp2.so` 软链接到插件路径

```
cd /usr/lib/mozilla/plugins/
sudo ln -s /usr/java/jre1.6.0_27/lib/i386/libnpjp2.so
```

### 启动 Firefox

在终端启动 Firefox 可以找出缺少的依赖包。首先打开附加组件，查看插件选项中是否已经有了 Java Plugin，并激活它。

![FirefoxPlugin](/assets/2018/04/16/firefox_plugin.png "FirefoxPlugin")

如果碰到以下情况

```
XPCOMGlueLoad error for file /home/chinesejar/Downloads/firefox/libxul.so:
libdbus-glib-1.so.2: cannot open shared object file: No such file or directory
Couldn't load XPCOM.
```

以上输出提示缺少 `dbus-glib-0.110-2.fc28.i686` 这个依赖库

解决方法

```
sudo dnf install dbus-glib-0.110-2.fc28.i686
```

### 大功告成

总算装完了，登录了一下，成功了。

![FirefoxTrust](/assets/2018/04/16/firefox_trust.png "FirefoxTrust")

**免责声明**

我们只是提供一个解决方案，无法为他们的 SSL VPN 服务负责，更不是推广宣传其服务。
