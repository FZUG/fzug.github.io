---
layout: post
title: 在 Fedora 上安装使用 Pipenv
date: 2018-07-25 21:23:32 +0800
categories: python
---

> 本文翻译自 Michal Cyprian 的文章 [How to install Pipenv on Fedora](https://fedoramagazine.org/install-pipenv-fedora/)。

Pipenv 的目标是将打包生态中的精华（bundler、composer、npm、cargo、yarn 等等）引入到 Python 生态中来。它试图解决一些问题，并简化整个管理过程。

目前，Python 应用程序的依赖管理在某些时候似乎仍是一项挑战。开发人员通常为每个新项目创建一个[**虚拟环境**](https://packaging.python.org/tutorials/installing-packages/#creating-virtual-environments)，并使用 [**pip**](https://developer.fedoraproject.org/tech/languages/python/pypi-installation.html) 将依赖项安装到其中。此外，他们必须将已安装的软件包集合存储到 **requirements.txt** 文本文件中。诸多旨在自动化此工作流程的工具和包装器已经屡见不鲜了。但是，我们仍然需要组合多个实用程序，而且 **requirements.txt** 格式本身并不适用于更复杂的场景。

## 工具之中的集大成者

Pipenv 可以正确管理复杂的相互依赖关系，它还能提供已安装包的手动记录。例如，开发、测试和生产环境通常需要一组不同的包。过去，每个项目需要维护多个 **requirements.txt**。Pipenv 引入了新的 [Pipfile](https://github.com/pypa/pipfile) 格式文件，该文件中使用的是 [TOML](https://github.com/toml-lang/toml) 语法。得益于此，我们终于可以在单个文件中维护不同环境的多组需求了。

在将第一行代码提交到项目中后，仅一年时间，Pipenv 便已成为官方推荐的管理 Python 应用程序依赖关系的工具。现在它也终于进入了 Fedora 软件仓库。

## 在 Fedora 上安装 Pipenv

在全新安装 Fedora 28 或更高版本后，我们只需在终端上运行此命令即可安装 Pipenv：

```
$ sudo dnf install pipenv
```

现在，系统已经就绪，我们可以在 Pipenv 的协助下开发新的 Python 3 应用程序了。

还有一点比较重要的是，虽然这个工具为应用程序提供了很好的解决方案，但它并不是为处理库需求而设计的。编写 Python 库时，不需要固定依赖项。我们应该在 **setup.py** 文件中指定 **install_requires**。

## 基本依赖关系管理

首先为项目创建一个目录：

```
$ mkdir new-project && cd new-project
```

接下来为此项目创建虚拟环境：

```
$ pipenv --three
```

这里的 `--three` 选项是将虚拟环境的 Python 版本设置为Python 3。

安装依赖：

```
$ pipenv install requests
Installing requests…
Adding requests to Pipfile's [packages]…
Pipfile.lock not found, creating…
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
```

最后生成一个 lockfile：

```
$ pipenv lock
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
Updated Pipfile.lock (b14837)
```

我们还可以检查依赖关系图：

```
$ pipenv graph
- certifi [required: >=2017.4.17, installed: 2018.4.16]
- chardet [required: <3.1.0,>=3.0.2, installed: 3.0.4]
- idna [required: <2.8,>=2.5, installed: 2.7]
- urllib3 [required: >=1.21.1,<1.24, installed: 1.23]
```

有关 Pipenv 及其命令的更多详细信息，请参见[官方文档](https://docs.pipenv.org/)。
