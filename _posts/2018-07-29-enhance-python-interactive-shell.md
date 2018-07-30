---
layout: post
title: 使用交互式 shell 来改善 Python 编程体验
date: 2018-07-29 00:06:58 +0800
categories: python
---

> 本文翻译自 Clément Verna 的文章 [Enhance your Python with an interactive shell](https://fedoramagazine.org/enhance-python-interactive-shell/)。

Python 编程语言已成为 IT 业界最流行的语言之一。其成功的原因之一便是它可以用来解决多种问题。从 Web 开发到数据科学，机器学习到任务自动化，Python 生态系统包含多种流行的框架和库。本文会介绍 Fedora 软件包仓库中提供的一些实用的 Python shell，从而帮助开发。

## Python Shell

Python shell 可以让我们以交互的方式来使用解释器。对于测试代码或尝试新库来说非常有用。在 Fedora 中，我们可以在终端会话中键入 *python3* 来调用默认 shell。不过，Fedora 上还可以使用一些更高级的增强 shell。

## IPython

IPython 为 Python shell 提供了许多有用的增强功能。例如 tab 补全、对象内省（object introspection，按 tab 键可以提示相关参数、对象方法等——译者注）、系统 shell 访问和命令历史检索。其中许多功能也被 [Jupyter Notebook](https://ipython.org/notebook.html) 使用，因为它的底层就使用了 IPython。

### 安装并运行 IPython

```
dnf install ipython3
ipython3
```

使用 tab 补全可以列出可供选择的内容。当使用不熟悉的库时，这一功能会派上用场。

![](/assets/2018/07/29/ipython-tabcompletion.png)

如果需要更多信息，可以使用 *?* 命令来查阅其文档。如果需要更进一步的详细信息，可以使用 *??* 命令。

![](/assets/2018/07/29/ipyhton_doc1.png)

另一个很酷的功能就是可以使用 *!* 字符来执行系统 shell 命令。该命令的结果还可以引用在 IPython shell 中。

![](/assets/2018/07/29/ipython_shell.png)

IPython 完整的功能列表可以参考其[官方文档](https://ipython.readthedocs.io/en/stable/overview.html#main-features-of-the-interactive-shell)。

## bpython

bpython 与 IPython 有所不同，不过它依然在一个简单而且轻量级的包中提供了一系列非常有用的功能。bpython 提供的功能包括且不限于以下几种：

- 行内语法高亮
- 打字同时给出自动补全建议
- 预期参数列表
- 还可以将代码发送或保存至 pastebin 服务或文件

### 安装并运行 bpython

```
dnf install bpython3
bpython3
```

在输入的过程中，bpython 会提示自动补全代码的选择。

![](/assets/2018/07/29/bpython1.png)

调用函数或方法时，将自动显示预期参数和文档字符串。

![](/assets/2018/07/29/bpython2.png)

另一个简洁的功能是可以使用功能键 *F7* 在外部编辑器中打开当前的 bpython 会话（默认情况下为 *Vim*）。这在测试更复杂的程序时非常有用。

有关配置和功能的更多详细信息，请参阅 bpython [文档](https://docs.bpython-interpreter.org/)。

## 结语

使用增强的 Python shell 是提高工作效率的好办法。它为我们提供了增强功能，便于我们编写快速原型或试用新库。你使用的是增强型 Python shell 吗？欢迎和我们交流反馈。
