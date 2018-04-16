---
layout: post
title:  "在 Fedora 中使用 SSL VPN"
date:   2018-04-16 19:09:19 +0800
categories: vpn
---

> 有一家名叫深信服(Sangfor)的公司，貌似很多公司都是用他们家的 SSL VPN，尤其是让 Linux 用户头疼，因为他的连接方式是通过浏览器，而且浏览器必须得支持 Java 虚拟机，这年头，还有哪家浏览器继续支持着 Java 虚拟机。

如果你不能改变你所使用的 VPN，那就改变自己的使用方式。虽然很让人抓狂，但是 VPN 总还是要用的。下面介绍一下在 Fedora 上使用 Sangfor VPN 的解决方法。

来吧！

### 安装 Opera 10.60

这是 2010 年发布的版本，正好是 Sangfor 针对 Linux 教程的对应浏览器版本。就用这个吧。

[点击下载 Opera 10.60](!https://pan.baidu.com/s/1miNZCla)

下载完之后解压，运行 install 一切按默认方式安装即可完成。

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

### 启动 Opera

Opera 安装路径在 `~/.local/bin/opera` 里，在终端启动 Opera 可以找出缺少的依赖包

```
~/.local/bin/opera
```

启动了，接受一下条款。显然这个界面，已经不忍直视了。

![Opera](/assets/2018/04/16/opera.png "Opera")

首先执行以下操作

- 点击 `菜单 -> 设置 -> 首选项 -> 高级`，启用 `允许使用插件`
- 并在 `插件选项` 菜单中 `更改路径` ，将 `/usr/lib/mozilla/plugins` 添加进去。
- 在地址栏输入 `about:plugins` 查看是否安装了 Java 虚拟机。

如果没有则看终端输出内容

#### 情况一

```
/home/chinesejar/.local/lib/opera//operapluginwrapper-ia32-linux: error while loading shared libraries: libstdc++.so.6: cannot open shared object file: No such file or directory
```

以上输出提示缺少 `libstdc++.so.6` 这个依赖库

解决方法

```
sudo dnf install libstdc++-7.3.1-5.fc27.i686
```

#### 情况二

```
/home/chinesejar/.local/lib/opera//operapluginwrapper-ia32-linux: error while loading shared libraries: libXt.so.6: cannot open shared object file: No such file or directory
```

以上输出提示缺少 `libXt.so.6` 这个依赖库

解决方法

```
sudo dnf install libXt-1.1.5-6.fc27.i686
```

#### 情况三

```
/home/chinesejar/.local/lib/opera//operapluginwrapper-ia32-linux: error while loading shared libraries: libXext.so.6: cannot open shared object file: No such file or directory
```

以上输出提示缺少 `libXext.so.6` 这个依赖库

解决方法

```
sudo dnf install libXext-1.3.3-7.fc27.i686
```

**如果解决了以上三种情况， 至此，再打开 `about:plugins` 就能看到 Java 虚拟机了。**

![OperaPlugin](/assets/2018/04/16/opera_plugin.png "OperaPlugin")

#### 情况四

如果你在登录 SSL VPN 的过程中出现了以下情况

```
Opera : Failed to load library libgtk-x11-2.0.so.0
Opera : Failed to load library libgdk-x11-2.0.so.0
Opera : Failed to load library libglib-2.0.so.0
Opera : Failed to load library libgobject-2.0.so.0
Opera Plugin Proxy: Could not start up plugin
```

解决方法

```
sudo dnf install gtk2-2.24.32-1.fc27.i686
```

#### 情况五

如果你在登录 SSL VPN 的过程中出现了以下情况

```
(operapluginwrapper-ia32-linux:18506): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“adwaita”，

(operapluginwrapper-ia32-linux:18506): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“murrine”，

(operapluginwrapper-ia32-linux:18506): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“adwaita”，
Gtk-Message: Failed to load module "pk-gtk-module"
Gtk-Message: Failed to load module "canberra-gtk-module"
```

解决方法

```
sudo dnf install PackageKit-gtk3-module-1.1.8-1.fc27.i686
```

#### 情况六

如果你在登录 SSL VPN 的过程中出现了以下情况

```
(operapluginwrapper-ia32-linux:20892): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“adwaita”，

(operapluginwrapper-ia32-linux:20892): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“murrine”，

(operapluginwrapper-ia32-linux:20892): Gtk-WARNING **: 无法在模块路径中找到主题引擎：“adwaita”，
Gtk-Message: Failed to load module "canberra-gtk-module"

```

解决方法

```
sudo dnf install libcanberra-gtk2-0.30-15.fc27.i686
```

### 大功告成

依赖的 i686 包太多了，但总算装完了，登录了一下，总算是成功了。

![OperaTrust](/assets/2018/04/16/opera_trust.png "OperaTrust")

![OperaLogin](/assets/2018/04/16/opera_login.png "OperaLogin")
