---
layout: post
title:  "让Firefox Quantum在Fedora 27中看起来更加自然"
date:   2017-12-08 16:05:19 +0800
categories: firefox
---

你或许已经知道Fedora[已经引入了Firefox Quantum](https://fedoramagazine.org/firefox-57-coming-soon-quantum-leap/)。由于Firefox Quantum给扩展中心带来了新的变化，你可能会想知道如何让它在Fedora下看起来像一个原生的Fedora应用程序。这就是`客户端装饰`（client side decoration）。本文将会告诉你如何使用这个特性来让Firefox和其他Fedora下的应用更加类似。

# 配置客户端装饰

客户端装饰能让应用程序使用自己的窗口装饰。在传统的架构中，窗口管理器提供标题栏、关闭按钮、窗口边框、调整大小的手柄等。这些图形元素叫做（窗口）装饰。有了客户端装饰，应用程序能够告诉窗口管理器：“不了，谢谢你，我不想要这些装饰。”

Fedora 27中最新的Firefox Quantum原生支持客户端装饰。要使用这个特性，打开Firefox，在地址栏输入`about:config`，然后搜索`widget.allow-client-side-decoration`。双击这个条目，将值设置为`true`。好了，重启Firefox并享受新的外观吧。

![带客户端装饰的Firefox]({{ "/assets/2017/12/08/Screenshot-from-2017-11-17-13-55-05.png" }})

这是Firefox Quantum和其他[GNOME](https://www.gnome.org/)应用的对比图。如果你的GNOME使用非原装主题，客户端装饰也会从中选取一些自定义设定。客户端装饰产生的差异可能会更大一些。

![带客户端装饰的Firefox看起来更像GNOME原生应用](/assets/2017/12/08/Screenshot-from-2017-11-17-14-17-39.png)

> 本文翻译自 Muayyad Alsadi 的文章 [Make Firefox Quantum look native in Fedora 27](https://fedoramagazine.org/firefox-quantum-look-native-f27/)，图片同样来自该文章。
